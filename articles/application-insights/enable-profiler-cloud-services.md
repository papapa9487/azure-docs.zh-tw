---
title: "針對雲端服務資源啟用 Azure Application Insights Profiler | Microsoft Docs"
description: "了解如何在 Azure 雲端服務資源所裝載的 ASP.NET 應用程式上設定分析工具。"
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
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c2cae6129386260f2bf35f75d44fa001f7541d40
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>在 Azure 雲端服務資源上啟用 Application Insights Profiler

本逐步解說示範如何在 Azure 雲端服務資源所裝載的 ASP.NET 應用程式上啟用 Azure Application Insights Profiler。 範例包括對於虛擬機器、虛擬機器擴展集和 Azure Service Fabric 的支援。 範例全部依賴支援 Azure Resource Manager 部署模型的範本。 如需部署模型的詳細資訊，請參閱 [Azure Resource Manager 與傳統部署：了解部署模型和資源的狀態](/azure-resource-manager/resource-manager-deployment-model)。

## <a name="overview"></a>概觀

下圖說明分析工具如何針對 Azure 雲端服務資源來運作。 其使用 Azure 虛擬機器作為範例。

![概觀](./media/enable-profiler-compute/overview.png) 若要收集在 Azure 入口網站上處理及顯示的資訊，您必須安裝 Azure 雲端服務資源的診斷代理程式元件。 逐步解說的其餘部分，會提供如何安裝和設定診斷代理程式以啟用 Application Insights Profiler 的指引。

## <a name="prerequisites-for-the-walkthrough"></a>教學課程的必要條件

* 在虛擬機器 ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) 或擴展集 ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)) 上，安裝分析工具代理程式的部署 Resource Manager 範本。

* 啟用 Application Insights 執行個體以進行分析。 如需指示，請參閱[啟用設定檔](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)。

* 已安裝在目標 Azure 雲端服務資源上的 .NET Framework 4.6.1 或更新版本。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>在您的 Azure 訂用帳戶中建立資源群組
下列範例示範如何使用 PowerShell 指令碼建立資源群組：

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>在資源群組中建立 Application Insights 資源
在 [Application Insights] 刀鋒視窗中，輸入您的資源資訊，如範例中所示： 

