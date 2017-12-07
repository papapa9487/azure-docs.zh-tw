---
title: "重設 Windows VM 上的密碼或遠端桌面組態 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站或 Azure PowerShell 來重設 Windows VM 上的帳戶密碼或「遠端桌面」服務。"
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/06/2017
ms.author: genli
ms.openlocfilehash: 7690dd6d830b104e6f4c20affbfcc2384abe5eab
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>如何在 Windows VM 中重設遠端桌面服務或其登入密碼
如果您無法連線倒 Windows 虛擬機器 (VM)，您可以重設本機系統管理員密碼或重設「遠端桌面」服務組態。 您可以使用 Azure 入口網站或 Azure PowerShell 中的 VM 存取延伸模組來重設密碼。 如果您使用的是 PowerShell，請確定您已[安裝並設定最新的 PowerShell 模組](/powershell/azure/overview)，並已登入您的 Azure 訂用帳戶。 您也可以[針對使用傳統部署模型建立的 VM 執行這些步驟](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/classic/reset-rdp)。

## <a name="ways-to-reset-configuration-or-credentials"></a>重設組態或認證的方式
根據您的需求而定，您可以透過數種不同方式來重設遠端桌面服務和認證：

- [使用 Azure 入口網站重設](#azure-portal)
- [使用 Azure PowerShell 重設](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure 入口網站
若要展開入口網站功能表，請按一下左上角的三個橫條，然後按一下 [虛擬機器]：

![瀏覽您的 Azure VM](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**

選取您的 Windows 虛擬機器，然後按一下 [支援與疑難排解] > [重設密碼]。 將會顯示 [密碼重設] 刀鋒視窗︰

![密碼重設頁面](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

輸入使用者名稱和新密碼，然後按一下 [更新] 。 嘗試再次連接到您的 VM。

### <a name="reset-the-remote-desktop-service-configuration"></a>**重設遠端桌面服務組態**

選取您的 Windows 虛擬機器，然後按一下 [支援與疑難排解] > [重設密碼]。 隨即會顯示 [密碼重設] 刀鋒視窗。 

![重設 RDP 組態](./media/reset-rdp/Portal-RM-RDP-Reset.png)

從下拉式功能表中選取 [僅重設設定]，然後按一下 [更新]。 嘗試再次連接到您的 VM。


## <a name="vmaccess-extension-and-powershell"></a>VMAccess 延伸模組和 PowerShell
請確定您已[安裝並設定最新的 PowerShell 模組](/powershell/azure/overview)，並使用 `Login-AzureRmAccount` Cmdlet 登入您的 Azure 訂用帳戶。

### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**
請使用 [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell Cmdlet，來重設系統管理員密碼或使用者名稱。 建立您的帳戶認證，如下所示：

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> 如果您輸入與您 VM 上目前本機系統管理員帳戶不同的名稱，則 VMAccess 擴充功能會重新命名本機系統管理員帳戶、將您指定的密碼指派給該帳戶，並發出遠端桌面登出事件。 如果您 VM 上的本機系統管理員帳戶已停用，則 VMAccess 擴充功能會將它啟用。

下列範例會將資源群組 `myResourceGroup` 中的 VM `myVM` 更新為指定的認證。

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().UserName -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**重設遠端桌面服務組態**
使用 [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell Cmdlet，重設對您 VM 的遠端存取。 下列範例會在資源群組 `myResourceGroup` 中的 VM `myVM` 上重設存取擴充功能 `myVMAccess`：

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> 不論如何，一部 VM 只能有一個 VM 存取代理程式。 若要成功設定 VM 存取代理程式屬性，可以使用 `-ForceRerun` 選項。 使用 `-ForceRerun` 時，請務必使用與任何前述命令中所使用之 VM 存取代理程式相同的名稱。

如果您仍然無法從遠端連接虛擬機器，請參閱 [針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，以取得其他值得一試的步驟。


## <a name="next-steps"></a>後續步驟
如果 Azure VM 存取延伸項目沒有回應，而且您無法重設密碼，可以[離線重設本機 Windows 密碼](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此方法是更進階的程序，會要求您將有問題的 VM 之中的虛擬硬碟連接至另一個 VM。 請先依照這篇文章中說明的步驟進行，並且只在最後無計可施時才嘗試離線密碼重設方法。

[Azure VM 延伸模組與功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[透過 RDP 或 SSH 連接至 Azure 虛擬機器](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

