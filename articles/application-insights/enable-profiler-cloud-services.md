---
title: "針對計算資源啟用 Azure Application Insights Profiler | Microsoft Docs"
description: "了解如何設定 Profiler"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>如何在 Azure 計算資源上啟用 Application Insights Profiler

本逐步解說示範如何在 Azure 計算資源所裝載的 ASP.NET 應用程式上啟用 Application Insights Profiler。 範例包括對於虛擬機器、虛擬機器擴展集和 Service Fabric 的支援。 所有範例全都依賴支援 Azure 資源管理部署模型的範本。 如需部署模型的詳細資訊，請參閱 [Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態](/azure-resource-manager/resource-manager-deployment-model)。

## <a name="overview"></a>概觀

下圖說明 Profiler 如何針對 Azure 計算資源來運作。 它使用 Azure 虛擬機器作為範例。

![概觀](./media/enable-profiler-compute/overview.png) 您必須安裝 Azure 計算資源的診斷代理程式元件，才能收集要在 Azure 入口網站上處理及顯示的資訊。 逐步解說的其餘部分會提供如何安裝和設定診斷代理程式以啟用 Application Insights Profiler 的指引。

## <a name="prerequisites-for-the-walkthrough"></a>教學課程的必要條件

* 下載部署 Resource Manager 範本，以在 VM 或擴展集上安裝 Profiler 代理程式。

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* 啟用 Application Insights 執行個體以進行分析。 檢查 https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler，以了解如何執行此作業。
* 在目標 Azure 計算資源中安裝 .NET Framework > = 4.6.1。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>在您的 Azure 訂用帳戶中建立資源群組
下列範例示範如何使用 PowerShell 指令碼建立資源群組：

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>在資源群組中建立 Application Insights 資源

