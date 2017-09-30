---
title: "使用 Azure Cloud Shell 中的 New-AzVM Cmdlet 建立 Windows VM | Microsoft Docs"
description: "快速了解如何使用 Azure Cloud Shell 中的 New-AzVM Cmdlet 來建立 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c303235ead2af7cfaa368a5b5f00567ae44cfb86
ms.contentlocale: zh-tw
ms.lasthandoff: 09/23/2017

---

# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>以 Cloud Shell 中的 New-AzVM (預覽) 來建立 Windows 虛擬機器 

New-AzVM (預覽) Cmdlet 是使用 PowerShell 建立新 VM 的簡化方式。 本指南詳細說明如何使用 Azure Cloud Shell 中的 PowerShell (已預先安裝了 New-AzVM Cmdlet)，來建立執行 Windows Server 2016 的新 Azure 虛擬機器。 部署完成後，我們會使用 RDP 連線到伺服器。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>建立 VM

您可以使用 **New-AzVM** Cmdlet 來建立虛擬機器，其中包括使用來自於 Azure Marketplace 的 Windows Server 2016 Datacenter 映像等智慧型預設值。 您也可以單獨使用 New-AzVM，它會使用資源名稱的預設值。 在此範例中，我們將 **-Name** 參數設為 *myVM*。 此 Cmdlet 會使用 *myVM* 作為資源名稱的前置詞，來建立所有必要的資源。 

請確定已在 Cloud Shell 中選取了 **PowerShell**，然後鍵入：

```azurepowershell-interactive
New-AzVm -Name myVM
```

系統會要求您建立 VM 的使用者名稱和密碼，稍後在本主題中當您連線到 VM 時會用到。 密碼長度必須是 12-123 個字元，且符合下列四個複雜性需求的其中三項：1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。

只要花一分鐘即可建立虛擬機器和相關聯的資源。 完成時，您可以看到所有使用 [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) Cmdlet 所建立的的資源。

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>連接至 VM

完成部署之後，建立與虛擬機器的遠端桌面連線。

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 命令，以傳回虛擬機器的公用 IP 位址。 記下此 IP 位址。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

在您的本機電腦上開啟命令提示字元，並使用 **mstsc** 命令以啟動遠端桌面工作階段與新的虛擬機器。 以虛擬機器的 IP 位址取代 &lt;publicIPAddress&gt;。 出現提示時，請輸入建立 VM 時指定的使用者名稱和密碼。

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組、VM 和所有相關資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本主題中，您已使用 New-AzVM 部署了簡單的虛擬機器，並透過 RDP 連線到該虛擬機器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)

