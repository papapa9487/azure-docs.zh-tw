---
title: "使用 Azure PowerShell 模組建立和管理 Windows VM | Microsoft Docs"
description: "教學課程 - 使用 Azure PowerShell 模組建立和管理 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c612a251105197ab2b46bf448ae39253e5a65f36
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>使用 Azure PowerShell 模組建立和管理 Windows VM

Azure 虛擬機器提供完全可設定且彈性的計算環境。 本教學課程涵蓋基本的「Azure 虛擬機器」部署項目，例如選取 VM 大小、選取 VM 映像、部署 VM。 您會了解如何：

> [!div class="checklist"]
> * 建立及連線到 VM
> * 選取及使用 VM 映像
> * 檢視及使用特定 VM 大小
> * 調整 VM 的大小
> * 檢視及了解 VM 狀態


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，本教學課程會要求使用 Azure PowerShell 模組版本 3.6 或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。 

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立資源群組。 

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。 在此範例中，會在 EastUS 區域中建立名為 myResourceGroupVM 的資源群組。 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

在建立或修改 VM 時，會指定資源群組，在本教學課程的整個過程中可以看到此操作。

## <a name="create-virtual-machine"></a>Create virtual machine

虛擬機器必須連線到虛擬網路。 您使用公用 IP 位址透過網路介面卡與虛擬機器通訊。

### <a name="create-virtual-network"></a>建立虛擬網路

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 建立子網路：

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路：

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>建立公用 IP 位址

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 建立公用 IP 位址：

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>建立網路介面卡

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 建立網路介面卡：

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>建立網路安全性群組

Azure [網路安全性群組](../../virtual-network/virtual-networks-nsg.md) (NSG) 控制一或多部虛擬機器的輸入和傳出流量。 網路安全性群組規則可允許或拒絕特定連接埠或連接埠範圍上的網路流量。 這些規則也可以包含來源位址首碼，所以只有在預先定義的來源產生的流量可以與虛擬機器通訊。 若要存取您安裝的 IIS Web 伺服器，您必須新增輸入 NSG 規則。

若要建立輸入 NSG 規則，請使用 [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)。 下列範例會建立名為 myNSGRule 的 NSG 規則，該規則會為虛擬機器開啟連接埠 3389︰

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

使用 myNSGRule 與 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 建立 NSG：

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 在虛擬網路中將 NSG 新增至子網路：

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 更新虛擬網路：

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Create virtual machine

建立虛擬機器時，有數個可用的選項，例如作業系統映像、磁碟大小及系統管理認證。 在此範例中，使用 myVM 名稱建立的虛擬機器會執行最新版本的 Windows Server 2016 Datacenter。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 設定虛擬機器上系統管理員帳戶所需的使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 建立虛擬機器的初始組態：

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 將作業系統資訊新增至虛擬機器組態：

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

使用 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) 將映像資訊新增至虛擬機器組態：

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

使用 [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) 將作業系統磁碟設定新增至虛擬機器組態：

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

使用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 將您先前建立的網路介面卡新增至虛擬機器組態：

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立虛擬機器。

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>連線到 VM

完成部署之後，建立與虛擬機器的遠端桌面連線。

執行下列命令，以傳回虛擬機器的公用 IP 位址。 記下這個 IP 位址，您便可以使用瀏覽器進行連線，以在未來步驟中測試 Web 連線能力。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

在本機電腦上使用下列命令，建立使用虛擬機器的遠端桌面工作階段。 以虛擬機器的 publicIPAddress 取代 IP 位址。 出現提示時，請輸入您在建立虛擬機器時所使用的認證。

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>了解 VM 映像

Azure Marketplace 包含許多可用來建立新虛擬機器的虛擬機器映像。 在先前步驟中，已使用 Windows Server 2016-Datacenter 映像建立虛擬機器。 在此步驟中，PowerShell 模組用來搜尋 Marketplace 中的其他 Windows 映像，其也可作為新 VM 的基底。 此程序包括尋找發行者、供應項目及映像名稱 (Sku)。 

