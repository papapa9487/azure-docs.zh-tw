---
title: "Azure 儲存體總管疑難排解指南 | Microsoft Docs"
description: "Azure 兩個偵錯功能的概觀"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: e06c73c2c00b27178f8431b83b5c5a42110b6b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 儲存體總管疑難排解指南

Microsoft Azure 儲存體總管 (預覽) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 應用程式可以連線至裝載於 Azure、Sovereign Clouds 和 Azure Stack 上的儲存體帳戶。

本指南摘要說明儲存體總管中常見的問題解決方案。

## <a name="sign-in-issues"></a>登入問題

僅支援 Azure Active Directory (AAD) 帳戶。 如果使用的是 ADFS 帳戶，應無法登入儲存體總管。 在繼續之前，請先嘗試重新啟動您的應用程式，查看是否能修正問題。

### <a name="error-self-signed-certificate-in-certificate-chain"></a>錯誤：憑證鏈結中的自我簽署憑證

發生這個錯誤的原因有好幾個，最常見的兩個原因是：

1. 應用程式是透過「透明 proxy」連線，這表示伺服器 (例如您公司的伺服器) 是使用自我簽署憑證攔截 HTTPS 流量、解密再加密。

2. 您正在執行的應用程式，例如防毒軟體，會將自我簽署的 SSL 憑證插入您收到的 HTTPS 訊息中。

當儲存體總管發生其中一個問題時，就不會知道所接收的 HTTPS 訊息是否遭竄改。 如果您有一份自我簽署憑證，您可以讓儲存體總管信任該憑證。 如果不確定是誰插入的憑證，請依照下列步驟來尋找憑證：

1. 安裝 Open SSL

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版足可應對)

    - Mac 和 Linux：應該包含在作業系統中

2. 執行 Open SSL

    - Windows：開啟安裝目錄，按一下 **/bin/**，再按兩下 **openssl.exe**。
    - Mac 和 Linux：從終端機執行 **openssl**。

3. 執行 s_client -showcerts -connect microsoft.com:443

4. 尋找自我簽署憑證。 如果不確定哪些是自我簽署的憑證，請尋找主旨 ("s:") 和簽發者 ("i:") 相同的所有位置。

5. 發現任何自我簽署的憑證時，請將每個憑證從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。

6. 開啟儲存體總管，按一下 [編輯] > [SSL 憑證] > [匯入憑證]，然後使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果使用上述步驟找不到任何自我簽署的憑證，請透過意見反應工具與我們連絡，以取得更多協助。

### <a name="unable-to-retrieve-subscriptions"></a>無法擷取訂用帳戶

如果成功登入後無法擷取您的訂用帳戶，請依照下列步驟對此問題進行疑難排解：

- 透過登入 Azure 入口網站確認您的帳戶可存取訂閱的項目。

- 確定已使用正確的環境登入 (Azure、Azure 中國、Azure 德國、Azure 美國政府或自訂環境/Azure Stack)。

- 如果您是在 proxy 背景，請確定已正確設定儲存體總管的 proxy。

- 嘗試移除再重新新增帳戶。

- 嘗試從根目錄 (亦即 C:\Users\ContosoUser) 刪除下列檔案，再重新新增帳戶：

    - .adalcache

    - .devaccounts

    - .extaccounts

- 登入出現任何錯誤訊息時，查看開發人員工具主控台 (按 F12)：

![開發人員工具](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>看不到驗證頁面

如果看不到驗證頁面，請依照下列步驟對此問題進行疑難排解：

- 載入登入頁面可能需要一些時間，視連線速度而定，請等待至少一分鐘再關閉 [驗證] 對話方塊。

- 如果您是在 proxy 背景，請確定已正確設定儲存體總管的 proxy。

- 按下 F12 鍵，檢視開發人員主控台。 查看開發人員主控台的回應，看看能否找到任何驗證無法運作的線索。

### <a name="cannot-remove-account"></a>無法移除帳戶

如果無法移除帳戶，或重新驗證連結不執行任何動作，請依照下列步驟對此問題進行疑難排解：

- 嘗試從根目錄刪除下列檔案，再重新新增帳戶：

    - .adalcache

    - .devaccounts

    - .extaccounts

- 如果您想要移除 SAS 連接的儲存體資源，請刪除下列檔案：

    - Windows 是 %AppData%/StorageExplorer 資料夾

    - Mac 是 /Users/<您的名稱>/Library/Applicaiton SUpport/StorageExplorer

    - Linux 是 ~/.config/StorageExplorer

> [!NOTE]
>  如果刪除這些檔案，您必須重新輸入所有認證。

## <a name="proxy-issues"></a>Proxy 問題

首先，確定您輸入的下列資訊都正確：

- Proxy URL 和連接埠號碼

- 使用者名稱和密碼 (如果 proxy 要求)

### <a name="common-solutions"></a>常見的解決方案

如果問題持續發生，請依照下列步驟進行疑難排解：

- 如果可以不使用 proxy 連接到網際網路，請確認儲存體總管不啟用 proxy 設定也能運作。 如果是這種情況，可能是 proxy 設定發生問題。 請和您的 proxy 系統管理員合作找出問題。

- 確認使用 proxy 伺服器的其他應用程式是否如預期般運作。

- 確認可以使用網頁瀏覽器連接到 Microsoft Azure 入口網站

- 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果可以連線，您應該會收到 InvalidQueryParameterValue 或類似的 XML 回應。

- 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果他們可以連線，您可能必須連絡您的 proxy 伺服器管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具，例如 Fiddler for Windows，您可以診斷問題，如下所示：

- 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。

- 檢查網路工具使用的連接埠號碼。

- 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 如果作業正確，您的網路工具就會開始記錄儲存體總管向管理和服務端點提出的網路要求。 例如，在瀏覽器中輸入 blob 端點 https://cawablobgrs.blob.core.windows.net/，您會收到類似下面的回應，這表示雖然您無法存取，但資源確實存在。

![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果 proxy 設定正確，您可能要連絡您的 proxy 伺服器管理員，並

- 確定 proxy 不會封鎖 Azure 管理或資源端點的流量。

- 確認 proxy 伺服器使用的驗證通訊協定。 儲存體總管目前不支援 NTLM proxy。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果透過 proxy 連線至 Azure，請確認您的 proxy 設定正確無誤。 如已獲授權可存取訂用帳戶或帳戶擁有者的資源，請確認您已閱讀或列出該資源的權限。

### <a name="issues-with-sas-url"></a>SAS URL 問題
如果要連線到使用 SAS URL 的服務，但發生此錯誤：

- 請確認 URL 提供讀取或列出資源的必要權限。

- 請確認 URL 尚未過期。

- 如果 SAS URL 是以存取原則為基礎，請確認尚未撤銷存取原則。

## <a name="next-steps"></a>後續步驟

如果這些解決方案都不能解決您的問題，請使用電子郵件透過意見反應工具提交您的問題，盡可能附上問題的詳細資料，以便我們與您連絡修正問題。

若要這樣做，請按一下 [說明] 功能表，再按一下 [傳送意見反應]**傳送回函**。

![意見反應](./media/storage-explorer-troubleshooting/4022503_en_1.png)
