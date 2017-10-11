---
title: "Azure Active Directory 風險事件 | Microsoft Docs"
description: "本主題詳述何謂風險事件。"
services: active-directory
keywords: "azure active directory identity protection, 安全性, 風險, 風險層級, 弱點, 安全性原則"
author: MarkusVi
manager: femila
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 71ab5cb02ac70871fb8207ab9220b45d1c842dde
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-risk-events"></a>Azure Active Directory 風險事件

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 偵測到的每個可疑動作會儲存在名為*風險事件*的記錄中。

Azure Active Directory 目前會偵測六種風險事件類型：

- [認證外洩的使用者](#leaked-credentials) 
- [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) 
- [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) 
- [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations)
- [從受感染的裝置登入](#sign-ins-from-infected-devices) 
- [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) 


![風險事件](./media/active-directory-reporting-risk-events/91.png)

本主題提供哪些屬於風險事件，以及您如何使用它們來保護 Azure AD 身分識別的概觀。


## <a name="risk-event-types"></a>風險事件類型

風險事件類型屬性是風險事件記錄已為之建立的可疑動作的識別碼。  
Microsoft 針對偵測程序的持續投資的結果是︰

- 改善現有風險事件的偵測精確度 
- 未來將會新增的新風險事件類型

### <a name="leaked-credentials"></a>認證外洩

當網路罪犯入侵合法使用者的有效密碼時，罪犯通常會共用這些認證。 他們的做法通常是在深層網路上公開張貼或貼上站台，或是在黑市上交易或銷售認證。 Microsoft 的認證外洩服務取得使用者名稱 / 密碼組的方式，是監視公用及深層網路，以及使用：

- 研究員
- 執法機關
- Microsoft 安全性小組
- 其他受信任的來源 

當服務取得使用者名稱 / 密碼組時，它們會針對 AAD 使用者目前的有效認證進行檢查。 找到相符項目時，表示使用者的密碼已遭入侵，並已建立認證外洩風險事件。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。


### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

此風險事件類型會識別來自距離遙遠的位置的兩次登入，而根據使用者過去的行為，其中至少有一個位置可能不尋常。 此外，兩次登入之間的時間比使用者從第一個位置移到第二個位置所需的時間還要短，這表示有不同的使用者正在使用相同的認證。 

這種機器學習演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

此風險事件類型會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的 / 不熟悉的位置。 系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 從不在熟悉位置清單中的位置登入時，會觸發此風險事件。 系統有為期 30 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險事件類型會識別從感染惡意程式碼的裝置登入，已知這類登入會主動與 Bot 伺服器通訊。 讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
此風險事件類型會識別在短期內透過多個使用者帳戶多次嘗試登入失敗的 IP 位址。 這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。 這種機器學習演算法會忽略明顯的「誤判」，例如，組織中的其他使用者定期使用的 IP 位址。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。


## <a name="detection-type"></a>偵測類型

偵測類型屬性是風險事件的偵測時間範圍指標 (即時或離線)。  
目前，大部分的風險事件都是在風險事件發生之後，在後處理作業中以離線方式偵測的。

下表列出偵測類型要在相關報告中顯示所花費的時間：

| 偵測類型 | 報告延遲 |
| --- | --- |
| 即時 | 5 至 10 分鐘 |
| 離線 | 2 至 4 小時 |


針對 Azure Active Directory 要偵測的風險事件類型，偵測類型包括：

| 風險事件類型 | 偵測類型 |
| :-- | --- | 
| [認證外洩的使用者](#leaked-credentials) | 離線 |
| [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) | 即時 |
| [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) | 離線 |
| [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) | 即時 |
| [從受感染的裝置登入](#sign-ins-from-infected-devices) | 離線 |
| [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) | 離線|


## <a name="risk-level"></a>風險層級

風險事件的風險層級屬性是風險事件的嚴重性和信賴度的指標 (高、中或低)。 這個屬性可協助您排定必須採取之動作的優先順序。 

風險事件的嚴重性代表做為身分識別入侵預測工具的訊號強度。  
信賴度是發生誤判可能性的指標。 

例如， 

* **高**：高信賴度和高嚴重性風險事件。 這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

* **中**：高嚴重性，但信賴度較低的風險事件，或反之亦然。 這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

* **低**：低信賴度和低嚴重性風險事件。 此事件可能不需要採取立即行動，但與其他風險事件結合時，可能強烈指出身分識別遭到入侵。

![風險層級](./media/active-directory-reporting-risk-events/01.png)

### <a name="leaked-credentials"></a>認證外洩

認證外洩風險事件會被歸類為**高**，因為它們清楚指出攻擊者可使用使用者名稱和密碼。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險事件類型的風險層級為**中**，因為匿名 IP 位址並未強烈指出帳戶遭到入侵。  
我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。


### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

不可能的移動通常會明顯指出駭客已能夠成功登入。 不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。 另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。 由於這些誤判，以致此風險事件的風險層級為**中**。

> [!TIP]
> 您可以藉由設定[具名位置](active-directory-named-locations.md)，來降低針對此風險事件類型所報告的誤判數量。 

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

不熟悉的位置可以強烈指出攻擊者可能使用遭竊的身分識別。 當使用者正在移動、試用新裝置或使用新的 VPN 時，可能會發生誤判。 由於這些誤判，以致此事件類型的風險層級為**中**。

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險事件可識別 IP 位址，而不是使用者裝置。 如果單一 IP 位址背後有數個裝置，而只有某些裝置受 Bot 網路控制，則來自其他裝置的登入可能會不必要地觸發此事件，這就是將此風險事件歸類為**低**的原因。  

建議您連絡使用者並掃描使用者的所有裝置。 使用者的個人裝置也可能受到感染，或如前所述，可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。 受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)。


### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。 此事件類型的風險層級為「**中**」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。 


 
## <a name="next-steps"></a>後續步驟

風險事件是保護您 Azure AD 身分識別的基礎。 Azure AD 目前可以偵測六種風險事件： 


| 風險事件類型 | 風險層級 | 偵測類型 |
| :-- | --- | --- |
| [認證外洩的使用者](#leaked-credentials) | 高 | 離線 |
| [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) | 中型 | 即時 |
| [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) | 中型 | 離線 |
| [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) | 中型 | 即時 |
| [從受感染的裝置登入](#sign-ins-from-infected-devices) | 低 | 離線 |
| [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) | 中型 | 離線|

您可以在何處找到已在您的環境中偵測到的風險事件？
您可以在兩個地方檢閱已報告的風險事件：

 - **Azure AD 報告** - 風險事件屬於 Azure AD 的安全性報表。 如需詳細資訊，請參閱[有風險的安全性報告上的使用者](active-directory-reporting-security-user-at-risk.md)和[有風險的登入安全性報告](active-directory-reporting-security-risky-sign-ins.md)。

 - **Azure AD Identity Protection** - 風險事件也屬於 [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 的報告功能。
    

儘管偵測風險事件已經代表保護您身分識別的重要層面，但您還是可以選擇手動處理它們，或甚至可藉由設定條件式存取原則來實作自動化回應。 如需詳細資料，請參閱 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)。
 
