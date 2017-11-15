---
title: "使用 Azure 虛擬機器擴展集進行作業系統自動升級 | Microsoft Docs"
description: "了解如何在擴展集中的 VM 執行個體上自動升級作業系統"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: guybo
ms.openlocfilehash: 32358b23bb0a0a878e986150dd992513579d61c4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure 虛擬機器擴展集的作業系統自動升級

作業系統映像自動升級是 Azure 虛擬機器擴展集的一個預覽功能，可將所有 VM 自動升級為最新的作業系統映像。

作業系統自動升級具有下列特性︰

- 一旦設定之後，映像發行者所發行的最新作業系統映像便會自動套用至擴展集，完全不需要使用者介入。
- 每次發行者發行新的平台映像時，都會以循環方式批次升級執行個體。
- 與應用程式健康情況探查整合 (選擇性，但基於安全性考量，強烈建議使用)。
- 適用於所有 VM 大小。
- 適用於 Windows 和 Linux 平台映像。
- 您可以隨時選擇不自動升級 (您也可以手動起始作業系統升級)。
- VM 的作業系統磁碟會取代為使用最新映像版本建立的新作業系統磁碟。 系統會執行已設定的延伸模組和自訂資料指令碼，同時保留已保存的資料磁碟。


## <a name="preview-notes"></a>預覽注意事項 
在預覽時，適用下列限制和約束：