![建立 Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中套用 Application Insights 檢測金鑰
如果您尚未下載範本，請從下列位置下載範本。 [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![尋找 AI 金鑰](./media/enable-profiler-compute/copyaikey.png)

![取代範本值](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>建立可裝載 Web 應用程式的 Azure VM
* 建立安全字串以儲存密碼
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* 部署 Azure Resource Manager 範本

將 PowerShell 主控台中的目錄變更為 Resource Manager 範本所在的資料夾。 若要部署範本，請執行下列命令：

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

順利執行指令碼之後，您應該可在資源群組中找到名為 *MyWindowsVM* 的 VM。

## <a name="configure-web-deploy-on-the-vm"></a>在 VM 上設定 Web Deploy
確定已在您的 VM 上啟用 **Web Deploy**，如此便可從 Visual Studio 發佈您的 Web 應用程式。

您可以透過 WebPI 手動在 VM 上安裝 Web Deploy：[在 IIS 8.0 或更新版本上安裝和設定 Web Deploy](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) \(英文\)

以下是如何使用 Azure Resource Manager 範本自動安裝 Web Deploy 的範例：[建立、設定 Web 應用程式並部署到 Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)

如果要部署 ASP.NET MVC 應用程式，必須移到 [伺服器管理員]、[新增角色及功能] | [網頁伺服器 (IIS)] | [網頁伺服器] | [應用程式開發]，然後在您的伺服器上啟用 ASP.NET 4.5。
![新增 ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>將 Azure Application Insights SDK 安裝至專案
* 在 Visual Studio 中，開啟您的 ASP.NET Web 應用程式
* 以滑鼠右鍵按一下專案並選取 [新增] | [已連線的服務]
* 選擇 Application Insights
* 遵循頁面上的指示執行。 選取您稍早建立的 Application Insights 資源
* 按一下 [註冊] 按鈕


## <a name="publish-the-project-to-azure-vm"></a>將專案發佈到 Azure VM
有數種方式可將應用程式發佈到 Azure VM。 一種方式是在 Visual Studio 2017 中執行此動作。
若要完成發佈程序，以滑鼠右鍵按一下專案，然後選取 [發佈…]。 選取 [Azure 虛擬機器] 作為發佈目標，並遵循步驟執行。

![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

針對您的應用程式中執行某些負載測試。 您應該能夠在 Application Insights 執行個體入口網站網頁中看到結果。


## <a name="enable-the-profiler-in-application-insights"></a>在 Application Insights 中啟用 Profiler
移至 Application Insights 的 [效能] 刀鋒視窗。 按一下 [設定] 圖示和 [啟用 Profiler]

![啟用 Profiler 的步驟 1](./media/enable-profiler-compute/enableprofiler1.png)

![啟用 Profiler 的步驟 2](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>在應用程式中新增可用性測試
瀏覽至您稍早建立的 Application Insights 資源。 移至 [可用性] 刀鋒視窗，並新增效能測試，將 Web 要求傳送到您的應用程式 URL。 如此一來，我們就能收集要在 Application Insights Profiler 中顯示的一些範例資料

![新增效能測試][./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>檢視效能資料

等候 10-15 分鐘，讓 Profiler 能夠收集和分析資料。 接著，移至 AI 資源中的 [效能] 刀鋒視窗，並檢視當其低於負載時應用程式執行的狀況。

![檢視效能][./media/enable-profiler-compute/view-aiperformance.png]

按一下 [範例] 下方的圖示，以開啟 [追蹤檢視] 刀鋒視窗。

![追蹤檢視][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>使用現有的範本

1. 在部署範本中，找出 Windows Azure 診斷 (WAD) 資源宣告。
  * 如果您還沒有宣告，請建立一個 (請參閱如何完成此動作的完整範例)。
  * 您可以從 Azure 資源網站 (https://resources.azure.com) 更新範本。
2. 將發佈者從 "Microsoft.Azure.Diagnostics" 修改為 "AIP.Diagnostics.Test"。
3. 使用 typeHandlerVersion 作為 "0.0"。
4. 確定已將 autoUpgradeMinorVersion 設為 true。
5. 在 WadCfg 設定物件中，加入新的 ApplicationInsightsProfiler 接收執行個體，如下列範例所示。

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>在虛擬機器擴展集上啟用 Profiler
下載 [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json) 範本，以了解如何啟用 Profiler。 將 VM 範本中的相同變更套用到虛擬機器擴展集診斷延伸模組資源。
確定擴展集中的每個執行個體都能存取網際網路，如此一來，Profiler 代理程式就能將所收集的樣本傳送到 Application Insights 以進行分析及顯示。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>在 Service Fabric 應用程式上啟用 Profiler
目前在 Service Fabric 應用程式上啟用 Profiler需要下列項目：
1. 佈建 Service Fabric 叢集，其中具有可安裝 Profiler 代理程式的 WAD 延伸模組
2. 在專案中安裝 Application Insights SDK 並設定 AI 金鑰
3. 新增應用程式程式碼以檢測遙測

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>佈建 Service Fabric 叢集，其中具有可安裝 Profiler 代理程式的 WAD 延伸模組
Service Fabric 叢集不一定是安全的。 您可將一個閘道叢集設定為不安全的，如此一來，它不需要憑證就能進行存取。 裝載商務邏輯和資料的叢集應該是安全的。 您可以在安全和不安全的 Service Fabric 叢集啟用 Profiler。 本逐步解說使用不安全的叢集作為範例，來說明需要進行哪些變更才能啟用 Profiler。 您可以透過相同方式來佈建安全的叢集。

下載 [ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json)。 與 VM 和虛擬機器擴展集一樣，使用您的 AI 金鑰來取代 Application Insights 金鑰：

```
"publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
```

使用 PowerShell 指令碼部署範本：
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>在專案中安裝 Application Insights SDK 並設定 AI 金鑰
從 NuGet 封裝安裝 Application Insights SDK。 確定您已安裝的穩定版本 2.3 或更新版本。 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 請參閱[搭配 Application Insights 使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) \(英文\)，以便在您的專案中設定 Application Insights。

## <a name="add-application-code-to-instrument-telemetry"></a>新增應用程式程式碼以檢測遙測
針對您想要檢測的任何程式碼片段，在其周圍加上 using 陳述式。 在下列範例中，以下的 RunAsync 方法會執行一些工作，而 telemetryClient 類別會在其啟動之後擷取遙測。 事件在整個應用程式中需要唯一的名稱。

```
protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
```

將應用程式部署到 Service Fabric 叢集。 等候 10 分鐘來執行應用程式。 如需更好的效果，您可以在應用程式上執行負載測試。 移至 Application Insights 入口網站的 [效能] 刀鋒視窗，您應該會看到顯示分析追蹤的範例。

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>後續步驟

- 尋找針對分析工具問題進行疑難排解的協助：[分析工具的疑難排解](app-insights-profiler.md#troubleshooting)

- 深入了解分析工具：[Application Insights Profiler](app-insights-profiler.md)。

