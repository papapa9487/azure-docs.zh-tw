---
title: "Azure Active Directory B2C︰使用自訂原則新增 ADFS 作為 SAML 識別提供者"
description: "使用 SAML 通訊協定和自訂原則來設定 ADFS 2016 的使用說明文章"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 8713fc7dd27023e1244ccb00673dd1652689baf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C︰使用自訂原則新增 ADFS 作為 SAML 識別提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文將說明如何使用[自訂原則](active-directory-b2c-overview-custom.md)，讓 ADFS 帳戶的使用者登入。

## <a name="prerequisites"></a>必要條件

完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)一文中的步驟。

這些步驟包括：

1.  建立 ADFS 信賴憑證者信任。
2.  將 ADFS 信賴憑證者信任憑證新增至 Azure AD B2C。
3.  將宣告提供者新增至原則。
4.  向使用者旅程圖註冊 ADFS 帳戶宣告提供者。
5.  將原則上傳至 Azure AD B2C 租用戶並加以測試。

## <a name="to-create-a-claims-aware-relying-party-trust"></a>若要建立宣告感知信賴憑證者信任

若要在 Azure Active Directory (Azure AD) B2C 中使用 ADFS 作為識別提供者，您必須建立 ADFS 信賴憑證者信任，並對其提供正確的參數。

若要使用 AD FS 管理嵌入式管理單元來新增新的信賴憑證者信任並手動進行設定，請在同盟伺服器上執行下列程序。

