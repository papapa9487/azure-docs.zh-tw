---
title: "在 Azure 中從受控 VM 映像建立 VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中使用 Azure PowerShell，從一般化受控 VM 映像建立 Windows 虛擬機器。"
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
ms.date: 05/22/2017
ms.author: cynthn
ms.openlocfilehash: 8157b77976a2152cc0b012fe6ad5fa116223bef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>從 Managed 映像建立 VM

您可以在 Azure 中從受控 VM 映像建立多個 VM。 受控 VM 映像包含建立 VM 所需的資訊，包括 OS 和資料磁碟。 組成映像的 VHD (包括 OS 磁碟和任何資料磁碟) 會儲存為受控磁碟。 


## <a name="prerequisites"></a>必要條件

您必須已經[建立受控 VM 映像](capture-image-resource.md)，才能用來建立新的 VM。 

請確定您擁有最新版的 AzureRM.Compute 和 AzureRM.Network PowerShell 模組。 以系統管理員身分開啟 PowerShell 命令提示字元，執行下列命令安裝它們。

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。



## <a name="collect-information-about-the-image"></a>收集映像相關資訊

首先，我們需要收集映像的基本資訊，並建立映像的變數。 這個範例使用名為 **myImage** 的受控 VM 映像，其位於**美國中西部**位置的 **myResourceGroup** 資源群組中。 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
建立[虛擬網路](../../virtual-network/virtual-networks-overview.md)的 vNet 和子網路。

1. 建立子網路。 這個範例會建立名為 **mySubnet** 且具有位址首碼 **10.0.0.0/24** 的子網路。  
   
    ```azurepowershell-interactive
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. 建立虛擬網路 這個範例會建立名為 **myVnet** 且具有位址首碼 **10.0.0.0/16** 的虛擬網路。  
   
    ```azurepowershell-interactive
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>建立公用 IP 位址和網路介面

若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

1. 建立公用 IP 位址。 此範例會建立名為 **myPip** 的公用 IP 位址。 
   
    ```azurepowershell-interactive
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. 建立 NIC。 此範例會建立名為 **myNic** 的 NIC。 
   
    ```azurepowershell-interactive
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則

若要能夠使用 RDP 登入 VM，您必須有可在連接埠 3389 上允許 RDP 存取的網路安全性規則 (NSG)。 

此範例會建立名為 **myNsg** 的 NSG，其包含的規則 **myRdpRule** 可允許透過連接埠 3389 的 RDP 流量。 如需 NSG 的詳細資訊，請參閱[使用 PowerShell 對 Azure 中的 VM 開啟連接埠](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>建立虛擬網路的變數

為已完成的虛擬網路建立變數。 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>取得 VM 的認證

下列 Cmdlet 會開啟視窗，讓您輸入新的使用者名稱和密碼，作為從遠端存取 VM 時使用的本機管理員帳戶。 

```azurepowershell-interactive
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>設定 VM 名稱、電腦名稱和 VM 大小的變數

1. 建立 VM 名稱和電腦名稱的變數。 此範例將 VM 名稱設定為 **myVM**，將電腦名稱設定為 **myComputer**。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. 設定虛擬機器的大小。 這個範例會建立 **Standard_DS1_v2** 大小的 VM。 如需詳細資訊，請參閱 [VM 大小](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)文件。

    ```azurepowershell-interactive
    $vmSize = "Standard_DS1_v2"
    ```

3. 將 VM 名稱和大小新增至 VM 設定。

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>將 VM 映像設定為新 VM 的來源映像

使用受控 VM 映像的識別碼設定來源影像。

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>設定 OS 組態並新增 NIC。

輸入儲存體類型 (PremiumLRS 或 StandardLRS) 和 OS 磁碟的大小。 這個範例將帳戶類型設定為 **PremiumLRS**、將磁碟大小設定為 **128GB**，並將磁碟快取設定為 **ReadWrite**。

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>建立 VM

使用我們已建立並儲存在 **$vm** 變數中的組態來建立新 VM。

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

```azurepowershell-interactive
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>後續步驟
若要使用 PowerShell 來管理您的新虛擬機器，請參閱[使用 Azure PowerShell 模組來建立和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

