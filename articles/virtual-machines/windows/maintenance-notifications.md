---
title: "處理 Azure 中 Windows VM 的維修通知 | Microsoft Docs"
description: "檢視 Azure 中執行的 Windows 虛擬機器的維修通知，並開始自助維修。"
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zivr
ms.openlocfilehash: b0103acf1e407a6a198159fad227b7ccc25052d2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>處理 Windows 虛擬機器預定進行的維修作業通知

為提升虛擬機器之主機基礎結構的可靠性、效能和安全性，Azure 會定期執行更新。 更新會變更，例如裝載環境的修補或將硬體升級與解除委任。 這些更新大多數都會在不影響託管虛擬機器的情況下執行。 不過，更新在某些情況下確實會造成影響：

- 如果維護不需要重新開機，Azure 會在主機更新時使用就地移轉來暫停 VM。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 在這些情況下，您會獲得一段時間，供您在適合的時間自行開始維修。


預定進行的維修作業若需要重新開機，會排定在不同波段。 每一波段有不同的範圍 (區域)。

- 波段開始時會傳送通知給客戶。 根據預設，通知會傳送給訂用帳戶擁有者和共同擁有者。 您可以新增多個收件者和訊息的選項，例如電子郵件、 SMS 和 Webhook，使用 Azure 通知[活動記錄檔警示](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。  
- 在該通知上方，時間*自助視窗*開放使用。 在此期間，您可以尋找此 wave 中包含哪些虛擬機器的並主動依據排程的需求來啟動維護。
- 自助服務 視窗中之後,*排程的維護期間*開始。 在此期間某個時間點，Azure 排程，並適用於您的虛擬機器所需的維護。 

有兩個期間的目標是要讓您在知道 Azure 何時將會自動開始維修的同時，有足夠時間開始維修，並將虛擬機器重新啟動。


您可以使用 Azure 入口網站、PowerShell、REST API 和 CLI 來查詢您的 VM 的維修期間，並開始自助維修。

 > [!NOTE]
 > 如果您嘗試啟動維護，而且要求失敗，Azure 會將標示為 VM**略過**。 您將不再能夠使用客戶起始維護選項。 您的 VM 必須由 Azure 排程的維護階段期間重新啟動。


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>您應該開始維護使用自助期間嗎？  

下列指導方針有助於您決定是否應該使用這項功能，然後在您自己的時間開始維護。

> [!NOTE] 
> 自助服務維護不可能適用於所有 Vm。 若要判斷是否可供您的 VM 主動式重新部署，請尋找**立即開始**維護狀態。 自助服務維護目前不是適用於雲端服務 （Web/背景工作角色）、 Service Fabric 和虛擬機器規模集。


不建議使用部署在自助服務維護**可用性設定組**因為這些是高可用性安裝，其中只有一個更新網域會影響在任何指定時間。 
    - 可讓 Azure 觸發程序維護，但請注意，受到影響的更新網域的順序不一定會以循序方式，以及有更新網域之間的 30 分鐘暫停。
    - 如果暫時無法使用某些容量 （1/更新網域計數） 的問題，它可以輕鬆地彌補在維護期間配置加入執行個體。 

**不要**以下情況使用自助服務維護： 
    - 如果您關閉 Vm 常見問題，請以手動方式，使用 DevTest labs，使用 自動關閉，或下列排程，其無法還原維護狀態，因此會造成額外的停機時間。
    - 在您知道將會刪除維護 wave 結尾之前存留較短 Vm。 
    - 大型的狀態儲存在本機 （臨時） 磁碟維護更新時想要的工作負載。 
    - 其中您通常調整 VM 的情況下，因為它無法回復維護狀態。 
    - 如果您採用排程的事件可讓主動式容錯移轉或您的工作負載的正常關機的維護關機起始之前 15 分鐘

**使用**自助維護，如果您打算執行排程的維護階段期間不會中斷您的 VM，而且反上面所述的指示都適用。 

最好是在下列情況下使用自助服務維護：
    - 您需要管理或一般客戶的確切的維護視窗進行通訊。 
    - 您需要完成維護工作在給定的日期。 
    - 您需要控制序列的維護工作，例如，若要保證安全復原多層式應用程式。
    - 您需要超過 30 分鐘的兩個更新網域 (Ud) 之間的 VM 復原時間。 若要控制更新網域之間的時間，您必須在 Vm 一個更新網域 (UD) 上觸發維護一次。

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>使用 PowerShell 檢查維修狀態

您也可以使用 Azure Powershell 來查看 VM 排定何時進行維修。 使用 `-status` 參數時，可從 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) Cmdlet 取得預定進行的維修作業的相關資訊。
 
只在有預定進行的維修作業時，才會傳回維修資訊。 如果沒有會影響 VM 的排定維修，則 Cmdlet 不會傳回任何維修資訊。 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

下列是 MaintenanceRedeployStatus 下傳回的內容： 
| 值 | 說明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指出您目前是否可以在 VM 上開始維修 ||
| PreMaintenanceWindowStartTime         | 維修自助期間的開始，此時您可以在 VM 上起始維修 ||
| PreMaintenanceWindowEndTime           | 維修自助期間的結束，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowStartTime            | 排定維修期間的開始，此時您可以在 VM 上起始維修 ||
| MaintenanceWindowEndTime              | 排定維修期間的結束，此時您可以在 VM 上起始維修 ||
| LastOperationResultCode               | 前次嘗試在 VM 上起始維修的結果 ||



您也可以使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 而不指定 VM 來取得資源群組中所有 VM 的維修狀態。
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

下列 PowerShell 函式會取得您的訂用帳戶識別碼，並印出已排定進行維修的 VM 清單。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>使用 PowerShell 在 VM 上開始維修

