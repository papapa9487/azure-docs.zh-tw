---
title: "Azure Active Directory v2.0 驗證程式庫 | Microsoft Docs"
description: "對 Azure Active Directory v2.0 端點而言，相容的用戶端程式庫和伺服器中介軟體程式庫清單，以及相關的文件庫/來源/範例連結。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 50a1cc0bf9e00cf5b866b88b3e88c62b06a2376b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 驗證程式庫
[Azure Active Directory (Azure AD) v2.0 端點](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare)支援業界標準 OAuth 2.0 和 OpenID Connect 1.0 通訊協定。 您可以使用 Microsoft 和其他廠商提供的各種程式庫搭配 v2.0 端點。

在建置使用 v2.0 端點的應用程式時，建議您使用通訊協定網域專家遵循安全性開發生命週期 (SDL) 方法 (例如 [Microsoft 遵循的方法][Microsoft-SDL]) 所撰寫的程式庫。 如果您決定手工撰寫通訊協定的支援，建議您遵循 SDL 方法並仔細觀察各通訊協定的標準規格中的安全性考量。

> [!NOTE]
> 在尋找 Azure AD v1.0 程式庫 (ADAL) 嗎？ 請參閱 [ADAL 程式庫指南](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-authentication-libraries)。 
> 
> 

## <a name="types-of-libraries"></a>程式庫類型
Azure AD v2.0 端點適用於兩種程式庫類型︰

* **用戶端程式庫**。 原生用戶端和伺服器會使用用戶端程式庫，取得用來呼叫資源 (例如 Microsoft Graph) 的存取權杖。
* **伺服器中介軟體程式庫**。 Web 應用程式會使用伺服器中介軟體程式庫進行使用者登入。 Web API 會使用伺服器中介軟體程式庫來驗證原生用戶端或其他伺服器所傳送的權杖。

## <a name="library-support"></a>程式庫支援
因為您可以在使用 v2.0 端點時選擇任何符合標準的文件庫，所以一定要知道哪裡可以尋求支援。 如需程式庫程式碼中的問題和功能要求，請連絡程式庫擁有者。 如需服務端通訊協定實作中的問題和功能要求，請連絡 Microsoft。

程式庫的支援類型有兩種︰

* **Microsoft 支援**。 Microsoft 會提供這些程式庫的修正程式，並已完成這些程式庫的 SDL 審查評鑑。
* **相容**。 Microsoft 已在基本案例中測試這些程式庫並確認其使用 v2.0 端點。 Microsoft 不提供這些程式庫的修正程式，且尚未審查這些程式庫。 問題和功能要求應重新導向至程式庫的開放原始碼專案。

如需使用 V2.0 端點的程式庫清單，請參閱本文的後面幾節。


## <a name="microsoft-supported-client-libraries"></a>Microsoft 支援的用戶端程式庫

> [!IMPORTANT]
> MSAL 預覽程式庫適合在生產環境中使用。 我們針對這些程式庫所提供的生產層級支援，和為目前的生產程式庫 (ADAL) 所提供的支援相同。 在預覽期間，我們可能會對 MSAL API、內部快取格式，以及這些程式庫的其他機制進行一些變更，且恕不另行通知。您必須連同錯誤修正或功能改善一起接受這些變更。 這可能會影響您的應用程式。 例如，快取格式變更可能會影響您的使用者，例如需要他們再次登入。 API 變更可能需要您更新程式碼。 當我們提供正式發行版本時，將會要求您在六個月內更新至正式發行版本，因為屆時使用程式庫預覽版本所撰寫的應用程式可能無法繼續運作。

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET 用戶端、Windows 市集、UWP、Xamarin iOS 和 Android | MSAL .NET (預覽) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [傳統型應用程式](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (預覽) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS、macOS | MSAL (預覽) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 應用程式](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (預覽) | [中央儲存機制](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 應用程式](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支援的伺服器中介軟體程式庫

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect 中介軟體 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 應用程式](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | 適用於 AzureAD 的 OWIN OAuth Bearer 中介軟體 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | 適用於 .NET 4.5 的 JWT 處理常式 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect 中介軟體 |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC 應用程式 (英文)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth Bearer 中介軟體 |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | 適用於 .NET Core 的 JWT 處理常式  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 應用程式](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>相容的用戶端程式庫
| 平台 | 程式庫名稱 | 測試的版本 | 原始程式碼 | 範例 |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[原生應用程式範例](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[原生應用程式範例](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |

## <a name="compatible-server-middleware-libraries"></a>相容的伺服器中介軟體程式庫
| 平台 | 程式庫名稱 | 測試的版本 | 原始程式碼 | 範例 |
|:---:|:---:|:---:|:---:|:---:|
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [3.2.0 版](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [1.4.2 版](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |[Web 應用程式](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>相關內容
如需 Azure AD v2.0 端點的詳細資訊，請參閱 [Azure AD 應用程式模型 v2.0 概觀][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