使用 [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 命令傳回映像發佈者清單。  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

使用 [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) 傳回映像提供者清單。 使用這個命令，根據指定的發行者篩選傳回的清單。 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

[Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) 命令會接著根據發行者和提供項目名稱篩選，以傳回映像名稱清單。

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

此資訊可用來以特定映像部署 VM。 本範例會在 VM 物件上設定映像名稱。 請參考本教學課程中先前的範例，以取得完整的部署步驟。

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>了解 VM 大小

虛擬機器大小會決定可供虛擬機器使用的計算資源 (例如 CPU、GPU 和記憶體) 數量。 需要使用適合預期工作負載的大小來建立虛擬機器。 如果工作負載增加，可以調整現有虛擬機器的大小。

### <a name="vm-sizes"></a>VM 大小

下表會將大小分類成各種使用案例。  

| 類型                     | 大小           |    說明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| 一般用途         |DSv2、Dv2、DS、D、Av2、A0-7| 平衡的 CPU 對記憶體。 適合用於開發 / 測試及小型到中型應用程式和資料解決方案。  |
| 計算最佳化      | Fs、F             | CPU 與記憶體的比例高。 適用於中流量應用程式、網路設備及批次處理。        |
| 記憶體最佳化       | GS、G、DSv2、DS、Dv2、D   | 高記憶體對 CPU。 適用於關聯式資料庫、中型到大型快取，以及記憶體內分析。                 |
| 儲存體最佳化       | Ls                | 高磁碟輸送量及 IO。 適用於巨量資料、SQL 及 NoSQL 資料庫。                                                         |
| GPU           | NV、NC            | 以大量圖形轉譯和影片編輯為目標的特製化 VM。       |
| 高效能 | H、A8-11          | 我們的最強大 CPU VM，可搭配選用的高輸送量網路介面 (RDMA)。 


### <a name="find-available-vm-sizes"></a>尋找可用的 VM 大小

若要查看特定區域中可用的 VM 大小清單，請使用 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令。

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>調整 VM 的大小

在部署 VM 之後，可以調整其大小以增加或減少資源配置。

在調整 VM 的大小之前，檢查目前的 VM 叢集上是否有所需的大小。 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令會傳回大小清單。 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

如果有所需的大小，即可從已開機狀態調整 VM 的大小，但是會在作業期間重新開機。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

如果目前的叢集上沒有所需的大小，則必須在調整大小作業發生之前，解除配置 VM。 請注意，在 VM 重新開機後，暫存磁碟上的所有資料都會遭到移除，而公用 IP 位址會變更，除非正在使用靜態 IP 位址。 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>VM 電源狀態

Azure VM 的電源狀態可以是許多電源狀態的其中一種。 這個狀態代表從 Hypervisor 的觀點來看，VM 的目前狀態。 

### <a name="power-states"></a>電源狀態

| 電源狀態 | 說明
|----|----|
| 啟動中 | 表示虛擬機器正在啟動。 |
| 執行中 | 表示虛擬機器正在執行。 |
| 停止中 | 表示虛擬機器正在停止。 | 
| 已停止 | 表示虛擬機器已停止。 請注意，處於已停止狀態的虛擬機器仍會產生計算費用。  |
| 解除配置中 | 表示虛擬機器正在解除配置。 |
| 已解除配置 | 表示虛擬機器已從 Hypervisor 中完全移除，但仍可在控制平面中使用。 處於已解除配置狀態的虛擬機器不會產生計算費用。 |
| - | 表示虛擬機器的電源狀態不明。 |

### <a name="find-power-state"></a>尋找電源狀態

若要擷取特定 VM 的狀態，請使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 命令。 務必指定虛擬機器和資源群組的有效名稱。 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

輸出：

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>管理工作

在虛擬機器的生命週期內，您可以執行一些管理工作，例如啟動、停止或刪除虛擬機器。 此外，您可以建立指令碼來自動執行重複或複雜的工作。 使用 Azure PowerShell，可以從命令列或在指令碼中執行許多常見的管理工作。

### <a name="stop-virtual-machine"></a>停止虛擬機器

使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 停止及解除配置虛擬機器：

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

如果您想要將虛擬機器保留在佈建狀態，請使用 -StayProvisioned 參數。

### <a name="start-virtual-machine"></a>啟動虛擬機器

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>刪除資源群組

刪除資源群組同時會刪除其內含的所有資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解基本的 VM 建立和管理，像是如何：

> [!div class="checklist"]
> * 建立及連線到 VM
> * 選取及使用 VM 映像
> * 檢視及使用特定 VM 大小
> * 調整 VM 的大小
> * 檢視及了解 VM 狀態

請前進到下一個教學課程，以了解 VM 磁碟。  

> [!div class="nextstepaction"]
> [建立和管理 VM 磁碟](./tutorial-manage-data-disk.md)
