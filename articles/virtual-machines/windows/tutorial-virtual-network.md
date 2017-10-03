---
title: "Azure 虛擬網路和 Windows 虛擬機器 | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 來管理 Azure 虛擬網路和 Windows 虛擬機器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 54a4a37d581f023610cd61835bdc76814fbd46e0
ms.contentlocale: zh-tw
ms.lasthandoff: 09/01/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>使用 Azure PowerShell 來管理 Azure 虛擬網路和 Windows 虛擬機器

Azure 虛擬機器會使用 Azure 網路進行內部和外部的網路通訊。 在本教學課程中，您會在虛擬網路中建立多部虛擬機器 (VM)，並設定這些機器間的網路連線。 您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路子網路
> * 使用網路安全性群組來控制網路流量
> * 檢視作用中的流量規則

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="create-vnet"></a>建立 VNet

VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 在 VNet 內，您可以找到子網路、可供連線到這些子網路的規則，以及從 VM 到子網路的連線。

您必須先使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 來建立資源群組，才能建立任何其他 Azure 資源。 下列範例會在 EastUS 位置建立名為 myRGNetwork 的資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。 NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 來建立子網路：

```azurepowershell-interactive
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

使用 myFrontendSubnet 和 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立一個名為 *myVNet* 的 VNET：

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>建立前端 VM

若要讓 VM 在 VNet 中進行通訊，它需要一個虛擬網路介面 (NIC)。 存取 myFrontendVM 時是從網際網路存取，因此它也需要一個公用 IP 位址。 

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 來建立公用 IP 位址：

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 來建立 NIC：


```azurepowershell-interactive
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼。 您需要執行其他步驟以使用這些認證來連線至 VM：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)、[Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem)、[Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)、[Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)、[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 和 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 來建立 VM。 

```azurepowershell-interactive
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>安裝 Web 伺服器

您可以使用遠端桌面工作階段在 myFrontendVM 上安裝 IIS。 您必須取得 VM 的公用 IP 位址才能存取它。

您可以使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 來取得 myFrontendVM 的公用 IP 位址。 下列範例會取得稍早建立的 *myPublicIPAddress* IP 位址︰

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

請記下此 IP 位址，以便在未來的步驟中使用它。

使用下列命令來建立與 myFrontendVM 的遠端桌面工作階段。 以您先前記錄下來的位址取代 *<publicIPAddress>*。 出現提示時，請輸入您在建立 VM 時所使用的認證。

```
mstsc /v:<publicIpAddress>
``` 

您現在已登入 myFrontendVM，可以使用一行 PowerShell 來安裝 IIS，並啟用本機防火牆規則來允許 Web 流量通過。 從 RDP 工作階段開啟 VM 上的 PowerShell 提示字元，並執行下列命令：

使用 [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) 來執行會安裝 IIS Web 伺服器的自訂指令碼擴充功能：

```azurepowershell-interactive
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

現在您可以使用公用 IP 位址來瀏覽至 VM，以查看 IIS 網站。

![IIS 預設網站](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>管理內部網路流量

網路安全性群組 (NSG) 包含安全性規則清單，這些規則可允許或拒絕傳送到已連線至 VNet 之資源的網路流量。 NSG 可與子網路或連接到 VM 的個別 NIC 建立關聯。 您可以使用 NSG 規則來透過連接埠開啟或關閉對 VM 的存取。 建立 myFrontendVM 時，會自動開啟輸入連接埠 3389 以供 RDP 連線使用。

您可以使用 NSG 來設定 VM 的內部通訊。 在本節中，您將了解如何在網路中建立額外的子網路並將 NSG 指派給它，以允許透過連接埠 1433 從 myFrontendVM 連線到 myBackendVM。 此子網路會接著在 VM 建立後會指派給 VM。

您可以為後端子網路建立 NSG，以僅允許從 myFrontendVM 傳送內部流量給 myBackendVM。 下列範例會使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) 來建立名為 myBackendNSGRule 的 NSG 規則︰

```azurepowershell-interactive
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 來新增名為 myBackendNSG 的網路安全性群組：

```azurepowershell-interactive
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>新增後端子網路

使用 [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) 將 myBackEndSubnet 新增至 myVNet：

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>建立後端 VM

建立後端 VM 的最簡單方式是使用 SQL Server 映像。 本教學課程只會建立具有資料庫伺服器的 VM，而不會提供有關存取該資料庫的資訊。

建立 myBackendNic：

```azurepowershell-interactive
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

使用 Get-Credential 來設定 VM 上系統管理員帳戶所需的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

建立 myBackendVM：

```azurepowershell-interactive
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

所使用的映像已安裝 SQL Server，但在本教學課程中並不使用。 包含它是為了示範如何設定一個 VM 來處理 Web 流量、一個 VM 來處理資料庫管理。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立並保護與虛擬機器相關的 Azure 網路。 

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路子網路
> * 使用網路安全性群組來控制網路流量
> * 檢視作用中的流量規則

請前進到下一個教學課程，了解如何使用 Azure 備份監視保護虛擬機器上的資料。 。

> [!div class="nextstepaction"]
> [備份 Azure 中的 Windows 虛擬機器](./tutorial-backup-vms.md)

