---
title: "在 Azure 中從受控映像建立 VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中使用 Azure PowerShell 或 Azure 入口網站，從一般化受控映像建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>從受控映像建立 VM

您可以使用 PowerShell 或 Azure 入口網站，從受控 VM 映像建立多個 VM。 受控 VM 映像包含建立 VM 所需的資訊，包括 OS 和資料磁碟。 組成映像的 VHD (包括 OS 磁碟和任何資料磁碟) 會儲存為受控磁碟。 

您必須已經[建立受控 VM 映像](capture-image-resource.md)，才能用來建立新的 VM。 

## <a name="use-the-portal"></a>使用入口網站

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在左側功能表中，選取 [所有資源]。 您可以依 [類型] 排序資源，以輕鬆找到您的映像。
3. 從清單選取您要使用的映像。 映像的 [概觀] 頁面隨即開啟。
4. 按一下功能表中的 [+ 建立 VM]。
5. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 完成時，按一下 [確定]。 您可以在現有的資源群組中建立新的 VM ，或選擇 [建立新項目] 來建立用於儲存 VM 的新資源群組。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 
7. 在 [設定] 底下，視需要變更，然後按一下 [確定]。 
8. 在 [摘要] 頁面上，您應該會看到您的映像名稱列為**私人映像**。 按一下 [確定] 以開始虛擬機器部署。


## <a name="use-powershell"></a>使用 PowerShell

### <a name="prerequisites"></a>先決條件

請確定您擁有最新版的 AzureRM.Compute 和 AzureRM.Network PowerShell 模組。 以系統管理員身分開啟 PowerShell 命令提示字元，執行下列命令安裝它們。

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。



### <a name="collect-information-about-the-image"></a>收集映像相關資訊

首先，我們需要收集映像的基本資訊，並建立映像的變數。 這個範例使用名為 **myImage** 的受控 VM 映像，其位於**美國中西部**位置的 **myResourceGroup** 資源群組中。 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>建立虛擬網路
建立[虛擬網路](../../virtual-network/virtual-networks-overview.md)的 vNet 和子網路。

建立子網路。 這個範例會建立名為 **mySubnet** 且具有位址首碼 **10.0.0.0/24** 的子網路。  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

建立虛擬網路 這個範例會建立名為 **myVnet** 且具有位址首碼 **10.0.0.0/16** 的虛擬網路。  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>建立公用 IP 位址和網路介面

若要能夠與虛擬網路中的虛擬機器進行通訊，您需要 [公用 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 和網路介面。

建立公用 IP 位址。 此範例會建立名為 **myPip** 的公用 IP 位址。 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
建立 NIC。 此範例會建立名為 **myNic** 的 NIC。 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組和 RDP 規則

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


### <a name="create-a-variable-for-the-virtual-network"></a>建立虛擬網路的變數

為已完成的虛擬網路建立變數。 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>取得 VM 的認證

下列 Cmdlet 會開啟視窗，讓您輸入新的使用者名稱和密碼，作為從遠端存取 VM 時使用的本機管理員帳戶。 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>設定 VM 名稱、電腦名稱和 VM 大小的變數

建立 VM 名稱和電腦名稱的變數。 此範例將 VM 名稱設定為 **myVM**，將電腦名稱設定為 **myComputer**。

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

設定虛擬機器的大小。 這個範例會建立 **Standard_DS1_v2** 大小的 VM。 如需詳細資訊，請參閱 [VM 大小](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)文件。

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

將 VM 名稱和大小新增至 VM 設定。

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>將 VM 映像設定為新 VM 的來源映像

使用受控 VM 映像的識別碼設定來源影像。

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>設定 OS 組態並新增 NIC。

輸入儲存體類型 (PremiumLRS 或 StandardLRS) 和 OS 磁碟的大小。 這個範例將帳戶類型設定為 **PremiumLRS**、將磁碟大小設定為 **128GB**，並將磁碟快取設定為 **ReadWrite**。

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>建立 VM

使用我們已建立並儲存在 **$vm** 變數中的組態來建立新 VM。

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>確認已建立 VM
完成時，在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 底下，或是使用下列 PowerShell 命令，應該就可以看到新建立的 VM：

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>後續步驟
若要使用 PowerShell 來管理您的新虛擬機器，請參閱[使用 Azure PowerShell 模組來建立和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