![[Application Insights] 刀鋒視窗](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>在 Azure Resource Manager 範本中套用 Application Insights 檢測金鑰

1. 如果您尚未下載範本，請從 [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json) 下載範本。

2. 尋找 Application Insights 金鑰。
   
   ![金鑰的位置](./media/enable-profiler-compute/copyaikey.png)

3. 取代範本值。
   
   ![在範本中取代的值](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>建立 Azure VM 以裝載 Web 應用程式
1. 建立安全字串以儲存密碼。

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. 部署 Azure Resource Manager 範本。

   將 PowerShell 主控台中的目錄變更為包含 Resource Manager 範本的資料夾。 若要部署範本，請執行下列命令：

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

順利執行指令碼之後，您應該可在資源群組中找到名為 **MyWindowsVM** 的 VM。

## <a name="configure-web-deploy-on-the-vm"></a>在 VM 上設定 Web Deploy
確定已在您的 VM 上啟用 Web Deploy，如此便可從 Visual Studio 發佈您的 Web 應用程式。

若要透過 WebPI 手動在 VM 上安裝 Web Deploy，請參閱[在 IIS 8.0 或更新版本上安裝和設定 Web Deploy](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)。 如需如何使用 Azure Resource Manager 範本自動安裝 Web Deploy 的範例，請參閱[建立、設定 Web 應用程式並部署到 Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)。

如果要部署 ASP.NET MVC 應用程式，必須移到 [伺服器管理員]，選取 [新增角色及功能] > [網頁伺服器 (IIS)] > [網頁伺服器] > [應用程式開發]，然後在您的伺服器上啟用 ASP.NET 4.5。

![新增 ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>將 Azure Application Insights SDK 安裝至專案
1. 在 Visual Studio 中，開啟您的 ASP.NET Web 應用程式。

2. 以滑鼠右鍵按一下專案，選取 [新增] > [已連線的服務]。

3. 選取 [Application Insights] 。

4. 遵循頁面上的指示執行。 選取您稍早建立的 Application Insights 資源。

5. 選取 [註冊] 按鈕。


## <a name="publish-the-project-to-an-azure-vm"></a>將專案發佈到 Azure VM
有數種方式可將應用程式發佈到 Azure VM。 一種方式是使用 Visual Studio 2017。

1. 以滑鼠右鍵按一下專案，然後選取 [發佈]。

2. 選取 [Microsoft Azure 虛擬機器] 做為發佈目標，並遵循步驟執行。

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. 針對您的應用程式執行負載測試。 您應該會在 Application Insights 執行個體入口網站網頁中看到結果。


## <a name="enable-the-profiler"></a>啟用分析工具
1. 移至 Application Insights 的 [效能] 刀鋒視窗，然後選取 [設定]。
   
   ![設定圖示](./media/enable-profiler-compute/enableprofiler1.png)
 
2. 選取 [啟用分析工具]。
   
   ![啟用分析工具圖示](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-a-performance-test-to-your-application"></a>將效能測試新增至您的應用程式
遵循下列步驟，就能收集要在 Application Insights Profiler 中顯示的一些範例資料：

1. 瀏覽至您稍早建立的 Application Insights 資源。 

2. 移至 [可用性] 刀鋒視窗，並新增效能測試，將 Web 要求傳送到您的應用程式 URL。 

   ![新增效能測試](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>檢視效能資料

1. 等候 10-15 分鐘，讓分析工具收集和分析資料。 

2. 移至 Application Insights 資源中的 [效能] 刀鋒視窗，並檢視當其低於負載時應用程式執行的狀況。

   ![檢視效能](./media/enable-profiler-compute/aiperformance.png)

3. 按一下 [範例] 下方的圖示，以開啟 [追蹤檢視] 刀鋒視窗。

   ![開啟 [追蹤檢視] 刀鋒視窗](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>使用現有的範本

1. 在部署範本中，找出 Azure 診斷資源宣告。
   
   如果您沒有宣告，您可以建立一個類似下列範例中的宣告。 您可以從 [Azure 資源總管網站](https://resources.azure.com)更新範本。

2. 將發行者從 `Microsoft.Azure.Diagnostics` 變更為 `AIP.Diagnostics.Test`。

3. 如需 `typeHandlerVersion`，請使用 `0.0`。

4. 請確定 `autoUpgradeMinorVersion` 已設為 `true`。

5. 在 `WadCfg` 設定物件中，加入新的 `ApplicationInsightsProfiler` 接收執行個體，如下列範例所示：

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

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>在虛擬機器擴展集上啟用分析工具
若要了解如何啟用分析工具，請下載 [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json) 範本。 將 VM 範本中的相同變更套用到虛擬機器擴展集的診斷延伸模組資源。

請確定擴展集中的每個執行個體具有網際網路的存取權。 然後 Profiler 代理程式可將收集的範例傳送給 Application Insights 以供顯示及分析。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>在 Service Fabric 應用程式上啟用分析工具
1. 將 Service Fabric 叢集佈建為具有安裝 Profiler 代理程式的 Azure 診斷延伸模組。

2. 在專案中安裝 Application Insights SDK 並設定 Application Insights 金鑰。

3. 新增應用程式程式碼以檢測遙測。

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>將 Service Fabric 叢集佈建為具有安裝 Profiler 代理程式的 Azure 診斷延伸模組
Service Fabric 叢集不一定是安全的。 您可將一個閘道叢集設定為不安全的，如此一來，它不需要憑證就能進行存取。 裝載商務邏輯和資料的叢集應該是安全的。 您可以在安全和不安全的 Service Fabric 叢集啟用分析工具。 本逐步解說使用不安全的叢集作為範例，來說明需要進行哪些變更才能啟用分析工具。 您可以透過相同方式來佈建安全的叢集。

1. 下載 [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。 VM 和虛擬機器擴展集的做法一樣，使用您的 Application Insights 金鑰來取代 `Application_Insights_Key`：

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

2. 使用 PowerShell 指令碼部署範本：

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>在專案中安裝 Application Insights SDK 並設定 Application Insights 金鑰
從 [NuGet 封裝](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)安裝 Application Insights SDK。 確定您已安裝穩定版本 2.3 或更新版本。 

如需在您的專案中設定 Application Insights 的相關資訊，請參閱[將 Service Fabric 與 Application Insights 搭配使用](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。

### <a name="add-application-code-to-instrument-telemetry"></a>新增應用程式程式碼以檢測遙測
1. 針對您想要檢測的任何程式碼片段，在其周圍加上 using 陳述式。 

   在下列範例中，`RunAsync` 方法會執行一些工作，而 `telemetryClient` 類別會在其啟動之後擷取遙測。 事件在整個應用程式中需要唯一的名稱。

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

2. 將應用程式部署到 Service Fabric 叢集。 等候 10 分鐘來執行應用程式。 如需更好的效果，您可以在應用程式上執行負載測試。 移至 Application Insights 入口網站的 [效能] 刀鋒視窗，您應該會看到顯示分析追蹤的範例。

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

- 在[分析工具的疑難排解](app-insights-profiler.md#troubleshooting)中尋找針對分析工具問題進行疑難排解的說明。

- 深入了解 [Application Insights Profiler](app-insights-profiler.md) 中的分析工具。

