---
title: "Azure Active Directory 中的應用程式、權限及同意 | Microsoft Docs"
description: "Azure AD Connect 會整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合的 Office 365、Azure 和 SaaS 應用程式提供通用身分識別。"
keywords: "Azure AD 簡介, 應用程式, 何謂 Azure AD Connect, 安裝 active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 9f51f46b0dc942bb9749f8e962a2872a8618245a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Azure Active Directory 中的應用程式、權限及同意
在 Azure Active Directory 內，您可以在目錄中新增應用程式。  應用程式可能會根據應用程式類型而有所不同。  若要在傳統入口網站中檢視應用程式，請選取目錄並選擇應用程式。

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。

## <a name="types-of-apps"></a>應用程式類型

1. **單一租用戶應用程式** </br>
    - **單一租用戶應用程式** - 通常稱為企業營運 (LOB) 應用程式。 這是組織內的某人開發了自己的應用程式，並希望組織中的使用者能夠登入應用程式的情況。
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **應用程式 Proxy 應用程式** - 當您使用 Azure AD 應用程式 Proxy 公開內部部署應用程式時，便會在租用戶中註冊單一租用戶應用程式 (以及應用程式 Proxy 服務)。 此應用程式便是代表所有雲端互動 (例如驗證) 之內部部署應用程式的項目  (應用程式 Proxy 需要 Azure AD Basic 或更高版本)。


2. **多租用戶應用程式**
    - **其他人可同意的多租用戶應用程式** - 類似於「組織開發的單一租用戶應用程式」。 主要差異 (除了應用程式本身的邏輯外) 是其他租用戶的使用者也可以同意並登入應用程式。</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **其他人開發、且 Contoso 可同意的多租用戶應用程式**  (或簡稱「已同意的應用程式」)。這是「組織所開發之多租用戶應用程式」的缺點。 當另一個組織開發了多租用戶應用程式時，您組織的使用者可以同意應用程式並進行登入。
    - **Microsoft 第一方應用程式** - 代表 Microsoft 服務的應用程式。 您註冊服務的事實便代表同意。 某些第一方應用程式有時候會有在建立關於應用程式的存取原則時常會使用的特殊 UX 和邏輯。</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **預先整合的應用程式** - Azure AD 應用程式庫中可用的應用程式，您可以新增至目錄以對熱門的 SaaS 應用程式提供單一登入 (以及在某些情況下提供佈建)。
    - **Azure AD 單一登入**：可透過 SAML 2.0 或 OpenID Connect 等受支援登入通訊協定來與 Azure AD 整合之應用程式的「真實」SSO。 精靈會引導您完成設定。
    - **密碼單一登入**：Azure AD 會安全地儲存應用程式的使用者認證，並由 Azure AD 應用程式存取瀏覽器延伸模組將認證「插入」至登入表單。 也稱為「密碼儲存庫存」。

## <a name="permissions"></a>權限

在註冊應用程式時，執行應用程式註冊的使用者 (也就是開發人員) 會定義應用程式需要存取的權限，以及哪些資源  (資源本身會定義為其他應用程式)。比方說，建置郵件讀取程式應用程式的某人會指出，其應用程式需要「Office 365 Exchange Online」資源的「以登入使用者身分存取信箱」的權限︰
    
![](media/active-directory-apps-permissions-consent/apps6.png)

為了讓某個應用程式 (用戶端) 從另一個應用程式 (資源) 要求特定權限，資源應用程式的開發人員會定義存在的權限。 在我們的範例中，「Office 365 Exchange Online」資源應用程式的擁有者 (Microsoft) 已定義名為「以登入使用者身分存取信箱」的權限。

在定義權限時，應用程式開發人員必須定義是否可以同意權限，或權限是否需要系統管理員同意。 這可讓開發人員允許使用者自行同意只要求了低敏感度權限的應用程式，但敏感度更高的權限則需要系統管理員同意。 比方說，「Azure Active Directory」資源應用程式已定義完成，因此使用者可以同意要求有限唯讀權限的應用程式。  不過，行使系統管理員同意則需要完整讀取權限和所有寫入權限。

因為原生用戶端未經過驗證，因此定義為原生用戶端應用程式的應用程式只能要求委派的權限。 這表示在取得權杖時一律必須由實際使用者涉入。 在取得存取權杖時，Web 應用程式和 Web API (機密用戶端) 一律必須向 Azure AD 驗證。 這表示它們也有可能會要求僅限應用程式的權限。 例如，如果某個後端服務需要向另一個後端服務驗證。 要求僅限應用程式權限的應用程式一律需要系統管理員的同意。

摘要︰



