---
title: "Azure Active Directory B2C：使用自訂原則新增 LinkedIn 作為 OAuth2 識別提供者"
description: "使用 OAuth2 通訊協定和自訂原則來設定 LinkedIn 應用程式的使用說明文章"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自訂原則新增 LinkedIn 作為識別提供者
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文將說明如何使用[自訂原則](active-directory-b2c-overview-custom.md)，讓 LinkedIn 帳戶的使用者登入。

## <a name="prerequisites"></a>必要條件
完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文中的步驟。

這些步驟包括：
1.  建立 LinkedIn 帳戶應用程式。
2.  將 LinkedIn 帳戶應用程式金鑰新增至 Azure AD B2C
3.  將宣告提供者新增至原則
4.  向使用者旅程圖註冊 LinkedIn 帳戶宣告提供者
5.  將原則上傳至 Azure AD B2C 租用戶並測試原則

## <a name="step-1-create-a-linkedin-account-application"></a>步驟 1：建立 LinkedIn 帳戶應用程式
如果要在 Azure Active Directory (Azure AD) B2C 中將 LinkedIn 作為識別提供者，您必須建立 LinkedIn 應用程式，然後為其提供正確的參數。 您可以在這裡註冊 LinkedIn 應用程式：[https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  瀏覽至 [LinkedIn 應用程式管理](https://www.linkedin.com/secure/developer?newapp=)網站，使用您的 LinkedIn 帳戶認證登入，然後按一下 [建立應用程式]。

    ![LinkedIn 帳戶 - 建立應用程式](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  輸入您的 [公司名稱]，提供新應用程式的描述性的 [名稱] 和 [說明]。
    2.  上傳**應用程式標誌**
    3.  選取 [應用程式使用]
    4.  對於 [網站 URL] 值，貼上 `https://login.microsoftonline.com`。
    5.  輸入您的 [公司電子郵件] 和 [公司電話]
    6.  在頁面底部，閱讀並接受使用條款。 然後按一下 [提交]

    ![LinkedIn 帳戶 - 設定應用程式屬性](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  從功能表按一下 [驗證]。 記下**用戶端識別碼**和**用戶端密碼**的值。

    針對 [授權重新導向 URL]，貼上 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`。 使用您的租用戶名稱 (例如 contosob2c.onmicrosoft.com) 來取代 {tenant}。 並確定您使用的是 HTTPS 配置，然後按一下 [新增]

    ![LinkedIn 帳戶 - 設定授權的重新導向 URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >用戶端密碼是重要的安全性認證。 請勿將此密碼告訴任何人或隨應用程式一起散發。

4.  按一下 [設定] 索引標籤，將 [應用程式狀態] 變更為 [線上]，然後按一下 [更新]。

    ![LinkedIn 帳戶 - 設定應用程式狀態](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>步驟 2：將 LinkedIn 應用程式金鑰新增至 Azure AD B2C
具有 LinkedIn 帳戶的同盟需要 LinkedIn 帳戶的用戶端密碼，才能代表應用程式信任 Azure AD B2C。 您需要在 Azure AD B2C 租用戶中儲存 LinkdIn 應用程式密碼：  

1.  移至您的 Azure AD B2C 租用戶，並選取 [B2C 設定] > [身分識別體驗架構]
2.  選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3.  按一下 [+新增]。
4.  針對 [選項] 使用 [手動]。
5.  針對 [名稱] 使用 `LinkedInSecret`。  
    可能會自動加入前置詞 `B2C_1A_`。
6.  在 [密碼] 方塊中，從 https://apps.dev.microsoft.com 輸入您的 LinkdIn 應用程式密碼
7.  針對 [金鑰使用方法] 使用 [加密]。
8.  按一下 [建立]  
9.  確認您已建立金鑰 `B2C_1A_LinkedInSecret`。

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>步驟 3：在擴充原則中新增宣告提供者
如果需要讓使用者使用 LinkedIn 帳戶登入，您必須將 LinkedIn 定義為宣告提供者。 換句話說，您必須指定要與 Azure AD B2C 通訊的端點。 此端點提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

定義 LinkedIn 作為宣告提供者，方法是在擴充原則檔案中新增 `<ClaimsProvider>` 節點：

1.  從您的工作目錄中開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。 
2.  尋找 `<ClaimsProviders>` 區段
3.  在 `<ClaimsProviders>` 節點下，新增下列 XML 程式碼片段：  

  ```xml
  <ClaimsProvider>
    <Domain>linkedin.com</Domain>
    <DisplayName>LinkedIn</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="LinkedIn-OAUTH">
        <DisplayName>LinkedIn</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">linkedin</Item>
          <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
          <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
          <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
          <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
          <Item Key="ClaimsEndpointFormatName">format</Item>
          <Item Key="ClaimsEndpointFormat">json</Item>
          <Item Key="scope">r_emailaddress r_basicprofile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your LinkedIn application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
          <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
  ```

4.  使用您的 LinkedIn 應用程式用戶端識別碼來取代 `client_id` 值
5.  儲存檔案。

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>步驟 4：向註冊或登入使用者旅程圖註冊 LinkedIn 帳戶宣告提供者
此時，就已設定識別提供者。 不過，無法在任何註冊/登入畫面中使用。 您現在必須將 LinkedIn 帳戶識別提供者新增至您的使用者 `SignUpOrSignIn` 使用者旅程圖。

### <a name="step-41-make-a-copy-of-the-user-journey"></a>步驟 4.1 建立使用者旅程圖的副本
為讓它可供使用，您會建立重複的現有範本使用者旅程圖。 然後您會新增 LinkedIn 識別提供者：

> [!NOTE]
>
>如果您從原則的基底檔案將 `<UserJourneys>` 元素複製到擴充檔案 (TrustFrameworkExtensions.xml)，就可以跳過本節。

1.  開啟原則的基本檔案 (例如，TrustFrameworkBase.xml)。
2.  尋找 `<UserJourneys>` 元素，並複製 `<UserJourneys>` 節點的完整內容。
3.  開啟擴充檔案 (例如，TrustFrameworkExtensions.xml)，並尋找 `<UserJourneys>` 元素。 如果此元素不存在，請新增。
4.  貼上您複製作為 `<UserJourneys>` 元素之子元素的 `<UserJournesy>` 節點的完整內容。

### <a name="step-42-display-the-button"></a>步驟 4.2 顯示「按鈕」
`<ClaimsProviderSelections>` 元素會定義宣告提供者選擇的選項清單和它們的順序。  `<ClaimsProviderSelection>` 元素類似於註冊/登入頁面上的識別提供者按鈕。 如果您新增 LinkedIn 帳戶的 `<ClaimsProviderSelection>` 元素，當使用者登陸頁面時，會出現新的按鈕。 若要新增此元素：

1.  在您複製的使用者旅程圖中，尋找包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 節點。
2.  找出包含 `Order="1"` 的 `<OrchestrationStep>` 節點
3.  在 `<ClaimsProviderSelections>` 節點下，新增下列 XML 程式碼片段：
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>步驟 4.3 將按鈕連結至動作
現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 LinkedIn 帳戶通訊以接收權杖。 透過連結 LinkedIn 帳戶宣告提供者的技術設定檔，將按鈕連結至動作：

1.  尋找 `<OrchestrationStep>`，它在 `<UserJourney>` 節點中包含 `Order="2"`。
2.  在 `<ClaimsExchanges>` 節點下，新增下列 XML 程式碼片段：

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * 請確定 `Id` 具有與上一節中之 `TargetClaimsExchangeId` 相同的值
    > * 請確定 `TechnicalProfileReferenceId` 識別碼設定為您稍早建立之技術設定檔的識別碼 (LinkedIn-OAuth)。

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步驟 5：將原則上傳至您的租用戶
1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後按一下 [Azure AD B2C]。
2.  選取 [識別體驗架構]。
3.  按一下 [所有原則]。
4.  選取 [上傳原則]。
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步驟 6：使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。

    > [!NOTE]
    >
    >[立即執行] 需要在租用戶上至少預先註冊一個應用程式。 若要了解如何註冊應用程式，請參閱 Azure AD B2C [開始使用](active-directory-b2c-get-started.md)一文或[應用程式註冊](active-directory-b2c-app-registration.md)一文。

2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3.  您應該能夠使用 LinkedIn 帳戶進行登入。

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>步驟 7：[選用] 向設定檔編輯使用者旅程圖註冊 LinkedIn 帳戶宣告提供者
您也需要將 LinkedIn 帳戶識別提供者新增至您的使用者 `ProfileEdit` 使用者旅程圖。 若要使用，您可以重複步驟 #4。 此時，選取包含 `Id="ProfileEdit"` 的 `<UserJourney>` 節點。 儲存、上傳，並測試您的原則。

## <a name="download-the-complete-policy-files"></a>下載完整的原則檔案
選擇性：建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節，而非使用這些範例檔案。  [參考的範例原則檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)