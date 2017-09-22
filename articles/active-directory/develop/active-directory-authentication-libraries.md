---
title: "Azure Active Directory 驗證程式庫 | Microsoft Docs"
description: "Azure AD 驗證程式庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆地向雲端或內部部署 Active Directory (AD) 驗證使用者，然後取得存取權杖來保護 API 呼叫。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 1b79fb5b280b0cb4e087c2acde07796fd51e81fb
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 驗證程式庫
Azure Active Directory 驗證程式庫 (ADAL) 可讓應用程式開發人員向雲端或內部部署 Active Directory (AD) 驗證使用者，並取得權杖來保護 API 呼叫。 ADAL 透過下列功能使開發人員更容易驗證：
 - 儲存存取權杖和重新整理權杖的可設定權杖快取
 - 存取權杖到期而有重新整理權杖可用時的自動權杖重新整理
 - 支援非同步方法呼叫
 - 還有更多

> [!NOTE]
> 在尋找 Azure AD v2.0 程式庫 (MSAL) 嗎？ 請參閱 [MSAL 程式庫指南](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries)。 
> 
> 

### <a name="client-libraries"></a>用戶端程式庫

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET 用戶端、Windows 市集、UWP、Xamarin iOS 和 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [傳統型應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[參考](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET 用戶端、Windows 市集、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [傳統型應用程式](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[單一頁面應用程式](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [參考](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[中央儲存機制](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web 應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>伺服器程式庫 

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET |適用於 AzureAD 的 OWIN|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |適用於 OpenIDConnect 的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web 應用程式](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |適用於 WS-同盟的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web 應用程式](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |適用於 .NET 4.5 的身分識別通訊協定延伸模組 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |適用於 .NET 4.5 的 JWT 處理常式 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>v2.0 用戶端程式庫 (MSAL)

[Azure AD v2.0 端點](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare)透過一個端點結合 Azure AD 和 Microsoft 帳戶。 若要存取此端點，開發人員可以使用[支援生產環境的預覽 MSAL 程式庫](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries)，而不是 ADAL。

| 平台 | 程式庫 | 下載 | 原始程式碼 | 範例 | 參考
| --- | --- | --- | --- | --- | --- |
| .NET 用戶端、Windows 市集、UWP、Xamarin iOS 和 Android |適用於 .NET 的 MSAL (預覽) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [傳統型應用程式](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[參考](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |適用於 JavaScript 的 MSAL (預覽) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [單一頁面應用程式](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [參考](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |適用於 iOS 的 MSAL (預覽) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS App](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [參考](https://azuread.github.io/docs/objc/) |
| Android |適用於 Android 的 MSAL (預覽) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android App](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [參考](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>案例

以下三種常見案例使用 ADAL 驗證存取遠端資源的用戶端：  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>驗證在裝置上執行原生用戶端應用程式的使用者 

在此案例中，開發人員會有 WPF 用戶端應用程式，其必須存取由 Azure AD 所保護的遠端資源，例如 Web API。 他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道 Web API 使用的 Azure AD 租用戶。 如此一來，他可以使用 ADAL 來協助向 Azure AD 進行驗證，完全將驗證經驗委派給 ADAL 或明確地處理使用者認證。 ADAL 使得驗證使用者變得輕鬆，只要取得存取權杖並對 Azure AD 更新權杖，然後使用該存取權杖向 Web API 提出要求。

如需示範如何向 Azure AD 進行驗證的程式碼範例，請參閱 [原生用戶端 WPF 應用程式至 Web API](https://github.com/azureadsamples/nativeclient-dotnet)。

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>驗證網頁伺服器上執行的機密用戶端應用程式

在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。 他有 Azure 訂用帳戶、知道如何叫用下游服務，並知道 Web API 使用的 Azure AD 租用戶。 如此一來，他可以透過明確地處理應用程式的認證，使用 ADAL 來協助向 Azure AD 進行驗證。 ADAL 使得從 Azure AD 擷取權杖變得輕鬆，只需使用應用程式的用戶端認證，然後使用該權杖向 Web API 提出要求。 ADAL 也會處理存取權杖的存留期的管理，方法是快取它並視需要更新。 如需示範此案例的程式碼範例，請參閱[精靈主控台應用程式至 Web API](https://github.com/AzureADSamples/Daemon-DotNet)。

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>代表使用者，驗證伺服器上執行的機密用戶端應用程式 

在此案例中，開發人員會有在伺服器上執行的應用程式，需要存取由 Azure AD 所保護的遠端資源，例如 Web API。 要求也必須代表 Azure AD 使用者提出。 他有 Azure 訂用帳戶、知道如何叫用下游 Web API，並知道服務使用的 Azure AD 租用戶。 一旦向 Web 應用程式驗證了使用者，應用程式便可以從 Azure AD 取得使用者的授權碼。 然後，Web 應用程式可以使用 ADAL 代表使用者，利用與應用程式相關聯的授權碼和用戶端認證從 Azure AD 取得存取權杖和更新權杖。 在 Web 應用程式取得存取權杖之後，它可以呼叫 Web API，直到權杖到期。 權杖到期時，Web 應用程式可以使用 ADAL 透過先前收到的更新權杖來取得新的存取權杖。 如需示範此案例的程式碼範例，請參閱[原生用戶端至 Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)
- [Azure Active Directory 的驗證案例](active-directory-authentication-scenarios.md)
- [Azure Active Directory 程式碼範例](active-directory-code-samples.md)

