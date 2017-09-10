---
title: "Azure 虛擬機器的常用 PowerShell 命令 | Microsoft Docs"
description: "可讓您在 Azure 中開始建立及管理 Windows VM 的常用 PowerShell 命令。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7744f0bef5969a41130c09635d608df1a2952993
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>用於建立及管理 Azure 虛擬機器的常用 PowerShell 命令

本文涵蓋一些 Azure PowerShell 命令，您可用來建立及管理您的 Azure 訂用帳戶中的虛擬機器。  如需特定命令列參數和選項的詳細說明，您可以使用 **Get-Help** *命令*。

如需如何安裝最新版 Azure PowerShell、選取訂用帳戶，以及登入帳戶的相關資訊，請參閱[如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

如果您要執行本文中的多個命令，下列變數可能會相當實用：

- $location - 虛擬機器的位置。 您可以使用 [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) 來尋找適合您的[地理區域](https://azure.microsoft.com/regions/)。
- $myResourceGroup - 包含虛擬機器之資源群組的名稱。
- $myVM - 虛擬機器的名稱。

## <a name="create-a-vm"></a>建立 VM

| Task | 命令 |
| ---- | ------- |
| 建立 VM 組態 |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 組態用來定義或更新 VM 的設定。 系統會使用 VM 的名稱及其 [大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)初始化組態。 |
| 新增組態設定 |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>包含[認證](https://technet.microsoft.com/library/hh849815.aspx)的作業系統設定會新增至您先前使用 New-AzureRmVMConfig 建立的組態物件。 |
| 新增網路介面 |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必須有[網路介面](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)才可在虛擬網路中進行通訊。 您也可以使用 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 擷取現有的網路介面物件。 |
| 指定平台映像 |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[映像資訊](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 會新增至您先前使用 New-AzureRmVMConfig 建立的組態物件。 只有在您設定作業系統磁碟使用平台映像時，才會使用此命令傳回的物件。 |
| 設定作業系統磁碟使用平台映像 |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>作業系統磁碟的名稱及其在[儲存體](../../storage/common/storage-powershell-guide-full.md)中的位置會新增至您先前建立的組態物件。 |
| 設定作業系統磁碟使用一般化映像 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>作業系統磁碟的名稱、來源映像的位置，以及磁碟在[儲存體](../../storage/common/storage-powershell-guide-full.md)中的位置會新增至組態物件。 |
| 設定作業系統磁碟使用一般化映像 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| 建立 VM |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>所有資源都會在[資源群組](../../azure-resource-manager/powershell-azure-resource-manager.md)中建立。 執行此命令之前，請執行 New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface 和 Set-AzureRmVMOSDisk。 |

## <a name="get-information-about-vms"></a>取得 VM 的相關資訊

| Task | 命令 |
| ---- | ------- |
| 列出訂用帳戶中的 VM |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| 列出資源群組中的 VM |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>若要取得您的訂用帳戶中的資源群組清單，請使用 [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup)初始化組態。 |
| 取得 VM 的相關資訊 |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>管理 VM
| Task | 命令 |
| --- | --- |
| 啟動 VM |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 停止 VM |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 重新啟動執行中的 VM |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 刪除 VM |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 一般化 VM |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>您在執行 Save-AzureRmVMImage 前需執行此命令。 |
| 擷取 VM |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>虛擬機器必須[就緒、關閉並一般化](prepare-for-upload-vhd-image.md)後，才能用以建立映像。 請在執行此命令前，執行 Set-AzureRmVm。 |
| 更新 VM |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>使用 Get-AzureRmVM 取得目前的 VM 組態，變更 VM 物件上的組態設定，然後執行此命令。 |
| 將資料磁碟新增至 VM |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。 指定磁碟的 LUN 編號和大小。 執行 Update-AzureRmVM 以將組態變更套用至 VM。 您新增的磁碟尚未初始化。 |
| 從 VM 移除資料磁碟 |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>使用 Get-AzureRmVM 來取得 VM 物件。 執行 Update-AzureRmVM 以將組態變更套用至 VM。 |
| 將延伸模組新增至 VM |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>使用您要安裝之擴充功能的適當[組態資訊](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions)執行這個命令。 |
| 移除 VM 延伸模組 |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>後續步驟
* 請參閱 [使用 Resource Manager 和 PowerShell 建立 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中建立虛擬機器的基本步驟。


