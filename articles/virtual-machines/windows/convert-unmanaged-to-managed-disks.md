---
title: "將 Windows 虛擬機器從非受控磁碟轉換成受控磁碟 - Azure 受控磁碟 | Microsoft Docs"
description: "如何在 Resource Manager 部署模型中使用 PowerShell 將 Windows VM 從非受控磁碟轉換成受控磁碟"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 53681c58ca1eff394d6a3db2d6a026845ac03df1
ms.contentlocale: zh-tw
ms.lasthandoff: 08/05/2017

---

# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>將 Windows 虛擬機器從非受控磁碟轉換成受控磁碟

如果現有的 Windows 虛擬機器 (VM) 使用非受控磁碟，您可以透過 [Azure 受控磁碟](../../storage/storage-managed-disks-overview.md)服務，將這些 VM 轉換成使用受控磁碟。 此程序會轉換 OS 磁碟和任何附加的資料磁碟。

本文說明如何使用 Azure PowerShell 來轉換 VM。 如果您需要安裝或升級 Azure PowerShell，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps.md)。

## <a name="before-you-begin"></a>開始之前


* 檢閱[規劃移轉至受控磁碟](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>轉換單一執行個體的 VM
本節說明如何將單一執行個體 Azure VM 從非受控磁碟轉換為受控磁碟。 (如果您的 VM 位於可用性設定組中，請參閱下一節)。 

1. 使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) Cmdlet 將 VM 解除配置。 下列範例會解除配置 `myResourceGroup` 資源群組中名為 `myVM` 的 VM： 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. 使用 [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) Cmdlet 將 VM 轉換成受控磁碟。 下列程序會轉換先前的 VM，包括 OS 磁碟和任何資料磁碟︰

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. 轉換成受控磁碟之後，使用 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) 來啟動 VM。 下列範例會重新啟動先前的 VM：

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>轉換可用性設定組中的 VM

如果您想要轉換為受控磁碟的 VM 位於可用性設定組中，您必須先將此可用性設定組轉換為受控可用性設定組。

1. 使用 [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) Cmdlet 來轉換可用性設定組。 下列範例會更新 `myResourceGroup` 資源群組中名為 `myAvailabilitySet` 的可用性設定組：

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  如果您的可用性設定組所在區域只有 2 個受控容錯網域，但非受控容錯網域的數目為 3，此命令就會顯示類似以下的錯誤：「指定的錯誤網域計數 3 必須介於 1 到 2 之間。」 若要解決此錯誤，請將容錯網域更新為 2，並將 `Sku` 更新為 `Aligned`，如下所示：

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 解除配置並轉換可用性設定組中的 VM。 下列指令碼會使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) Cmdlet 將每個 VM 解除配置、使用 [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) 來轉換它，然後使用 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) 將它重新啟動：

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>將標準受控磁碟轉換成進階受控磁碟
將 VM 轉換成受控磁碟之後，您可以切換儲存體類型。 您也可以有混合使用標準和進階儲存體的磁碟。 

在下列範例中，我們會示範如何從標準儲存體切換成進階儲存體。 若要使用進階受控磁碟，您的 VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換成可支援進階儲存體的大小。

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>疑難排解

如果在轉換期間發生錯誤，或如果 VM 因為先前轉換問題而處於失敗狀態，請再次執行 `ConvertTo-AzureRmVMManagedDisk` Cmdlet。 簡單重試通常會讓這種情況解除封鎖。


## <a name="next-steps"></a>後續步驟

使用[快照](snapshot-copy-managed-disk.md)來取得 VM 的唯讀複本。


