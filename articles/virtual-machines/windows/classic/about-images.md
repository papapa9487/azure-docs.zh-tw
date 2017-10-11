---
title: "關於 Windows 虛擬機器的映像 | Microsoft Docs"
description: "了解如何將映像與 Azure 中的 Windows 虛擬機器搭配使用。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="about-images-for-windows-virtual-machines"></a>關於 Windows 虛擬機器的映像
> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 如需在 Resource Manager 模型中尋找和使用映像的詳細資訊，請參閱[這裡](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>使用映像

您可以使用 Azure PowerShell 模組和 Azure 入口網站，來管理 Azure 訂用帳戶可使用的映像。 Azure PowerShell 模組提供更多命令選項，讓您可以準確地指出想要查看或執行的項目。 Azure 入口網站提供一個 GUI，供許多日常系統管理工作使用。

以下是一些使用 Azure PowerShell 模組的範例。

* **取得所有映像**：`Get-AzureVMImage` 會傳回您目前訂用帳戶中可用的所有映像清單︰您的映像和 Azure 或協力廠商所提供的映像。 產生的清單可能很長。 下面的範例示範如何取得較短的清單。
* **取得映像系列**：`Get-AzureVMImage | select ImageFamily`顯示字串 **ImageFamily** 屬性以取得映像系列清單。
* **取得特定系列中的所有映像**：`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **尋找 VM 映像**：`Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` 這個 Cmdlet 運作的方式是篩選 DataDiskConfiguration 屬性，僅適用於 VM 映像。 此範例也會篩選輸出並僅列出標籤和映像名稱。
* **儲存一般化映像**：`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **儲存特殊化映像**：`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > 需要使用 OSState 參數，才能建立 VM 映像，其中包含作業系統磁碟和連接的資料磁碟。 如果您不使用參數，Cmdlet 就會建立 OS 映像。 根據作業系統磁碟是否準備為重複使用，參數的值會表示是否要將映像一般化或特殊化。

* **删除映像**：`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>後續步驟
您也可以[使用 Azure 入口網站來建立 Windows 機器](tutorial.md)。
