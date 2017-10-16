---
title: "了解 Azure AD 支援的不同權杖和宣告類型 | Microsoft Docs"
description: "可供了解及評估 Azure Active Directory (AAD) 所簽發之 SAML 2.0 和 JSON Web Token (JWT) 權杖中的宣告的指南。"
documentationcenter: na
author: dstrockis
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: be28230b9c56dcbca4ba8f70e44741f65a447f73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-token-reference"></a>Azure AD 權杖參考
Azure Active Directory (Azure AD) 會在處理每個驗證流程時發出數種安全性權杖。 本文件說明每種權杖的格式、安全性特性和內容。

## <a name="types-of-tokens"></a>權杖的類型
Azure AD 支援 [OAuth 2.0 授權通訊協定](active-directory-protocols-oauth-code.md)，該通訊協定會使用 access_tokens 和 refresh_tokens。  它也支援透過 [OpenID Connect](active-directory-protocols-openid-connect-code.md) 進行驗證和登入，這引進第三種類型的權杖 - id_token。  每個權杖都是以「持有人權杖」表示。

持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。 從這個意義上說，「持有者」是可出示權杖的任何一方。 雖然必須向 Azure AD 驗證才能接收持有人權杖，但必須採取步驟來保護權杖，以避免遭非預期人士攔截。 因為持有人權杖沒有內建的機制可防止未經授權的人士使用權杖，必須在安全通道，例如傳輸層安全性 (HTTPS) 來傳輸它們。 如果以純文字傳輸持有人權杖，便可能利用攔截式攻擊來取得權杖，然後取得對受保護的資源未經授權的存取。 儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。 務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。 關於持有者權杖的其他安全性考量，請參閱 [RFC 6750 第 5 節](http://tools.ietf.org/html/rfc6750)。

許多由 Azure AD 所簽發的權杖都會實作為 JSON Web 權杖或 JWT。  JWT 是一種精簡的 URL 安全方法，可在兩方之間傳輸資訊。  JWT 中包含的資訊也稱為權杖持有人及主體相關資訊的「宣告」或判斷提示。  JWT 中的宣告是為了傳輸而編碼和序列化的 JSON 物件。  因為 Azure AD 所簽發的 JWT 已簽署但未加密，所以您可以輕鬆地檢查 JWT 的內容以便偵錯。  有數個工具可以進行這項操作，例如 [jwt.ms](https://jwt.ms/)。 如需 JWT 的詳細資訊，您可以參考 [JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

## <a name="idtokens"></a>Id_tokens
Id_tokens 是您的應用程式使用 [OpenID Connect](active-directory-protocols-openid-connect-code.md) 執行驗證時收到的一種登入安全性權杖形式。  其以 [JWT](#types-of-tokens)表示，而且包含可用於讓使用者登入您的應用程式的宣告。  您可以適時使用 id_token 中的宣告 - 通常用來顯示顯示帳戶資訊或在應用程式中進行存取控制決策。

Id_token 已簽署，但這次不加密。  當您的應用程式收到 id_token 時，它必須[驗證簽章](#validating-tokens)以證明權杖的真實性，以及驗證權杖中的幾個宣告來證明其有效性。  應用程式所驗證的宣告會視案例需求而有所不同，但您的應用程式必須在每一種案例中執行一些 [常見的宣告驗證](#validating-tokens) 。

如需 Id_tokens 宣告以及 id_token 的相關範例，請參閱下一節。  請注意，id_token 中的宣告不依任何特定順序傳回。  此外，新的宣告可以隨時引進 id_token 中 - 您的應用程式不會在引進新的宣告時中斷。  下列清單包含本文撰寫時您的應用程式可確實解譯的宣告。  如有需要，在 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html)中可找到更多詳細資料。

#### <a name="sample-idtoken"></a>範例 id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> 練習時，請試著將範例 id_token 中的宣告貼入 [jwt.ms](https://jwt.ms/) 中進行檢查。
> 
> 

#### <a name="claims-in-idtokens"></a>id_tokens 中的宣告
> [!div class="mx-codeBreakAll"]
| JWT 宣告 | Name | 說明 |
| --- | --- | --- |
| `appid` |應用程式識別碼 |識別使用權杖來存取資源的應用程式。 應用程式代表本身或使用者行事。 應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。 <br><br> **範例 JWT 值**： <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |對象 |權杖的預定接收者。 接收權杖的應用程式必須確認對象值正確無誤，並拒絕任何適用於不同對象的權杖。 <br><br> **範例 SAML 值**： <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **範例 JWT 值**： <br> `"aud":"https://contoso.com"` |
| `appidacr` |應用程式驗證內容類別參考 |指出如何驗證用戶端。 若為公用用戶端，此值為 0。 如果使用用戶端識別碼和用戶端密碼，此值為 1。 <br><br> **範例 JWT 值**： <br> `"appidacr": "0"` |
| `acr` |驗證內容類別參考 |指出主體的驗證方式 (相對於應用程式驗證內容類別參考宣告中的用戶端)。 值為 "0" 表示使用者驗證不符合 ISO/IEC 29115 的需求。 <br><br> **範例 JWT 值**： <br> `"acr": "0"` |
| 驗證即刻 |記錄驗證發生的日期和時間。 <br><br> **範例 SAML 值**： <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |驗證方法 |識別如何驗證權杖的主體。 <br><br> **範例 SAML 值**： <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **範例 JWT 值**：`“amr”: ["pwd"]` |
| `given_name` |名字 |提供使用者的名字 (如 Azure AD 使用者物件上所設定)。 <br><br> **範例 SAML 值**： <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"given_name": "Frank"` |
| `groups` |群組 |提供代表主體群組成員資格的物件識別碼。 這些值都是唯一的 (請參閱「物件識別碼」)，而且可安全地用來管理存取權，例如強制授權以存取資源。 群組宣告中包含的群組會透過應用程式資訊清單的 "groupMembershipClaims"屬性，針對每個應用程式進行設定。 Null 值將會排除所有群組，"SecurityGroup" 值只會包含 Active Directory 安全性群組成員資格，而 "All" 值將會包含安全性群組和 Office 365 通訊群組清單。 <br><br> **範例 SAML 值**： <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **範例 JWT 值**： <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |識別提供者 |記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶位於與簽發者不同的租用戶中，否則這個值與 Issuer 宣告值相同。 <br><br> **範例 SAML 值**： <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |儲存權杖簽發的時間。 這通常用來測量權杖有效時間。 <br><br> **範例 SAML 值**： <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **範例 JWT 值**： <br> `"iat": 1390234181` |
| `iss` |簽發者 |識別負責建構並傳回權杖的 Security Token Service (STS)。 在 Azure AD 傳回的權杖中，簽發者為 sts.windows.net。 簽發者宣告值中的 GUID 是 Azure AD 目錄的租用戶識別碼。 租用戶識別碼是目錄的不可變且可靠的識別碼。 <br><br> **範例 SAML 值**： <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **範例 JWT 值**： <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |姓氏 |提供使用者的姓氏 (如 Azure AD 使用者物件中所定義)。 <br><br> **範例 SAML 值**： <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"family_name": "Miller"` |
| `unique_name` |名稱 |提供人類看得懂的值，用以識別權杖的主體。 此值不保證是租用戶中的唯一值，而且僅限用於顯示目的。 <br><br> **範例 SAML 值**： <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |物件識別碼 |包含 Azure AD 中物件的唯一識別碼。 這個值不可變，而且無法重新指派或重複使用。 使用物件識別碼來識別 Azure AD 查詢中的物件。 <br><br> **範例 SAML 值**： <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |角色 |表示透過群組成員資格，直接或間接授與主體的所有應用程式角色，而且可用來強制執行角色型存取控制。 應用程式角色是透過應用程式資訊清單的 `appRoles` 屬性，針對每個應用程式進行定義。 每個應用程式角色的 `value` 屬性就是出現在角色宣告中的值。 <br><br> **範例 SAML 值**： <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **範例 JWT 值**： <br> `“roles”: ["Admin", … ]` |
| `scp` |Scope |識別授與用戶端應用程式的模擬權限。 預設權限為 `user_impersonation`。 受保護資源的擁有者可以在 Azure AD 中註冊其他的值。 <br><br> **範例 JWT 值**： <br> `"scp": "user_impersonation"` |
| `sub` |主旨 |識別權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用，因此可用來安全地執行授權檢查。 因為主體一律存在於 Azure AD 簽發的權杖中，所以建議您將此值使用於一般用途授權系統中。 <br> `SubjectConfirmation` 不是宣告。 它會描述如何驗證權杖的主體。 `Bearer` 表示主體已經由持有權杖來確認。 <br><br> **範例 SAML 值**： <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **範例 JWT 值**： <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |租用戶識別碼 |不可變、無法重複使用的識別碼，用以識別簽發權杖的目錄租用戶。 您可以使用此值來存取多租用戶應用程式中的租用戶特定目錄資源。 例如，您可以使用此值來識別在圖形 API 呼叫中的租用戶。 <br><br> **範例 SAML 值**： <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **範例 JWT 值**： <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`、`exp` |權杖存留期 |定義權杖有效的時間間隔。 驗證權杖的服務應確認目前的日期在權杖存留期內，否則應該拒絕此權杖。 此服務最多允許超過權杖存留期範圍五分鐘，以考量 Azure AD 與服務之間的時鐘時間差異 (「時間偏差」)。 <br><br> **範例 SAML 值**： <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **範例 JWT 值**： <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |使用者主體名稱 |儲存使用者主體的使用者名稱。<br><br> **範例 JWT 值**： <br> `"upn": frankm@contoso.com` |
| `ver` |版本 |儲存權杖的版本號碼。 <br><br> **範例 JWT 值**： <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>存取權杖
一旦驗證成功，Azure AD 會傳回存取權杖，用來存取受保護的資源。 存取權杖是以 base 64 編碼的 JSON Web 權杖 (JWT)，而且可以透過解碼器執行它來檢查其內容。

如果您的應用程式僅「使用」存取權杖來存取 API，您可以 (應該) 將存取權杖視為完全不透明 - 這些都是您的應用程式可以在 HTTP 要求中傳遞給資源的字串。

當您要求存取權杖時，Azure AD 也會傳回一些有關存取權杖的中繼資料，以供您的應用程式取用。  此資訊包括存取權杖的到期時間以及其有效範圍。  這可讓您的應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。

如果您的應用程式是使用 Azure AD 保護的 API 且在 HTTP 要求中需要存取權杖，則您應該執行所收到權杖的驗證和檢查。 您的應用程式應該先驗證存取權杖，然後再用它來存取資源。 如需有關驗證的詳細資訊，請參閱[驗證權杖](#validating-tokens)。  
如需有關如何使用 .NET 執行此作業的詳細資訊，請參閱[使用 Azure AD 的持有者權杖來保護 Web API](active-directory-devquickstarts-webapi-dotnet.md)。

## <a name="refresh-tokens"></a>重新整理權杖

重新整理權杖是安全性權杖，可供您的應用程式用來在 OAuth 2.0 流程中取得新存取權杖。  它可讓您的應用程式代表使用者長期存取資源，而不需使用者互動。

重新整理權杖屬於多資源權杖。  也就是說，在一個資源的權杖要求期間收到的重新整理權杖可以兌換完全不同資源的存取權杖。 若要這樣做，請在目標資源的要求中設定 `resource` 參數。

重新整理權杖對您的應用程式完全不透明。 它們屬於長效權杖，但不得將您的應用程式撰寫成預期重新整理權杖將持續任何一段時間。  重新整理權杖可能會因為各種原因而隨時失效。  讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是對 Azure AD 權杖端點提出權杖要求以嘗試兌換。

當您兌換重新整理權杖做為新的存取權杖時，您會在權杖回應中收到新的重新整理權杖。  您應儲存新簽發的重新整理權杖，並取代您使用於要求中的重新整理權杖。  這將保證您的重新整理權杖盡可能長期保持有效。

## <a name="validating-tokens"></a>驗證權杖

若要驗證 id_token 或 access_token，您的應用程式應該驗證權杖的簽章和宣告。 為了驗證存取權杖，您的應用程式也應該驗證簽發者、受眾及簽署權杖。 這些都需要對 OpenID 探索文件中的值進行驗證。 例如，租用戶獨立版的文件，位於 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)。 Azure AD 中介軟體已內建驗證存取權杖的功能，您可以瀏覽我們的[範例](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-code-samples)，以找到您所選擇語言的範例。 如需如何明確地驗證 JWT 權杖的詳細資訊，請參閱[手動 JWT 驗證範例 (manual JWT validation sample)](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)。  

我們提供的程式庫和程式碼範例會示範如何輕鬆地處理權杖驗證 - 想要了解基礎程序的使用者可以參閱以下資訊。  另外還有多個協力廠商開放原始碼程式庫可用於 JWT 驗證 - 幾乎每個平台和語言都有至少一個選項。 如需有關 Azure AD 驗證程式庫和程式碼範例的詳細資訊，請參閱 [Azure AD 驗證程式庫](active-directory-authentication-libraries.md)。

#### <a name="validating-the-signature"></a>驗證簽章

JWT 包含三個區段 (以 `.` 字元分隔)。  第一個區段稱為**標頭**、第二個稱為**主體**，而第三個稱為**簽章**。  簽章區段可用來驗證權杖的真實性，您的應用程式才得以信任。

Azure AD 所簽發的權杖是使用業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。 JWT 標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg` 宣告表示用來簽署權杖的演算法，而 `x5t` 宣告則表示用來簽署權杖的特定公開金鑰。

在任何指定的時間點，Azure AD 可能會使用一組特定公開-私密金鑰組的其中一個金鑰組來簽署 id_token。 Azure AD 會定期替換一組可能的金鑰，所以應將您的應用程式撰寫成自動處理這些金鑰變更。  檢查 Azure AD 所用公開金鑰的更新的合理頻率為每 24 小時。

您可以使用位於下列位置的 OpenID Connect 中繼資料文件來取得驗證簽章所需的簽署金鑰資料：

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> 在瀏覽器中嘗試此 URL！
> 
> 

此中繼資料文件是 JSON 物件，內含幾項實用的資訊，例如執行 OpenID Connect 驗證所需的各種端點的位置。  

此外，還包含 `jwks_uri`，其提供用來簽署權杖的公用金鑰組的位置。  位於 `jwks_uri` 的 JSON 文件包含在該特定時間點使用的所有公開金鑰資訊。  您的應用程式可以使用 JWT 標頭中的 `kid` 宣告選取本文件中已用來簽署特定權杖的公開金鑰。  接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

執行簽章驗證已超出本文件的範圍 - 有許多開放原始碼程式庫可協助您這麼做 (如有必要)。

#### <a name="validating-the-claims"></a>驗證宣告

如果您的應用程式收到權杖 (在使用者登入時收到 id_token，或在 HTTP 要求中收到存取權杖做為持有人權杖)，則也應該對權杖中的宣告執行一些檢查。  包含但不限於：

* **對象**宣告 - 確認權杖預定要提供給您的應用程式。
* **生效時間**和**到期時間**宣告 - 確認權杖尚未過期。
* **簽發者** 宣告 - 確認權杖確實是由 Azure AD 簽發給您的應用程式。
* **Nonce** -降低權杖重新執行攻擊。
* 不勝枚舉...

如需您的應用程式應對 ID 權杖執行的完整宣告驗證清單，請參閱 [OpenID Connect 規格](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)。 先前的 [id_token 一節](#id-tokens)包含這些宣告的預期值的詳細資料。

## <a name="sample-tokens"></a>權杖範例

本節顯示 Azure AD 所傳回的 SAML 和 JWT 權杖範例。 這些範例可讓您查看內容中的宣告。

### <a name="saml-token"></a>SAML 權杖

這是典型的 SAML 權杖範例。

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT 權杖 - 使用者模擬
這是在授權碼授與流程中使用的典型 JSON Web 權杖 (JWT) 範例。
除了宣告以外，權杖包含 **ver** 和 **appidacr** 中的版本號碼、驗證內容類別參考 (這表示用戶端的驗證方式)。 若為公用用戶端，此值為 0。 如果使用用戶端識別碼或用戶端密碼，此值為 1。

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>相關內容
* 若要深入了解透過 Azure AD Graph API 管理權杖存留期原則，請參閱 Azure AD Graph [原則作業](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)和[原則實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)。
* 如需透過 PowerShell Cmdlet 管理原則的詳細資訊和範例，包括範例，請參閱[在 Azure AD 中設定權杖存留期](../active-directory-configurable-token-lifetimes.md)。 
