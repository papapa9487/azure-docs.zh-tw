---
title: "自動調整與虛擬機器擴展集 | Microsoft Docs"
description: "深入了解使用診斷和自動調整資源，以自動調整擴展集中的虛擬機器。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: adegeo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06ff9d9ae1dd8256f0d22c1a60ed6a85554f1f17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-automatic-scaling-and-virtual-machine-scale-sets"></a>如何使用自動調整與虛擬機器擴展集
擴展集內的虛擬機器自動調整是依需求建立或刪除集合中的機器以符合效能需求。 當工作量增長時，應用程式可能需要額外的資源，才能有效執行工作。

自動調整是自動化程序，可協助減輕管理額外負荷。 透過減少額外負荷，您便不需要持續監視系統效能或決定資源的管理方式。 調整是一項彈性的程序。 當負載增加時，可以新增更多資源。 此外，可隨著需求的降低來移除資源，以將成本降至最低並維持效能層級。

使用 Azure Resource Manager 範本、Azure PowerShell、Azure CLI 或 Azure 入口網站，在擴展集上設定自動調整。

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>使用 Resource Manager 範本，設定調整
您不是分開部署與管理應用程式的每個資源，而是使用範本，藉此經由協調的單一作業來部署所有資源。 在範本中，會定義應用程式資源，且會針對不同的環境指定部署參數。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。 若要深入了解，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

在範本中，您可以指定容量元素︰

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 3
},
```

容量會識別集合中的虛擬機器數目。 您可以使用不同值部署範本，手動變更容量。 如果您部署範本只是要變更容量，您可以僅包含具有更新容量的 SKU 元素。

透過使用 **autoscaleSettings** 資源和診斷擴充功能，可自動調整擴展集的容量。

### <a name="configure-the-azure-diagnostics-extension"></a>設定 Azure 診斷擴充
自動調整只能夠在擴展集中的每個虛擬機器上的度量集合成功時完成。 Azure 診斷擴充提供監視和診斷功能，符合自動調整資源的度量集合需求。 您可以安裝擴充做為 Resource Manager 範本的一部分。

此範例會顯示在範本中用來設定診斷擴充的變數︰

```json
"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
"wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
```

參數將會於範本部署時提供。 在這個範例中，會提供儲存體帳戶名稱 (在其中儲存資料) 及擴展集名稱 (在其中收集資料)。 此外，在這個 Windows Server 範例中，將只會收集執行緒計數效能計數器。 Windows 或 Linux 中所有可用的效能計數器都可以用來收集診斷資訊，並且可以包含在擴充組態中。

此範例會顯示範本中擴充的定義︰

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Insights.VMDiagnosticsSettings",
      "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
          "storageAccount": "[variables('diagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
          "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
          "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
          "storageAccountEndPoint": "https://core.windows.net"
        }
      }
    }
  ]
}
```

當診斷擴充功能執行時，會在您指定的儲存體帳戶中，於資料表中儲存和收集資料。 在 **WADPerformanceCounters** 資料表中，您可以找到收集的資料：

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>設定 autoScaleSettings 資源
autoscaleSettings 資源會使用來自診斷擴充的資訊，以決定是否要在擴展集中增加或減少虛擬機器數目。

此範例會顯示範本中資源的組態︰

```json
{
  "type": "Microsoft.Insights/autoscaleSettings",
  "apiVersion": "2015-04-01",
  "name": "[concat(parameters('resourcePrefix'),'as1')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
  ],
  "properties": {
    "enabled": true,
    "name": "[concat(parameters('resourcePrefix'),'as1')]",
    "profiles": [
      {
        "name": "Profile1",
        "capacity": {
          "minimum": "1",
          "maximum": "10",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 650
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 550
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ],
    "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
  }
}
```

在上述範例中，兩個規則用於定義自動調整動作。 第一個規則定義相應放大動作，第二個規則定義相應縮小動作。 在規則中提供這些值︰

