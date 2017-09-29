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
ms.date: 09/14/2017
ms.author: zivr
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---


# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>處理 Windows 虛擬機器預定進行的維修作業通知

為提升虛擬機器之主機基礎結構的可靠性、效能和安全性，Azure 會定期執行更新。 更新會變更，例如裝載環境的修補或將硬體升級與解除委任。 這些更新大多數都會在不影響託管虛擬機器的情況下執行。 不過，更新在某些情況下確實會造成影響：

- 如果維護不需要重新開機，Azure 會在主機更新時使用就地移轉來暫停 VM。

- 如果維護需要重新開機，您會在規劃維護時收到通知。 在這些情況下，您會獲得一段時間，供您在適合的時間自行開始維修。


預定進行的維修作業若需要重新開機，會排定在不同波段。 每一波段有不同的範圍 (區域)。

- 波段開始時會傳送通知給客戶。 根據預設，通知會傳送給訂用帳戶擁有者和共同擁有者。 您可以對通知新增更多收件者和傳訊選項，例如電子郵件、SMS 和 Webhook。  
- 在通知之後很快即會開始自助期間。 在此期間，您可以找到此波段中包含的虛擬機器，並使用主動式重新部署開始維修。 
- 在自助期間之後，排定的維修期間隨即開始。 此時，Azure 會排定並將必要的維修套用於您的虛擬機器。 

有兩個期間的目標是要讓您在知道 Azure 何時將會自動開始維修的同時，有足夠時間開始維修，並將虛擬機器重新啟動。


您可以使用 Azure 入口網站、PowerShell、REST API 和 CLI 來查詢您的 VM 的維修期間，並開始自助維修。

 > [!NOTE]
 > 如果您嘗試開始維修並失敗時，Azure 會將您的 VM 標示為**已略過**，並且不會在排定的維修期間將它重新啟動。 相反地，稍後會與您連絡，以取得新的排程。 


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
Get-AzureRmVM -ResourceGroupName rgName --Status
```

下列 PowerShell 函式會取得您的訂用帳戶識別碼，並印出已排定進行維修的 VM 清單。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
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
 

## <a name="next-steps"></a>後續步驟

了解如何使用[排定的事件](scheduled-events.md)從 VM 內註冊取得維修事件。
