---
title: "建立 Azure 受控磁碟的複本作為備份 | Microsoft Docs"
description: "了解如何建立 Azure 受控磁碟的複本作為備份，或用於針對磁碟問題進行疑難排解。"
documentationcenter: 
author: cwatson-cat
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a7527b12f4f0d2b45713a0c0109d81ff51293fd8
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>使用受控快照集建立 VHD 的複本並儲存為 Azure 受控磁碟
建立受控磁碟的快照集作為備份，或從快照集建立受控磁碟並將它附加至測試虛擬機器進行疑難排解。 受控快照集是 VM 受控磁碟的完整時間點複本。 它會建立 VHD 的唯讀複本，而且根據預設儲存為標準受控磁碟。 如需受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

如需價格的詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/managed-disks/)。 

## <a name="before-you-begin"></a>開始之前
如果您使用 PowerShell，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。

## <a name="copy-the-vhd-with-a-snapshot"></a>使用快照集複製 VHD
使用 Azure 入口網站或 PowerShell 建立受控磁碟的快照集。

### <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 入口網站建立快照集 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左上方開始，按一下 [新增]，搜尋**快照集**。
3. 在 [快照集] 刀鋒視窗中，按一下 [建立]。
4. 輸入快照集的 [名稱]。
5. 選取現有的[資源群組](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或輸入新群組的名稱。 
6. 選取 Azure 資料中心的 [位置]。  
7. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]。 除非需要儲存在高效能磁碟上，否則建議選取 **Standard_LRS**。
9. 按一下 [建立] 。

### <a name="use-powershell-to-take-a-snapshot"></a>使用 PowerShell 建立快照集
下列步驟示範如何取得要複製的 VHD 磁碟、建立快照集設定，以及使用 New-AzureRmSnapshot Cmdlet<!--Add link to cmdlet when available--> 建立磁碟的快照集。 

1. 設定部分參數。 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  取代參數值︰
  -  "myResourceGroup" 取代為 VM 的資源群組。
  -  "southeastasia" 取代為要用來儲存受控快照集的地理位置。 <!---How do you look these up? -->
  -  "ContosoMD_datadisk1" 取代為您想要複製的 VHD 磁碟名稱。
  -  "ContosoMD_datadisk1_snapshot1" 取代為要用於新快照集的名稱。

2. 取得要複製的 VHD 磁碟。

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. 建立快照集設定。 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. 建立快照集。

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
如果您計劃使用快照集建立受控磁碟，並將它連結至必須是高效能的 VM，請在 New-AzureRmSnapshot 命令中使用參數 `-AccountType Premium_LRS`。 此參數建立的快照集會儲存為進階受控磁碟。 進階受控磁碟的價格高於「標準」受控磁碟。 因此，使用該參數之前，請確定您真的需要「進階」受控磁碟。



