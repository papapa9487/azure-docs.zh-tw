---
title: "Azure 中 Windows VM 的可用性設定組教學課程 | Microsoft Docs"
description: "了解 Azure 中 Windows VM 的可用性設定組。"
documentationcenter: 
services: virtual-machines-windows
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
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3657f222e1f52d341cd05532e29ed4924b308e6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-availability-sets"></a>如何使用可用性設定組

在本教學課程中，您會學到如何使用稱為「可用性設定組」的功能，增加 Azure 虛擬機器解決方案的可用性和可靠性。 可用性設定組可確保您在 Azure 上部署的 VM 會分散到多個各自獨立的硬體叢集中。 這麼做可以確保當 Azure 發生硬體或軟體故障時，受到影響的只會是一部分的 VM 子集，您整體的解決方案則會維持可用且正常運作。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="availability-set-overview"></a>可用性設定組概觀

可用性設定組是一種可在 Azure 中使用的邏輯群組功能，用以確保其中所放置的 VM 資源在部署到 Azure 資料中心時會彼此隔離。 Azure 可確保您在可用性設定組中所放置的 VM，會橫跨多部實體伺服器、計算機架、儲存體單位和網路交換器來執行。 如果硬體或 Azure 軟體發生故障時，只有一部分的 VM 子集會受到影響，整體的應用程式則會保持運作狀態，可供客戶繼續使用。 如果您想要建置可靠的雲端解決方案，就一定要使用可用性設定組功能。

請想想典型的 VM 架構解決方案，在此解決方案中，您可能有 4 個前端 Web 伺服器，並使用 2 個後端 VM 來裝載資料庫。 在使用 Azure 時，建議您先定義兩個可用性設定組，再部署 VM︰一個可用性設定組用來放置 Web 層，另一個可用性設定組用來放置資料庫層。 當您建立新的 VM 時，您便可以將可用性設定組指定為 az vm create 命令的參數，Azure 會自動確保您在可用性設定組內所建立的 VM，會跨多個實體硬體資源來隔離。 如果其中一個用來執行 Web 伺服器或資料庫伺服器 VM 的實體硬體發生問題，Web 伺服器和資料庫 VM 的其他執行個體會繼續執行，因為它們位於不同硬體上。

如果您想要在 Azure 內部署可靠的 VM 架構解決方案，請使用可用性設定組。

## <a name="create-an-availability-set"></a>建立可用性設定組

您可以使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 建立可用性設定組。 在此範例中，我們將針對 *myResourceGroupAvailability* 資源群組中名為 *myAvailabilitySet* 的可用性設定組，同時將更新數目和容錯網域數目設為 *2*。

建立資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 搭配 **-sku aligned** 參數建立受管理的可用性設定組。

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>建立位於可用性設定組內的 VM

您必須將 VM 建立於可用性設定組內，才能確保 VM 會在硬體中正確地分散。 您無法在建立可用性設定組之後，將現有的 VM 加入至其中。 

位置中的硬體已分為多個更新網域和容錯網域。 **更新網域**是一組虛擬機器和可同時重新啟動的基礎實體硬體。 相同**容錯網域**中的 VM 會共用一般儲存體以及通用電源和網路交換器。 

當您使用 [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 建立 VM 組態時，會使用 `-AvailabilitySetId` 參數來指定可用性設定組的識別碼。

在可用性設定組中使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立兩部 VM。

```azurepowershell-interactive
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

建立及設定這兩部 VM 需要幾分鐘的時間。 完成後，您將有兩部分散於基礎硬體上的虛擬機器。 

如果您在入口網站中移至 [資源群組] > [myResourceGroupAvailability] > [myAvailabilitySet] 來查看可用性設定組，您應會看到 VM 分散在 2 個容錯和更新網域上。

![入口網站中的可用性設定組](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>檢查可用的 VM 大小 

您稍後可以將更多 VM 加入至可用性設定組，但是需要知道硬體上有哪些可用的 VM 大小。 針對可用性設定組，使用 [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 來列出硬體叢集上所有可用的大小。

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立可用性設定組
> * 在可用性設定組中建立 VM
> * 檢查可用的 VM 大小

請前進到下一個教學課程，以了解虛擬機器擴展集。

> [!div class="nextstepaction"]
> [建立 VM 擴展集](tutorial-create-vmss.md)