- 應用程式 (用戶端) 會指出其所需的其他應用程式 (資源) 權限。
- 應用程式 (資源) 會指出要對其他應用程式 (用戶端) 公開哪些權限。
- 權限可以是僅限應用程式權限或委派的權限。
- 委派的權限可以標示為「允許使用者同意」或「需要系統管理員同意」。
- 應用程式可以做為用戶端 (藉由宣告其需要資源的權限)、做為資源 (藉由宣告其公開的權限) 或同時做為兩者。

## <a name="controls"></a>控制

以下是所有這種行為可用之不同系統管理員控制的清單。 在傳統入口網站中，可以透過在目錄下進行設定，來存取系統管理員控制。

![](media/active-directory-apps-permissions-consent/apps7.png)

在 Azure 入口網站中，於 [管理]、[使用者設定] 下。

![](media/active-directory-apps-permissions-consent/apps11.png)



- 您可以控制使用者是否可以同意應用程式︰

在傳統入口網站中，選取 [使用者可以賦予應用程式存取其資料的權限]。
![](media/active-directory-apps-permissions-consent/apps8.png)

在 Azure 入口網站中，選取 [使用者可以允許應用程式存取其資料]。
![](media/active-directory-apps-permissions-consent/apps12.png)



- 您可以控制使用者是否可以註冊自己的單一租用戶 LOB 應用程式︰在傳統入口網站中，選取 [使用者可以新增整合的應用程式]。
![](media/active-directory-apps-permissions-consent/apps9.png)

在 Azure 入口網站中，選取 [使用者可以允許應用程式存取其資料]。
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>即使您已允許使用者註冊單一租用戶 LOB 應用程式，所能註冊的項目仍會有限制。  
>例如，不是目錄管理員的開發人員。
>
>- 使用者無法讓單一租用戶應用程式變成多租用戶應用程式。
>- 在註冊單一租用戶 LOB 應用程式時，使用者無法要求其他應用程式的僅限應用程式權限。
>- 在註冊單一租用戶 LOB 應用程式時，如果其他應用程式的委派權限需要系統管理員同意，使用者就無法要求這些權限。
>- 使用者無法變更他們未擁有的應用程式。



- 您可以控制使用者是否可以自行新增使用密碼 SSO 的預先整合應用程式 (也稱為「密碼儲存庫存」) ![](media/active-directory-apps-permissions-consent/apps10.png)



- 您可以控制在哪些情況下可以存取應用程式 (也就是條件式存取)。 請注意，這一點同時適用於用戶端應用程式和資源應用程式。 因此，假設您設定了條件式存取原則，其指出「Office 365 Exchange Online」應用程式只能從相容的機器存取。  當使用者嘗試使用會要求 Exchange Online 權限的用戶端應用程式時，此原則也會適用。



- 您可以看到已同意的應用程式以及正在使用的應用程式。

1.  當使用者同意應用程式時，租用戶中會建立 ServicePrincipal 物件。 稽核報告中包含 ServicePrincipal 建立。
2.  使用者登入活動報告會告訴您使用者所登入的應用程式。 

## <a name="example"></a>範例

舉例來說，我們來看看您已經注意到租用戶中之使用者所登入的「FabrikamMail for Office 365」應用程式。 「FabrikamMail」是 Android 版的郵件讀取程式應用程式，由「Fabrikam, Inc.」所發行。 這屬於「其他人開發、且 Contoso 可同意的多租用戶應用程式」。

如果允許使用者同意，系統會在他們第一次登入時看到同意提示︰![](media/active-directory-apps-permissions-consent/apps14.png)

「存取您的信箱」是「Office 365 Exchange Online」(也就是 Exchange) 所公開之「以登入使用者身分存取信箱」權限的使用者端同意字串。

藉由查閱在註冊 Office 365 時所新增的 Exchange ServicePrincipal 物件 (資源)，您可以看到權限。 您可以將 ServicePrincipal 物件看作是應用程式在租用戶中的「執行個體」，其可用來記錄不同的選項和組態。  您可以在 PowerShell 中使用 `Get-AzureADServicePrincipal` 來查看此項目。

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

當使用者按一下 [接受] 時，便會起始同意。 首先，「FabrikamMail for Office 365」的 ServicePrincipal 物件會建立於租用戶中。 ServicePrincipal 看起來像這樣︰

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

同意應用程式會在下列項目之間建立 Oauth2PermissionGrant 連結︰
  
- 使用者物件
- 用戶端應用程式 ServicePrincipalName (SPN)
- 資源應用程式 ServicePrincipalName (SPN)
- 資源應用程式中的權限。  

若為 FabrikamMail，它看起來像這樣︰

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** 是 FabrikamMail 的服務主體物件識別碼 (剛剛建立的)，**PrincipalId** 是 (已同意之使用者的) 使用者物件識別碼，**ResourceId** 是 Exchange 的服務主體物件識別碼，Scope 是已同意之 Exchange 中的權限)。

如果不允許使用者同意，他們就會看到一個畫面，指出需要有權限。