- 作業系統自動升級僅支援[三個作業系統 SKU](#supported-os-images)。 沒有任何 SLA 或保證。 建議您不要在預覽期間，對實際執行的重要工作負載使用自動升級。
- 即將推出 Service Fabric 叢集中的擴展集支援。
- 虛擬機器擴展集的作業系統自動升級目前**不**支援 Azure 磁碟加密 (目前處於預覽狀態)。
- 入口網站體驗即將推出。


## <a name="register-to-use-automatic-os-upgrade"></a>註冊使用作業系統自動升級
若要使用自動化的作業系統升級功能，請向 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 註冊預覽提供者，如下所示：

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

將註冊狀態回報為*已註冊*大約需要 10 分鐘的時間。 您可以使用 [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 檢查目前的註冊狀態。 一旦註冊之後，請確認已向 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 註冊 *Microsoft.Compute* 提供者，如下所示：

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

建議您的應用程式使用健康情況探查。 若要註冊健康情況探查的提供者功能，請使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)，如下所示：

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

再次提醒，將註冊狀態回報為*已註冊*大約需要 10 分鐘的時間。 您可以使用 [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 檢查目前的註冊狀態。 一旦註冊之後，請確認已向 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 註冊 *Microsoft.Network* 提供者，如下所示：

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>支援的作業系統映像
目前僅支援特定的作業系統平台映像。 您目前無法使用您自己建立的自訂映像。 平台映像的 *version* 屬性必須設為 *latest*。

目前支援下列的 SKU (之後將會新增更多)：
    
| 發佈者               | 提供項目         |  SKU               | 版本  |
|-------------------------|---------------|--------------------|----------|
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | 最新   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | 最新   |
| Canonical               | UbuntuServer  | 16.04-LTS          | 最新   |


## <a name="application-health"></a>應用程式健康情況
在作業系統升級期間，擴展集中的 VM 執行個體一次升級一個批次。 只有客戶應用程式在已升級的 VM 執行個體上狀況良好時，升級才應該繼續。 建議應用程式向擴展集的作業系統升級引擎提供健康情況訊號。 根據預設，平台在作業系統升級期間會考慮 VM 電源狀態和延伸模組佈建狀態，以判斷 VM 執行個體在升級之後是否狀況良好。 在 VM 執行個體的作業系統升級期間，VM 執行個體上的作業系統磁碟會根據最新的映像版本，取代為新的磁碟。 作業系統升級完成後，已設定的延伸模組便會在這些 VM 上執行。 只有在 VM 上成功佈建所有擴充功能之後，才會將應用程式視為狀況良好。 

您可以使用應用程式健康情況探查選擇性地設定擴展集，以便為平台提供精確的應用程式持續狀態的相關資訊。 應用程式健康情況探查是當作健康情況訊號使用的自訂負載平衡器探查。 在擴展集 VM 執行個體上執行的應用程式可以回應外部 HTTP 或 TCP 要求，以指出它是否狀況良好。 如需有關自訂負載平衡器探查運作方式的詳細資訊，請參閱[了解負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。 作業系統自動升級並不需要但強烈建議使用應用程式健康情況探查。

如果擴展集設定為使用多個放置群組，則需要用到使用[標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)的探查。


### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>將自訂負載平衡器探查設定為擴展集上的應用程式健康情況探查
最佳作法是，針對擴展集健康情況明確地建立負載平衡器探查。 系統可能會針對現有的 HTTP 探查或 TCP 探查使用相同的端點，但健康情況探查可能會需要不同於傳統負載平衡器探查的行為。 例如，如果執行個體的負載太高，傳統負載平衡器探查可能會傳回狀況不良，因而可能不適用於判斷作業系統自動升級期間的執行個體健康情況。 將探查設定為具有不到兩分鐘的高探查率。

您可以在擴展集的 *networkProfile* 中參考負載平衡器探查，而且可以與對內或對外公開的負載平衡器建立關聯，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>強制跨訂用帳戶執行作業系統映像升級原則
為了進行安全的升級，強烈建議強制執行升級原則。 此原則可能需要有跨訂用帳戶的應用程式健康情況探查。 下列 Azure Resource Manager 原則會拒絕未設定自動化作業系統映像升級設定的部署：

1. 使用 [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) 取得內建的 Azure Resource Manager 原則定義，如下所示：

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. 使用 [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment)，將原則指派給訂用帳戶，如下所示：

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>設定自動更新
若要設定自動升級，請確認 *automaticOSUpgrade* 屬性在擴展集模型定義中設定為 *true*。 您可以使用 Azure PowerShell 或 Azure CLI 2.0 設定這個屬性。

下列範例會使用 Azure PowerShell (4.4.1 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集設定自動升級：

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


下列範例會使用 Azure CLI (2.0.20 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集設定自動升級：

```azure-cli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>檢查作業系統自動升級的狀態
您可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API，檢查您擴展集上最近執行之作業系統升級的狀態。

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell (4.4.1 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
下列範例會使用 Azure CLI (2.0.20 或更新版本)，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```azure-cli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
下列範例會使用 REST API，為名為 *myResourceGroup* 的資源群組中，稱為 *myVMSS* 的擴展集檢查其狀態：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET 呼叫會傳回類似下列範例輸出的內容：

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>作業系統自動升級執行
若要擴展對應用程式健康情況探查的使用，擴展集作業系統升級會執行下列步驟：

1. 如果有超過 20% 的執行個體狀況不良，請停止升級，否則會繼續執行。
2. 使用總執行個體計數的 20% 為批次上限，識別要升級的下一個批次 VM 執行個體。
3. 升級下一個批次 VM 執行個體的作業系統。
4. 如果有超過 20% 已升級的執行個體狀況不良，請停止升級，否則會繼續執行。
5. 如果客戶已設定應用程式健康情況探查，則升級最多會等待 5 分鐘，讓探查變成狀況良好，然後立即繼續進行下一個批次；否則，它會等待 30 分鐘，然後再移至下一個批次。
6. 如果還有要升級的執行個體，請為下一個批次移至步驟 1)，否則就表示升級已完成。

擴展集的作業系統升級引擎會在升級每個批次之前，先檢查整體的 VM 執行個體健康情況。 升級某個批次的同時，在 Azure 資料中心可能會有其他並行的計劃性或非計劃性維護，這些維護作業可能會影響 VM 的可用性。 因此，超過 20% 的執行個體可能會暫時關閉。 在這種情況下，擴展集升級會在目前批次結束時停止。


## <a name="deploy-with-a-template"></a>透過範本進行部署

您可以使用下列範本部署使用自動升級的擴展集 <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>自動輪流升級 - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>後續步驟
如需有關如何搭配擴展集使用作業系統自動升級的其他範例，請參閱[預覽功能的 GitHub 儲存機制](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