使用來自上一節函式的資訊，如果 **IsCustomerInitiatedMaintenanceAllowed** 設為 true，以下命令會在 VM 上開始維修。

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>傳統部署

如果您仍有使用傳統部署模型部署的舊版 VM，可以使用 PowerShell 查詢 VM 並起始維護。

若要取得 VM 的維護狀態，請鍵入：

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

若要啟動維護傳統 VM 上，輸入：

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>常見問題集


**問：為什麼需要立即重新啟動我的虛擬機器？**

**答：**雖然對 Azure 平台的大部分更新與升級不會影響虛擬機器的可用性，但是有時候我們無法避免重新啟動裝載於 Azure 的虛擬機器。 我們已經累積幾項變更，需要我們重新啟動伺服器，這會導致虛擬機器重新開機。

**問：如果我遵循建議針對高可用性使用可用性設定組，是否安全？**

**答：**部署可用性的虛擬機器設定，或虛擬機器擴展集將概念的更新網域 (UD)。 執行維護時，Azure 會接受 UD 條件約束，並且不會從不同的 UD (在相同的可用性設定組內) 重新啟動虛擬機器。  Azure 也會等候至少 30 分鐘，再移至下一個虛擬機器群組。 

如需有關高可用性的詳細資訊，請參閱[區域和在 Azure 虛擬機器的可用性](regions-and-availability.MD)。

**問：我如何取得規劃維護的通知？**

**答：**規劃的維護是從對一或多個 Azure 區域設定排程開始。 之後，電子郵件通知會傳送至訂用帳戶擁有者 (每個訂用帳戶一封電子郵件)。 可以使用「活動記錄警示」來設定此通知的其他通道和收件者。 如果您將虛擬機器部署到已排程規劃的維護之區域，您不會收到通知，但是需要檢查 VM 的維護狀態。

**問：我在入口網站、Powershell 或 CLI 中都看不到任何關於規劃的維護之指示，發生什麼情況？**

**答：**與規劃的維護相關之資訊僅在規劃的維護期間，適用於受到該維護影響的 VM。 也就是說，如果您看不到資料，可能是維護已完成 (或尚未啟動)，或者您的虛擬機器是裝載在更新的伺服器上。

**問：是否有方法可以確切知道我的虛擬機器何時會受到影響？**

**答：**設定排程時，我們會定義數天的時間期間。 不過，此期間內伺服器 (和 VM) 的確切順序則未知。 想要知道其 Vm 的確切時間的客戶可以使用[排程事件](scheduled-events.md)從虛擬機器內，以及查詢收到 VM 重新開機之前 15 分鐘通知。

**問：重新啟動我的虛擬機器需要多久時間？**

**答：**根據您的 VM 大小，重新啟動最長可能需要幾分鐘的時間。 請注意，如果您使用雲端服務 （Web/背景工作角色）、 虛擬機器設定小數位數，或可用性設定組，您將有 30 分鐘每個群組的 Vm (UD) 之間。 

**問： 什麼是在雲端服務 （Web/背景工作角色）、 Service Fabric 和虛擬機器擴展集的情況下經驗？**

**答：**這些平台會受到規劃的維護影響，使用這些平台的客戶是安全的，因為只有單一升級網域 (UD) 中的 VM 會在任何指定時間受到影響。 自助服務維護目前不是適用於雲端服務 （Web/背景工作角色）、 Service Fabric 和虛擬機器規模集。

**問：我收到一封關於硬體解除委任的電子郵件，這與規劃的維護相同嗎？**

**答：**硬體解除委任是規劃的維護事件，我們尚未讓這個使用案例上架到新的體驗。  

**問：我在我的 VM 上看不到任何維護資訊。哪裡發生錯誤？**

**答：**您在 VM 上看不到任何維護資訊有以下幾個原因：
1.  您使用標示為 Microsoft 內部的訂用帳戶。
2.  您的 VM 未排程進行維護。 可能是維護已經結束、取消或修改，所以您的 VM 不再受到它的影響。
3.  您不需要**維護**加入至您的 VM 清單檢視資料行。 雖然我們已將此資料行新增至預設檢視，但是設定為查看非預設資料行的客戶必須將 [維護] 資料行手動新增至其 VM 清單檢視。

**問：我的 VM 第二次排程進行維護。原因為何？**

**答：**有數個使用案例，您會在您已完成維護重新部署之後，看到 VM 排程進行維護：
1.  我們已取消維護，並且使用不同的裝載重新啟動它。 可能是我們偵測到發生錯誤的裝載，只是需要部署額外承載。
2.  因為硬體故障，您的 VM 已「服務修復」到另一個節點
3.  您已選取停止 (解除配置)，然後重新啟動 VM
4.  您已為 VM 開啟 [自動關機]


**問： 我的可用性設定組維護需要較長的時間，而且我現在會看到 「 略過 」 狀態在我可用性的某些設定執行個體。原因為何？** 

**答：**如果您已按下的可用性設定組簡短的連續更新多個執行個體，Azure 會將佇列在這些要求，並啟動一次更新一個更新網域 (UD) 中的 Vm。 不過，因為可能有更新的網域之間的暫停時間，更新會顯示可能需要更長時間。 如果更新佇列費時超過 60 分鐘的時間，就會顯示某些情況下**略過**狀態，即使它們已成功更新。 若要避免此錯誤的狀態，請更新您的可用性集合只有一個可用性的執行個體上，即可設定，並等候更新完成，再按一下 下一步 VM 不同更新網域中的該 VM 上。


## <a name="next-steps"></a>後續步驟

了解如何使用[排定的事件](scheduled-events.md)從 VM 內註冊取得維修事件。