完成此程序必須至少具有本機電腦上的**管理員**或同等成員資格。 請在[本機與網域預設群組](http://go.microsoft.com/fwlink/?LinkId=83477)檢閱有關使用適當帳戶和群組成員資格的詳細資料

1.  在 [伺服器管理員] 中按一下 [工具]，然後選取 [ADFS 管理]。

2.  按一下 [新增信賴憑證者信任]。
    ![新增信賴憑證者信任](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  在 [歡迎] 頁面上選擇 [宣告感知]，然後按一下 [開始]。
    ![在 [歡迎] 頁面上選擇 [宣告感知]](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  在 [選取資料來源] 頁面上，依序按一下 [手動輸入信賴憑證者的相關資料]、[下一步]。
    ![輸入信賴憑證者的相關資料](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  在 [指定顯示名稱] 頁面的 [顯示名稱] 中輸入名稱、在 [備忘稿] 下輸入此信賴憑證者信任的描述，然後按 [下一步]。
    ![指定顯示名稱和注意事項](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  選用。 如果您有選擇性的權杖加密憑證，請在 [設定憑證] 頁面上，按一下 [瀏覽] 找出您的憑證檔案，然後按 [下一步]。
    ![設定憑證](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  在 [設定 URL] 頁面上，選取 [啟用 SAML 2.0 WebSSO 通訊協定的支援] 核取方塊。 在 [信賴憑證者 SAML 2.0 SSO 服務 URL] 下，輸入此信賴憑證者信任的安全性聲明標記語言 (SAML) 服務端點 URL，然後按 [下一步]。  針對 [信賴憑證者 SAML 2.0 SSO 服務 URL]，貼上 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`。 將 {tenant} 取代為您的租用戶名稱 (例如，contosob2c.onmicrosoft.com)，並將 {policy} 取代為您的擴充原則名稱 (例如，B2C_1A_TrustFrameworkExtensions)。
    > [!IMPORTANT]
    >原則名稱是 signup_or_signin 原則所繼承的名稱，在此案例中為：`B2C_1A_TrustFrameworkExtensions`。
    >例如，URL 可能是：https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**。

    ![信賴憑證者 SAML 2.0 SSO 服務 URL](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. 在 [設定識別碼] 頁面上，指定與上一個步驟相同的 URL，按一下 [新增] 以將其新增至清單中，然後按 [下一步]。
    ![信賴憑證者信任識別碼](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  在 [選擇存取控制原則] 上選擇原則，然後按 [下一步]。
    ![選擇存取控制原則](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  在 [準備新增信任] 頁面上檢閱設定，然後按 [下一步] 以儲存您的信賴憑證者信任資訊。
    ![儲存您的信賴憑證者信任資訊](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  在 [完成] 頁面上按一下 [關閉]，此動作就會自動顯示 [編輯宣告規則] 對話方塊。
    ![編輯宣告規則](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. 按一下 [新增規則] 。  
      ![新增規則](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  在 [宣告規則範本] 中，選取 [傳送 LDAP 屬性作為宣告]。
    ![選取傳送 LDAP 屬性作為宣告範本規則](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  提供**宣告規則名稱**。 針對 [屬性存放區]，選取 [選取 Active Directory] 來新增下列宣告，然後按一下 [完成] 和 [確定]。
    ![設定規則屬性](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  在 [伺服器管理員] 中，選取 [信賴憑證者信任]，然後選取您所建立的信賴憑證者信任，並按一下 [屬性]。
    ![信賴憑證者編輯屬性](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  在信賴憑證者信任 (B2C 示範) 屬性視窗上，按一下 [簽章] 索引標籤上，然後按一下 [新增]。  
    ![設定簽章](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  新增簽章憑證 (.cert 檔案，不含私密金鑰)。  
    ![新增簽章憑證](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  在信賴憑證者信任 (B2C 示範) 屬性視窗上，按一下 [進階] 索引標籤，然後將 [安全雜湊演算法] 變更為 [SHA-1]，並按一下 [確定]。  
    ![將安全雜湊演算法設定為 SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>將 ADFS 帳戶應用程式金鑰新增至 Azure AD B2C
具有 ADFS 帳戶的同盟需要 ADFS 帳戶的用戶端祕密，才能代表應用程式信任 Azure AD B2C。 您需要將 ADFS 憑證儲存在 Azure AD B2C 租用戶中。 

1.  移至您的 Azure AD B2C 租用戶，並選取 [B2C 設定] > [身分識別體驗架構]
2.  選取**原則金鑰**以檢視您的租用戶中可用的金鑰。
3.  按一下 [+新增]。
4.  針對 [選項] 使用 [上傳]。
5.  針對 [名稱] 使用 `ADFSSamlCert`。  
    可能會自動加入前置詞 `B2C_1A_`。
6.  在檔案上傳中，** 選取包含私密金鑰的憑證 .pfx 檔案。 注意：這個憑證 (含私密金鑰) 應該與發給及用於 ADFS 信賴憑證者的是同一個。
![上傳原則金鑰](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  按一下 [建立] 
8.  確認您已建立金鑰 `B2C_1A_ADFSSamlCert`。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>在擴充原則中新增宣告提供者
如果需要讓使用者使用 ADFS 帳戶登入，您必須將 ADFS 帳戶定義為宣告提供者。 換句話說，您必須指定要與 Azure AD B2C 通訊的端點。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

定義 ADFS 作為宣告提供者，方法是在擴充原則檔案中新增 `<ClaimsProvider>` 節點：

1. 從您的工作目錄中開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。 如果您需要 XML 編輯器，請[試用 Visual Studio 程式碼](https://code.visualstudio.com/download)，這是一個輕巧的跨平台編輯器。
2. 尋找 `<ClaimsProviders>` 區段
3. 在 `ClaimsProviders` 元素下新增下列 XML 程式碼片段，並將 `identityProvider` 取代為您的 DNS (指出網域的任意值)，然後儲存檔案。 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>向註冊或登入使用者旅程圖註冊 ADFS 帳戶宣告提供者
此時，就已設定識別提供者。  不過，無法在任何註冊/登入畫面中使用。 您現在必須將 ADFS 帳戶識別提供者新增至您的使用者 `SignUpOrSignIn` 使用者旅程圖。 若要使用，我們建立了重複的現有範本使用者旅程圖。  然後，我們要修改它，使其包含 ADFS 識別提供者：

>[!NOTE]
>如果您先前從原則的基底檔案將 `<UserJourneys>` 元素複製到擴充檔案 (TrustFrameworkExtensions.xml)，就可以跳過本節。

1.  開啟原則的基本檔案 (例如，TrustFrameworkBase.xml)。
2.  尋找 `<UserJourneys>` 元素，並複製 `<UserJourneys>` 節點的完整內容。
3.  開啟擴充檔案 (例如，TrustFrameworkExtensions.xml)，並尋找 `<UserJourneys>` 元素。 如果此元素不存在，請新增。
4.  貼上您複製作為 `<UserJourneys>` 元素之子元素的 `<UserJournesy>` 節點的完整內容。

### <a name="display-the-button"></a>顯示按鈕
`<ClaimsProviderSelections>` 元素會定義宣告提供者選擇的選項清單和它們的順序。  `<ClaimsProviderSelection>` 元素類似於註冊/登入頁面上的識別提供者按鈕。 如果您新增 ADFS 帳戶的 `<ClaimsProviderSelection>` 元素，當使用者登陸頁面時，會出現新的按鈕。 若要新增此元素：

1.  在您複製的使用者旅程圖中，尋找包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 節點。
2.  找出包含 `Order="1"` 的 `<OrchestrationStep>` 節點
3.  在 `<ClaimsProviderSelections>` 節點下，新增下列 XML 程式碼片段：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 ADFS 帳戶通訊以接收權杖。 透過連結 ADFS 帳戶宣告提供者的技術設定檔，將按鈕連結至動作：

1.  尋找 `<OrchestrationStep>`，它在 `<UserJourney>` 節點中包含 `Order="2"`。
2.  在 `<ClaimsExchanges>` 節點下，新增下列 XML 程式碼片段：

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * 請確定 `Id` 具有與上一節中之 `TargetClaimsExchangeId` 相同的值。
> * 請確定 `TechnicalProfileReferenceId` 設定為您稍早建立之技術設定檔的識別碼 (Contoso-SAML2)。

## <a name="upload-the-policy-to-your-tenant"></a>將原則上傳至您的租用戶
1.  在 [Azure 入口網站](https://portal.azure.com)中，切換至[您的 Azure AD B2C 租用戶環境](active-directory-b2c-navigate-to-b2c-context.md)，然後開啟 [Azure AD B2C] 刀鋒視窗。
2.  選取 [識別體驗架構]。
3.  開啟 [所有原則] 刀鋒視窗。
4.  選取 [上傳原則]。
5.  勾選 [覆寫已存在的原則] 方塊。
6.  **上傳** TrustFrameworkExtensions.xml，並確定它通過驗證

## <a name="test-the-custom-policy-by-using-run-now"></a>使用 [立即執行] 測試自訂原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。
2.  開啟 **B2C_1A_signup_signin**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3.  您應該能夠使用 ADFS 帳戶進行登入。

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[選用] 向設定檔編輯使用者旅程圖註冊 ADFS 帳戶宣告提供者
您也需要將 ADFS 帳戶識別提供者新增至您的使用者 `ProfileEdit` 使用者旅程圖。 若要使用，我們會重複最後兩個步驟：

### <a name="display-the-button"></a>顯示按鈕
1.  開啟原則的擴充檔案 (例如，TrustFrameworkExtensions.xml)。
2.  在您複製的使用者旅程圖中，尋找包含 `Id="ProfileEdit"` 的 `<UserJourney>` 節點。
3.  找出包含 `Order="1"` 的 `<OrchestrationStep>` 節點
4.  在 `<ClaimsProviderSelections>` 節點下，新增下列 XML 程式碼片段：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作
1.  尋找 `<OrchestrationStep>`，它在 `<UserJourney>` 節點中包含 `Order="2"`。
2.  在 `<ClaimsExchanges>` 節點下，新增下列 XML 程式碼片段：

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>使用 [立即執行] 來測試自訂設定檔編輯原則
1.  開啟 [Azure AD B2C 設定]，然後移至 [識別體驗架構]。
2.  開啟 **B2C_1A_ProfileEdit**，此為您上傳的信賴憑證者 (RP) 自訂原則。 選取 [立即執行]。
3.  您應該能夠使用 ADFS 帳戶進行登入。

## <a name="download-the-complete-policy-files"></a>下載完整的原則檔案
選擇性：建議您在使用自訂原則逐步解說完成使用者入門後，使用自己的自訂原則檔案來建置您的情節。 [原則範例檔案僅供參考](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
