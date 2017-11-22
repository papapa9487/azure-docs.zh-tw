---
title: "使用主領域探索原則為應用程式設定登入自動加速 | Microsoft Docs"
description: "說明 Azure AD 租用戶是什麼，以及如何透過 Azure Active Directory 管理 Azure"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>使用主領域探索 (HRD) 原則為應用程式設定登入自動加速

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>主領域探索和自動加速簡介
下列文件提供主領域探索和自動加速的簡介。

### <a name="home-realm-discovery"></a>主領域探索
主領域探索是讓 Azure Active Directory 判斷使用者登入時需在何處驗證的程序。  登入 Azure AD 租用戶存取資源或 Azure AD 一般登入頁面時，使用者會輸入使用者名稱 (UPN)。  Azure AD 使用該值來探索使用者需要登入的位置。   使用者可能需要透過下列其中一種方式來進行驗證：

- 使用者的主租用戶 (可能和使用者嘗試存取之資源的租用戶相同)。 
- Microsoft 帳戶。   使用者是資源租用戶中的來賓
- 與 Azure AD 租用戶同盟的另一個識別提供者。  內部部署識別提供者，例如 AD FS。

### <a name="auto-acceleration"></a>自動加速 
某些組織會將其 Azure Active Directory 租用戶設為與另一個 IdP (例如用於使用者驗證的 AD FS) 同盟。  在這些情況下，使用者在登入應用程式時會先看到 Azure AD 登入頁面，一旦他們輸入其 UPN，即會前往 IdP 登入頁面。  在合理的情況下，系統管理員可能想要在使用者登入特定應用程式時直接將他們導向登入頁面，而略過初始的 Azure Active Directory 頁面。 這稱為「登入自動加速」。

當租用戶與另一個用於登入的 IdP 同盟時，啟用自動加速可讓使用者簡化登入程序，例如當您知道登入的每個人都可由該 IdP 驗證的情況下。  您可以為個別的應用程式設定自動加速。

>[!NOTE]
>如果您為應用程式設定自動加速，來賓使用者會無法登入。 將使用者直接導向同盟 IdP 進行驗證是單向的，因為無法回到 Azure Active Directory 登入頁面。  因為將略過主領域探索步驟，可能需要導向到其他租用戶或外部 IdP (如 Microsoft 帳戶) 來進行驗證的來賓使用者將無法登入應用程式。  

有兩種方式可控制自動加速至同盟 IdP。  下列任一方法︰   

- 在應用程式的驗證要求上使用網域提示 
- 設定 HomeRealmDiscovery 原則以啟用自動加速

## <a name="domain-hints"></a>網域提示 
網域提示是應用程式的驗證要求中包含的指示詞。  網域提示可用來加速使用者前往其同盟 IdP 登入頁面，多租用戶應用程式也可使用網域提示來加速使用者直接前往其租用戶的品牌化 Azure AD 登入頁面。  例如，應用程式 largeapp.com 可能讓客戶存取位於自訂 URL (contoso.largeapp.com) 的應用程式，並可能在驗證要求中包含 Contoso.com 網域提示。 網域提示語法會根據所使用的通訊協定而有所不同，而且通常會設定於應用程式中。

**WS-同盟**：查詢字串中的 whr=contoso.com

**SAML**：包含網域提示的 SAML 驗證要求，或是查詢字串 whr=contoso.com

**Open ID Connect**：查詢字串 domain_hint=contoso.com 

如果網域提示包含在應用程式的驗證要求中，而租用戶與該網域同盟，則 Azure AD 會嘗試將登入重新導向到為該網域設定的 IdP。  如果網域提示未參考已驗證的同盟網域，則會忽略它，並叫用標準的主領域探索。

如需使用 Azure Active Directory 支援之網域提示自動加速的詳細資訊，請參閱此[部落格文章](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) \(英文\)。

>[!NOTE]
>如果網域提示包含在驗證要求中，它的存在將優先於為應用程式設定的任何 HRD 原則。

## <a name="home-realm-discovery-hrd-policy"></a>主領域探索 (HRD) 原則
某些應用程式未提供設定它們所發出驗證要求的方法，在這些情況下，就無法使用網域提示來控制自動加速。   您可以透過原則設定自動加速來達成相同的行為。  

### <a name="setting-hrd-policy"></a>設定 HRD 原則
在應用程式上設定登入自動加速包含三個步驟：


1. 建立自動加速的 HRD 原則
2. 尋找服務主體以附加原則
3. 將原則附加至服務主體。  原則可能已在租用戶中建立，但在它們附加到實體之前不具有任何作用。  HRD 原則可以附加至服務主體，且一個指定實體一次只能有一個 HRD 原則。  

您可以直接使用 Microsoft Azure Active Directory Graph API 或 Azure Active Directory PowerShell Cmdlet 來設定使用 HRD 原則自動加速

管理原則的 Graph API 描述於[這裡](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) \(英文\)。

以下是範例 HRD 原則定義：
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

原則類型是 "HomeRealmDiscoveryPolicy"。

