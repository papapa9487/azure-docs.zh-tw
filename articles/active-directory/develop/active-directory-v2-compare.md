---
title: "Azure AD v2.0 端點中有哪些差異？ | Microsoft Docs"
description: "原始的 Azure AD 和 v2.0 端點之間的比較。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 81de65b0e825dec64383f52b02c5ee56c9434807
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="whats-different-about-the-v20-endpoint"></a>V2.0 端點有哪些差異？
如果您熟悉 Azure Active Directory 或過去已整合 app 與 Azure AD，v2.0 端點中可能會有一些您預期不到的差異。  本文件集結了這些差異，讓您得以瞭解。

> [!NOTE]
> v2.0 端點並非支援每個 Azure Active Directory 案例和功能。  若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft 帳戶和 Azure AD 帳戶
v2.0 端點讓開發人員能夠撰寫可接受使用單一驗證端點同時從 Microsoft 帳戶和 Azure AD 帳戶登入的 app。  這可讓您撰寫您的應用程式且帳戶完全無從驗證；可以忽略使用者登入時所用的帳戶類型。  當然，您 *可以* 讓您的應用程式知道特定工作階段中所使用的帳戶類型，但您不必這麼做。

例如，如果您的 app 呼叫 [Microsoft Graph](https://graph.microsoft.io)，則有一些額外的功能和資料可供企業使用者使用，例如他們的 SharePoint 網站或目錄資料。  但在許多動作 (例如 [讀取使用者的郵件](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message)) 中，針對 Microsoft 帳戶和 Azure AD 帳戶撰寫的程式碼可以完全相同。  

整合應用程式與 Microsoft 帳戶和 Azure AD 帳戶現在是一個簡單的程序。  您可以利用一組端點、單一資源庫和單一應用程式註冊來進入消費者和企業的世界。  若要深入了解 v2.0 端點，請參閱 [概觀](active-directory-appmodel-v2-overview.md)。

## <a name="new-app-registration-portal"></a>新的 App 註冊入口網站
若要註冊可與 v2.0 端點搭配使用的應用程式，您必須使用新的應用程式註冊入口網站：[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。您可以在這個入口網站取得應用程式識別碼、自訂應用程式登入頁面的外觀等等。  您存取入口網站時唯一需要的是 Microsoft 提供的帳戶 - 個人或工作/學校帳戶。

## <a name="one-app-id-for-all-platforms"></a>所有平台使用一個應用程式識別碼
如果您已經使用 Azure Active Directory，您可能已針對單一專案註冊數個不同的應用程式。  例如，如果您同時建置了網站和 iOS app，就必須使用兩個不同的應用程式識別碼分別註冊它們。 Azure AD 應用程式註冊入口網站會在註冊過程中強制您做此區分：

![舊版應用程式註冊 UI](../../media/active-directory-v2-flows/old_app_registration.PNG)

同樣地，如果您有網站和後端 Web API，您可能已在 Azure AD 中將它們註冊為個別的應用程式。  或者，如果您有 iOS 應用程式和 Android 應用程式，您也可能已註冊兩個不同的應用程式。  註冊應用程式的每個元件，會導致針對開發人員和其客戶出現某些非預期的行為：

* 每個元件會在每個客戶的 Azure Active Directory 租用戶中顯示為個別的應用程式。
* 當租用戶系統管理員嘗試套用原則至應用程式、管理對應用程式的存取，或刪除應用程式時，他們就必須針對應用程式的每個元件執行此操作。
* 當客戶同意使用應用程式時，每個元件會以個別應用程式的形式出現在同意畫面中。

您現在可以使用 v2.0 端點，以單一應用程式註冊的方式註冊您專案中的所有元件，並針對整個專案使用單一應用程式識別碼。  您可以將數個「平台」加入至每個專案，並且為您加入的每個平台提供適當的資料。  當然，您可以根據您的需求建立任意數量的應用程式，但在大多數的情況下應該只需要一個應用程式識別碼。

我們的目標是促成更簡化的應用程式管理及開發經驗，並且為您可能正在處理的單一專案建立更加整合的檢視。

## <a name="scopes-not-resources"></a>範圍，而非資源
在 Azure Active Directory 中，應用程式能以「資源」，或是權杖收件者的方式運作。  資源可以定義它所了解的許多**範圍**或 **oAuth2Permissions**，讓用戶端應用程式得以針對特定一組範圍要求該資源的權杖。  請考慮以 Azure AD Graph API 做為資源的範例：

* 資源識別碼，或`AppID URI`：`https://graph.windows.net/`
* 範圍，或`OAuth2Permissions`：`Directory.Read`、`Directory.Write` 等。  

這一切都適用於 v2.0 端點。  應用程式仍可做為資源、定義範圍並依據 URI 識別。  用戶端應用程式仍可要求存取這些範圍。  不過，用戶端用來要求這些權限的方式已改變。  在過去，Azure AD 的 OAuth 2.0 授權要求可能如下所示：

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

其中 **resource** 參數指出用戶端應用程式要求授權的資源。  Azure AD 根據 Azure 入口網站中的靜態設定計算應用程式所需的權限，並據以發出權杖。  現在，相同的 OAuth 2.0 授權要求如下所示：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

其中 **scope** 參數指出用戶端應用程式授權的資源和權限。 所需的資源仍是要求中最新的 - 它只會包含在 scope 參數的每個值中。  以此方式使用 scope 參數可讓 v2.0 端點更符合 OAuth 2.0 規格，並且更貼近常見的業界作法。  它也可以讓應用程式執行下一節所描述的 [增量同意](#incremental-and-dynamic-consent)。

## <a name="incremental-and-dynamic-consent"></a>增量和動態同意
已在 Azure AD 中註冊的應用程式，必須於應用程式建立時，在 Azure 入口網站中指定其所需的 OAuth 2.0 權限：

![權限註冊 UI](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

應用程式所需的權限是以 **靜態**方式進行設定。  雖然這可讓應用程式的設定存在於 Azure 入口網站中並使程式碼好用又簡單，但開發人員會面臨一些問題：

* 應用程式必須在應用程式建立時知道可能會需要的所有權限。  隨著時間新增權限有所困難。
* 應用程式必須事先知道可能會存取的所有資源。  很難建立能夠存取任意數目的資源的應用程式。
* 應用程式必須在使用者第一次登入時要求可能會需要的權限。  在某些情況下，這會導致非常冗長的權限清單，而讓使用者在初始登入時打消核准應用程式存取權的念頭。

透過 v2.0 端點，您可以在執行階段 **動態**指定您的 app 在一般使用期間所需的權限。  若要這麼做，您可以在授權要求的 `scope` 參數中包含範圍，以在任何指定的時間點指定您的應用程式所需的範圍：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

應用程式的上述要求權限可讀取 Azure AD 使用者的目錄資料，以及將資料寫入至其目錄。  如果使用者過去曾針對此特定應用程式同意這些權限，他們只要輸入其認證並登入應用程式。  如果使用者未曾同意這些權限的任何一項，v2.0 端點會要求使用者同意這些權限。  若要深入了解，您可以參閱 [權限、同意和範圍](active-directory-v2-scopes.md)。

允許應用程式透過 `scope` 參數動態要求權限，您即可完全掌控使用者的經驗。  如果您希望，您可以選擇將您的同意體驗提前，並在一個初始授權要求中要求所有的權限。  或者，如果您的應用程式需要大量的權限，您可以選擇在嘗試使用您的應用程式的某些功能時，以遞增方式向使用者收集這些權限。

## <a name="well-known-scopes"></a>知名的範圍
#### <a name="offline-access"></a>離線存取
使用 v2.0 端點的應用程式可能需要針對應用程式使用新的已知權限：`offline_access` 範圍。  如果應用程式需要長期代表使用者存取資源，則所有應用程式都需要要求此權限，即使使用者可能不會主動使用此應用程式亦然。  在同意對話方塊中， `offline_access` 範圍會對使用者顯示做為「離線存取您的資料」，而使用者必須加以同意。  要求 `offline_access` 權限可讓您的 Web 應用程式從 v2.0 端點獲取 OAuth 2.0 refresh_token。  Refresh_token 屬於長效權杖，可用來交換新的 OAuth 2.0 access_token 以延長存取期間。  

如果您的應用程式未要求 `offline_access` 範圍，則不會收到 refresh_token。  這表示當您在 OAuth 2.0 授權碼流程中兌換 authorization_code 時，只會從 `/token` 端點接收回一個 access_token。  該 access_token 會短時間維持有效 (通常是一小時)，但最後終將過期。  屆時，您的應用程式必須將使用者重新導向回到 `/authorize` 端點以擷取新的 authorization_code。  在此重新導向期間，視應用程式的類型而定，使用者或許不需要再次輸入其認證或重新同意權限。

若要深入了解 OAuth 2.0、refresh_token 和 access_token，請參閱 [v2.0 通訊協定參考](active-directory-v2-protocols.md)。

#### <a name="openid-profile-and-email"></a>OpenID、設定檔和電子郵件
過去，搭配 Azure Active Directory 之最基本的 OpenID Connect 登入流程，會在產生的 id_token 中提供豐富的使用者相關資訊。  id_token 中的宣告可以包含使用者的名稱、慣用使用者名稱、電子郵件地址、物件識別碼等等。

我們現在會限制 `openid` 範圍可提供 app 存取權的資訊。  ‘openid’ 範圍只允許您的 app 將使用者登入，並接收使用者的 app 特定識別碼。  如果您想要取得有關您的應用程式中的使用者的個人識別資訊 (PII)，您的應用程式必須向使用者要求其他權限。  我們引進的兩個新範圍 (`email` 和 `profile` 範圍) 可讓您執行這項操作。

`email` 範圍非常簡單，它可讓您的 app 透過 id_token 中的 `email` 宣告存取使用者的主要電子郵件地址。  `profile` 範圍可以讓您的應用程式存取使用者的所有其他基本資訊 – 其名稱、慣用的使用者名稱、物件識別碼等等。

這可讓您以最低洩漏的方式編碼應用程式 – 您只可以向使用者要求應用程式執行其作業所需的資訊集。  如需這些範圍的詳細資訊，請參閱 [v2.0 範圍參考](active-directory-v2-scopes.md)。

## <a name="token-claims"></a>權杖宣告
V2.0 端點所簽發的權杖中的宣告與公開上市 Azure AD 端點所簽發的權杖不會相同 - 移轉至新服務的應用程式不應假設特定的宣告會存在於 id_token 或 access_token 中。 若要深入了解 v2.0 權杖中發出的特定宣告，請參閱 [v2.0 權杖參考](active-directory-v2-tokens.md)。

## <a name="limitations"></a>限制
使用 v2.0 端點時有一些要注意的限制。  請參閱 [v2.0 限制文件](active-directory-v2-limitations.md) ，以了解是否有任何限制適用於您特定的案例。
