---
title: "概觀 - Azure AD B2C | Microsoft Docs"
description: "使用 Azure Active Directory B2C 開發取用者導向應用程式"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: saeedakhter-msft
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 44d5d31d49c375c802a67511d1f962df20656559
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Azure AD B2C︰將焦點放在您的應用程式，我們會擔心註冊和登入

Azure AD B2C 是適用於 Web 和行動應用程式的雲端身分識別管理解決方案。 這是高可用性的全域服務，可處理數億規模的身分識別。 Azure AD B2C 建置於企業級安全平台，可確保您的應用程式、企業和客戶受到安全防護。

採用最低組態，Azure AD B2C 可讓您的應用程式驗證︰

* **社交帳戶** (例如 Facebook、Google、LinkedIn 等等)
* **企業帳戶** (使用開放式標準通訊協定 OpenID Connect 或 SAML)
* **本機帳戶** (電子郵件地址和密碼，或使用者名稱和密碼)

## <a name="get-started"></a>開始使用

首先，使用[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中概述的步驟來取得自己的租用戶。

然後選擇應用程式開發案例：

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![行動和桌面應用程式](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />行動和桌面應用程式</center> | [概觀](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Web Apps](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [概觀](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Single Page Apps](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Single Page Apps</center> | [概觀](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Web API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [呼叫 .NET Web API](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>新功能

請不時返回此處查看，瞭解關於 Azure Active Directory B2C 未來變更的相關資訊。 我們也會使用 @AzureAD 發佈任何更新的相關推文。

* 除了內建原則 (一般可用性)，公開預覽版本現在已提供[「自訂原則」](active-directory-b2c-overview-custom.md)功能。  自訂原則適用於需要控制其身分識別經驗的身分識別專業人員。
* 公開預覽版本現在已提供[存取權杖](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview)功能。
* 已經宣告[以歐洲為主的 Azure AD B2C 一般可用性](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/)目錄。
* 查看 [GitHub 上不斷成長的程式碼範例](https://github.com/Azure-Samples?q=b2c)程式庫！

## <a name="how-to-articles"></a>操作說明文章

瞭解如何使用特定 Azure Active Directory B2C 功能：

* 設定您要在取用者導向應用程式中使用的 [Facebook](active-directory-b2c-setup-fb-app.md)、[Google+](active-directory-b2c-setup-goog-app.md)、[Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)、[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md)。
* [使用自訂屬性來收集取用者相關資訊](active-directory-b2c-reference-custom-attr.md)。
* [在取用者導向應用程式中啟用 Azure Multi-Factor Authentication](active-directory-b2c-reference-mfa.md)。
* [設定取用者的自助式密碼重設](active-directory-b2c-reference-sspr.md)。
* [自訂註冊、登入和其他取用者導向頁面的外觀與風格](active-directory-b2c-reference-ui-customization.md) (這些頁面由 Azure Active Directory B2C 提供)。
* [使用 Azure Active Directory 圖形 API 來以程式設計方式建立、讀取、更新和刪除取用者](active-directory-b2c-devquickstarts-graph-dotnet.md) 。

## <a name="next-steps"></a>後續步驟

以下連結有助於深入探索服務：

* 請參閱 [Azure Active Directory B2C 價格資訊](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
* 檢閱 Azure Active Directory B2C 的[程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c)。 
* 使用 [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c) 標記，取得 Stack Overflow 的相關說明。
* 透過 [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)告訴我們您的想法，我們冀望瞭解您的看法！
* 檢閱 [Azure AD B2C 通訊協定參考](active-directory-b2c-reference-protocols.md)。
* 檢閱 [Azure AD B2C 權杖參考](active-directory-b2c-reference-tokens.md)。
* 閱讀 [Azure Active Directory B2C 常見問題集](active-directory-b2c-faqs.md)。
* [提出 Azure Active Directory B2C 的支援要求](active-directory-b2c-support.md)。

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們鼓勵您造訪 [此頁面](https://technet.microsoft.com/security/dd252948) 並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。


