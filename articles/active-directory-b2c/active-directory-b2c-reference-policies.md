---
title: "Azure Active Directory B2C：內建原則 | Microsoft Docs"
description: "關於 Azure Active Directory B2C 的可延伸原則架構及如何建立各種原則類型的主題"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mbaldwin
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: daad3af089afdf76b930053728bb11a5cf4c2a92
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C：內建原則


Azure Active Directory (Azure AD) B2C 的可延伸原則架構是服務的核心力量。 原則可完整描述取用者身分識別體驗，例如註冊、登入或設定檔編輯。 比方說，註冊原則可讓進行下列設定來控制行為：

* 可供取用者用來註冊應用程式的帳戶類型 (例如 Facebook 等社交帳戶，或像是電子郵件地址的本機帳戶)
* 註冊時向取用者收集的屬性 (例如名字、郵遞區號和鞋子尺寸)
* Azure Multi-Factor Authentication 的使用
* 所有註冊頁面的外觀與風格
* 原則執行完成時應用程式所接收的資訊 (以權杖中的宣告表示)

您可以在租用戶中建立多個不同類型的原則，並視需要在您的應用程式中使用。 原則可以跨應用程式重複使用。 此一彈性可讓開發人員在少量變更或完全不變更程式碼的情況，定義及修改取用者身分識別體驗。

透過簡單的開發人員介面就可使用原則。 您的應用程式使用標準 HTTP 驗證要求來觸發原則 (在要求中傳遞原則參數)，並從回應中收到自訂的權杖。 例如，在叫用註冊原則的要求和叫用登入原則的要求之間，唯一的差別在於 "p" 查詢字串參數中使用的原則名稱：

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

如需原則架構的詳細資訊，請參閱 [Enterprise Mobility and Security 部落格上關於 Azure AD B2C 的這篇部落格文章](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx)。

## <a name="create-a-sign-up-or-sign-in-policy"></a>建立註冊或登入原則

此原則可使用單一組態處理取用者註冊和登入經驗。 視內容而定，取用者會被引導到正確的路徑 (註冊或登入)。 此原則也會描述在成功註冊或登入時，應用程式將收到的權杖內容。您可以 [在這裡找到](active-directory-b2c-devquickstarts-web-dotnet-susi.md)註冊或登入原則的代碼範例。  建議您使用這個原則，而不要使用註冊原則和登入原則。  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>建立註冊原則

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>建立登入原則

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>建立設定檔編輯原則

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>建立密碼重設原則

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>如何將註冊或登入原則與密碼重設原則連結在一起？
當您建立註冊或登入原則 (搭配本機帳戶) 時，您會在該體驗的第一個頁面上看到[忘記密碼?] 連結。 按一下此連結並不會自動觸發密碼重設原則。 

相反地，系統會將錯誤碼 **`AADB2C90118`** 傳回您的應用程式。 您的應用程式必須叫用特定的密碼重設原則來處理此錯誤碼。 如需詳細資訊，請參閱[示範連結原則方法的範例](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)。

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>應該使用註冊原則、登入原則還是註冊原則加上登入原則？
我們建議使用註冊原則或是登入原則，而不要使用註冊原則加上登入原則。  

註冊原則或登入原則的功能比登入原則還多。 它也可讓您使用頁面 UI 自訂，並且對當地語系化有更強的支援。 

如果您不需要將原則當地語系化，只需要較少的自訂功能來設定商標，而且想要內建密碼重設功能，則建議您使用登入原則。

## <a name="next-steps"></a>後續步驟
* [權杖、工作階段及單一登入組態](active-directory-b2c-token-session-sso.md)
* [在取用者註冊期間停用電子郵件驗證](active-directory-b2c-reference-disable-ev.md)

