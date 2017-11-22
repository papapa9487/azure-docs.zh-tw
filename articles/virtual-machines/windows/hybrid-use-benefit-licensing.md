---
title: "適用於 Windows Server 的 Azure Hybrid Benefit | Microsoft Docs"
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
ms.date: 11/13/2017
ms.author: kmouss
ms.openlocfilehash: 11b491b52fe359427c5e395d5d8c3be3cddcdc89
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>適用於 Windows Server 的 Azure Hybrid Benefit
對於擁有軟體保證的客戶，適用於 Windows Server 的 Azure Hybrid Benefit 讓您能夠以較低的成本來使用內部部署 Windows Server 授權，以及在 Azure 上執行 Windows 虛擬機器。 您可以使用適用於 Windows Server 的 Azure Hybrid Benefit，從任何 Azure 支援的平台 Windows Server 映像或 Windows 自訂映像來部署新的虛擬機器。 本文章會詳述使用適用於 Windows Server 的 Azure Hybrid Benefit 來部署新 VM 的步驟，以及您如何更新現有的執行中 VM。 如需有關適用於 Windows Server 之 Azure Hybrid Benefit 的授權和節省成本詳細資訊，請參閱[適用於 Windows Server 的 Azure Hybrid Benefit 授權頁面](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

> [!IMPORTANT]
> Azure Marketplace 上針對 Enterprise 合約客戶所發行的舊版 '[HUB]' Windows Server 映像已於 2017 年 9 月 11 日終止服務。請使用入口網站上附有「省錢」選項的標準 Windows Server ，以取得適用於 Windows Server 的 Azure Hybrid Benefit。 如需詳細資訊，請參閱此[文章](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)。
>

> [!NOTE]
> 搭配需支付如 SQL Server 或任何協力廠商市集映像等其他軟體費用的 VM ，來使用適用於 Windows Server 的 Azure Hybrid Benefit 已推出。如果您收到 409 錯誤，例如：不允許變更屬性 'LicenseType'，則您是嘗試轉換或部署有其他軟體成本的新 Windows Server VM，在該區域中可能不受支援。
>


> [!NOTE]
> 對於傳統 VM，只支援從內部部署的自訂映像部署新 VM。 若要充分利用本文章所支援的功能，您必須先將傳統 VM 移轉至 Resource Manager 模型。
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>使用適用於 Windows Server 的 Azure Hybrid Benefit 的方式
有幾種方式可以搭配 Azure Hybrid Benefit 使用 Windows 虛擬機器：

1. 您可以從 [Azure Marketplace 上所提供的其中一個 Windows Server 映像](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) \(英文\) 來部署 VM
2. 您可以[上傳自訂 VM](#upload-a-windows-vhd)，並使用 [Resource Manager 範本](#deploy-a-vm-via-resource-manager)或 [Azure PowerShell](#detailed-powershell-deployment-walkthrough) 進行部署
3. 您可以在使用 Azure Hybrid Benefit 執行或支付 Windows Server 的隨選成本之間，切換及轉換現有的 VM
4. 您也可以使用適用於 Windows Server 的 Azure Hybrid Benefit 部署新的虛擬機器擴展集

> [!NOTE]
> 不支援將現有的虛擬機器擴展集轉換為使用適用於 Windows Server 的 Azure Hybrid Benefit
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>從 Windows Server 市集映像部署 VM
Azure Marketplace 所提供的所有 Windows Server 映像都可以搭配適用於 Windows Server 的 Azure Hybrid Benefit 使用。 例如，Windows Server 2016、Windows Server 2012R2、Windows Server 2012 及 Windows Server 2008SP1 等等。 您可以使用這些映像直接從 Azure 入口網站、Resource Manager 範本、Azure PowerShell 或其他 SDK 來部署 VM。

您可以直接從 Azure 入口網站部署這些映像。 若要在 Resource Manager 範本中使用，並與 Azure PowerShell 搭配使用，請檢視映像的清單，如下所示︰

### <a name="powershell"></a>Powershell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
您可以遵循步驟來[使用 PowerShell 建立 Windows 虛擬機器](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)，並傳遞 LicenseType = "Windows_Server"。 此選項可讓您在 Azure 上使用現有的 Windows Server 授權。

### <a name="portal"></a>入口網站
您可以遵循步驟來[使用 Azure 入口網站建立 Windows 虛擬機器](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)，然後選取要使用現有 Windows Server 授權的選項。

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>轉換使用適用於 Windows Server 的 Azure Hybrid Benefit 的現有 VM
如果您有想要將其轉換為利用適用於 Windows Server 的 Azure Hybrid Benefit 的現有 VM，您可以更新 VM 的授權類型，如下所示：

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>轉換為使用適用於 Windows Server 的 Azure Hybrid Benefit
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>轉換回隨用隨付
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>入口網站
您可以從入口網站 VM 刀鋒視窗中，藉由選取 [設定] 選項並且切換 [Azure Hybrid Benefit] 選項，將 VM 更新為使用 Azure Hybrid Benefit

## <a name="upload-a-windows-server-vhd"></a>上傳 Windows Server VHD
若要在 Azure 中部署 Windows Server VM，您必須先建立包含基底 Windows 組建的 VHD。 您必須先透過 Sysprep 妥善準備這個 VHD，再將其上傳至 Azure。 您可以深入了解 [VHD 需求和 Sysprep 處理序](upload-generalized-managed.md)及 [伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。 執行 Sysprep 前，請先備份 VM。 

備妥 VHD 之後，請將 VHD 上傳到 Azure 儲存體帳戶，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint 伺服器、Dynamics 也可以使用您的軟體保證授權。 您應需要安裝應用程式元件，並提供相應的授權金鑰，然後將磁碟映像上傳至 Azure，藉此準備 Windows Server 映像。 檢閱以您的應用程式執行 Sysprep 的相關文件，例如 [使用 Sysprep 安裝 SQL Server 的考量](https://msdn.microsoft.com/library/ee210754.aspx)或 [建置 SharePoint Server 2016 參照映像 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)。
>
>

您也可以深入了解 [將 VHD 上傳至 Azure 的程序](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## <a name="deploy-a-vm-via-resource-manager-template"></a>透過 Resource Manager 範本部署 VM
在 Resource Manager 範本內，必須指定 `licenseType` 的額外參數。 您可以進一步了解如何 [製作 Azure Resource Manager 範本](../../resource-group-authoring-templates.md)。 將 VHD 上傳至 Azure 之後，請編輯 Resource Manager 範本以將授權類型納入計算提供者，之後再照常部署範本即可：

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>透過 PowerShell 快速入門部署 VM
透過 PowerShell 部署 Windows Server VM 時，您會有額外的 `-LicenseType` 參數。 將 VHD 上傳至 Azure 之後，使用 `New-AzureRmVM` 建立 VM 並指定授權類型，如下所示：

對於 Windows Server：
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

您可以閱讀透過不同步驟來[使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 的詳述指南。

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>確認您的 VM 可享受授權權益
透過 PowerShell、Resource Manager 範本或入口網站部署 VM 之後，您可以依下列方式使用 `Get-AzureRmVM` 來驗證授權類型：

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

針對 Windows Server 的輸出類似下列範例：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

此輸出和下列在沒有適用於 Windows Server 的 Azure Hybrid Benefit 授權下所部署的 VM 之間，有著顯著的差異：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>列出訂用帳戶中所有適用於 Windows Server 的 Azure Hybrid Benefit VM

若要查看和計算利用適用於 Windows Server 的 Azure Hybrid Benefit 部署的所有虛擬機器，您可以從訂用帳戶執行下列命令：

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>使用適用於 Windows Server 的 Azure Hybrid Benefit 部署虛擬機器擴展集
在虛擬機器擴展集 Resource Manager 範本內，必須指定 `licenseType` 的額外參數。 您可以進一步了解如何 [製作 Azure Resource Manager 範本](../../resource-group-authoring-templates.md)。 編輯您的 Resource Manager 範本，將 licenseType 屬性納入擴展集的 virtualMachineProfile，並和平常一樣地部署您的範本。請參閱下列使用 2016 Windows Server 映像的範例︰


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
您也可以[建立和部署虛擬機器擴展集](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create)，並設定 LicenseType 屬性

## <a name="next-steps"></a>後續步驟
深入了解[如何使用 Azure Hybrid Benefit 來節省成本](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。

深入了解[適用於 Windows Server 的 Azure Hybrid Benefit 授權詳細指導方針](http://go.microsoft.com/fwlink/?LinkId=859786)

深入了解如何[使用 Resource Manager 範本](../../azure-resource-manager/resource-group-overview.md)。

深入了解[適用於 Windows Server 的 Azure Hybrid Benefit 和 Azure Site Recovery 能使將應用程式移轉至 Azure 更符合成本效益](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) \(英文\)。

深入了解[常見問題集](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
