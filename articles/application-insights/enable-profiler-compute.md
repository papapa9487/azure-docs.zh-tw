---
title: "針對 Azure 計算應用程式啟用 Application Insights Profiler | Microsoft Docs"
description: "了解如何在 Azure 計算中執行的應用程式上設定 Profiler。"
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>在 Azure 虛擬機器、Service Fabric 和雲端服務上啟用 Application Insights Profiler

本逐步解說示範如何在 Azure 計算資源所裝載的 Azure ASP.NET 應用程式上啟用 Application Insights Profiler。  
範例包括對於 Azure 虛擬機器、Azure 虛擬機器擴展集、Azure Service Fabric 和 Azure 雲端服務的支援。  
範例依賴支援 [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 部署模型的範本。  


## <a name="overview"></a>概觀

下圖說明 Profiler 如何針對 Azure 資源來運作。 其使用 Azure 虛擬機器作為範例。

![概觀](./media/enable-profiler-compute/overview.png)


若要完整啟用 Profiler，以下三個位置的設定都必須完成：

1. Application Insights 執行個體入口網站刀鋒視窗。
2. 應用程式原始程式碼 (例如：ASP.NET Web 應用程式)。
3. 環境部署定義原始程式碼 (例如：虛擬機器部署範本 json 檔案)。


## <a name="configure-the-application-insights-instance"></a>設定 Application Insights 執行個體

建立或造訪您要使用之 Application Insights 執行個體的 Azure 入口網站刀鋒視窗，並記下其檢測金鑰。 它對於其他設定步驟很有用：

  ![金鑰的位置](./media/enable-profiler-compute/CopyAIKey.png)

這個執行個體應該與您的應用程式設定來將遙測資料傳送至每個要求的執行個體相同。
此外，Profiler 結果可以在此執行個體中使用。  

依然在 Azure 入口網站中，遵循[啟用 Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) 以完成針對 Profiler 設定 AI 執行個體。 不需要針對本逐步解說的目的連結 Web 應用程式，只需要確定 Profiler 會在入口網站中啟用。


## <a name="configure-the-application-source-code"></a>設定應用程式原始程式碼

您的應用程式應該設定為將遙測資料傳送至每個 `Request` 作業上的 Application Insights 執行個體。  

1. 將 [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) 新增至您的應用程式專案。 請確定 nuget 套件版本如下：  
  - 針對 ASP.NET 應用程式：[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 版本 2.3.0 或更新版本。
  - 針對 ASP.NET Core 應用程式：[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0。 或更新版本。
  - 針對其他 .NET 和.NET 核心應用程式 (例如 Service Fabric 無狀態服務、雲端服務背景工作角色)：[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 或更新版本。  

2. 如果您的應用程式「不是」ASP.NET 或 ASP.NET Core 應用程式 (例如：雲端服務背景工作角色、Service Fabric 無狀態 API)，則需要下列額外的檢測設定：  

  2.1. 將下列程式碼新增至應用程式存留期中的早期點。  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  如需這個全域檢測金鑰設定的詳細資訊，請參閱[搭配 Application Insights 使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。  

  2.2. 針對您想要檢測的任何程式碼片段，在其周圍新增 `StartOperation<RequestTelemetry>``using` 陳述式，如下列範例所示：

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> 不支援在另一個 `StartOperation<RequestTelemetry>` 範圍中呼叫 `StartOperation<RequestTelemetry>`。 您可以改為在巢狀範圍中使用 `StartOperation<DependencyTelemetry>`。 範例：  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>設定環境部署定義

在 Profiler 和應用程式執行可以是虛擬機器、虛擬機器擴展集、Service Fabric 叢集或雲端服務的環境。  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>虛擬機器、虛擬機器擴展集或 Service Fabric

完整範例：  
  * [虛擬機器](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [虛擬機器擴展集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric 叢集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認部署的作業系統是 `Windows Server 2012 R2` 或更新版本就足夠。

2. 在部署範本檔案中找出 [Azure 診斷](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)擴充功能，並新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素，將 `ApplicationInsightsProfiler` 屬性值取代為您自己的 AI 檢測金鑰：  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

請參閱這些範例和[本指南](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以取得如何將診斷擴充功能新增至部署範本的詳細資訊。


### <a name="cloud-services"></a>雲端服務

1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認 `ServiceConfiguration.*.cscfg` 檔案包含 `osFamily` 作為 `"5"` 或更新版本就足夠。

2. 針對您的應用程式角色找出 [Azure 診斷](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)`diagnostics.wadcfgx`檔案：  
![診斷組態檔的位置](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
如果找不到檔案，請參閱[本指南](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)，以取得如何在 Azure 雲端服務專案中啟用診斷擴充功能的詳細資訊。

3. 新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素：  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> 如果 `diagnostics.wadcfgx` 檔案也包含類型 `ApplicationInsights` 的另一個接收，則必須符合所有三個檢測金鑰：  
>  * 您的應用程式所使用的檢測金鑰  
>  * `ApplicationInsights` 接收所使用的檢測金鑰  
>  * `ApplicationInsightsProfiler` 接收所使用的檢測金鑰  
>
>`ApplicationInsights` 接收接收所使用的檢測金鑰可以在 `ServiceConfiguration.*.cscfg` 檔案中找到。  
>Visual Studio 15.5 Azure SDK 發行之後，只有應用程式和 `ApplicationInsightsProfiler` 接收所使用的檢測金鑰必須彼此相符。


## <a name="environment-deployment-and-runtime-configurations"></a>環境部署和執行階段組態

1. 部署修改過的環境部署定義。  
一般而言，若要套用所做的修改，會牽涉到透過 PowerShell Cmdlet 或 Visual Studio 發佈的完整範本部署或雲端服務。  
以下是只會觸及其診斷擴充功能之 `Virtual Machines` 的替代方法：  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/platform/server.aspx) 執行，則必須啟用 `IIS Http Tracing` Windows 功能：  
  建立環境的遠端存取，然後使用 [新增 Windows 功能][]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) 視窗或在 PowerShell 中執行下列命令 (以系統管理員身分)：  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  如果建立遠端存取發生問題，您可以使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) 來執行下列命令：  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>如何在內部部署伺服器上啟用 Profiler

在獨立模式中也稱為執行中 AI Profiler (未繫結至診斷擴充功能修改)。  
我們尚未規劃正式針對內部部署伺服器支援 Profiler。
如果您對於此案例中的測試有興趣，您可以從[這裡](https://github.com/ramach-msft/AIProfiler-Standalone)下載支援程式碼。  
我們「不」負責維護該程式碼或回應相關的問題與功能要求。


## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如：啟動[可用性測試](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability))，並允許 10-15 分鐘讓追蹤開始傳送至 Application Insights 執行個體。

- 請參閱 Azure 入口網站中的 [Profiler 追蹤](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)。

- 在 [Profiler 疑難排解](app-insights-profiler.md#troubleshooting)中尋找針對 Profiler 問題進行疑難排解的說明。

- 深入了解 [Application Insights Profiler](app-insights-profiler.md) 中的 Profiler。
