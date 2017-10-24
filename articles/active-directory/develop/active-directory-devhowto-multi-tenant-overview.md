---
title: "如何建置可讓任何 Azure AD 使用者登入的應用程式"
description: "如何建置可讓使用者從任何 Azure Active Directory 租用戶登入之應用程式 (也稱為多租用戶應用程式) 的逐步解說。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b409aa762b60c6bed0ee26f4b9fa7c347d9eb997
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>如何使用多租用戶應用程式模式登入任何 Azure Active Directory (AD) 使用者
如果您提供「軟體即服務」應用程式給許多公司，您可以將您的應用程式設定為可接受來自任何 Azure AD 租用戶的登入。  Azure AD 會呼叫此組態來讓您的應用程式可使用多租用戶。  任何 Azure AD 租用戶中的使用者在同意搭配您的應用程式使用其帳戶之後，便可登入您的應用程式。  

如果您的現有應用程式有其自己的帳戶系統，或支援從其他雲端提供者執行其他登入方式，則新增從任何租用戶執行 Azure AD 登入很簡單。 只要註冊您的應用程式、透過 OAuth2、OpenID Connect 或 SAML 新增登入程式碼，然後將 [使用 Microsoft 登入] 按鈕放在您的應用程式上即可。 按下面的按鈕來深入了解如何將應用程式加上商標。

[![登入按鈕][AAD-Sign-In]][AAD-App-Branding]

本文假設您已經熟悉如何為 Azure AD 建置單一租用戶應用程式。  如果並非如此，請返回[開發人員指南首頁][AAD-Dev-Guide]，然後試試其中一個快速入門！

將您的應用程式轉換成 Azure AD 多租用戶應用程式包含四個簡單的步驟︰

1. 將您的應用程式註冊更新成多租用戶
2. 將您的程式碼更新成將要求傳送給 /common 端點 
3. 將您的程式碼更新成可處理多個簽發者值
4. 了解使用者和系統管理員的同意意向並進行適當的程式碼變更

讓我們仔細看看每個步驟。 您也可以直接跳到[這份多租用戶範例清單][AAD-Samples-MT]。

## <a name="update-registration-to-be-multi-tenant"></a>將註冊更新成多租用戶
Azure AD 中的 Web 應用程式/API 註冊預設是單一租用戶。  您只要在 [Azure 入口網站][AZURE-portal]中的應用程式註冊內容頁面上，找出 [多租用戶] 切換開關並將它設定為 [是]，即可將您的註冊轉換成多租用戶。

此外請注意，Azure AD 會要求應用程式的「應用程式識別碼 URI」必須具全域唯一性，才能被設定成多租用戶應用程式。 「應用程式識別碼 URI」是其中一種可在通訊協定訊息中識別應用程式的方式。  在單一租用戶應用程式中，只要該租用戶內有唯一的應用程式識別碼 URI 就已足夠。  就多租用戶應用程式而言，該 URI 則必須具全域唯一性，Azure AD 才能在所有租用戶中找到該應用程式。  系統會透過要求「應用程式識別碼 URI」必須具有與已驗證的 Azure AD 租用戶網域相符的主機名稱，來強制執行全域唯一性。  例如，如果租用戶的名稱是 contoso.onmicrosoft.com，則有效的「應用程式識別碼 URI」會是 `https://contoso.onmicrosoft.com/myapp`。  如果租用戶具有已驗證的網域 `contoso.com`，則有效的「應用程式識別碼 URI」也會是 `https://contoso.com/myapp`。  如果「應用程式識別碼 URI」沒有按照這個模式，將應用程式設定成多租用戶時就會失敗。

原生用戶端註冊預設即為多租用戶。  您不需要採取任何動作來將原生用戶端應用程式註冊轉換成多租用戶。

