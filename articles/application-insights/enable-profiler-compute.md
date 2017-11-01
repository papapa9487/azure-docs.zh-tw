---
title: "針對 Azure 計算應用程式啟用 Application Insights Profiler | Microsoft Docs"
description: "了解如何在 Azure 計算中執行的應用程式上設定 Application Insights Profiler。"
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 66ea24cfe9dd03ed62c06daa76ee043886ad7bcc
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>針對 Azure VM、Service Fabric 和雲端服務啟用 Application Insights Profiler

本文示範如何在 Azure 計算資源所裝載的 Azure ASP.NET 應用程式上啟用 Application Insights Profiler。 

本文中的範例包括對於 Azure 虛擬機器、虛擬機器擴展集、Azure Service Fabric 和 Azure 雲端服務的支援。 範例依賴支援 [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 部署模型的範本。  


## <a name="overview"></a>概觀

下圖顯示 Application Insights Profiler 如何使用 Azure 資源。 映像使用 Azure 虛擬機器作為範例。

  ![概觀](./media/enable-profiler-compute/overview.png)

若要完全啟用分析工具，您必須變更三個位置的設定：

* Azure 入口網站中的 Application Insights 執行個體窗格。
* 應用程式原始程式碼 (例如，ASP.NET Web 應用程式)。
* 環境部署定義原始程式碼 (例如，VM 部署範本 .json 檔案)。


## <a name="set-up-the-application-insights-instance"></a>設定 Application Insights 執行個體

在 Azure 入口網站中，建立或前往您想要使用的 Application Insights 資源。 記下執行個體檢測金鑰。 您會在其他設定步驟使用檢測金鑰。

  ![金鑰檢測的位置](./media/enable-profiler-compute/CopyAIKey.png)

這個執行個體應該與您的應用程式相同。 它被設定來將遙測資料傳送至每個要求。
Profiler 結果也可以在此執行個體中使用。  

在 Azure 入口網站中，完成[啟用分析工具](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)中所述的步驟，以完成設定分析工具的 Application Insights 執行個體。 您不需要針對這篇文章中的範例連結 Web 應用程式。 只要確定在入口網站中啟用分析工具即可。


## <a name="set-up-the-application-source-code"></a>設定應用程式原始程式碼

設定您的應用程式以將遙測資料傳送至每個 `Request` 作業上的 Application Insights 執行個體：  

1. 將 [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) 新增至您的應用程式專案。 請確定 NuGet 套件版本如下：  
  - 針對 ASP.NET 應用程式：[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 或更新版本。
  - 針對 ASP.NET Core 應用程式：[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 或更新版本。
  - 針對其他 .NET 和.NET Core 應用程式 (例如，Service Fabric 無狀態服務或雲端服務背景工作角色)：[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 或更新版本。  

2. 如果您的應用程式「不是」ASP.NET 或 ASP.NET Core 應用程式 (例如，如果是雲端服務背景工作角色或 Service Fabric 無狀態 API)，則需要下列額外的檢測設定：  

  1. 在應用程式存留期早期新增下列程式碼：  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  如需這個全域檢測金鑰設定的詳細資訊，請參閱[搭配 Application Insights 使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。  

  2. 針對您想要檢測的任何程式碼片段，在其周圍新增 `StartOperation<RequestTelemetry>` **USING** 陳述式，如下列範例所示：

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

  不支援在另一個 `StartOperation<RequestTelemetry>` 範圍中呼叫 `StartOperation<RequestTelemetry>`。 您可以改為在巢狀範圍中使用 `StartOperation<DependencyTelemetry>`。 例如：  

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


## <a name="set-up-the-environment-deployment-definition"></a>設定環境部署定義

在 Profiler 和應用程式執行可以是虛擬機器、虛擬機器擴展集、Service Fabric 叢集或雲端服務執行個體的環境。  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>虛擬機器、虛擬機器擴展集或 Service Fabric

完整範例：  
  * [虛擬機器](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [虛擬機器擴展集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric 叢集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認部署的作業系統是 `Windows Server 2012 R2` 或更新版本就足夠。

2. 在部署範本檔案中找出 [Azure 診斷](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)擴充功能，並新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素。 將 `ApplicationInsightsProfiler` 屬性值取代為您自己的 Application Insights 檢測金鑰：  
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

  如需將診斷擴充功能新增至部署範本的詳細資訊，請參閱[使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


### <a name="cloud-services"></a>雲端服務

1. 若要確保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本，確認 ServiceConfiguration.\*.cscfg 檔案的 `osFamily` 值為 **"5"** 或更新版本就足夠。

2. 針對您的應用程式角色找出 [Azure 診斷](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 檔案：  
  ![診斷組態檔的位置](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  如果找不到檔案以便了解如何在您的雲端服務專案中啟用診斷擴充功能，請參閱[為 Azure 雲端服務和虛擬機器設定診斷](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)。

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
>  * 您的應用程式所使用的檢測金鑰。  
>  * `ApplicationInsights` 接收所使用的檢測金鑰。  
>  * `ApplicationInsightsProfiler` 接收所使用的檢測金鑰。  
>
> 您可以在 ServiceConfiguration.\*.cscfg 檔案中找到 `ApplicationInsights` 接收所使用的實際檢測金鑰值。  
> Visual Studio 15.5 Azure SDK 發行之後，只有應用程式和 `ApplicationInsightsProfiler` 接收所使用的檢測金鑰必須彼此相符。


## <a name="environment-deployment-and-runtime-configurations"></a>環境部署和執行階段組態

1. 部署修改過的環境部署定義。  

  若要套用所做的修改，通常會牽涉到透過 PowerShell Cmdlet 或 Visual Studio 發佈的完整範本部署或雲端服務。  

  以下是只會觸及其 Azure 診斷擴充功能之現有虛擬機器的替代方法：  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. 如果預期的應用程式是透過 [IIS](https://www.microsoft.com/web/platform/server.aspx) 執行，則啟用 `IIS Http Tracing` Windows 功能：  
  
  1. 建立環境的遠端存取，然後使用 [新增 Windows 功能][]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) 視窗或在 PowerShell 中執行下列命令 (以系統管理員身分)：  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. 如果建立遠端存取發生問題，您可以使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) 來執行下列命令：  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>在內部部署伺服器上啟用 Profiler

在內部部署伺服器上啟用分析工具也稱為在獨立模式中 (未繫結至 Azure 診斷擴充程式修改) 執行Application Insights Profiler。 

我們尚未規劃正式針對內部部署伺服器支援分析工具。 如果您對於此案例中的測試有興趣，您可以[下載支援程式碼](https://github.com/ramach-msft/AIProfiler-Standalone)。 我們「不」負責維護該程式碼，或回應與該程式碼相關的問題和功能要求。

## <a name="next-steps"></a>後續步驟

- 產生應用程式的流量 (例如，啟動[可用性測試](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
- 請參閱 Azure 入口網站中的 [Profiler 追蹤](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)。
- 在 [Profiler 疑難排解](app-insights-profiler.md#troubleshooting)中取得針對 Profiler 問題進行疑難排解的說明。
- 深入了解 [Application Insights Profiler](app-insights-profiler.md) 中的分析工具。
