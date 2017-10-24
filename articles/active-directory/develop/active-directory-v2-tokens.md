---
title: "Azure Active Directory v2.0 權杖參考 | Microsoft Docs"
description: "Azure AD v2.0 端點所發出之權杖和宣告的類型"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ec25d4375647a2c8983d7573b9912e544fc3e7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 權杖參考
Azure Active Directory (Azure AD) v2.0 端點會在每個[驗證流程](active-directory-v2-flows.md)中發出數種安全性權杖。 本參考文件說明每種權杖的格式、安全性特性及內容。

> [!NOTE]
> v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。 若要判斷您是否應該使用 v2.0 端點，請參閱 [v2.0 限制](active-directory-v2-limitations.md)。
>
>

## <a name="types-of-tokens"></a>權杖的類型
v2.0 端點支援 [OAuth 2.0 授權通訊協定](active-directory-v2-protocols.md)，此通訊協定會使用存取權杖和重新整理權杖。 v2.0 端點也支援透過 [OpenID Connect](active-directory-v2-protocols.md) 進行驗證和登入。 OpenID Connect 引進了第三種權杖，即識別碼權杖。 這些權杖中的每一個都是以「持有人」權杖來表示。

持有人權杖是輕巧型的安全性權杖，可授權持有人存取受保護的資源。 持有人是可出示權杖的任何一方。 雖然某一方必須先向 Azure AD 進行驗證，才能收到持有人權杖，但如果在傳輸和儲存期間未採取步驟來保護權杖，它就可能會被非預期的一方攔截和使用。 有些安全性權杖有內建的機制，可防止未經授權的對象使用它們，但持有人權杖並沒有這種機制。 持有人權杖必須在安全的通道 (例如傳輸層安全性 (HTTPS)) 中傳輸。 如果在沒有這種安全性的情況下傳輸持有人權杖，惡意人士便可能使用「攔截式攻擊」來取得權杖，並使用它以未經授權的方式存取受保護的資源。 儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。 請一律確保您的應用程式以安全的方式傳輸和儲存持有人權杖。 如需了解持有人權杖的更多安全性考量，請參閱 [RFC 6750 第 5 節](http://tools.ietf.org/html/rfc6750)。

許多由 v2.0 端點所簽發的權杖都是以「Json Web 權杖」(JWT) 的形式實作。 JWT 是一種精簡的 URL 安全方法，可在兩方之間傳輸資訊。 JWT 中的資訊稱為「宣告」。 它是權杖持有人及主體相關資訊的判斷提示。 JWT 中的宣告是針對傳輸進行編碼和序列化的「JavaScript 物件標記法」(JSON) 物件。 由於 v2.0 端點所簽發的 JWT 已簽署但未加密，因此您可以輕鬆地檢查 JWT 的內容以便進行偵錯。 如需有關 JWT 的詳細資訊，請參閱 [JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

### <a name="id-tokens"></a>ID 權杖
識別碼權杖是您的應用程式使用 [OpenID Connect](active-directory-v2-protocols.md) 來執行驗證時所收到的一種登入安全性權杖形式。 識別碼權杖會以 [JWT](#types-of-tokens)表示，並且包含了您可用來讓使用者登入應用程式的宣告。 您可以用各種方式使用識別碼權杖中的宣告。 通常，系統管理員會使用識別碼權杖來顯示顯示帳戶資訊，或是在應用程式中進行存取控制決策。 v2.0 端點只會簽發一種識別碼權杖，不論登入的使用者是哪一種類型，都有一組一致的宣告。 就個人 Microsoft 帳戶使用者及工作或學校帳戶而言，識別碼權杖的格式和內容都相同。

目前識別碼權杖已簽署但未加密。 當您的應用程式收到識別碼權杖時，它必須[驗證簽章](#validating-tokens)以證明權杖的真實性，以及驗證權杖中的一些宣告來證明其有效性。 應用程式所驗證的宣告會視案例需求而有所不同，但您的應用程式必須在每一種案例中都執行一些[常見的宣告驗證](#validating-tokens)。

在接下來的幾節中，除了範例識別碼權杖之外，我們還會提供有關識別碼權杖中宣告的完整詳細資料。 請注意，識別碼權杖中的宣告不會以特定順序傳回。 此外，隨時都可以在識別碼權杖中導入新的宣告。 導入新宣告時，您的應用程式應該不會發生中斷的現象。 下列清單包含您應用程式目前可確實解譯的宣告。 您可以在 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)中找到更多詳細資料。

#### <a name="sample-id-token"></a>範例 ID 權杖
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> 練習時，為了檢查範例識別碼權杖中的宣告，請將範例識別碼權杖貼到 [calebb.net](http://calebb.net/) 中。
>
>

#### <a name="claims-in-id-tokens"></a>ID 權杖中的宣告
| 名稱 | 宣告 | 範例值 | 說明 |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |識別權杖的預定接收者。 在識別碼權杖中，對象是在「Microsoft 應用程式註冊入口網站」中指派給您應用程式的「應用程式識別碼」。 您的應用程式應驗證此值，並拒絕值不相符的權杖。 |
| 簽發者 |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 您的應用程式應驗證簽發者宣告，以確保權杖來自 v2.0 端點。 它也應該使用宣告的 GUID 部分來限制可登入應用程式的租用戶集合。 指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 |
| 簽發時間 |`iat` |`1452285331` |簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 |`exp` |`1452289231` |權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 |
| 生效時間 |`nbf` |`1452285331` |權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 這通常與簽發時間相同。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 |
| 版本 |`ver` |`2.0` |Azure AD 所定義的識別碼權杖版本。 就 v2.0 端點而言，值會是 `2.0`。 |
| 租用戶識別碼 |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |代表使用者是來自哪個 Azure AD 租用戶的 GUID。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要 `profile` 範圍才能接收此宣告。 |
| 代碼雜湊 |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |只有當識別碼權杖是隨著 OAuth 2.0 授權碼一起簽發時，代碼雜湊才會包含在識別碼權杖中。 它可用來驗證授權碼的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| 存取權杖雜湊 |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |只有當識別碼權杖是隨著 OAuth 2.0 存取權杖一起簽發時，存取權杖雜湊才會包含在識別碼權杖中。 它可用來驗證存取權杖的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| nonce |`nonce` |`12345` |Nonce 是緩和權杖重新執行攻擊的策略。 您的應用程式可以使用 `nonce` 查詢參數，在授權要求中指定 Nonce。 您在要求中提供的值會不經修改在識別碼權杖的 `nonce` 宣告中發出。 您的應用程式可根據它在要求上指定的值來驗證此值，使應用程式的工作階段與特定的識別碼權杖產生關聯。 您的應用程式應在 ID 權杖驗證程序中執行這項驗證。 |
| 名稱 |`name` |`Babe Ruth` |名稱宣告會提供人類看得懂的值，用以識別權杖的主體。 此值不保證是唯一值，它是可變動的，並且在設計上僅用於顯示。 需要 `profile` 範圍才能接收此宣告。 |
| 電子郵件 |`email` |`thegreatbambino@nyy.onmicrosoft.com` |與使用者帳戶相關聯的主要電子郵件地址 (如果有的話)。 其值是可變動的，並且可能隨著時間改變。 需要 `email` 範圍才能接收此宣告。 |
| 慣用的使用者名稱 |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |代表 v2.0 端點中使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。 需要 `profile` 範圍才能接收此宣告。 |
| 主旨 |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查 (例如當權杖用於存取資源時)，並可做為資料庫資料表中的索引鍵。 由於主體一律是存在於 Azure AD 所簽發的權杖中，因此建議您在一般用途的授權系統中使用此值。 不過，主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。  因此，如果單一使用者使用兩個不同的用戶端識別碼登入兩個不同的應用程式，這些應用程式會收到兩個不同的主體宣告值。  視您的架構和隱私權需求而定，這不一定是您想要的。 |
| 物件識別碼 |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | 物件在 Microsoft 身分識別系統中的不可變識別碼，在此案例為使用者帳戶。  它也可用來安全地執行授權檢查，以及做為資料庫資料表中的索引鍵。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。  這表示在對 Microsoft 線上服務 (例如 Microsoft Graph) 進行查詢時可使用此識別碼。  Microsoft Graph 會傳回這個識別碼做為指定使用者帳戶的 `id` 屬性。  因為 `oid` 可讓多個應用程式相互關聯使用者，因此需要 `profile` 範圍才能接收此宣告。 請注意，如果單一使用者存在於多個租用戶，使用者將會在每個租用戶中包含不同的物件識別碼，它們會被視為不同帳戶，即使使用者使用相同認證來登入各個帳戶也是如此。 |

### <a name="access-tokens"></a>存取權杖
目前 v2.0 端點所簽發的存取權杖僅供「Microsoft 服務」取用。 針對任何目前支援的案例，您的應用程式應該不需要執行任何存取權杖驗證或檢查。 您可以將存取權杖視為完全不透明。 它們只是您應用程式可以在 HTTP 要求中傳遞給 Microsoft 的字串。

在不久的將來，v2.0 端點將引進可讓您的應用程式從其他用戶端接收存取權杖的功能。 屆時，本參考主題中的資訊將會更新為應用程式執行存取權杖驗證和其他類似工作所需的資訊。

當您向 v2.0 端點要求存取權杖時，v2.0 端點也會傳回存取權杖的相關中繼資料，以供您的應用程式使用。 此資訊包括存取權杖的到期時間以及其有效範圍。 您的應用程式可使用此中繼資料來執行存取權杖的智慧型快取，而不需剖析存取權杖本身。

### <a name="refresh-tokens"></a>重新整理權杖
重新整理權杖是安全性權杖，可供您的應用程式用來在 OAuth 2.0 流程中取得新的存取權杖。 您的應用程式可以使用重新整理權杖來代表使用者長期存取資源，而不需與使用者互動。

重新整理權杖屬於多資源權杖。 在一個資源的權杖要求期間收到的重新整理權杖可以兌換完全不同資源的存取權杖。

若要在權杖回應中接收重新整理權杖，您的應用程式必須要求並獲得 `offline_acesss` 範圍。 若要深入了解 `offline_access` 範圍，請參閱[同意和範圍](active-directory-v2-scopes.md)一文。

重新整理權杖對您的應用程式來說，一律是完全不透明的。 它們是由 Azure AD v2.0 端點所簽發，只能由 v2.0 端點檢查和解譯。 它們屬於長效權杖，但不得將您的應用程式撰寫成預期重新整理權杖將持續任何一段時間。 重新整理權杖可能會因為各種原因而隨時失效。 讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是對 v2.0 端點提出權杖要求以嘗試兌換。

當您以重新整理權杖兌換新的存取權杖 (而且如果您的應用程式已獲得 `offline_access` 範圍) 時，您會在權杖回應中收到新的重新整理權杖。 請儲存新簽發的重新整理權杖，以取代您在要求中使用的重新整理權杖。 這可保證您的重新整理權杖儘可能長期保持有效。

## <a name="validating-tokens"></a>驗證權杖
目前，您應用程式應需要執行的唯一權杖驗證就是驗證識別碼權杖。 若要驗證識別碼權杖，您的應用程式應該同時驗證識別碼權杖的簽章和識別碼權杖中的宣告。

<!-- TODO: Link -->
Microsoft 提供示範如何輕鬆處理權杖驗證的程式庫和程式碼範例。 在後續小節中，我們將說明基礎程序。 此外，也有數個協力廠商開放原始碼程式庫可用於 JWT 驗證。 幾乎每個平台和語言都有至少一個程式庫選項。

### <a name="validate-the-signature"></a>驗證簽章
JWT 包含三個區段 (以 `.` 字元分隔)。 第一個區段稱為「標頭」、第二個區段稱為「主體」，而第三個區段則稱為「簽章」。 簽章區段可用來驗證 ID 權杖的真實性，以便獲得應用程式的信任。

ID 權杖是經由業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。 識別碼權杖的標頭包含用來簽署權杖之金鑰和加密方法的相關資訊。 例如：

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` 宣告指出用來簽署權杖的演算法。 `kid` 宣告指出用來簽署權杖的公開金鑰。

v2.0 端點隨時都可能使用一組特定的公開/私密金鑰組的其中一個金鑰組來簽署識別碼權杖。 v2.0 端點會定期替換一組可能的金鑰，因此應將您的應用程式撰寫成自動處理這些金鑰變更。 若要檢查對 v2.0 端點所用公開金鑰的更新，合理的頻率為每隔 24 小時。

您可以使用位於下列位置的 OpenID Connect 中繼資料文件，來取得驗證簽章所需的簽署金鑰資料：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 請在瀏覽器中嘗試該 URL！
>
>

此中繼資料文件是 JSON 物件，內含幾項實用的資訊，例如執行 OpenID Connect 驗證所需的各種端點的位置。  此文件也包含 *jwks_uri*，此 URI 提供用來簽署權杖的公用金鑰組的位置。 位於 jwks_uri 的 JSON 文件包含目前使用中的所有公開金鑰資訊。 您的應用程式可以使用 JWT 標頭中的 `kid` 宣告，來選取本文件中已用來簽署權杖的公開金鑰。 它可接著使用正確的公開金鑰和所指出的演算法，來執行簽章驗證。

執行簽章驗證已超出本文的範圍。 有許多開放原始碼程式庫可協助您進行這方面的操作。

### <a name="validate-the-claims"></a>驗證宣告
如果您的應用程式在使用者登入時收到識別碼權杖，則它應該一併對識別碼權杖中的宣告執行一些檢查。 包含但不限於：

* **對象**宣告 - 用以確認識別碼權杖預定是要提供給您的應用程式
* **生效時間**和**到期時間**宣告 - 用以確認識別碼權杖尚未過期
* **簽發者**宣告 - 用以確認權杖是由 v2.0 端點簽發給您的應用程式
* **Nonce** - 作為權杖重新執行攻擊的緩和措施

如需您應用程式應執行的宣告驗證完整清單，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)。

這些宣告的預期值詳細資料均包含在[識別碼權杖](# ID tokens)一節中。

## <a name="token-lifetimes"></a>權杖存留期
以下提供的權杖存留期僅供您參考。 當您對應用程式進行開發和偵錯時，此資訊或許可協助您。 撰寫應用程式時，請勿將其撰寫成預期任何這些存留期會保持不變。 權杖存留期可以而且將會隨時變更。

| 權杖 | 存留期 | 說明 |
| --- | --- | --- |
| 識別碼權杖 (工作或學校帳戶) |1 小時 |識別碼權杖的有效期通常為 1 小時。 您的 Web 應用程式可以使用此相同的存留期來維護自己與使用者的工作階段 (建議)，或者您也可以選擇一個完全不同的工作階段存留期。 如果您的應用程式需要取得新的識別碼權杖，它就需要對 v2.0 授權端點提出新的登入要求。 如果使用者有 v2.0 端點的有效瀏覽器工作階段，則可能不需要再次輸入其認證。 |
| 識別碼權杖 (個人帳戶) |24 小時 |個人帳戶的識別碼權杖有效期通常為 24 小時。 您的 Web 應用程式可以使用此相同的存留期來維護自己與使用者的工作階段 (建議)，或者您也可以選擇一個完全不同的工作階段存留期。 如果您的應用程式需要取得新的識別碼權杖，它就需要對 v2.0 授權端點提出新的登入要求。 如果使用者有 v2.0 端點的有效瀏覽器工作階段，則可能不需要再次輸入其認證。 |
| 存取權杖 (工作或學校帳戶) |1 小時 |在權杖回應中指出為權杖中繼資料的一部分。 |
| 存取權杖 (個人帳戶) |1 小時 |在權杖回應中指出為權杖中繼資料的一部分。 可以為代表個人帳戶簽發的存取權杖設定不同的存留期，但通常是一小時。 |
| 重新整理權杖 (工作或學校帳戶) |最多 14 天 |單一重新整理權杖的有效期最多 14 天。 不過，重新整理權杖可能會因為各種原因而隨時失效，因此您的應用程式應該繼續嘗試使用重新整理權杖，直到其失敗為止，或直到您的應用程式以新的重新整理權杖取代它為止。 如果自使用者輸入其認證之後已達 90 天，則重新整理權杖也會失效。 |
| 重新整理權杖 (個人帳戶) |最多 1 年 |單一重新整理權杖的有效期最多 1 年。 不過，重新整理權杖可能會因為各種原因而隨時失效，因此您的應用程式應該繼續嘗試使用重新整理權杖，直到其失敗為止。 |
| 授權碼 (工作或學校帳戶) |10 分鐘 |授權碼的存留期是蓄意設定得較短，應該在收到權杖時立即兌換存取權杖和重新整理權杖。 |
| 授權碼 (個人帳戶) |5 分鐘 |授權碼的存留期是蓄意設定得較短，應該在收到權杖時立即兌換存取權杖和重新整理權杖。 代表個人帳戶簽發的授權碼是供單次使用。 |
