---
title: "在 Azure Active Directory 中管理企業應用程式的單一登入 | Microsoft Docs"
description: "從 Azure Active Directory 應用程式庫管理公司內企業應用程式的單一登入設定"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: 882180a553fffee05a612cf70ddd9a0f30108415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>管理企業應用程式的單一登入

本文說明如何使用 [Azure 入口網站](https://portal.azure.com)來管理企業應用程式的單一登入設定。 企業應用程式是您組織內部署和使用的應用程式。 本文特別適用於從 [Azure Active Directory 應用程式庫](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)新增的應用程式。 

## <a name="finding-your-apps-in-the-portal"></a>在入口網站中尋找您的應用程式
在 Azure 入口網站中可以檢視及管理針對單一登入設定的所有企業應用程式。 這些應用程式可在入口網站的 [更多服務] &gt; [企業應用程式] 區段中找到。 

![企業應用程式刀鋒視窗][1]

選取 [所有應用程式] 以檢視已設定的所有應用程式清單。 選取應用程式會顯示該應用程式的資源，您可以在其中檢視該應用程式的報告，且可管理各種設定。

若要管理單一登入設定，請選取 [單一登入] 。

![應用程式資源刀鋒視窗][2]

## <a name="single-sign-on-modes"></a>單一登入模式
[單一登入] 一開始是 [模式] 功能表，可在此設定單一登入模式。 可用的選項包括：

* **SAML 型登入** - 如果應用程式支援使用 SAML 2.0 通訊協定、WS 服務同盟或 OpenID connect 通訊協定的 Azure Active Directory 完整同盟單一登入，便可使用此選項。
* **密碼型登入** - 如果 Azure AD 支援此應用程式的密碼表單填入，便可使用此選項。
* **連結型登入** - 之前稱為「現有單一登入」，此選項可讓管理員將此應用程式的連結放在其使用者的 Azure AD 存取面板或 Office 365 應用程式啟動程式中。

如需這些模式的詳細資訊，請參閱 [單一登入如何搭配 Azure Active Directory 運作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。

## <a name="saml-based-sign-on"></a>SAML 型登入
**SAML 型登入** 選項分成四個部分︰

### <a name="domains-and-urls"></a>網域和 URL
這個部分顯示已加入您的 Azure AD 目錄的應用程式網域和 URL 的所有詳細資訊。 應用程式單一登入運作所需的所有輸入都會直接顯示在畫面上，選取 [顯示進階 URL 設定]  核取方塊可以檢視所有選擇性輸入。 支援的輸入有︰

* **登入 URL** – 使用者將由此處登入此應用程式。 如果應用程式設定為執行服務提供者起始單一登入，則當使用者開啟此 URL，服務提供者會重新導向至 Azure AD，以進行驗證並將使用者登入。 
  * 如果已填入此欄位，Azure AD 將使用此 URL 從 Office 365 和 Azure AD 存取面板中啟動應用程式。
  * 如果略過這個欄位，則 Azure AD 會改為執行身分識別提供者 - 即從 Office 365、Azure AD 存取面板或 Azure AD 單一登入 URL 啟動應用程式時起始登入。
* **識別碼** - 此 URI 應專門用於識別正在設定單一登入的應用程式。 Azure AD 會將此值傳送回應用程式做為 SAML 權杖的 Audience 參數值，應用程式預期會驗證它。 在應用程式中提供的任何 SAML 中繼資料中，此值也會顯示為實體識別碼。
* **回覆 URL** -回覆 URL 是應用程式預期接收 SAML 權杖的位置。 這也稱為判斷提示取用者服務 (ACS) URL。 在輸入這些資料後，請按 [下一步] 繼續前往下一個畫面。 此畫面會提供相關資訊來說明在應用程式端需要進行哪些設定，才能使應用程式接受來自於 Azure AD 的 SAML 權杖。
* **轉送狀態** - 轉送狀態是選擇性參數，可幫助告知應用程式在驗證完成後，將使用者重新導向的位置。 此值通常是對應用程式有效的 URL，不過有些應用程式以不同方式使用此值 (詳細資訊請參閱應用程式的單一登入相關文件)。 設定轉送狀態的功能是新 Azure 入口網站獨有的新功能。

### <a name="user-attributes"></a>使用者屬性
這個部分可供管理員檢視及編輯 SAML 權杖中傳送的屬性，而此權杖是在使用者每次登入時由 Azure AD 簽發給應用程式。

唯一支援的可編輯屬性是 **User Identifier** 屬性。 這個屬性的值是 Azure AD 中可唯一識別應用程式中每個使用者的欄位。 例如，如果應用程式為使用電子郵件地址做為使用者名稱和唯一識別碼，則此值會設為 Azure AD 中的 user.mail 欄位。

### <a name="saml-signing-certificate"></a>SAML 簽署憑證
這個部分顯示 Azure AD 用來簽署 SAML 權杖 (於每次使用者驗證時簽發給應用程式) 的憑證詳細資料。 可用來檢查目前憑證的內容，包括到期日。

### <a name="application-configuration"></a>應用程式組態
最後一個部分提供文件和/或設定應用程式本身使用 Azure Active Directory 做為身分識別提供者所需的控制項。

[設定應用程式]  彈出式功能表提供新的、簡潔、內嵌的應用程式設定指示。 這是新 Azure 入口網站另一個獨有的新功能。

> [!NOTE]
> 如需內嵌文件的完整範例，請參閱 Salesforce.com 應用程式。 將持續新增其他應用程式的文件。
> 
> 

![內嵌的文件][3]

## <a name="password-based-sign-on"></a>密碼型登入
如果應用程式支援此種登入，選取密碼型 SSO 模式，然後選取 [儲存]  ，即可立即將它設定為進行密碼型 SSO。 如需部署密碼型 SSO 的詳細資訊，請參閱 [單一登入如何搭配 Azure Active Directory 運作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。

![密碼型登入][4]

## <a name="linked-sign-on"></a>連結型單一登入
如果應用程式支援此種登入，選取連結的 SSO 模式可讓您輸入 URL，做為當使用者按一下此應用程式時您想要 Azure AD 存取面板或 Office 365 重新導向的目標。 如需連結型 SSO (以前稱為「現有 SSO」) 的詳細資訊，請參閱 [單一登入如何搭配 Azure Active Directory 運作](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)。

![連結型單一登入][5]

##<a name="feedback"></a>意見反應

我們希望您喜歡使用改進後的 Azure AD 經驗。 請繼續提供意見反應！ 請將您的意見反應和改進想法張貼在我們的[意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)的**管理員入口網站**區段中。  我們每天都很期待發展酷炫的新功能，並依照您的指導來塑造和定義我們接下來所要發展的項目。

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
