---
title: "不使用 Azure 代理程式重設本機 Windows 密碼 | Microsoft Docs"
description: "在 Azure 客體代理程式未安裝或運作於 VM 的情況下，如何重設本機 Windows 使用者帳戶的密碼"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/07/2017
ms.author: iainfou
ms.openlocfilehash: 880f5e5967298401fc2522124af3746d9906ffa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>如何重設 Azure VM 的本機 Windows 密碼
您可以使用 [Azure 入口網站或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 在 Azure 中重設 VM 的本機 Windows 密碼 (假設已安裝 Azure 客體代理程式)。 這個方法是為 Azure VM 重設密碼的主要方式。 如果您遇到 Azure 客體代理程式沒有回應，或無法在上傳自訂映像後進行安裝等問題，您可以手動重設 Windows 密碼。 本文將詳細說明如何將來源 OS 虛擬磁碟連接至另一部 VM，以重設本機帳戶密碼。 

> [!WARNING]
> 只能使用此程序做為最後手段。 一律先嘗試使用 [Azure 入口網站或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 重設密碼。
> 
> 

## <a name="overview-of-the-process"></a>程序概觀
無法存取 Azure 客體代理程式時，在 Azure 中為 Windows VM 執行本機密碼重設的核心步驟如下所示︰

* 刪除來源 VM。 虛擬磁碟會保留下來。
* 將來源 VM 的 OS 磁碟連接到 Azure 訂用帳戶內位於相同位置的另一部 VM。 此 VM 也稱為疑難排解 VM。
* 使用疑難排解 VM，在來源 VM 的 OS 磁碟上建立一些組態檔。
* 從疑難排解 VM 卸離 VM 的 OS 磁碟。
* 使用原始虛擬磁碟，透過 Resource Manager 範本建立 VM。
* 當新的 VM 開機時，您建立的組態檔會更新所需使用者的密碼。

## <a name="detailed-steps"></a>詳細步驟
一律先嘗試使用 [Azure 入口網站或 Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 重設密碼，再嘗試下列步驟。 在開始之前，確定您有 VM 的備份。 

1. 在 Azure 入口網站中刪除受影響的 VM。 刪除 VM 只會刪除中繼資料 (Azure 內 VM 的參考)。 刪除 VM 時會保留虛擬磁碟：
   
   * 在 Azure 入口網站中選取 VM，請按一下 [刪除]：
     
     ![刪除現有的 VM](./media/reset-local-password-without-agent/delete_vm.png)
2. 將來源 VM 的 OS 磁碟連接到疑難排解 VM。 疑難排解 VM 必須位於與來源 VM 的作業系統磁碟相同的區域 (例如 `West US`)：
   
   * 在 Azure 入口網站中選取疑難排解 VM。 按一下 [磁碟] | [連接現有項目]：
     
     ![連接現有磁碟](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     選取 [VHD 檔案]，然後選取包含來源 VM 的儲存體帳戶：
     
     ![選取儲存體帳戶](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     選取來源容器。 來源容器通常是 vhd：
     
     ![選取儲存體容器](./media/reset-local-password-without-agent/disks_select_container.png)
     
     選取要連接的 OS vhd。 按一下 [選取]，完成此程序：
     
     ![選取來源虛擬磁碟](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. 使用遠端桌面連接到疑難排解 VM，並確定看得見來源 VM 的 OS 磁碟︰
   
   * 在 Azure 入口網站中選取疑難排解 VM，然後按一下 [連接]。
   * 開啟下載的 RDP 檔案。 輸入疑難排解 VM 的使用者名稱和密碼。
   * 在檔案總管中，尋找您所連接的資料磁碟。 如果來源 VM 的 VHD 是連接到疑難排解 VM 的唯一資料磁碟，則應該是 F: 磁碟機︰
     
     ![檢視連接的資料磁碟](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. 在來源 VM 磁碟機的 `\Windows\System32\GroupPolicy` 中建立 `gpt.ini` (如果存在 gpt.ini，請將它重新命名為 gpt.ini.bak)︰
   
   > [!WARNING]
   > 確保您不會在 C:\Windows (疑難排解 VM 的 OS 磁碟機) 中不小心建立下列檔案。 在連接成為資料磁碟的來源 VM OS 磁碟機中建立下列檔案。
   > 
   > 
   
   * 將下列幾行新增至您建立的 `gpt.ini` 檔案：
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![建立 gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. 在 `\Windows\System32\GroupPolicy\Machine\Scripts` 中建立 `scripts.ini`。 確定已顯示隱藏的資料夾。 如有需要，請建立 `Machine` 或 `Scripts` 資料夾。
   
   * 將下列幾行新增至您建立的 `scripts.ini` 檔案：
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![建立 scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. 使用下列內容在 `\Windows\System32` 中建立 `FixAzureVM.cmd`，並以您自己的值取代 `<username>` 和 `<newpassword>`：
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add

    ```

    ![建立 FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    定義新的密碼時，必須符合針對 VM 設定的密碼複雜性需求。
7. 在 Azure 入口網站中，從疑難排解 VM 卸離磁碟：
   
   * 在 Azure 入口網站中選取疑難排解 VM，按一下 [磁碟]。
   * 選取在步驟 2 中連接的資料磁碟，按一下 [卸離]：
     
     ![卸離磁碟](./media/reset-local-password-without-agent/detach_disk.png)
8. 建立 VM 之前，取得來源 OS 磁碟的 URI：
   
   * 在 Azure 入口網站中選取儲存體帳戶，按一下 [Blob]。
   * 選取容器。 來源容器通常是 vhd：
     
     ![選取儲存體帳戶 Blob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     選取您的來源 VM OS VHD，然後按一下 [URL] 名稱旁邊的 [複製] 按鈕：
     
     ![複製磁碟 URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. 從來源 VM 的 OS 磁碟建立 VM：
   
   * 使用[此 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)，從特定的 VHD 建立 VM。 按一下 `Deploy to Azure` 按鈕開啟 Azure 入口網站，其中包含為您填入的樣板化詳細資料。
   * 如果您想要保留 VM 的所有先前設定，請選取 [編輯範本] 以提供現有的 VNet、子網路、網路介面卡或公用 IP。
   * 在 `OSDISKVHDURI` 參數文字方塊中，貼上您在前一個步驟中取得的來源 VHD URI︰
     
     ![從範本建立 VM](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. 執行新的 VM 後，使用您在 `FixAzureVM.cmd` 指令碼指定中的新密碼，透過遠端桌面連接到 VM。
11. 從新 VM 的遠端工作階段，移除下列檔案以清理環境︰
    
    * 從 %windir%\System32
      * 移除 FixAzureVM.cmd
    * 從 %windir%\System32\GroupPolicy\Machine\
      * 移除 scripts.ini
    * 從 %windir%\System32\GroupPolicy
      * 移除 gpt.ini (如果 gpt.ini 早已存在，且您已將它重新命名為 gpt.ini.bak，請將此 .bak 檔案重新命名為 gpt.ini)

## <a name="next-steps"></a>後續步驟
如果您仍然無法使用遠端桌面進行連接，請參閱 [RDP 疑難排解指南](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 [詳細的 RDP 疑難排解指南](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)會探討疑難排解方法，而不是特定的步驟。 您也可以[開啟 Azure 支援要求](https://azure.microsoft.com/support/options/)，以取得實際操作協助。

