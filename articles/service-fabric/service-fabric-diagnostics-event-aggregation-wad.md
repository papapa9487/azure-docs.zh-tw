---
title: "使用 Windows Azure 診斷的 Azure Service Fabric 事件彙總 | Microsoft Docs"
description: "了解如何使用 WAD 彙總及收集事件，來監視和診斷 Azure Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 5773361fdec4cb8ee54fa2856f6aa969d5dac4e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>使用 Windows Azure 診斷的事件彙總和收集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。 將記錄檔集中在中央位置，可協助您分析並針對叢集或該叢集中執行之應用程式與服務的問題進行疑難排解。

上傳和收集記錄的其中一種方式就是使用「Windows Azure 診斷 (WAD)」延伸模組，此延伸模組可將記錄上傳到「Azure 儲存體」，也可以選擇將記錄傳送至 Azure Application Insights 或「事件中樞」。 您也可以使用外部程序來讀取儲存體中的事件，然後將它們放在 [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) 這類的分析平台產品或其他記錄剖析解決方案中。

## <a name="prerequisites"></a>必要條件
這些工具是用來執行這份文件中的某些作業：

* [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md) (與 Azure 雲端服務相關，但具備有用的資訊和範例)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager 用戶端](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager 範本](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>記錄和事件來源

### <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
如[本文](service-fabric-diagnostics-event-generation-infra.md)所述，Service Fabric 會設定一些現成的記錄通道；其中，使用 WAD 可以輕鬆地設定下列通道，將監視和診斷資料傳送至儲存體資料表或其他位置：
  * 操作事件：Service Fabric 平台所執行之較高層級的作業。 範例包括建立應用程式和服務、節點狀態變更和升級資訊。 這些是以 Windows 事件追蹤 (ETW) 記錄的形式發出。
  * [Reliable Actors 程式設計模型事件](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services 程式設計模型事件](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>應用程式事件
 從您的應用程式和服務程式碼發出，且使用 Visual Studio 範本中所提供的 EventSource 協助程式類別所寫出的事件。 如需如何從應用程式寫入 EventSource 記錄的詳細資訊，請參閱[監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署診斷擴充功能
收集記錄檔的第一個步驟是將診斷擴充功能部署在 Service Fabric 叢集的每個 WM 上。 診斷擴充功能會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。 步驟視您使用 Azure 入口網站或 Azure Resource Manager 而稍微有所不同。 步驟也會視部署為叢集建立的一部分，或是針對現有的叢集而有所不同。 讓我們看看每個案例的步驟。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>透過 Azure 入口網站建立叢集時部署診斷延伸模組
為了在建立叢集時將診斷延伸模組部署至叢集中的 WM，您會使用下圖所示的 [診斷設定] 面板。請確定 [診斷] 設定為 [開啟] \(預設設定\) 。 建立叢集之後，您就無法使用入口網站變更這些設定。

![入口網站中用於建立叢集的 Azure 診斷設定](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

當您使用入口網站來建立叢集時，強烈建議您**在按一下 [確定] 來建立叢集之前**，先下載範本。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本來設定 Service Fabric 叢集](service-fabric-cluster-creation-via-arm.md)。 您需要範本以在稍後進行變更，因為您無法使用入口網站進行某些變更。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 在建立叢集時部署診斷擴充功能
若要使用 Resource Manager 建立叢集，您需要在建立叢集之前，將診斷組態 JSON 加入至完整的叢集 Resource Manager 範本。 我們在 Resource Manager 範本範例中提供一個五 VM 叢集 Resource Manager 範本，且已在其中加入診斷設定。 您可以在 Azure 資源庫中的這個位置看到它： [具有診斷 Resource Manager 範本範例的五節點叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)。

若要查看 Resource Manager 範本中的 [診斷] 設定，請開啟 azuredeploy.json 檔案，並搜尋 **IaaSDiagnostics**。 若要使用這個範本建立叢集，請選取上一個連結所提供的 [部署到 Azure] 按鈕。

或者，您也可以下載資源管理員範例，對它進行變更，然後在 Azure PowerShell 視窗中使用 `New-AzureRmResourceGroupDeployment` 命令來使用修改過的範本建立叢集。 針對您傳遞給命令的參數，請參閱以下程式碼。 如需如何使用 PowerShell 部署資源群組的詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](../azure-resource-manager/resource-group-template-deploy.md)。

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>將診斷擴充功能部署到現有的叢集
如果您具有未部署診斷的現有叢集，或是想要修改現有的組態，您可以使用下列步驟來新增或更新它。 修改用來建立現有叢集的 Resource Manager 範本，或是以上述方式從入口網站下載範本。 執行下列工作來修改 template.json 檔案。

藉由新增儲存體資源到資源區段，以將其新增至範本。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 接下來，新增至參數區段中儲存體帳戶定義之後的位置，位於 `supportLogStorageAccountName` 和 `vmNodeType0Name` 之間。 以儲存體帳戶的名稱取代預留位置文字 *storage account name goes here*。

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
然後，透過在擴充功能陣列內新增下列內容，更新 template.json 檔案的 `VirtualMachineProfile` 區段。 請務必在開頭或結尾 (取決於其插入的位置) 加入逗點。

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

如所述修改 template.json 檔案之後，將 Resource Manager 範本重新發佈。 如果已匯出範本，執行 deploy.ps1 檔案將會重新發佈範本。 部署之後，請確認 **ProvisioningState** 為 **Succeeded**。

## <a name="collect-health-and-load-events"></a>收集健康情況和負載事件

從 Service Fabric 5.4 版開始，健康情況和負載計量事件均可供收集。 這些事件可藉由使用健康情況或負載報告 API (例如 [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) 或 [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx))，反映系統或程式碼所產生的事件。 這可讓您彙總及檢視一段時間的系統健康情況，以及根據健康情況或負載事件發出警示。 若要在 Visual Studio 的「診斷事件檢視器」中檢視這些事件，請將 "Microsoft-ServiceFabric:4:0x4000000000000008" 新增到 ETW 提供者清單中。

若要收集事件，請修改 Resource Manager 範本，使其包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>收集反向 proxy 事件

從 Service Fabric 5.7 版開始，[反向 proxy](service-fabric-reverseproxy.md) 事件可供收集。
反向 proxy 會將事件發至兩個通道，其中一個包含可反映要求處理失敗的錯誤事件，而另一個包含在反向 proxy 處理之所有要求的詳細資訊事件。 

1. 收集錯誤事件︰若要在 Visual Studio 的「診斷事件檢視器」中檢視這些事件，請將 "Microsoft-ServiceFabric:4:0x4000000000000010" 新增到 ETW 提供者清單中。
若要從 Azure 叢集收集事件，請修改 Resource Manager 範本，使其包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. 收集所有要求處理事件：在 Visual Studio 的診斷事件檢視器中，將 ETW 提供者清單中的 Microsoft-ServiceFabric 項目更新為 "Microsoft-ServiceFabric:4:0x4000000000000020"。
對於 Azure Service Fabric 叢集，修改資源管理員範本，使其包含

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> 建議審慎地允許從此通道收集事件，因為這可透過反向 proxy 收集所有流量並可快速地取用儲存體容量。

對於 Azure Service Fabric 叢集，所有節點的事件都會收集在 SystemEventTable 中並且彙總。
如需反向 proxy 事件的詳細疑難排解，請參閱[反向 proxy 診斷指南](service-fabric-reverse-proxy-diagnostics.md)。

## <a name="collect-from-new-eventsource-channels"></a>從新的 EventSource 通道收集

若要更新診斷以從新的 EventSource 通道 (代表您將要部署的新應用程式) 收集記錄，請執行先前所述的相同步驟，以針對現有叢集設定診斷。

在您使用 `New-AzureRmResourceGroupDeployment` PowerShell 命令套用組態更新之前，請先更新 template.json 檔案中的 `EtwEventSourceProviderConfiguration` 區段，以新增 EventSource 通道的項目。 在 Visual Studio 產生的 ServiceEventSource.cs 檔案中，事件來源的名稱定義為程式碼的一部分。

例如，如果您的事件資源名稱是 My-Eventsource，新增下列程式碼以將來自 My-Eventsource 的事件置於名稱為 MyDestinationTableName 的資料表中。

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

若要收集效能計數器或事件記錄檔，請以[使用 Azure Resource Manager 範本建立具有監視和診斷的 Windows 虛擬機器](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中提供的範例來修改 Resource Manager 範本。 然後請重新發佈 Resource Manager 範本。

## <a name="collect-performance-counters"></a>收集效能計數器

若要收集叢集中的效能計量，請在叢集的 Resource Manager 範本中將效能計數器新增至 "WadCfg > DiagnosticMonitorConfiguration"。 如需我們建議收集的效能計數器，請參閱 [Service Fabric 效能計數器](service-fabric-diagnostics-event-generation-perf.md)。

例如，我們在這裡設定一個效能計數器，其每 15 秒取樣一次 (此值可以變更，並遵循格式 "PT\<時間>\<單位>"，例如，PT3M 會以三分鐘的間隔取樣一次)，且每分鐘傳送到適當的儲存體資料表一次。

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
如果您要使用 Application Insights 接收 (如下節所述)，而且想要將這些計量顯示在 Application Insights 中，則請確定在 "sinks" 區段中新增接收名稱 (如上所示)。 此外，請考慮建立個別的資料表以傳送效能計數器，讓它們不會塞滿來自其他已啟用之記錄通道的資料。


## <a name="send-logs-to-application-insights"></a>將記錄傳送至 Application Insights

將監視和診斷資料傳送至 Application Insights (AI) 的作業，可以在設定 WAD 時進行。 如果您決定使用 AI 進行事件分析和視覺效果，請閱讀[使用 Application Insights 進行事件分析和視覺效果](service-fabric-diagnostics-event-analysis-appinsights.md)，以在 "WadCfg" 時設定「AI 接收」。

## <a name="next-steps"></a>後續步驟

正確設定 Azure 診斷之後，您會從 ETW 和 EventSource 記錄中看到「儲存體」資料表中的資料。 如果您選擇使用 OMS、Kibana 或未直接設定於 Resource Manager 範本的任何其他資料分析和視覺效果平台，請務必設定您要用來讀取這些儲存體資料表資料的平台。 對 OMS 而言，這是非常一般的作業，且會在[透過 OMS 的事件和記錄分析](service-fabric-diagnostics-event-analysis-oms.md)中進行。 也就是說，Application Insights 是特殊案例，因為它可以在設定診斷延伸模組時設定；因此，如果您選擇使用 AI，則請參閱[適當的文章](service-fabric-diagnostics-event-analysis-appinsights.md)。

>[!NOTE]
>目前沒有任何方法可以篩選或清理已傳送至資料表的事件。 如果不實作從資料表移除事件的處理序，資料表將會繼續成長。 目前，我們可以提供具有 [Watchdog 範例](https://github.com/Azure-Samples/service-fabric-watchdog-service)中所執行資料清理服務的範例，除非您必須儲存超過 30 或 90 天時間範圍的記錄，否則建議您自行撰寫資料清理服務。

* [了解如何使用診斷擴充功能收集效能計數器或記錄檔](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Application Insights 進行事件分析和視覺效果](service-fabric-diagnostics-event-analysis-appinsights.md)
* [使用 OMS 進行事件分析和視覺效果](service-fabric-diagnostics-event-analysis-oms.md)