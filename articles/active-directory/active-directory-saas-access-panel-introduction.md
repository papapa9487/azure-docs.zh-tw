---
title: "什麼是 Azure Active Directory 中的存取面板？ | Microsoft Docs"
description: "了解如何使用各種存取面板 (網頁瀏覽器、Android 應用程式、iPhone 和 iPad 應用程式) 存取 SaaS 應用程式。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a63e2d583e1203708e49c0fcef99876d2055343a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="what-is-the-access-panel"></a>什麼是存取面板？

存取面板是網頁型入口網站。 它可讓在 Azure Active Directory 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 您也可以透過存取面板使用自助群組和應用程式管理功能。

存取面板與 Azure 入口網站分開，您不需要具備 Azure 訂用帳戶也能使用。

![存取面板][1]

存取面板可讓您編輯某些設定檔設定，包括以下功能：

- 變更與公司或學校帳戶相關聯的密碼

- 編輯密碼重設設定

- 編輯與 Multi-Factor Authentication 相關的連絡人和喜好設定的設定 (系統管理員已要求使用此功能的帳戶)

- 檢視帳戶詳細資料，例如，使用者識別碼、替代電子郵件、行動電話和辦公室電話號碼以及裝置

- 檢視和啟動 Azure AD 系統管理員已授權存取的雲端式應用程式。 如需以使用者觀點為主的存取面板詳細資訊，請參閱使用存取面板。 

- 自我管理群組。 更具體地說，系統管理員可以在 Azure AD 中建立及管理安全性群組，並要求安全性群組成員資格。 如需詳細資訊，請參閱 [Azure AD 中使用者的自助群組管理](active-directory-accessmanagement-self-service-group-management.md)與[管理群組](active-directory-manage-groups.md)。




## <a name="accessing-the-access-panel"></a>存取存取面板

您可以在網頁瀏覽器中瀏覽以下 URL，以存取「存取面板」：`http://myapps.microsoft.com`

如果您已經為您的登入頁面設定自訂商標，您可以將您組織的網域附加到 URL 結尾以載入此商標：`http://myapps.microsoft.com/<your domain>.com`

在此情況下，您可以使用已在 Azure 入口網站中設定的任何作用中或已驗證網域名稱。

![Wingtip Toys 網域名稱][2]  

您必須將 URL 散佈給要登入與 Azure AD 整合之應用程式的所有使用者。

## <a name="authentication"></a>驗證

若要觸達存取面板，您必須透過 Azure AD 中的公司或學校帳戶進行驗證。 Azure AD 可以直接驗證您。 或者，如果組織已經使用 Active Directory 同盟服務 (ADFS) 或其他技術設定同盟，則可由 Windows Server Active Directory 驗證您。

如果您具備 Azure 或 Office 365 的訂用帳戶，而且已在使用 Azure 入口網站或 Office 365 應用程式，您可以看到應用程式清單，而不需再次登入。 如果您未經過驗證，系統會提示您在 Azure AD 中使用您帳戶的使用者名稱和密碼進行登入。 如果您的組織已設定同盟，則輸入使用者名稱已經足夠。

經過驗證後，您就能夠與由系統管理員整合到目錄的應用程式互動。 若要了解如何整合應用程式與 Azure AD，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="web-browser-requirements"></a>網頁瀏覽器需求

存取面板至少需要有支援 JavaScript 且啟用 CSS 的瀏覽器。 若要讓使用者透過密碼單一登入 (SSO) 登入應用程式，必須在您的瀏覽器中安裝存取面板擴充功能。 當您選取已設定密碼型 SSO 的應用程式時，系統就會自動下載此擴充功能。

存取面板延伸模組目前適用於 Internet Explorer 8 和更新版本、Edge、Chrome 與 Firefox 瀏覽器。

## <a name="mobile-app-support"></a>行動應用程式支援

Azure Active Directory 團隊發佈 My Apps 行動應用程式。 當您安裝此應用程式時，可以在 iOS 和 Android 裝置上登入密碼 SSO 應用程式。

> [!NOTE]
> 您不需要外掛程式或行動應用程式，就能在幾乎任何裝置的任何網頁瀏覽器上，登入支援與 Azure AD 同盟的應用程式 (包括 Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 和其他 70 種以上的應用程式)。 同時也不需要在行動裝置上使用 My Apps 行動應用程式，就能[體驗其餘存取面板](https://myapps.microsoft.com/)的功能。
>
>

### <a name="my-apps-for-android"></a>My Apps for Android

執行 Android 4.1 版和更新版本的任何 Android 裝置支援 My Apps for Android。  
已在 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.myapps)中提供。

