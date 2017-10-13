---
title: "使用 PowerShell 來調整 Azure 中 Windows VM 的大小 | Microsoft Docs"
description: "使用 Azure Powershell 可調整以 Resource Manager 部署模型建立的 Windows 虛擬機器大小。"
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 742efd1496de9ce76b1e5636297ef30f546bd108
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-windows-vm"></a>調整 Windows VM 大小
本文說明如何使用 Azure Powershell 調整以 Resource Manager 部署模型建立的 Windows VM 大小。

建立虛擬機器 (VM) 後，您可以透過變更 VM 的大小來放大或縮小 VM。 在某些情況下，您必須先解除配置 VM。 如果新的大小不適用於目前裝載 VM 的硬體叢集上，就會發生此情況。

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>調整不在可用性設定組中 Windows VM 的大小
1. 列出裝載 VM 的硬體叢集上適用的 VM 大小。 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. 如果所需的大小有列出，請執行以下命令調整 VM 大小。 如果所需的大小未列出，請移至步驟 3。
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. 如果所需的大小未列出，請執行以下命令解除配置 VM、調整其中小，然後重新啟動 VM。
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> 解除配置 VM 會將指派給 VM 的任何動態 IP 位址釋出。 不會影響作業系統和資料磁碟。 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>調整在可用性設定組中 Windows VM 的大小
如果可用性設定組中的 VM 新大小，不適用於目前裝載 VM 的硬體叢集，則必須解除配置可用性設定組中所有的 VM，才能調整 VM 大小。 在已調整某個 VM 的大小後，您也可能需要更新可用性設定組中其他 VM 的大小。 若要調整可用性設定組中 VM 的大小，請執行下列步驟。

1. 列出裝載 VM 的硬體叢集上適用的 VM 大小。
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. 如果所需的大小有列出，請執行以下命令調整 VM 大小。 如果未列出，請移至步驟 3。
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. 如果未列出所需的大小，請繼續進行下列步驟來解除配置可用性設定組中所有的 VM、 調整 VM 的大小，然後重新啟動 VM。
4. 停止可用性設定組中的所有 VM。
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. 將可用性設定組中的 VM 調整大小後重新啟動。
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>後續步驟
* 如需提高延展性，可執行多個 VM 執行個體並相應放大。如需詳細資訊，請參閱[在虛擬機器擴展集中自動調整機器](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。