## <a name="update-your-code-to-send-requests-to-common"></a>將您的程式碼更新成將要求傳送給 /common
在單一租用戶應用程式中，登入要求會傳送至租用戶的登入端點。 例如，以 contoso.onmicrosoft.com 來說，端點會是：

    https://login.microsoftonline.com/contoso.onmicrosoft.com

傳送給租用戶端點的要求可以讓該租用戶中的使用者 (或來賓) 登入該租用戶中的應用程式。  使用多租用戶應用程式時，應用程式事先並不知道使用者來自哪個租用戶，因此您無法將要求傳送給租用戶的端點。  取而代之的是，會將要求傳送給在所有 Azure AD 租用戶進行多工的端點：

    https://login.microsoftonline.com/common

當 Azure AD 在 /common 端點收到要求時，它會讓使用者登入，藉此探索使用者來自哪個租用戶。  /common 端點可以與 Azure AD 支援的所有驗證通訊協定搭配使用：OpenID Connect、OAuth 2.0、SAML 2.0 及「WS-同盟」。

然後，傳給應用程式的登入回應會包含代表使用者的權杖。  權杖中的簽發者值會告知應用程式該使用者來自哪個租用戶。  從 /common 端點傳回回應時，權杖中的簽發者值會與使用者的租用戶對應。  請務必注意，/common 端點不是租用戶，也不是簽發者，而只是一個多工器。  使用 /common 時，必須更新您應用程式中用來驗證權杖的邏輯，以便將這一點納入考量。 

如先前所述，多租用戶應用程式也應該為使用者提供一致的登入體驗，以遵循 Azure AD 應用程式的商標指導方針。 按下面的按鈕來深入了解如何將應用程式加上商標。

[![登入按鈕][AAD-Sign-In]][AAD-App-Branding]

讓我們深入地看一下 /common 端點的使用和您的程式碼實作。

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>將您的程式碼更新成可處理多個簽發者值
Web 應用程式和 Web API 會接收並驗證來自 Azure AD 的權杖。  

> [!NOTE]
> 雖然原生用戶端應用程式會要求並接收來自 Azure AD 的權杖，但它們這麼做是為了將權杖傳送給 API 來進行驗證。  原生應用程式不會驗證權杖，而且必須將它們視為不透明。
> 
> 

讓我們看看應用程式如何驗證它從 Azure AD 接收的權杖。  單一租用戶應用程式通常會採用類似以下的端點值：

    https://login.microsoftonline.com/contoso.onmicrosoft.com

然後使用它來建構中繼資料 URL (在此例中為 OpenID Connect)，例如︰

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

以下載用來驗證權杖的兩項關鍵資訊︰租用戶的簽署金鑰和簽發者值。  每個 Azure AD 租用戶都有具有採用下列格式的唯一簽發者值︰

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

其中的 GUID 值是租用戶的租用戶識別碼重新命名安全版本。  如果您按一下上方的 `contoso.onmicrosoft.com` 中繼資料連結，您可以在文件中看到此簽發者值。

當單一租用戶應用程式驗證權杖時，它會根據中繼資料文件中的簽署金鑰來檢查權杖的簽章。 這項檢查可讓它確定權杖中的簽發者值符合中繼資料文件中找到的值。

因為 /common 端點既不對應租用戶也不是簽發者，所以當您檢查 /common 之中繼資料中的簽發者值時，它擁有的是一個樣板化的 URL 而不是實際值︰

    https://sts.windows.net/{tenantid}/

因此，多租用戶應用程式無法僅透過將中繼資料中的簽發者值與權杖中的 `issuer` 值做比對來驗證權杖。  多租用戶應用程式需要有可根據簽發者值的租用戶識別碼部分來決定哪些簽發者值有效、哪些簽發者值無效的邏輯。  

例如，如果多租用戶應用程式只允許從已註冊服務的特定租用戶登入，它就必須檢查權杖中的簽發者值或 `tid` 宣告值，以確認該租用戶在其訂閱者清單中。  如果多租用戶應用程式只處理個人而不根據租用戶做出任何存取決策，則它可以完全忽略簽發者值。

