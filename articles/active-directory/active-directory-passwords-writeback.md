---
title: "Azure AD SSPR 與密碼回寫 | Microsoft Docs"
description: "使用 Azure AD 和 Azure AD Connect 將密碼回寫到內部部署目錄"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8ca760c3f144cda15920dd401c6a8726d3d53da0
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="password-writeback-overview"></a>密碼回寫概觀

使用密碼回寫時，您可以將 Azure Active Directory (Azure AD) 設定為將密碼回寫到內部部署 Active Directory。 密碼回寫可讓您不需設定和管理複雜的內部部署自助式密碼重設 (SSPR) 解決方案，並且提供便利的雲端方式，可讓您的使用者不論身在何處，都能重設其內部部署密碼。 密碼回寫是 [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) 的元件，可供目前 Premium [Azure Active Directory 版本](active-directory-whatis.md)的訂閱者啟用及使用。

密碼回寫提供下列功能：

* **提供零延遲的意見反應**：密碼回寫是一項同步作業。 如果使用者的密碼不符合原則，或因為任何原因而無法重設或變更，他們會立即收到通知。
* **針對使用 Active Directory 同盟服務 (AD FS) 或其他同盟技術的使用者支援密碼重設**：使用密碼回寫時，只要將同盟使用者帳戶同步處理到您的 Azure AD 租用戶，他們就能夠從雲端管理自己的內部部署 Active Directory 密碼。
* **針對使用[密碼雜湊同步處理](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)的使用者支援密碼重設**：當密碼重設服務偵測到同步處理的使用者帳戶已啟用密碼雜湊同步處理功能時，我們會同時重設此帳戶的內部部署密碼和雲端密碼。
* **支援從存取面板和 Office 365 變更密碼**：當同盟或已同步處理密碼的使用者變更其已過期或尚未過期的密碼時，我們會將這些密碼回寫到您的本機 Active Directory 環境。
* **支援在管理員從 Azure 入口網站重設密碼時將密碼回寫**：每當管理員在 [Azure 入口網站](https://portal.azure.com)中重設使用者密碼時，如果該使用者為同盟或已同步處理密碼的使用者，我們也會在本機 Active Directory 中設定管理員所選取的密碼。 Office 管理入口網站目前不支援此功能。
* **強制執行您的內部部署 Active Directory 密碼原則**：當使用者重設其密碼時，我們會確保該密碼符合您的內部部署 Active Directory 原則，然後才認可至該目錄。 這項檢閱包括檢查歷程記錄、複雜度、有效期、密碼篩選，以及您在本機 Active Directory 中已定義的任何其他密碼限制。
* **不需要任何輸入防火牆規則**：密碼回寫會使用「Azure 服務匯流排」轉送作為基礎通訊通道。 您不須在防火牆上開啟任何輸入連接埠，此功能就能正常運作。
* **針對存在於內部部署 Active Directory 中受保護群組內的使用者帳戶不支援**：如需有關受保護群組的詳細資訊，請參閱 [Active Directory 中受保護的帳戶和群組](https://technet.microsoft.com/library/dn535499.aspx) \(機器翻譯\)。

## <a name="how-password-writeback-works"></a>密碼回寫的運作方式

當同盟或密碼雜湊同步使用者過來重設或變更其雲端密碼時，會發生下列情況：

1. 我們會檢查看看使用者擁有哪一種密碼。 如果我們看到密碼是在內部部署環境中管理的：
   * 我們會檢查回寫服務是否已啟動並在執行中。 如果服務已啟動並在執行中，我們就會讓使用者繼續進行操作。
   * 如果回寫服務並未啟動，我們會告知使用者無法立即重設其密碼。
2. 接著，使用者會通過適當的驗證閘道，然後到達 [重設密碼] 畫面。
3. 使用者選取新的密碼，並加以確認。
4. 當使用者選取 [送出] 時，我們會以在回寫設定程序期間建立的對稱金鑰加密純文字密碼。
5. 在加密密碼後，我們會將它放入裝載中，透過 HTTPS 通道傳送到您租用戶特定的服務匯流排轉送 (我們也會在回寫設定過程中為您設定此轉送)。 此轉送受到只有您的內部部署安裝才會知道的隨機產生密碼所保護。
6. 在訊息抵達服務匯流排之後，密碼重設端點會自動甦醒，並看到有擱置中的重設要求。
7. 服務會接著使用雲端錨點屬性來尋找使用者。 若要讓此查閱成功︰

    * 使用者物件必須存在於 Active Directory 連接器空間中。
    * 使用者物件必須連結至對應的 Metaverse (MV) 物件。
    * 使用者物件必須連結至對應的 Azure Active Directory 連接器物件。
    * 從 Active Directory 連接器物件到 MV 的連結上必須有同步處理規則 `Microsoft.InfromADUserAccountEnabled.xxx`。 <br> <br>
    從雲端傳來呼叫時，同步處理引擎會使用 **cloudAnchor** 屬性來查閱 Azure Active Directory 連接器空間物件。 接著，它會依循連結回到 MV 物件，然後再依循連結回到 Active Directory 物件。 因為相同使用者可能會有多個 Active Directory 物件 (多樹系)，所以同步處理引擎需倚賴 `Microsoft.InfromADUserAccountEnabled.xxx` 連結來選出正確的物件。

    > [!Note]
    > 由於這個邏輯的緣故，Azure AD Connect 必須要能夠與主要網域控制站 (PDC) 模擬器通訊，密碼回寫才能運作。 如果需要手動啟用此功能，您可以將 Azure AD Connect 連線到 PDC 模擬器。 在 Active Directory 同步處理連接器的 [屬性] 上按一下滑鼠右鍵，然後選取 [設定目錄分割]。 從該處尋找 [網域控制站連線設定] 區段，然後核取標題為 [只使用慣用的網域控制站] 的方塊。 即使慣用的網域控制站不是 PDC 模擬器，Azure AD Connect 仍會嘗試連線至 PDC 來進行密碼回寫。

8. 找到使用者帳戶之後，我們會嘗試在適當的 Active Directory 樹系中直接重設密碼。
9. 如果密碼設定作業成功，我們會告訴使用者其密碼已變更。
    > [!NOTE]
    > 如果將使用者密碼同步至 Azure AD 時使用的是密碼同步處理，內部部署密碼原則就有可能比雲端密碼原則弱。 在此情況下，我們仍然會強制執行內部部署原則 (不論是怎樣的原則)，然後改為使用密碼雜湊同步處理來同步處理該密碼的雜湊。 此原則可確保不論您是使用密碼同步處理還是同盟來提供單一登入，都會在雲端強制執行您的內部部署原則。

10. 如果密碼設定作業失敗，我們會對使用者傳回錯誤，請他們再試一次。 作業可能會因下列原因而失敗：
    * 服務已停止運作。
    * 他們所選的密碼不符合組織原則。
    * 我們在本機 Active Directory 中找不到使用者。

    針對這當中的許多原因，我們都有提供特定訊息，並告知使用者該怎麼做來解決問題。

## <a name="configure-password-writeback"></a>設定密碼回寫

如果您想使用密碼回寫，我們建議您使用 [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) 的自動更新功能。

現在已不支援使用 DirSync 和 Azure AD Sync 來啟用密碼回寫。 如需可協助您進行轉換的詳細資訊，請參閱[從 DirSync 和 Azure AD 同步進行升級](connect/active-directory-aadconnect-dirsync-deprecated.md)。

下列步驟假設您已使用[快速](./connect/active-directory-aadconnect-get-started-express.md)或[自訂](./connect/active-directory-aadconnect-get-started-custom.md)設定在您的環境中設定 Azure AD Connect。

1. 若要設定和啟用密碼回寫，請登入 Azure AD Connect 伺服器，然後啟動 **Azure AD Connect** 設定精靈。
2. 在 [歡迎] 頁面上，選取 [設定]。
3. 在 [其他工作] 頁面上，選取 [自訂同步處理選項]，然後選取 [下一步]。
4. 在 [連線到 Azure AD] 頁面上，輸入全域管理員認證，然後選取 [下一步]。
5. 在 [連線目錄] 和 [網域/OU] 篩選頁面上，選取 [下一步]。
6. 在 [選用功能] 頁面上，選取 [密碼回寫] 旁邊的方塊，然後選取 [下一步]。
   ![在 Azure AD Connect 中啟用密碼回寫][Writeback]
7. 在 [準備好設定] 頁面上，選取 [設定]，然後等待程序完成。
8. 看到設定完成時，選取 [結束]。

如需了解有關密碼回寫的常見疑難排解工作，請參閱我們疑難排解文章中的[針對密碼回寫問題進行疑難排解](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)一節。

## <a name="active-directory-permissions"></a>Active Directory 權限

如果您想要在 SSPR 的範圍中，Azure AD Connect 公用程式中指定的帳戶必須設定好下列項目：

* **重設密碼** 
* **變更密碼** 
* `lockoutTime` 的**寫入權限**  
* `pwdLastSet` 的**寫入權限**
* 下列其中一項的**延伸權限**：
   * 該樹系中「每個網域」的根物件
   * 要在 SSPR 範圍中的使用者組織單位 (OU)

如果您不確定上述所指的是哪個帳戶，請開啟 Azure Active Directory Connect 組態 UI，然後選取 [檢視目前的設定] 選項。 您需要新增權限的帳戶會列在 [同步處理的目錄] 底下。

如果您設定這些權限，每個樹系的 MA 服務帳戶便可代表該樹系內的使用者帳戶管理密碼。 

> [!IMPORTANT]
> 如果您忘了指派這些權限，則即使回寫看似設定正確，但當使用者嘗試從雲端管理其內部部署密碼時，還是會遇到錯誤。
>

> [!NOTE]
> 最多可能需要一小時或更久，這些權限才會複寫至您目錄中的所有物件。
>

若要設定適當權限以進行密碼回寫，請完成下列步驟：

1. 以具有適當網域管理權限的帳戶開啟 [Active Directory 使用者及電腦]。
2. 從 [檢視] 功能表中，確定已開啟 [進階功能]。
3. 在左面板中，於代表網域根目錄的物件上按一下滑鼠右鍵，然後選取 [屬性] > [安全性] > [進階]。
4. 從 [權限] 索引標籤中，選取 [新增]。
5. 挑選要套用權限的帳戶 (從 Azure AD Connect 安裝程式)。
6. 在 [套用至] 下拉式清單中，選取 [下階使用者] 物件。
7. 在 [權限] 底下，核取下列方塊：
    * **重設密碼**
    * **變更密碼**
    * **寫入 lockoutTime**
    * **寫入 pwdLastSet**
8. 選取 [套用]/[確定] 以套用變更並結束任何開啟的對話方塊。

## <a name="licensing-requirements-for-password-writeback"></a>密碼回寫的授權需求

如需有關授權的資訊，請參閱[密碼回寫所需的授權](active-directory-passwords-licensing.md#licenses-required-for-password-writeback)或下列網站：

* [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企業版](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>針對密碼回寫支援的內部部署驗證模式

下列使用者密碼類型的密碼回寫支援︰

* **僅限雲端的使用者**︰密碼回寫不適用於這種情況，因為沒有內部部署密碼。
* **已同步處理密碼的使用者**︰支援密碼回寫。
* **同盟使用者**︰支援密碼回寫。
* **傳遞驗證使用者**︰支援密碼回寫。

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>密碼回寫支援的使用者和管理員作業

下列所有情況都會回寫密碼︰

* **支援的使用者作業**
  * 任何使用者自助式自願變更密碼作業
  * 任何使用者自助式強制變更密碼作業 (例如密碼到期)
  * 任何源自[密碼重設入口網站](https://passwordreset.microsoftonline.com)的使用者自助式密碼重設
* **支援的系統管理員作業**
  * 任何系統管理員自助式自願變更密碼作業
  * 任何系統管理員自助式強制變更密碼作業 (例如密碼到期)
  * 任何源自[密碼重設入口網站](https://passwordreset.microsoftonline.com)的系統管理員自助式密碼重設
  * 系統管理員從 [Azure 傳統入口網站](https://manage.windowsazure.com)起始的任何使用者密碼重設
  * 系統管理員從 [Azure 入口網站](https://portal.azure.com)起始的任何使用者密碼重設

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>密碼回寫不支援的使用者和管理員作業

下列任何情況均*不會*回寫密碼︰

* **不支援的使用者作業**
  * 任何由使用者使用 PowerShell 第 1 版、第 2 版或 Azure AD Graph API 來進行的自有密碼重設
* **不支援的系統管理員作業**
  * 系統管理員從 [Office 管理入口網站](https://portal.office.com)起始的任何使用者密碼重設
  * 任何由系統管理員從 PowerShell 第 1 版、第 2 版或 Azure AD Graph API 起始的使用者密碼重設

我們正努力移除這些限制，但目前還無法提出具體的時間表。

## <a name="password-writeback-security-model"></a>密碼回寫的安全性模型

密碼回寫是一項極為安全的服務。 為了確保您的資訊受到保護，我們會啟用四層式安全性模型，如下所述：

* **租用戶特定的服務匯流排轉送**
  * 當您設定服務時，我們會設定一個以隨機產生強式密碼保護的租用戶特定服務匯流排轉送，且 Microsoft 永遠無法得知該密碼。
* **受到鎖定的密碼編譯強式密碼加密金鑰**
  * 建立服務匯流排轉送之後，我們會建立強式對稱金鑰，以在透過線路傳送密碼時予以加密。 此金鑰只會存在於您公司的雲端密碼存放區中，並受到嚴密鎖定和稽核，就像目錄中的任何其他密碼一樣。
* **業界標準傳輸層安全性 (TLS)**
  1. 當雲端上發生密碼重設或變更作業時，我們會擷取純文字密碼，並以公開金鑰予以加密。
  2. 我們會將它放入 HTTPS 訊息中，使用 Microsoft SSL 憑證透過加密通道傳送到您的服務匯流排轉送。
  3. 在訊息抵達服務匯流排之後，您的內部部署代理程式會喚醒服務匯流排，並使用先前產生的強式密碼向其進行驗證。
  4. 內部部署代理程式會拾取加密訊息，然後使用我們所產生的私密金鑰進行解密。
  5. 內部部署代理程式會嘗試透過 AD DS SetPassword API 來設定密碼。 這個步驟可讓我們在雲端強制執行您的 Active Directory 內部部署密碼原則 (例如複雜度、有效期、歷程記錄、篩選等)。
* **訊息到期原則** 
  * 如果訊息因內部部署服務已停止運作而停留在服務匯流排中，在數分鐘之後，它就會逾時而被移除。 讓訊息逾時並予以移除可更進一步提升安全性。

### <a name="password-writeback-encryption-details"></a>密碼回寫的加密詳細資料

在使用者提交密碼重設之後，重設要求會先經過數個加密步驟，然後才抵達您的內部部署環境。 這些加密步驟可確保提供最高的服務可靠性和安全性。 這些步驟的說明如下：

* **步驟 1：採用 2048 位元 RSA 金鑰的密碼加密**在使用者提交要寫回到內部部署環境的密碼之後，所提交的密碼本身會以 2048 位元 RSA 金鑰加密。
* **步驟 2：採用 AES-GCM 的套件層級加密**：整個套件 (密碼 + 必要的中繼資料) 會以 AES-GCM 加密。 這個加密可防止任何可直接存取基礎 ServiceBus 通道的人員檢視或竄改內容。
* **步驟 3：所有通訊都會透過 TLS/SSL 進行**：與 ServiceBus 的所有通訊都會在 SSL/TLS 通道中進行。 這個加密可保護內容，免於遭到未經授權的第三方存取。
* **每 6 個月自動變換金鑰**：每 6 個月，或每次在 Azure AD Connect 上停用再重新啟用密碼回寫時。 我們會自動變換所有金鑰，以確保提供最高的服務安全性。

### <a name="password-writeback-bandwidth-usage"></a>密碼回寫頻寬使用量

密碼回寫是一種低頻寬服務，只有在下列情況下，才會將要求傳回給內部部署代理程式︰

* 透過 Azure AD Connect 啟用或停用此功能時會傳送兩則訊息。
* 每 5 分鐘傳送一則訊息作為服務活動訊號 (只要服務正在執行)。
* 每次提交新密碼時會傳送兩則訊息：
    * 第一則訊息是一個執行作業的要求。
    * 第二則訊息包含該作業的結果，並且會在下列情況下傳送︰
        * 每次在使用者自助式密碼重設期間提交新密碼時。
        * 每次在使用者密碼變更作業期間提交新密碼時。
        * 每次在系統管理員所起始的使用者密碼重設 (僅限從 Azure 系統管理員入口網站) 期間提交新密碼時。

#### <a name="message-size-and-bandwidth-considerations"></a>訊息大小和頻寬考量

上述每則訊息的大小通常會小於 1 KB。 即使在負載極大的情況下，密碼回寫服務本身每秒也只會耗用幾千位元的頻寬。 因為每則訊息都以即時方式傳送 (僅限密碼更新作業要求時)，且因為訊息大小是如此的小，所以回寫功能的頻寬使用量會因太小，以致沒有可測得的影響。

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)。
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)。
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中啟用密碼回寫"
