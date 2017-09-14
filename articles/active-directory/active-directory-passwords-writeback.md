---
title: "Azure AD SSPR 與密碼回寫 | Microsoft Docs"
description: "使用 Azure AD 和 Azure AD Connect 將密碼回寫到內部部署目錄"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: e460e734973622fb0d5745adfc4c1aa0178dd22e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="password-writeback-overview"></a>密碼回寫概觀

密碼回寫可讓您將 Azure AD 設定為將密碼回寫到您的內部部署 Active Directory。 不需設定和管理複雜的內部部署自助式密碼重設解決方案，並且提供便利的雲端方式，供您的使用者重設其內部部署密碼 (無論他們身處何處)。 密碼回寫是 [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) 的元件，可供目前的 [Azure Active Directory 版本](active-directory-editions.md)訂閱者啟用及使用。

密碼回寫提供下列功能：

* **零延遲的意見反應** - 密碼回寫是一項同步作業。 如果使用者的密碼不符合原則，或因為任何原因而無法重設或變更，他們會立即收到通知。
* **支援使用者使用 AD FS 或其他同盟技術來重設密碼** - 在使用密碼回寫後，只要同盟的使用者帳戶同步處理到您的 Azure AD 租用戶，他們就能夠從雲端管理自己的內部部署 AD 密碼。
* **支援使用者使用[密碼雜湊同步](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)來重設密碼** - 當密碼重設服務偵測到同步處理的使用者帳戶已啟用密碼雜湊同步時，我們會同時重設此帳戶的內部部署密碼和雲端密碼。
* **支援從存取面板和 Office 365 變更密碼** - 當同盟或密碼同步使用者過來變更已過期或尚未過期的密碼時，我們會將這些密碼回寫到您的本機 AD 環境。
* **支援當系統管理員從 Azure 入口網站重設密碼時將密碼回寫** - 每當系統管理員在 [Azure 入口網站](https://portal.azure.com)重設使用者的密碼，如果該使用者為同盟或密碼同步使用者，我們也會在您的本機 AD 上設定系統管理員所選取的密碼。 Office 系統管理入口網站目前不支援這項作業。
* **強制您的內部部署 AD 密碼原則** - 當使用者重設其密碼時，我們會確保它符合您的內部部署 AD 原則，然後再認可到該目錄。 這當中包括歷程記錄、複雜度、使用期限、密碼篩選和您在本機 AD 中定義的任何其他密碼限制。
* **不需要任何輸入防火牆規則** - 密碼回寫會以 Azure 服務匯流排轉送作為基礎通訊管道，這表示您不必在防火牆上開啟任何輸入連接埠，這項功能就能正常運作。
* **不支援存在於內部部署 Active Directory 中受保護群組內的使用者帳戶** - 如需受保護群組的詳細資訊，請參閱 [Active Directory 中受保護的帳戶和群組](https://technet.microsoft.com/library/dn535499.aspx)。

## <a name="how-password-writeback-works"></a>密碼回寫的運作方式

當同盟或密碼雜湊同步使用者過來重設或變更其雲端密碼時，會發生下列情況：

1. 我們會檢查看看使用者擁有哪一種密碼。
    * 如果我們看到密碼是在內部部署環境管理
        * 我們會檢查回寫服務是否已啟動並執行，如果情況正是如此，我們會讓使用者繼續進行
        * 如果回寫服務並未啟動，我們會告訴使用者不能立即重設其密碼
2. 接下來，使用者通過適當的驗證關卡，並到達重設密碼畫面。
3. 使用者選取新的密碼，並加以確認。
4. 按一下送出，我們便會以回寫設定程序期間所建立的對稱金鑰加密純文字密碼。
5. 在加密密碼後，我們會將它放入裝載中，透過 HTTPS 通道傳送到您租用戶特定的服務匯流排轉送 (我們也會在回寫設定過程中為您設定此轉送)。 此轉送受到只有您的內部部署安裝才會知道的隨機產生密碼所保護。
6. 一旦訊息抵達服務匯流排，密碼重設端點就會自動甦醒，並看到它有擱置中的重設要求。
7. 服務接著會使用雲端錨點屬性來尋找提出要求的使用者。 若要讓此查閱成功︰

    * 使用者物件必須存在於 AD 連接器空間內
    * 使用者物件必須連結至對應的 MV 物件
    * 使用者物件必須連結至對應的 AAD 連接器物件。
    * AD 連接器物件與 MV 的連結上必須有同步處理規則 `Microsoft.InfromADUserAccountEnabled.xxx`。 <br> <br>
    當呼叫是來自雲端時，同步處理引擎會使用 cloudAnchor 屬性來查閱 AAD 連接器空間物件，然後順著連結回到 MV 物件，接著再順著連結回到 AD 物件。 因為相同使用者可能有多個 AD 物件 (多樹系)，同步處理引擎需仰賴 `Microsoft.InfromADUserAccountEnabled.xxx` 連結來選出正確的物件。

    > [!Note]
    > 因為此邏輯，Azure AD Connect 必須能夠與 PDC 模擬器通訊，密碼回寫才能運作。 如果您需要手動啟用此功能，您可以在 Active Directory 同步處理連接器的**屬性**上按一下滑鼠右鍵，然後選取**設定目錄分割**，將 Azure AD Connect 連線至 PDC 模擬器。 從那裡尋找**網域控制站連線設定**區段，然後核取標題為**只使用慣用的網域控制站**的方塊。 即使慣用的 DC 不是 PDC 模擬器，Azure AD Connect 仍會嘗試連線至 PDC 進行密碼回寫。

8. 一旦找到使用者帳戶，我們會嘗試直接在適當的 AD 樹系中重設密碼。
9. 如果密碼設定作業成功，我們會告訴使用者其密碼已變更。
    > [!NOTE]
    > 在使用「密碼同步處理」將使用者密碼同步至 Azure AD 的情況下，有可能內部部署密碼原則會比雲端密碼原則弱。 在此情況下，我們仍然會強制執行內部部署原則 (不論是怎樣的原則)，並且改為允許密碼雜湊同步作業同步處理該密碼的雜湊。 這可確保不論您是使用密碼同步處理還是同盟來提供單一登入，都會在雲端強制執行您的內部部署原則。

10. 如果密碼設定作業失敗，我們會對使用者傳回錯誤，請他們再試一次。
    * 作業可能會失敗，原因如下：
        * 服務已關閉
        * 他們所選的密碼不符合組織原則
        * 我們在本機 AD 中找不到使用者

    其中的許多原因會有特定的訊息，讓使用者知道該怎麼做來解決問題。

## <a name="configuring-password-writeback"></a>設定密碼回寫

如果您想使用密碼回寫，我們建議您使用 [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) 的自動更新功能。

啟用密碼回寫功能已不再支援 DirSync 和 Azure AD 同步這兩種方法。[從 DirSync 和 Azure AD Sync 升級](connect/active-directory-aadconnect-dirsync-deprecated.md)一文有詳細資訊可協助您進行轉換。

下列步驟假設您已使用[快速](./connect/active-directory-aadconnect-get-started-express.md)或[自訂](./connect/active-directory-aadconnect-get-started-custom.md)設定在您的環境中設定 Azure AD Connect。

1. 若要設定和啟用密碼回寫，請登入 Azure AD Connect 伺服器並啟動 **Azure AD Connect** 設定精靈。
2. 在 [歡迎使用] 畫面中，按一下 [設定]。
3. 在 [其他工作] 畫面中，按一下 [自訂同步處理選項]，然後選擇 [下一步]。
4. 在 [連線到 Azure AD] 畫面中，輸入全域管理員認證，然後選擇 [下一步]。
5. 在 [連線您的目錄] 和 [網域與 OU 篩選] 畫面中，您可以選擇 [下一步]。
6. 在 [選用功能] 畫面中，勾選 [密碼回寫] 旁邊的方塊，然後按 [下一步]。
   ![在 Azure AD Connect 中啟用密碼回寫][Writeback]
7. 在 [準備好設定] 畫面中，按一下 [設定]，然後等待設定程序完成。
8. 當您看到「設定完成」時，您可以按一下 **[結束]**

## <a name="licensing-requirements-for-password-writeback"></a>密碼回寫的授權需求

如需授權相關資訊，請參閱[密碼回寫所需的授權](active-directory-passwords-licensing.md#licenses-required-for-password-writeback)主題或下列網站

* [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>密碼回寫支援的內部部署驗證模式

密碼回寫適用於下列使用者的密碼類型︰

* **僅限雲端的使用者**︰密碼回寫不適用於這種情況，因為沒有內部部署密碼
* **已同步處理密碼的使用者**︰支援密碼回寫
* **同盟使用者**︰支援密碼回寫
* **傳遞驗證使用者**︰支援密碼回寫

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>密碼回寫支援的使用者和系統管理員作業

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

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>密碼回寫不支援的使用者和系統管理員作業

下列任何情況均**不會**回寫密碼︰

* **不支援的使用者作業**
  * 任何使用 PowerShell v1、v2 或 Azure AD Graph API 重設自有密碼的使用者
* **不支援的系統管理員作業**
  * 系統管理員從 [Office 管理入口網站](https://portal.office.com)起始的任何使用者密碼重設
  * 系統管理員從 PowerShell v1、v2 或 Azure AD Graph API 起始的任何使用者密碼重設

雖然我們正努力移除這些限制，但我們並未提出任何具體的時間表。

## <a name="password-writeback-security-model"></a>密碼回寫的安全性模型

密碼回寫是一項極為安全的服務。  為了確保您的資訊受到保護，我們啟用了 4 層式的安全性模型，詳細情形如下所述。

* **租用戶特定的服務匯流排轉送**
  * 當您在設定服務時，我們會設定租用戶特定的服務匯流排轉送，並以隨機產生的強式密碼加以保護，Microsoft 永遠無法得知此密碼。
* **受到鎖定的密碼編譯強式密碼加密金鑰**
  * 在建立服務匯流排轉送後，我們會建立強式對稱金鑰，以在密碼透過線路傳送過來時予以加密。 此金鑰只會存在於您在雲端的公司密碼存放區中，並受到嚴密鎖定和稽核，情形就和目錄中的任何密碼一樣。
* **業界標準 TLS**
  1. 當雲端上發生密碼重設或變更作業時，我們會擷取純文字密碼，並以公開金鑰予以加密。
  2. 然後將它放入 HTTPS 訊息中，使用 Microsoft 的 SSL 憑證透過加密通道傳送到您的服務匯流排轉送中。
  3. 訊息抵達服務匯流排之後，您的內部部署代理程式會使用先前產生的強式密碼來喚醒及驗證服務匯流排。
  4. 內部部署代理程式會挑選加密訊息，並使用我們所產生的私密金鑰進行解密。
  5. 內部部署代理程式會接著嘗試透過 AD DS SetPassword API 設定密碼。
     * 這個步驟能讓我們在雲端上強制執行 AD 內部部署密碼原則 (複雜度、使用期限、歷程記錄、篩選等)。
* **訊息到期原則** 
  * 如果訊息因為內部部署服務已關閉而停留在服務匯流排，它將會在數分鐘後逾時並遭到移除，更進一步地提升安全性。

### <a name="password-writeback-encryption-details"></a>密碼回寫的加密詳細資料

以下說明在使用者提交密碼重設要求之後，在其抵達您的內部部署環境之前，該項要求所要經歷的加密步驟，以確保最高的服務可靠性和安全性。

* **步驟 1- 採用 2048 位元 RSA 金鑰的密碼加密** - 使用者一旦提交要寫回內部部署環境的密碼，首先，所提交的密碼本身會以 2048 位元 RSA 金鑰進行加密。
* **步驟 2 - 採用 AES-GCM 的套件層級加密** - 然後會使用 AES-GCM 將整個套件 (密碼 + 必要的中繼資料) 加密。 這可防止任何可直接存取基礎 ServiceBus 通道的人員檢視/竄改內容。
* **步驟 3 - 所有通訊都會透過 TLS / SSL 進行** - 所有與 ServiceBus 的通訊都會在 SSL/TLS 通道中進行。 這可保護內容，免於遭到未經授權的第三方存取。
* **每 6 個月自動變換金鑰**- 每 6 個月或每一次在 Azure AD Connect 上停用 / 重新啟用密碼回寫時，我們都會自動變換所有金鑰，以確保最高的服務安全性。

### <a name="password-writeback-bandwidth-usage"></a>密碼回寫頻寬使用量

密碼回寫是一種低頻寬的服務，只有在下列情況下，才可將要求傳回內部部署代理程式︰

1. 透過 Azure AD Connect 啟用或停用此功能時傳送兩則訊息。
2. 每 5 分鐘傳送一則訊息作為服務活動訊號 (只要服務正在執行)。
3. 每次提交新密碼時會傳送兩則訊息
    * 第一則訊息，要求執行作業
    * 第二則訊息，包含作業的結果並且在下列情況傳送︰
        * 每次在使用者自助式密碼重設期間提交新密碼時。
        * 每次在使用者密碼變更作業期間提交新密碼時。
        * 每次在系統管理員所起始的使用者密碼重設期間提交新密碼時 (僅限從 Azure 系統管理員入口網站)。

#### <a name="message-size-and-bandwidth-considerations"></a>訊息大小和頻寬考量

以上所述的每則訊息大小通常在 1 kb 以下，即使在極大負載之下，密碼回寫服務本身每秒耗用頻寬的幾千位元。 因為每則訊息都是即時傳送 (只限於密碼更新作業要求時)，而且因為訊息大小如此小，所以回寫功能的頻寬使用量實際上太小，以致沒有任何真正可測量的影響。

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**資料**](active-directory-passwords-data.md) -了解所需的資料以及如何將它使用於密碼管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並將它部署至使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀及操作方式。
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - -您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "在 Azure AD Connect 中啟用密碼回寫"

