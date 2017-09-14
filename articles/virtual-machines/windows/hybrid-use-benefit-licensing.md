---
title: "適用於 Windows Server 的 Azure Hybrid Use Benefit | Microsoft Docs"
description: "了解如何發揮 Windows 軟體保證的最大效益，以將內部部署授權帶到 Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/26/2017
ms.author: xujing
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: a986ddf22f059dc55bb9bff5c6eaf27324b716cd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>適用於 Windows Server 的 Azure Hybrid Use Benefit
對於擁有軟體保證的客戶，Azure Hybrid Use Benefit 讓您能夠以較低的成本來使用內部部署 Windows Server 和 Windows 用戶端授權，以及在 Azure 中執行 Windows 虛擬機器。 適用於 Windows Server 的 Azure Hybrid Use Benefit 包含 Windows Server 2008R2、Windows Server 2012、Windows Server 2012R2 及 Windows Server 2016。 如需詳細資訊，請瀏覽 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)授權頁面。

> [!NOTE]
> 本文會說明如何實作適用於 Windows Server 映像的授權權益。 您也可以對 [Windows 10 桌面版映像](#windows-desktop-multitenant-hosting-deployment)執行這些步驟。
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>如何使用 Azure Hybrid Use Benefit
有幾種不同方式可部署 Windows VM 與 Azure Hybrid Use Benefit︰

1. 您可以從 [特定 Marketplace 映像] 部署 VM
2. 您可以[上傳自訂 VM](#upload-a-windows-vhd)，並使用 [Resource Manager 範本來部署](#deploy-a-vm-via-resource-manager)或使用 [Azure PowerShell](#detailed-powershell-deployment-walkthrough)。

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>使用 Azure Marketplace 部署 VM
下列映像位於使用 Azure Hybrid Use Benefit - Windows Server 2016、Windows Server 2012R2、Windows Server 2012 和 Windows Server 2008SP1 預先設定的 Marketplace。 這些映像可以直接從 Azure 入口網站、Resource Manager 範本或 Azure PowerShell 部署。

您可以直接從 Azure 入口網站部署這些映像。 若要在 Resource Manager 範本中使用，並與 Azure PowerShell 搭配使用，請檢視映像的清單，如下所示︰

對於 Windows Server：
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
- 2016-Datacenter 版本 2016.127.20170406 或更高版本

- 2012-R2-Datacenter 版本 4.127.20170406 或更高版本

- 2012-Datacenter 版本 3.127.20170406 或更高版本

- 2008-R2-SP1 版本 2.127.20170406 或更高版本

對於 Windows 用戶端：
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

## <a name="upload-a-windows-server-vhd"></a>上傳 Windows Server VHD
若要在 Azure 中部署 Windows Server VM，您必須先建立包含基底 Windows 組建的 VHD。 您必須先透過 Sysprep 妥善準備這個 VHD，再將其上傳至 Azure。 您可以深入了解 [VHD 需求和 Sysprep 處理序](upload-generalized-managed.md)及 [伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。 執行 Sysprep 前，請先備份 VM。 

確定您已 [安裝並設定最新的 Azure PowerShell](/powershell/azure/overview)。 備妥 VHD 之後，請使用 `Add-AzureRmVhd` Cmdlet，將 VHD 上傳到 Azure 儲存體帳戶，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint 伺服器、Dynamics 也可以使用您的軟體保證授權。 您仍然需要安裝應用程式元件，並提供相應的授權金鑰，然後上傳磁碟映像至 Azure，藉此準備 Windows Server 映像。 檢閱以您的應用程式執行 Sysprep 的相關文件，例如 [使用 Sysprep 安裝 SQL Server 的考量](https://msdn.microsoft.com/library/ee210754.aspx)或 [建置 SharePoint Server 2016 參照映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。
>
>

您也可以深入了解 [將 VHD 上傳至 Azure 的程序](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-a-vm-via-resource-manager-template"></a>透過 Resource Manager 範本部署 VM
在 Resource Manager 範本內，可以指定 `licenseType` 的額外參數。 您可以進一步了解如何 [製作 Azure Resource Manager 範本](../../resource-group-authoring-templates.md)。 將 VHD 上傳至 Azure 之後，請編輯 Resource Manager 範本以將授權類型納入計算提供者，之後再照常部署範本即可：

對於 Windows Server：
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

對於只使用 Azure Marketplace 映像的 Windows 用戶端：
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>透過 PowerShell 快速入門部署 VM
透過 PowerShell 部署 Windows Server VM 時，您會有 `-LicenseType`的額外參數。 將 VHD 上傳至 Azure 之後，使用 `New-AzureRmVM` 建立 VM 並指定授權類型，如下所示：

對於 Windows Server：
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

對於只使用 Azure Marketplace 映像的 Windows 用戶端：
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

您可於下方進一步了解[透過 PowerShell 在 Azure 中部署 VM 的詳細逐步解說](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough)，或閱讀[使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中不同步驟的描述說明。


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>確認您的 VM 可享受授權權益
透過 PowerShell 或 Resource Manager 部署方法部署 VM 之後，請依下列方式使用 `Get-AzureRmVM` 來驗證授權類型：

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

針對 Windows Server 的輸出類似下列範例：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

下列 VM 在部署時未提供 Azure Hybrid Use Benefit 授權 (例如直接從 Azure 資源庫部署 VM)，您可看出此輸出的差異：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>詳細的 PowerShell 部署逐步解說
下列是說明完整部署 VM 的詳細 PowerShell 步驟。 您可以閱讀 [使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)深入了解更多內容，包括實際的 Cmdlet 和所建立的不同元件。 您將逐步進行建立資源群組、儲存體帳戶和虛擬網路，然後定義 VM 並完成建立 VM 的步驟。

首先，安全地取得認證，並設定位置和資源群組名稱：

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

建立公用 IP：

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

定義子網路、NIC 和 VNET：

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

命名 VM，並建立 VM 設定：

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

定義您的 OS：

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

將 NIC 加入 VM：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

定義要使用的儲存體帳戶：

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

上傳備妥的 VHD，並附加至 VM 以供使用：

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

最後，建立 VM 並定義授權類型，以採用 Azure Hybrid Use Benefit：

對於 Windows Server：
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="deploy-a-virtual-machine-scale-set-via-resource-manager-template"></a>透過 Resource Manager 範本來部署虛擬機器擴展集
在 VMSS Resource Manager 範本內，必須指定 `licenseType` 的額外參數。 您可以進一步了解如何 [製作 Azure Resource Manager 範本](../../resource-group-authoring-templates.md)。 編輯您的 Resource Manager 範本，將 licenseType 屬性納入擴展集的 virtualMachineProfile，並和平常一樣部署您的範本 - 請參閱下列使用 2016 Windows Server 映像的範例︰


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```

> [!NOTE]
> 即將推出的部署虛擬機器擴展集支援可透過 PowerShell 和其他 SDK 工具提供 AHUB 權益。
>

## <a name="next-steps"></a>後續步驟
深入了解 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)授權。

深入了解如何[使用 Resource Manager 範本](../../azure-resource-manager/resource-group-overview.md)。

深入了解 [Azure Hybrid Use Benefit 和 Azure Site Recovery 將應用程式移轉至 Azure 更符合成本效益](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)。

