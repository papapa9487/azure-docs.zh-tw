---
title: "將儲存體總管連線到 Azure Stack 訂用帳戶"
description: "了解如何將儲存體總管連線到 Azure Stack 訂用帳戶"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>將儲存體總管連線到 Azure Stack 訂用帳戶

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure Stack 儲存體資料。 將資料移動至 Azure Stack 儲存體以及從 Azure Stack 儲存體移動資料有數個工具可供使用。 如需詳細資訊，請參閱 [Azure Stack 儲存體適用的資料傳輸工具](azure-stack-storage-transfer.md)。

在本文中，您將了解如何使用儲存體總管連線到 Azure Stack 儲存體帳戶。 

如果您尚未安裝儲存體總管，請[下載](http://www.storageexplorer.com/)並安裝它。

連線到 Azure Stack 訂用帳戶之後，您可以使用 [Azure 儲存體總管文章](../../vs-azure-tools-storage-manage-with-storage-explorer.md)來處理 Azure Stack 資料。 

## <a name="prepare-an-azure-stack-subscription"></a>準備 Azure Stack 訂用帳戶

您必須可以存取 Azure Stack 主機電腦桌面或 VPN 連線，儲存體總管才能存取 Azure Stack 訂用帳戶。 若要深入了解如何設定 Azure Stack 的 VPN 連線，請參閱[使用 VPN 連線到 Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

若是 Azure Stack 開發套件，您必須匯出 Azure Stack 授權單位根憑證。

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>匯出 Azure Stack 憑證後再匯入

1. 在 Azure Stack 主機電腦或可使用 VPN 連線到 Azure Stack 的本機電腦上開啟 `mmc.exe`。 

2. 在 [檔案] 中，選取 [新增/移除嵌入式管理單元]，然後新增 [憑證] 以管理 [我的使用者帳戶]。



3. 在 **Console Root\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates** 之下尋找 **AzureStackSelfSignedRootCert**。

    ![透過 mmc.exe 載入 Azure Stack 根憑證][25]

4. 以滑鼠右鍵按一下憑證，選取 [所有工作] > [匯出]，然後依照指示，匯出 **Base-64 encoded X.509 (.CER)** 憑證。  

    下一個步驟中會使用所匯出的憑證。
5. 啟動儲存體總管 (預覽)，如果您看到 [連線至 Azure 儲存體] 對話方塊，請將它取消。

6. 在 [編輯] 功能表上，指向 [SSL 憑證]，然後按一下 [匯入憑證]。 使用檔案選擇器對話方塊來尋找和開啟您在上一個步驟中瀏覽的憑證。

    匯入之後，系統會提示您重新啟動儲存體總管。

    ![將憑證匯入儲存體總管 (預覽) 中][27]

現在已經準備就緒，可以將儲存體總管連線到 Azure Stack 訂用帳戶。

### <a name="to-connect-an-azure-stack-subscription"></a>連線到 Azure Stack 訂用帳戶


1. 儲存體總管 (預覽) 重新啟動後，請選取 [編輯] 功能表，然後確保已選取 [目標 Azure Stack]。 若未選取，請加以選取，然後重新啟動儲存體總管，變更才會生效。 不需要進行此設定，即可相容於 Azure Stack 環境。

    ![確保已選取目標 Azure Stack][28]

7. 在左側窗格中，選取 [管理帳戶]。  
    隨即顯示您已登入的所有 Microsoft 帳戶。

8. 若要連線到 Azure Stack 帳戶，請選取 [新增帳戶]。

    ![新增 Azure Stack 帳戶][29]

9. 在 [連線至 Azure 儲存體] 對話方塊的 [Azure 環境] 底下，選取 [使用 Azure Stack 環境]，然後按一下 [下一步]。

10. 若要使用至少與一個作用中 Azure Stack 訂用帳戶相關聯的 Azure Stack 帳戶登入，請填寫 [登入 Azure Stack 環境] 對話方塊。  

    每個欄位的詳細資料如下所示︰

    * **環境名稱**：使用者可以自訂此欄位。
    * **ARM 資源端點**：Azure Resource Manager 資源端點的範例︰

        * 若是雲端操作員：<br> https://adminmanagement.local.azurestack.external   
        * 若是租用戶：<br> https://management.local.azurestack.external
 
    * **租用戶識別碼**：選擇性。 只有在必須指定目錄時，才會提供此值。

12. 成功使用 Azure Stack 帳戶登入後，左窗格會填入與該帳戶相關聯的 Azure Stack 訂用帳戶。 選取您想要使用的 Azure Stack 訂用帳戶，然後選取 [套用]。 (選取或清除 [所有訂用帳戶] 核取方塊切換開關，可選取全部或不選取任何列出的 Azure Stack 訂用帳戶。)

    ![在填妥 [自訂雲端環境] 對話方塊後選取 Azure Stack 訂用帳戶][30]  
    左窗格會顯示與所選 Azure Stack 訂用帳戶相關聯的儲存體帳戶。

    ![包含 Azure Stack 訂用帳戶的儲存體帳戶清單][31]

## <a name="next-steps"></a>後續步驟
* [開始使用儲存體總管 (預覽)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack 儲存體：差異與注意事項](azure-stack-acs-differences.md)


* 若要深入了解 Azure 儲存體，請參閱 [Microsoft Azure 儲存體簡介](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