如果 **AccelerateToFederatedDomain** 是 false，則原則沒有任何作用，**PreferredDomain** 應指示要加速前往的網域，而且在租用戶只有一個同盟網域時可以省略。  如果省略，而且有多個已驗證的同盟網域，則原則沒有任何作用。

如果已指定 **PreferredDomain**，它必須符合租用戶的已驗證同盟網域，且相關應用程式的所有使用者必須都能夠在該網域登入。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 原則的優先順序和評估
您可以建立 HRD 原則，並將其指派給特定的組織及服務主體。 這意謂著可以將多個原則套用至某個特定的應用程式。 生效的 HRD 原則會遵循下列規則：


- 如果網域提示存在驗證要求中，則會忽略任何 HRD 原則，並使用網域提示指定的行為。
- 否則，如果已將原則明確指派給服務主體，就會強制執行該原則。 
- 如果沒有網域提示，且未將任何原則明確指派給服務主體，則會強制執行明確指派給該服務主體之父組織的原則。 
- 如果沒有網域提示，且未指派原則給服務主體或組織，則會使用預設 HRD 行為。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>使用 Azure Active Directory PowerShell Cmdlet 在應用程式上使用 HRD 原則設定登入自動加速的教學課程
我們將逐步解說一些案例，包括：


- 為具有單一同盟網域的租用戶設定應用程式自動加速
- 將應用程式的自動加速設為您的租用戶的其中一個已驗證網域
- 列出已設定原則的應用程式

### <a name="prerequisites"></a>必要條件
在下列範例中，您將建立、更新、連結和刪除 Azure AD 中應用程式服務主體上的原則。

1.  若要開始，請下載最新的 Azure AD PowerShell Cmdlet 預覽版。 
2.  有了 Azure AD PowerShell Cmdlet 之後，請執行 Connect 命令以使用您的系統管理員帳戶登入 Azure AD。

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  執行下列命令以查看您組織中的所有原則。

    ``` powershell
    Get-AzureADPolicy
    ```

如果沒有傳回任何內容，表示您的租用戶中未建立任何原則

### <a name="example-setting-auto-acceleration-for-an-application"></a>範例：為應用程式設定自動加速 
在此範例中，您將建立一個原則，讓使用者在登入應用程式時可以自動加速前往 AD FS 登入畫面。  使用者在這個過程中，不需要先於 Azure AD 登入頁面輸入使用者名稱。 

#### <a name="step-1-create-an-hrd-policy"></a>步驟 1：建立 HRD 原則
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

如果您有為應用程式驗證使用者的單一同盟網域，便只需要建立 HRD 原則。  
若要查看您的新原則並取得其 ObjectID，請執行下列命令。

``` powershell
Get-AzureADPolicy
```


有了 HRD 原則之後，若要啟用自動加速，您可以將它指派給多個應用程式服務主體。

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>步驟 2：尋找服務主體以指派原則。  
您需要服務主體的 ObjectId 以指派原則。 有幾種方式可尋找服務主體的物件識別碼。    

您可以使用入口網站、查詢 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) 或移至 [Graph Explorer Tool](https://graphexplorer.cloudapp.net/)，然後登入您的 Azure AD 帳戶以查看組織的所有服務主體，或因為您使用 Powershell，則可以使用 get-AzureADServicePrincipal Cmdlet 來列出服務主體及其識別碼。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>步驟 3：將原則指派給服務主體  
一旦您擁有要設定自動加速之應用程式的服務主體 ObjectId，請執行下列命令，以將步驟 1 中建立的 HRD 原則與步驟 2 中找到的服務主體關聯。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

您可以為要新增原則的每個服務主體重複此命令。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>步驟 4：檢查您的自動加速原則指派給哪些應用程式服務主體
若要檢查哪些應用程式已設定自動加速原則，請使用 Get-AzureADPolicyAppliedObject Cmdlet，並將您想檢查之原則的 objectId 傳遞給它。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>步驟 5：大功告成！
要檢查新原則是否正常運作，請嘗試應用程式。

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>範例：列出已設定自動加速原則的應用程式

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>步驟 1：列出您組織中建立的所有原則 

``` powershell
Get-AzureADPolicy
```

留意要列出指派之原則的**物件識別碼**。

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>步驟 2：列出已指派原則的服務主體。  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>範例：移除應用程式的自動加速原則
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>步驟 1：使用前一個範例來取得原則的 ObjectId，以及您想移除該原則之應用程式服務主體的 ObjectId
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>步驟 2：從應用程式服務主體移除原則指派。  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>步驟 3：列出已指派原則的服務主體，檢查移除是否成功 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>後續步驟
- 如需如何在 Azure AD 中進行驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](develop/active-directory-authentication-scenarios.md)。
- 如需使用者單一登入的詳細資訊，請參閱[搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-enterprise-apps-manage-sso.md)
- 如需所有開發人員相關內容的概觀，請瀏覽 [Active Directory 開發人員指南](develop/active-directory-developers-guide.md)。
