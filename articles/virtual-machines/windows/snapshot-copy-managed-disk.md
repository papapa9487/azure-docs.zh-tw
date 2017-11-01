---
title: "在 Azure 中建立 VHD 的快照集 | Microsoft Docs"
description: "了解如何建立 Azure VHD 的複本作為備份，或用來針對問題進行疑難排解。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>建立快照集

製作 OS 或資料磁碟 VHD 的快照集作為備份，或是用來針對 VM 問題進行疑難排解。 快照集是完整的 VHD 唯讀複本。 

## <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 入口網站建立快照集 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左上方開始，按一下 [新增]，搜尋**快照集**。
3. 在 [快照集] 刀鋒視窗中，按一下 [建立]。
4. 輸入快照集的 [名稱]。
5. 選取現有的[資源群組](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或輸入新群組的名稱。 
6. 選取 Azure 資料中心的 [位置]。  
7. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]。 除非需要儲存在高效能磁碟上，否則建議選取 **Standard_LRS**。
9. 按一下 [建立] 。

## <a name="use-powershell-to-take-a-snapshot"></a>使用 PowerShell 建立快照集
下列步驟示範如何取得要複製的 VHD 磁碟、建立快照集設定，以及使用 [New-AzureRmSnapshot Cmdlet](/powershell/module/azurerm.compute/new-azurermsnapshot) 建立磁碟的快照集。 

請確定您已安裝最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。


1. 設定部分參數。 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. 取得要複製的 VHD 磁碟。

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 建立快照集設定。 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 建立快照集。

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
如果您計劃使用快照集建立受控磁碟，並將它連結至必須是高效能的 VM，請在 New-AzureRmSnapshot 命令中使用參數 `-AccountType Premium_LRS`。 此參數建立的快照集會儲存為進階受控磁碟。 進階受控磁碟的價格高於「標準」受控磁碟。 因此，使用該參數之前，請確定您真的需要「進階」受控磁碟。

## <a name="next-steps"></a>後續步驟

從快照集建立受控磁碟，然後連結新的受控磁碟作為 OS 磁碟，以從快照集建立虛擬機器。 如需詳細資訊，請參閱[從快照集建立 VM](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) 範例。