在可於本文結尾的[相關內容](#related-content)一節中的多租用戶範例中，為了讓任何 Azure AD 租用戶都能登入，已將簽發者驗證停用。

現在，讓我們看看登入多租用戶應用程式之使用者的使用者體驗。

## <a name="understanding-user-and-admin-consent"></a>了解使用者和系統管理員的同意意向
若要讓使用者登入 Azuer AD 中的應用程式，必須以使用者的租用戶代表該應用程式。  這可讓組織執行一些操作，例如在來自其租用戶的使用者登入應用程式時套用唯一原則。  就單一租用戶應用程式而言，這個註冊程序相當簡單；就是您在 [Azure 入口網站][AZURE-portal]中註冊應用程式時所進行的程序。

就多租用戶應用程式而言，應用程式的初始註冊程序則是在開發人員所使用的 Azure AD 租用戶中進行。  當來自不同租用戶的使用者第一次登入應用程式時，Azure AD 會要求他們同意應用程式所要求的權限。  如果他們同意，系統就會在使用者的租用戶中建立一個稱為「服務主體」的應用程式代表，然後登入便可繼續進行。 系統也會在記錄使用者對應用程式之同意意向的目錄中建立委派。 如需有關應用程式之「應用程式」和「服務主體」物件的詳細資訊，請參閱[應用程式物件和服務主體物件][AAD-App-SP-Objects]。

![同意單層式應用程式][Consent-Single-Tier] 

這個同意體驗會受到應用程式所要求的權限影響。  Azure AD 支援兩種類型的權限，即僅限應用程式的權限和委派的權限︰

* 委派的權限可讓應用程式能夠充當登入的使用者來執行該使用者所能執行的一部分操作。  例如，您可以授與應用程式委派的權限來讀取登入之使用者的行事曆。
* 僅限應用程式的權限會直接授與應用程式的識別身分。  例如，您可以將僅限應用程式的權限授與應用程式來讀取租用戶中的使用者清單，而且不論是誰登入此應用程式。

有些權限可以由一般使用者同意，有些則需要租用戶系統管理員的同意。 

### <a name="admin-consent"></a>系統管理員同意
僅限應用程式的權限一律需要租用戶系統管理員的同意。  如果您的應用程式要求僅限應用程式的權限，當使用者嘗試登入應用程式時，將會出現錯誤訊息，指出該使用者無法同意。

有些委派的權限也需要租用戶系統管理員的同意。  例如，若要能夠以登入的使用者身分寫回 Azure AD，就需要租用戶系統管理員的同意。  與僅限應用程式的權限一樣，如果一般使用者嘗試登入要求委派權限的應用程式，而該權限需要系統管理員同意，您的應用程式將會收到錯誤。  權限是否需要系統管理員同意是由發佈資源的開發人員決定，而且可以在該資源的文件中找到這項資訊。  如需說明 Azure AD Graph API 和 Microsoft Graph API 可用權限的主題連結，請參閱本文的 [相關內容](#related-content) 一節。

如果您的應用程式使用需要系統管理員同意的權限，您就必須要有相關的表示，例如可供系統管理員起始動作的按鈕或連結。  您的應用程式針對此動作傳送的要求是一個一般的 OAuth2/OpenID Connect 授權要求，但此要求同時也包含 `prompt=admin_consent` 查詢字串參數。  在系統管理員同意且系統已在客戶的租用戶中建立服務主體之後，後續的登入要求就不再需要 `prompt=admin_consent` 參數。 由於系統管理員已決定可接受要求的權限，因此從該時間點之後，就不會再提示租用戶中的任何其他使用者行使同意權。

如果應用程式要求的權限不需要系統管理員同意，則應用程式也可以使用 `prompt=admin_consent` 參數。 這是在應用程式需要一種體驗的情況下完成，也就是租用戶系統管理員「註冊」一次，此後就不會再提示其他使用者要表示同意。

如果應用程式需要系統管理員同意，當系統管理員登入，但未傳送 `prompt=admin_consent` 參數時，系統管理員**只針對其使用者帳戶**才會順利同意此應用程式。  一般使用者仍然無法登入此應用程式並對其行使同意權。  當您想要先讓租用戶系統管理員能夠瀏覽您的應用程式，然後才允許其他使用者存取時，這個功能相當有用。

租用戶系統管理員可以停用一般使用者對應用程式行使同意權的能力。  如果停用這項功能，就一律需要系統管理員同意，才能在租用戶中設定應用程式。  如果您想要在停用一般使用者同意的情況下測試您的應用程式，您可以在 [Azure 入口網站][AZURE-portal]的 Azure AD 租用戶設定區段中找到設定參數。

> [!NOTE]
> 有些應用程式想要提供一種體驗，讓一般使用者能夠一開始即表示同意，之後應用程式即可讓系統管理員參與操作並要求需要系統管理員同意的權限。  目前在 Azure AD 中還沒有任何方法可以使用單一應用程式註冊來達到這個目的。  即將推出的 Azure AD Resource Manager 部署模型端點可讓應用程式在執行階段 (而不是在註冊階段) 要求權限，從而實現此情況。  如需詳細資訊，請參閱 [Azure AD 應用程式模型 Resource Manager 部署模型開發人員指南][AAD-V2-Dev-Guide]。
> 
> 

### <a name="consent-and-multi-tier-applications"></a>同意和多層應用程式
您的應用程式可能會有多層，每一層皆由它自己在 Azure AD 中的註冊代表。  例如，一個呼叫 Web API 的原生應用程式，或是一個呼叫 Web API 的 Web 應用程式。  在這兩種情況下，用戶端 (原生應用程式或 Web 應用程式) 都會要求可呼叫資源 (Web API) 的權限。  若要讓用戶端順利獲得客戶同意新增到其租用戶中，則它要求權限的所有資源必須都已經存在於客戶的租用戶中。  如果不符合此條件，Azuer AD 會傳回錯誤，指出必須先新增資源。

**單一租用戶中的多層**

如果您的邏輯應用程式包含兩個或更多個應用程式註冊 (例如個別的用戶端和資源)，這可能會造成問題。  如何先將資源新增到客戶租用戶中？  Azure AD 涵蓋此情況，支援在單一步驟中同意用戶端和資源。 使用者在同意頁面上會看到用戶端和資源所要求的權限總和。  若要啟用此行為，在資源的應用程式資訊清單中，資源的應用程式註冊就必須以 `knownClientApplications` 的形式包含用戶端的「應用程式識別碼」。  例如：

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

您可以透過資源[應用程式的資訊清單][AAD-App-Manifest]更新此屬性。 在本文結尾的[相關內容](#related-content)一節中，一個呼叫 Web API 的多層原生用戶端範例中會示範作法。 下圖提供同意單一租用戶中註冊之多層應用程式的概觀︰

![同意多層式已知用戶端應用程式][Consent-Multi-Tier-Known-Client] 

**多個租用戶中的多層**

如果在不同的租用戶中註冊不同的應用程式層，將會發生類似的情況。  例如，想像建置一個會呼叫 Office 365 Exchange Online API 的原生用戶端應用程式的情況。  若要開發此原生應用程式，然後讓此原生應用程式在客戶的租用戶中執行，就必須要有 Exchange Online 服務主體。  在此情況下，開發人員和客戶必須購買 Exchange Online，如此才能在其租用戶中建立服務主體。  

如果是 Microsoft 以外的組織所建置的 API，API 的開發人員必須提供方法讓客戶同意將應用程式新增至其客戶的租用戶。 建議的設計是第三方開發人員將 API 建置成也可當作 Web 用戶端來實作註冊︰

1. 遵循先前的章節，以確保 API 實作多租用戶應用程式註冊/程式碼的需求
2. 除了公開 API 的範圍/角色，請確定註冊包含「登入和讀取使用者設定檔」Azure AD 權限 (依預設會提供)
3. 遵循稍早討論的[系統管理員同意](#admin-consent)指引，在 Web 用戶端實作登入/註冊頁面 
4. 當使用者同意應用程式後，就會在其租用戶中會建立服務主體和同意委派連結，而原生應用程式可以取得 API 的權杖

下圖提供同意不同租用戶中註冊之多層應用程式的概觀︰

![同意多層式多方應用程式][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>撤銷同意
使用者和系統管理員可以隨時撤銷對您應用程式的同意：

* 使用者可藉由將個別應用程式從其[存取面板應用程式][AAD-Access-Panel]清單中移除，來撤銷對這些應用程式的存取權。
* 系統管理員可藉由使用 [Azure 入口網站][AZURE-portal]的 Azure AD 管理區段，將應用程式從 Azure AD 中移除，來撤銷對這些應用程式的存取權。

如果是由系統管理員代表租用戶中的所有使用者對應用程式行使同意權，使用者就不能個別撤銷存取權。  只有系統管理員才能撤銷存取權，並且只能針對整個應用程式撤銷。

### <a name="consent-and-protocol-support"></a>同意和通訊協定支援
在 Azure AD 中，是透過 OAuth、OpenID Connect、「WS-同盟」及 SAML 通訊協定來支援同意。  由於 SAML 和「WS-同盟」通訊協定並不支援 `prompt=admin_consent` 參數，因此系統管理員只能透過 OAuth 和 OpenID Connect 行使同意權。

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>多租用戶應用程式和快取存取權杖
多租用戶應用程式也可以取得存取權杖來呼叫受 Azure AD 保護的 API。  搭配多租用戶應用程式使用 Active Directory Authentication Library (ADAL) 時有一個常見的錯誤，就是一開始即使用 /common 來為使用者要求權杖、接收回應，然後也使用 /common 來為該相同使用者要求後續的權杖。  由於從 Azure AD 傳回的回應是來自租用戶而非 /common，因此 ADAL 快取權杖時會將它視為來自租用戶。 後續為了為使用者取得存取權杖而進行的 /common 呼叫會遺漏快取項目，因此系統會再次提示使用者登入。  為了避免遺漏快取，請確定後續為已登入之使用者進行的呼叫是對租用戶的端點發出。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何建置可讓使用者從任何 Azure Active Directory 租用戶登入的應用程式。 在啟用您應用程式與 Azure Active Directory 之間的單一登入功能之後，您也可以將應用程式更新成存取 Microsoft 資源 (例如 Office 365) 所公開的 API。 因此，您可以在應用程式中提供個人化的體驗，例如向使用者顯示內容資訊，像是他們的個人資料圖片或下一個行事曆約會。 若要深入了解如何對 Azure Active Directory 和 Office 365 服務 (例如 Exchange、SharePoint、OneDrive、OneNote、Planner、Excel 等) 進行 API 呼叫，請瀏覽：[Microsoft Graph API][MSFT-Graph-overview]。


## <a name="related-content"></a>相關內容
* [多租用戶應用程式範例][AAD-Samples-MT]
* [應用程式的商標指導方針][AAD-App-Branding]
* [Azure AD 開發人員指南][AAD-Dev-Guide]
* [應用程式物件和服務主體物件][AAD-App-SP-Objects]
* [整合應用程式與 Azure Active Directory][AAD-Integrating-Apps]
* [同意架構的概觀][AAD-Consent-Overview]
* [Microsoft Graph API 權限範圍][MSFT-Graph-permision-scopes]
* [Azure AD Graph API 權限範圍][AAD-Graph-Perm-Scopes]

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