| 規則 | 說明 |
| ---- | ----------- |
| metricName        | 此值與您在診斷擴充功能的 wadperfcounter 變數中定義的效能計數器相同。 在上述範例中，會使用「執行緒計數」計數器。    |
| metricResourceUri | 此值是虛擬機器擴展集的資源識別碼。 這個識別碼包含資源群組的名稱、資源提供者的名稱和要調整的擴展集的名稱。 |
| timeGrain         | 此值是所收集之計量的精細度。 在先前的範例中，資料是以一分鐘的間隔進行收集。 此值是搭配 timeWindow 使用。 |
| statistic         | 此值會決定如何結合計量以因應自動調整動作的需要。 可能的值為：Average、Min、Max。 |
| timeWindow        | 此值是收集執行個體資料的時間範圍。 其值必須介於 5 分鐘到 12 小時之間。 |
| timeAggregation   | 此值會決定收集的資料應如何隨著時間結合。 預設值為 Average。 可能的值為：Average、Minimum、Maximum、Last、Total、Count。 |
| operator          | 此值是用來比較計量資料和臨界值的運算子。 可能的值為：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。 |
| threshold         | 此值是觸發調整動作的值。 請確定會在**相應放大**及**相應縮小**動作的臨界值之間提供足夠的差異。 如果您為這兩個動作設定相同值，系統會預期持續性變更，這會防止系統實作調整動作。 例如，在先前的範例中將兩者設定為 600 個執行緒將無法運作。 |
| direction         | 此值會決定達到臨界值時所採取的動作。 可能的值為 Increase 或 Decrease。 |
| 類型              | 此值是所應採取的動作類型，必須設為 ChangeCount。 |
| value             | 此值是從擴展集內新增或移除的虛擬機器數目。 此值必須是 1 或更大。 |
| cooldown          | 此值是在上一個調整動作之後、下一個動作執行之前的等待時間量。 此值必須介於一分鐘到一週之間。 |

根據您使用的效能計數器而定，會以不同的方式使用範本設定中的某些元素。 在先前的範例中，效能計數器是「執行緒計數」、針對相應放大動作的臨界值為 650，而針對相應縮小動作的臨界值則為 550。 如果您使用如 %Processor Time 的計數器，臨界值會設定為能判斷調整動作的 CPU 使用量百分比。

觸發調整動作 (例如高負載) 時，集合的容量會根據範本中的值而增加。 例如，在容量設為 3 且調整動作值設為 1 的擴展集中：

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

當目前的負載導致平均執行緒計數超過 650 的臨界值時：

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

會觸發**相應放大**動作，讓集合的容量增加 1：

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 4
},
```

結果會將虛擬機器新增至擴展集：

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

經過五分鐘的冷卻期間之後，如果機器上的執行緒平均數目仍然超過 600，則會將另一部機器新增至集合。 如果平均執行緒計數保持在 550 以下，則擴展集的容量會減少 1，且機器會從集合中移除。

## <a name="set-up-scaling-using-azure-powershell"></a>使用 Azure PowerShell 設定調整

若要查看使用 PowerShell 設定自動調整的範例，請參閱 [Azure 監視器 PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md)。

## <a name="set-up-scaling-using-azure-cli"></a>使用 Azure CLI 設定調整

若要查看使用 Azure CLI 設定自動調整的範例，請參閱 [Azure 監視器跨平台 CLI 快速入門範例](../monitoring-and-diagnostics/insights-cli-samples.md)。

## <a name="set-up-scaling-using-the-azure-portal"></a>使用 Azure 入口網站設定調整

若要查看使用 Azure 入口網站來設定自動調整的範例，請參閱[使用 Azure 入口網站建立虛擬機器擴展集](virtual-machine-scale-sets-portal-create.md)。

## <a name="investigate-scaling-actions"></a>調查調整動作

* **Azure 入口網站**  
您目前可以使用入口網站來取得有限的資訊。

* **Azure 資源總管**  
此工具是瀏覽擴展集目前狀態的最佳選項。 按照此路徑，您應該會看到您所建立之調整集的執行個體檢視：  
**訂用帳戶 > {您的訂用帳戶} > resourceGroups > {您的資源群組} > 提供者 > Microsoft.Compute > virtualMachineScaleSets > {您的擴展集} > virtualMachines**

* **Azure PowerShell**  
使用下列命令可取得某些資訊：

  ```powershell
  Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
  Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
  ```

* 比照任何其他機器連接到 Jumpbox 虛擬機器，您即可從遠端存取調整集內的虛擬機器，以監視個別程序。

## <a name="next-steps"></a>後續步驟
* 請參閱 [在虛擬機器擴展集中自動調整機器](virtual-machine-scale-sets-windows-autoscale.md) ，以查看如何建立已設定自動調整的擴展集。

* 在 [Azure 監視器 PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md)中找到 Azure 監視器監視功能的範例

* 若要深入了解通知功能，請參閱[使用自動調整動作在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)。

* 深入了解如何[使用稽核記錄，在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

* 深入了解 [進階自動調整案例](virtual-machine-scale-sets-advanced-autoscale.md)。
