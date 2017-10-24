---
title: "Azure Active Directory B2C：KMSI| Microsoft Docs"
description: "示範如何設定「讓我保持登入」的主題"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: d09e15c6f6765eac436f573f89c6703039cd8397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C：啟用「讓我保持登入 (KMSI)」  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C 現在可讓您的 Web 和原生應用程式啟用「讓我保持登入 (KMSI)」功能。 此功能會授與讓使用者回到應用程式而不需要重新輸入使用者名稱和密碼提示的存取權。 當使用者登出時，即會撤銷此存取權。 

建議您對使用公用電腦的使用者核取此選項。 

![影像](images/kmsi.PNG)


## <a name="prerequisites"></a>必要條件

如[開始使用](active-directory-b2c-get-started-custom.md)所述，設定以允許本機帳戶註冊/登入的 Azure AD B2C 租用戶。

## <a name="how-to-enable-kmsi"></a>如何啟用 KMSI

在您的信任架構擴充原則中進行下列變更。

## <a name="adding-a-content-definition-element"></a>新增內容定義元素 

擴充檔案的 `BuildingBlocks` 節點必須包含 `ContentDefinitions` 元素。 

1. 在 `ContentDefinitions` 區段中，定義具有識別碼 `api.signuporsigninwithkmsi` 的新 `ContentDefinition`。
2. 您的新 `ContentDefinition` 必須包含 `LoadUri`、`RecoveryUri` 和 `DataUri`，如下所示。
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` 是機器可識別的識別碼，會在登入頁面中帶出 KMSI 核取方塊。 請確定您不會變更此值。 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>新增本機帳戶登入宣告提供者 

您可以在原則之擴充檔案的 `<ClaimsProvider>` 節點，將本機帳戶登入定義為宣告提供者：

1. 從您的工作目錄中開啟擴充檔案 (TrustFrameworkExtensions.xml)。 
2. 找出區段 `<ClaimsProviders>`。 如果不存在，請在根節點下新增。
3. 來自[使用者入門](active-directory-b2c-get-started-custom.md)的入門套件隨附了「本機帳戶登入」宣告提供者。 
4. 如果沒有，請新增 `<ClaimsProvider>` 節點，如下所示：

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
           <Metadata>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
           </Metadata>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>將應用程式識別碼新增至您的自訂原則

在擴充檔案 (`TrustFrameworkExtensions.xml`) 中新增應用程式識別碼：

1. 在擴充檔案 (TrustFrameworkExtensions.xml) 中，尋找 `<TechnicalProfile Id="login-NonInteractive">` 元素和 `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. 如[入門](active-directory-b2c-get-started-custom.md)所述，將 `IdentityExperienceFrameworkAppId` 的所有執行個體以身分識別體驗架構應用程式的應用程式識別碼取代。 下列是一個範例：

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. 如[快速入門](active-directory-b2c-get-started-custom.md)中所述，以 Proxy 識別體驗架構應用程式的應用程式識別碼，取代 `ProxyIdentityExperienceFrameworkAppId` 的所有執行個體。

4. 儲存擴充檔案。

## <a name="create-a-kmsi-in-enabled-user-journey"></a>在已啟用的使用者旅程圖中建立 KMSI

您現在需要將本機帳戶登入宣告提供者新增至您的使用者旅程圖。 

1. 開啟原則的基本檔案 (例如，TrustFrameworkBase.xml)。
2. 尋找 `<UserJourneys>` 元素，並複製含有 `Id="SignUpOrSignIn"` 的整個 `<UserJourney>`。
3. 開啟擴充檔案 (例如，TrustFrameworkExtensions.xml)，並尋找 `<UserJourneys>` 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 `<UserJourney>` 節點作為 `<UserJourneys>` 元素的子元素。
5. 重新命名新使用者旅程圖的識別碼 (例如，重新命名為 `SignUpOrSignInWithKmsi`)。
6. 最後，在 `OrchestrationStep 1` 中，將 `ContentDefinitionReferenceId` 變更為 `api.signuporsigninwithkmsi` (您在稍早的步驟中所定義)。 這會啟用使用者旅程圖中的核取方塊。 
7. 您已修改擴充檔案。 儲存並上傳這個檔案。 請確定所有驗證都成功。

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>建立信賴憑證者 (RP) 檔案

接下來，更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖：

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本。 然後，將它重新命名 (例如，SignUpOrSignInWithKmsi.xml)。

2. 開啟新檔案，將 `<TrustFrameworkPolicy>` 的 `PolicyId` 屬性更新成唯一值。 這會成為您的原則名稱 (例如，SignUpOrSignInWithKmsi)。

3. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 屬性，以符合您建立之新使用者旅程圖的 `Id` (例如，SignUpOrSignInWithKmsi)。

4. `UserJourneyBehaviors` 中即已設定 KMSI。 

5. **`KeepAliveInDays`** 會控制使用者保持登入的時間長度。 在下列範例中，KMSI 工作階段會在 14 天之以自動到期，而不論使用者執行無訊息驗證的頻率為何。

   將 `KeepAliveInDays` 值設為 0 會關閉 KMSI 功能。 根據預設，此值為 0

6. 如果 **`SessionExpiryType`** 是 [輪流]，那麼，每當使用者執行無訊息驗證，KMSI 工作階段即會延長 14 天。  如果已選取 [輪流]，建議您將保留的天數設定為最小值。 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. 儲存變更然後上傳檔案。

8. 若要測試您上傳的自訂原則，在 Azure 入口網站中，移至原則刀鋒視窗，然後按一下立即執行。


## <a name="link-to-sample-policy"></a>連結至範例原則

您可以在[這裡](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)找到範例原則。