![My Apps for Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps for iPhone 和 iPad

執行 iOS 版本 7 和更新版本的任何 iPhone 或 iPad 均支援 My Apps for iOS。  
已在 [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) 中提供。

![My Apps for iOS][4]    



## <a name="managed-browser-for-my-apps"></a>My Apps 適用的受管理瀏覽器

My Apps 也已在 Intune Managed Browser 中整合。 確保行動裝置上的資料安全無虞時，適用於 iOS 和 Android 裝置的 Intune Managed Browser 扮演重要角色。 它可讓您安全地檢視和瀏覽可能包含公司資訊的網頁，並提供安全的網頁瀏覽經驗。  
您可在 Managed Browser 首頁上以及您的書籤中尋求快速存取 My Apps，只有按幾下滑鼠就可以觸達您想要存取的任何應用程式。

已在 [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 和 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en)中提供。

![My Apps 適用的受管理瀏覽器][5]    





## <a name="tips-for-testing-the-user-experience"></a>測試使用者體驗的秘訣

如果您是 Azure 系統管理員，而且已使用目錄中的帳戶登入 Azure 入口網站，則會自動以目前的帳戶登入存取面板。 在此情況下，您可以看到已指派給您的所有應用程式。

**若要以「不同」的使用者帳戶測試：**

1. 按一下 Azure 入口網站或存取面板右上角的使用者功能表，然後選取 [登出]。 
2. 前往[存取面板](http://myapps.microsoft.com)。
3. 在登入頁面上，針對您在目錄中想要測試的帳戶，輸入使用者名稱和密碼。


## <a name="starting-applications"></a>啟動應用程式

存取面板上可能出現許多種應用程式。

### <a name="office-365-applications"></a>Office 365 應用程式

如果組織使用 Office 365 應用程式，且您已取得其授權，則 Office 365 應用程式會出現在您的存取面板上。

當您按一下 Office 365 應用程式的應用程式圖格時，系統會將您重新導向至該應用程式，並自動登入。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>已設定同盟 SSO 的 Microsoft 及第三方應用程式

系統管理員可以在 Azure 入口網站的 Active Directory 區段中新增應用程式，並將 SSO 模式設定為 [Azure AD 單一登入]。 只有當系統管理員已明確授權您存取應用程式時，您才能看到這些應用程式。

當您按一下其中一個應用程式的圖格時，系統會將您重新導向並自動登入該應用程式。

### <a name="password-based-sso-without-identity-provisioning"></a>不含身分識別佈建的密碼 SSO

系統管理員可以在 Azure 入口網站的 Active Directory 區段中新增應用程式，並將 SSO 模式設定為 [密碼單一登入]。 目錄中的所有使用者都可以看到已設定為此模式的所有應用程式。

您第一次按一下其中一個應用程式的圖格時，系統會提示您在 Internet Explorer 或 Chrome 中安裝密碼 SSO 外掛程式。 安裝之後，您可能需要重新啟動網頁瀏覽器。 當您返回存取面板，再次按一下應用程式圖格時，系統會提示您針對應用程式輸入使用者名稱和密碼。 您已輸入使用者名稱和密碼之後，這些認證會安全地儲存並連結至您在 Azure AD 中的帳戶。

您下次按一下應用程式圖格時，您將會自動登入應用程式。  
您不需要再次輸入認證，或安裝密碼 SSO 外掛程式。

如果您的認證已在目標第三方應用程式中變更，那麼您也必須更新儲存在 Azure AD 中的認證。 

**若要更新認證：**

1. 選取應用程式圖格上的圖示。
2. 選取 [更新認證]，重新輸入應用程式的使用者名稱和密碼。


### <a name="password-based-sso-with-identity-provisioning"></a>含身分識別佈建的密碼 SSO

系統管理員可以在 Azure 入口網站的 [Active Directory] 區段中新增應用程式，並將 SSO 模式設定為 [密碼單一登入]，同時佈建身分識別。

您第一次按一下其中一個應用程式的應用程式圖格時，系統會提示您安裝**適用於 Internet Explorer 或 Chrome 的密碼 SSO 外掛程式**。 安裝之後，您可能需要重新啟動網頁瀏覽器。  
當您返回存取面板，再次按一下應用程式圖格時，您會自動登入應用程式。

有些應用程式可能會要求您在第一次登入時變更密碼。 如果您的認證已在目標第三方應用程式中變更，那麼您也必須更新儲存在 Azure AD 中的認證。 

**若要更新認證：**

1. 選取應用程式圖格上的圖示。
2. 選取 [更新認證]，重新輸入應用程式的使用者名稱和密碼。


### <a name="application-with-existing-sso-solutions"></a>含現有 SSO 解決方案的應用程式

若要為應用程式設定 SSO，Azure 入口網站會提供稱為 [現有的單一登入] 的第三個選項。 這個選項可讓系統管理員建立應用程式的連結，並將連結放在選定使用者的存取面板上。

例如，如果應用程式設定為使用 AD FS 2.0 來驗證使用者，系統管理員可以使用 [現有的單一登入] 選項，在存取面板上建立該應用程式的連結。 當您存取連結時，將會透過 AD FS 2.0 或應用程式提供的任何現有 SSO 解決方案來驗證您。


## <a name="next-steps"></a>後續步驟

- 若要查看與應用程式管理相關的所有主題清單，請參閱 [Azure Active Directory 中的應用程式管理文章索引](active-directory-apps-index.md)。
 
- 若要深入了解如何將 SaaS 應用程式整合到 Azure AD 中，請參閱[有關如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)。
 
- 若要深入了解如何使用 Azure AD 管理應用程式，請參閱[單一登入及使用 Azure Active Directory 管理應用程式存取的簡介](active-directory-appssoaccess-whatis.md)。
 
- 若要深入了解使用者佈建，請參閱 [SaaS 應用程式的自動化使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)。

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
