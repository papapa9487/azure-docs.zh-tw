---
title: "使用 Azure PowerShell 自動調整虛擬機器擴展集 | Microsoft Docs"
description: "如何使用 Azure PowerShell 建立虛擬機器擴展集的自動調整規則"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1fbfbbc79a415af5e874c304412854849e134eb7
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>使用 Azure PowerShell 自動調整虛擬機器擴展集
當您建立擴展集時，您會定義您想要執行的 VM 執行個體數目。 當您的應用程式需求變更時，您可以自動增加或減少 VM 執行個體數目。 自動調整的能力可讓您在整個應用程式的生命週期中，跟上客戶的需求或對應用程式效能變更做出回應。

本文示範如何使用 Azure PowerShell 建立自動調整規則，來監視擴展集中的 VM 執行個體效能。 這些自動調整規則可增加或減少 VM 執行個體的數目，以回應這些效能計量。 您也可以使用 [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) 或在 [Azure 入口網站](virtual-machine-scale-sets-autoscale-portal.md)中完成這些步驟。


## <a name="prerequisites"></a>必要條件
若要建立自動調整規則，您會需要現有的虛擬機器擴展集。 您可以使用 [Azure 入口網站](virtual-machine-scale-sets-portal-create.md)、[Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell) 或 [Azure CLI 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli) 來建立擴展集。

若要更輕鬆地建立自動調整規則，請定義您擴展集的一些變數。 下列範例會在 myResourceGroup 資源群組和「美國東部」區域中定義 *myScaleSet* 擴展集的變數。 您的訂用帳戶識別碼是使用 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) 所取得。 如果您有多個與帳戶建立關聯的訂用帳戶，則只會傳回第一個訂用帳戶。 請調整名稱和訂用帳戶識別碼，如下所示：

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>建立規則以便自動相應放大
如果您的應用程式需求增加，您擴展集內 VM 執行個體上的負載也會跟著增加。 如果這樣的負載增加會持續而非只是短暫的需要，您就可以設定自動調整規則來增加擴展集內的 VM 執行個體數目。 建立這些 VM 執行個體並部署應用程式後，擴展集就會開始透過負載平衡器將流量分散給它們。 您可以控制要監視哪些計量 (例如 CPU 或磁碟)、應用程式負載必須符合給定的閾值多久，以及要將多少個 VM 執行個體新增至擴展集。

使用 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) 建立規則，以在平均 CPU 負載在 5 分鐘期間內大於 70% 時，增加擴展集中的 VM 執行個體數目。 觸發此規則時，VM 執行個體數目會增加 20%。 在具有少數 VM 執行個體的擴展集中，您可以遺漏 `-ScaleActionScaleType`，並只指定 `-ScaleActionValue` 增加 *1* 或 *2* 個執行個體。 若擴展集內 VM 執行個體的數目很多，則增加 VM 執行個體數目 10% 或 20% 可能更加適當。

下列參數用於此規則：

| 參數               | 說明                                                                                                         | 值          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | 要監視並套用擴展集動作的效能計量。                                                   | Percentage CPU |
| *-TimeGrain*            | 收集計量進行分析的頻率。                                                                   | 1 分鐘       |
| *-MetricStatistic*      | 定義應該如何彙總收集的計量以進行分析。                                                | 平均值        |
| *-TimeWindow*           | 在比較計量與閾值之前監視的時間長短。                                   | 10 分鐘      |
| *-Operator*             | 用以比較計量資料與閾值之間差異的運算子。                                                     | 大於   |
| *-Threshold*            | 讓自動調整規則觸發動作的值。                                                      | 70%            |
| *-ScaleActionDirection* | 定義擴展集在套用規則時該相應增加還是相應減少。                                             | 增加       |
| *-ScaleActionScaleType* | 指出 VM 執行個體數目應依百分比數量變更。                                 | 百分比變更 |
| *-ScaleActionValue*     | 觸發此規則時，應該變更多少 VM 執行個體百分比。                                            | 20             |
| *-ScaleActionCooldown*  | 再次套用規則前需要等待多久時間，以便讓自動調整動作生效。 | 5 分鐘      |

下列範例建立保存此相應增加規則的 *myRuleScaleOut* 物件。 *-MetricResourceId* 使用先前針對訂用帳戶識別碼、資源群組名稱和擴展集名稱所定義的變數：

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>建立規則以便自動相應縮小
在夜晚或週末，您的應用程式需求可能會減少。 若這樣的負載減少會持續一段時間，您就可以設定自動調整規則來減少擴展集內的 VM 執行個體數目。 這個相應縮小的動作可減少執行擴展集的成本，因為只會執行符合目前需求所需的執行個體數目。

使用 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) 建立規則，以在平均 CPU 負載在 10 分鐘期間內低於 30% 時，減少擴展集中的 VM 執行個體數目。 觸發規則時，VM 執行個體數目會減少 20%。下列範例建立保存此相應增加規則的 *myRuleScaleDown* 物件。 *-MetricResourceId* 使用先前針對訂用帳戶識別碼、資源群組名稱和擴展集名稱所定義的變數：

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>定義自動調整設定檔
若要將自動調整規則與擴展集建立關聯，您可以建立設定檔。 自動調整設定檔會定義預設、最小和最大擴展集容量，並建立自動調整規則的關聯。 使用 [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) 建立自動調整設定檔。 下列範例會設定 *2* 個 VM 執行個體的預設和最小容量，以及最多 *10* 個。 接著附加前面步驟中所建立的相應放大和相應縮小規則：

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>將自動調整規則套用至擴展集
最後一個步驟是將自動調整設定檔套用至擴展集。 您的擴展集接著可以根據應用程式需求自動相應縮小或相應放大。 使用 [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) 套用自動調整設定檔，如下所示：

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>監視擴展集中的執行個體數目
若要查看 VM 執行個體數目和狀態，請使用 [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM) 檢視擴展集中的執行個體清單。 狀態會指出是否因擴展集自動相應放大而正在佈建 VM 執行個體，或因擴展集自動相應縮小而正在佈建 VM 執行個體。 下列範例會在 myResourceGroup 資源群組中檢視 myScaleSet 擴展集的 VM 執行個體狀態：

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>根據排程自動調整
在先前的範例中，是根據基本主機計量 (如 CPU 使用量) 而自動相應放大或相應縮小。 您也可以根據排程來建立自動調整規則。 這些以排程為基礎的規則可讓您在應用程式需求預期增加之前 (例如核心工作時間)，自動相應增加 VM 執行個體的數目；並在預期需求減少時 (例如週末)，自動相應減少執行個體的數目。

若要根據排程建立自動調整規則，而不是主機計量，請使用 Azure 入口網站。 目前無法使用 Azure PowerShell 建立排程規則。


## <a name="next-steps"></a>後續步驟
在本文中，您已學到如何使用自動調整規則進行水平縮放，以及增加或減少擴展集內 VM 執行個體的「數目」。 您也可以進行垂直縮放，增加或減少 VM 執行個體的「大小」。 如需詳細資訊，請參閱[以虛擬機器擴展集進行垂直自動調整](virtual-machine-scale-sets-vertical-scale-reprovision.md)。

如需如何管理 VM 執行個體的資訊，請參閱[使用 Azure PowerShell 管理虛擬機器擴展集](virtual-machine-scale-sets-windows-manage.md)。

若要了解如何在自動調整規則觸發時產生警示，請參閱[使用自動調整動作在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)。 您也可以[使用稽核記錄，在 Azure 監視器中傳送電子郵件和 Webhook 警示通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)。
