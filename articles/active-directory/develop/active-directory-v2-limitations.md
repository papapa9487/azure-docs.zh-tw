---
title: "Azure Active Directory v2.0 端點限制 | Microsoft Docs"
description: "Azure AD v2.0 端點的限制清單。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5a9d455203e50da47208ef1494d38a950161bee1
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>我應該使用 v2.0 端點嗎？
當您建立與 Azure Active Directory 整合的應用程式時，您必須判斷 v2.0 端點和驗證通訊協定是否符合您的需求。 Azure Active Directory 的原始端點仍受到完整的支援，而且在某些方面擁有比 v2.0 更豐富的功能。 不過，v2.0 端點為開發人員[帶來極大的好處](active-directory-v2-compare.md)。

以下是我們在這個時間點針對開發人員所提供的簡化建議：

* 如果您必須在應用程式中支援個人 Microsoft 帳戶，請使用 v2.0 端點。 但在這樣做之前，請務必了解我們在本文中討論的限制。
* 如果您的應用程式只需要支援 Microsoft 公司和學校帳戶，請不要使用 v2.0 端點。 請改為參考我們的 [Azure AD 開發人員指南](active-directory-developers-guide.md)。

隨著時間經過，v2.0 端點會成長到排除此處所列的限制，屆時您只需要使用 v2.0 端點即可。 在此同時，本文旨在協助您判斷 v2.0 端點是否適合您。 我們將持續更新本文，以反映 v2.0 端點目前的狀態。 請記得回來重新評估您對 v2.0 功能的需求。

如果您有一個未使用 v2.0 端點的現有 Azure AD 應用程式，就不需要從頭開始。 未來，我們會為您提供可將現有 Azure AD 應用程式與 v2.0 端點搭配使用的方法。

## <a name="restrictions-on-app-types"></a>應用程式類型的限制
v2.0 端點目前不支援下列類型的應用程式。 如需所支援應用程式類型的描述，請參閱 [Azure Active Directory v2.0 端點的應用程式類型](active-directory-v2-flows.md)。

