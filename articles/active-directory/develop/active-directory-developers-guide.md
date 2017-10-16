---
title: "開發人員適用的 Azure Active Directory | Microsoft Docs"
description: "本文提供使用 Azure Active Directory 登入 Microsoft 工作和學校帳戶的概觀。"
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-for-developers"></a>開發人員適用的 Azure Active Directory
Azure Active Directory 是一項雲端識別服務，可讓開發人員使用 Microsoft 所支援的工作或學校帳戶安全地讓任何使用者登入。  此處的文件說明如何使用業界標準驗證通訊協定 (OAuth 和 OpenID Connect)，將 Azure AD 支援新增至您的應用程式。

| | |
| --- | --- |
|[驗證基本概念](active-directory-authentication-scenarios.md) | 使用 Azure AD 進行驗證的簡介 |
|[應用程式類型](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD 所支援的驗證案例概觀 |                                
                                                                              
## <a name="get-started"></a>開始使用
這些引導式安裝程式會逐步引導您使用我們的驗證程式庫讓 Azure Active Directory 使用者登入。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![行動和桌面應用程式](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />行動和桌面應用程式</center> | [概觀](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [概觀](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Single Page Apps](./media/active-directory-developers-guide/SPA.png)<br />Single Page Apps</center> | [概觀](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概觀](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![服務對服務](./media/active-directory-developers-guide/Service_App.png)<br />服務對服務</center> | [概觀](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 用戶端認證](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>手冊
這些文章說明如何使用 Azure Active Directory 執行一般工作。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[應用程式註冊](active-directory-integrating-applications.md)           | 如何在 Azure AD 中註冊應用程式 |
|[多租用戶應用程式](active-directory-devhowto-multi-tenant-overview.md)    | 如何登入任何 Microsoft 工作帳戶 |
|[OAuth 和 OpenID Connect](active-directory-protocols-openid-connect-code.md)| 如何使用我們的新式驗證通訊協定讓使用者登入並呼叫 Web API |
|[更多指南...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>參考
這些文章提供 API、通訊協定訊息和 Azure Active Directory 中所用詞彙的詳細資訊。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [驗證程式庫 (ADAL)](active-directory-authentication-libraries.md)   | Azure AD 所提供的程式庫和 SDK 概觀 |
| [程式碼範例](active-directory-code-samples.md)                                  | 所有 Azure AD 程式碼範例的清單 |
| [詞彙](active-directory-dev-glossary.md)                                      | 本文件中使用的術語和文字定義 |
| [更多參考資料...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>說明及支援
以下是取得在 Azure Active Directory 開發之協助的最佳位置。

|  |  
|---|
|[Stack Overflow 的 `azure-active-directory` 和 `adal` 標籤](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Azure Active Directory 的意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [試用 Microsoft Dev Chat (限時免費)](http://aka.ms/devchat) |

<br />

> [!NOTE]
> 如果您需要登入 Microsoft 個人帳戶，您可能要考慮使用 [Azure AD v2.0 端點](active-directory-appmodel-v2-overview.md)。  Azure AD v2.0 端點可將 Microsoft 個人帳戶和 Microsoft 工作帳戶 (來自 Azure AD) 統一成單一驗證系統。