### <a name="standalone-web-apis"></a>獨立的 Web API
您可以使用 v2.0 端點來[建置使用 OAuth 2.0 保護的 Web API](active-directory-v2-flows.md#web-apis)。 不過，該 Web API 只能從具有相同「應用程式識別碼」的應用程式接收權杖。 您無法從具有不同「應用程式識別碼」的用戶端存取 Web API。 該用戶端無法要求或取得您 Web API 的權限。

若要了解如何建置 Web API 以接受來自具有相同「應用程式識別碼」之用戶端的權杖，請參閱我們[開始使用](active-directory-appmodel-v2-overview.md#getting-started)一節中的 v2.0 端點 Web API 範例。

## <a name="restrictions-on-app-registrations"></a>應用程式註冊的限制
目前，針對您想要與 v2.0 端點整合的每個應用程式，您都必須在新的 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中建立應用程式註冊。 現有的 Azure AD 或 Microsoft 帳戶應用程式與 v2.0 端點不相容。 在「應用程式註冊入口網站」以外的任何入口網站註冊的應用程式也與 v2.0 端點不相容。 未來，我們打算提供使用現有應用程式作為 v2.0 應用程式的方法。 但目前還沒有可讓現有應用程式與 v2.0 端點搭配運作的移轉途徑。

此外，您在[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中建立的應用程式註冊還需注意下列幾點︰

* 每個「應用程式識別碼」只允許兩個應用程式密碼。
* 由使用個人 Microsoft 帳戶之使用者所註冊的應用程式註冊，只能透過單一開發人員帳戶來檢視及管理。 不能在多位開發人員之間共用。  如果您要和多位開發人員共用您的應用程式註冊，可以使用 Azure AD 帳戶登入註冊入口網站來建立應用程式。
* 所允許的重新導向 URI 的格式有幾項限制。 如需有關重新導向 URI 的詳細資訊，請參閱下一節。

## <a name="restrictions-on-redirect-uris"></a>重新導向 URI 的限制
針對已在「應用程式註冊入口網站」中註冊的應用程式，目前僅限使用一組受限的重新導向 URI 值。 Web 應用程式和服務的重新導向 URI 必須以配置 `https` 開頭，而且所有重新導向 URI 值必須共用單一 DNS 網域。 例如，您不能註冊具有下列其中一個重新導向 URI 的 Web 應用程式：

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

註冊系統會將現有重新導向 URI 的整個 DNS 名稱與您要新增之重新導向 URI 的 DNS 名稱做比較。 如果下列任一條件成立，新增 DNS 名稱的要求就會失敗︰  

* 新重新導向 URI 的整個 DNS 名稱與現有重新導向 URI 的 DNS 名稱不相符。
* 新重新導向 URI 的整個 DNS 名稱不是現有重新導向 URI 的子網域。

例如，如果應用程式具有下列重新導向 URI：

`https://login.contoso.com`

您可以在其中新增，就像這樣：

`https://login.contoso.com/new`

在此情況下，DNS 名稱會完全相符。 或者，您可以這樣做：

`https://new.login.contoso.com`

在此情況下，您參照的是 login.contoso.com 的 DNS 子網域。如果您希望應用程式以 login-east.contoso.com 和 login-west.contoso.com 作為重新導向 URI，您必須依下列序新增這些重新導向 URI︰

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

您可以新增後面兩個，因為它們是第一個重新導向 URI (contoso.com) 的子網域。即將推出的版本將會移除這項限制。

也請注意，一個特定應用程式只可以有 20 個回覆 URL。

若要了解如何在「應用程式註冊入口網站」中註冊應用程式，請參閱[如何使用 v2.0 端點註冊 App](active-directory-v2-app-registration.md)。

## <a name="restrictions-on-services-and-apis"></a>服務和 API 的限制
目前 v2.0 端點支援已在「應用程式註冊入口網站」中註冊及在[支援的驗證流程](active-directory-v2-flows.md)清單範圍內之所有應用程式的登入。 不過，這些應用程式只能取得非常有限的一組資源的 OAuth 2.0 存取權杖。 v2.0 端點只會針對下列項目簽發存取權杖：

* 要求權杖的應用程式。 如果邏輯應用程式是由數個不同的元件或層級組成，應用程式就可以為自己取得存取權杖。 若要查看此案例的執行狀況，請參閱 [使用者入門](active-directory-appmodel-v2-overview.md#getting-started) 教學課程。
* Outlook 郵件、行事曆和連絡人 REST API 全都位於 https://outlook.office.com。若要了解如何撰寫存取這些 API 的應用程式，請參閱[開始使用 Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 教學課程。
* Microsoft Graph API。 您可以深入了解 [Microsoft Graph](https://graph.microsoft.io)和可供您使用的資料。

目前不支援其他服務。 除了支援您自己的自訂建置 Web API 和服務之外，未來還將增加更多 Microsoft Online Services。

## <a name="restrictions-on-libraries-and-sdks"></a>程式庫和 SDK 的限制
目前對 v2.0 端點提供的程式庫支援相當有限。 如果您想要在實際執行應用程式中使用 v2.0 端點，您有下列選項：

* 如果您要建置 Web 應用程式，您可以安心地使用 Microsoft 正式運作的伺服器端中介軟體來執行登入和權杖驗證。 這些包括了適用於 ASP.NET 的 OWIN Open ID Connect 中介軟體和 Node.js Passport 外掛程式。 如需使用 Microsoft 中介軟體的程式碼範例，請參閱我們的[開始使用](active-directory-appmodel-v2-overview.md#getting-started)一節。
* 如果您正在建置傳統型或行動應用程式，可以使用我們的其中一個預覽 Microsoft Authentication Library (MSAL)。  這些程式庫處於支援生產環境的預覽階段，因此可以在生產應用程式中安全地使用。 您可以在我們的[驗證程式庫參考](active-directory-v2-libraries.md)中，閱讀更多和預覽條款與可用程式庫相關的詳細資訊。
* 針對 Microsoft 程式庫未包含的其他平台，您可以透過直接在應用程式程式碼中傳送和接收通訊協定訊息，來與 v2.0 端點整合。 v2.0 OpenID Connect 和 OAuth 通訊協定[有明確的文件記載](active-directory-v2-protocols.md)，可協助您執行這種整合。
* 最後，您可以使用開放原始碼 OpenID Connect 和 OAuth 程式庫來與 v2.0 端點整合。 v2.0 通訊協定應該不需進行重大變更，即可與許多開放原始碼通訊協定程式庫相容。 這類的程式庫的可用性會依語言和平台而有所不同。 [Open ID Connect](http://openid.net/connect/) 和 [OAuth 2.0](http://oauth.net/2/) 網站保有一份常用的實作清單。 如需詳細資訊，以及已用 v2.0 端點測試的開放原始碼用戶端程式庫和範例的清單，請參閱 [Azure Active Directory v2.0 和驗證程式庫](active-directory-v2-libraries.md)。

## <a name="restrictions-on-protocols"></a>通訊協定的限制
v2.0 端點不支援 SAML 或 WS-同盟，只支援 Open ID Connect 和 OAuth 2.0。  v2.0 端點中並未納入 OAuth 通訊協定的所有功能與特性。 v2.0 端點中目前「並未提供」這些通訊協定功能與特性：

* 即使您向使用者取得檢視其電子郵件的權限，v2.0 端點所簽發的識別碼權杖也不會包含使用者的 `email` 宣告。
* v2.0 端點上未實作 OpenID Connect UserInfo 端點。 不過，所有您可能會在此端點收到的使用者設定檔資料都可從 Microsoft Graph `/me` 端點取得。
* v2.0 端點不支援在識別碼權杖中發出角色或群組宣告。
* v2.0 端點不支援 [OAuth 2.0 資源擁有者密碼認證授與 (英文)](https://tools.ietf.org/html/rfc6749#section-4.3)。

此外，v2.0 端點不支援任何形式的 SAML 或 WS-同盟通訊協定。

若要進一步了解 v2.0 端點中支援的通訊協定功能範圍，請參閱我們的 [OpenID Connect 與 OAuth 2.0 通訊協定參考](active-directory-v2-protocols.md)。

## <a name="restrictions-for-work-and-school-accounts"></a>工作和學校帳戶的限制
如果您已在 Windows 應用程式中使用 Active Directory Authentication Library (ADAL)，您可能已利用使用「安全性聲明標記語言」(SAML) 判斷提示授與的 Windows 整合式驗證。 有了此授與之後，同盟 Azure AD 租用戶的使用者便可向其內部部署 Active Directory 執行個體以無訊息方式進行驗證，而不需輸入認證。 目前 v2.0 端點不支援 SAML 判斷提示授與。