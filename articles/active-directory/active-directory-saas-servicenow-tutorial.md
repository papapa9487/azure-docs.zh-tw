---
title: "教學課程：Azure Active Directory 與 ServiceNow 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 ServiceNow 和 ServiceNow Express 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>教學課程：Azure Active Directory 與 ServiceNow 整合
在本教學課程中，您會了解如何整合 ServiceNow and ServiceNow Express 與 Azure Active Directory (Azure AD)。

ServiceNow 和 ServiceNow Express 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 ServiceNow 和 ServiceNow Express 的人員
* 您可以讓您的使用者使用他們的 Azure AD 帳戶自動登入 ServiceNow 和 ServiceNow Express (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 ServiceNow 和 ServiceNow Express 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 若為 ServiceNow，ServiceNow 的執行個體或租用戶 (Calgary 版本或更新版本)
* 若為 ServiceNow Express，ServiceNow Express 的執行個體 (Helsinki 版本或更新版本)
* ServiceNow 租用戶必須啟用[多個提供者單一登入外掛程式](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 [提交服務要求](https://hi.service-now.com)即可達到此目的。 

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 ServiceNow
2. 為 ServiceNow 或 ServiceNow Express 設定和測試 Azure AD 單一登入

## <a name="adding-servicenow-from-the-gallery"></a>從資源庫新增 ServiceNow
若要設定 ServiceNow 或 ServiceNow Express 與 Azure AD 整合，您需要從資源庫將 ServiceNow 新增到受管理的 SaaS 應用程式清單。 

**若要從資源庫新增 ServiceNow，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]
6. 在搜尋方塊中，輸入 **ServiceNow**。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. 在結果窗格中，選取 [ServiceNow]，然後按一下 [完成] 來新增應用程式。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試透過 ServiceNow 或 ServiceNow Express 使用 Azure AD 單一登入功能。

若要讓單一登入運作，Azure AD 必須知道 ServiceNow 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 ServiceNow 中的相關使用者之間建立連結關聯性。
建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 ServiceNow 中 **Username** 的值。 若要設定及測試透過 ServiceNow 使用 Azure AD 單一登入功能，您需要完成下列建置組塊：

1. **[針對 ServiceNow 設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on-for-servicenow)** - 讓您的使用者能夠使用此功能。
2. **[針對 ServiceNow Express 設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - 讓您的使用者能夠使用此功能。
3. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[建立 ServiceNow 測試使用者](#creating-a-servicenow-test-user)** - 使 ServiceNow 中 Britta Simon 的對應使用者連結到她在 Azure AD 中的代表項目。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
6. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

> [!NOTE]
> 如果您想要設定 ServiceNow，請略過步驟 2。 同樣地，如果您想要設定 ServiceNow Express，請略過步驟 1。
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>為 ServiceNow 設定 Azure AD 單一登入
1. 在 Azure AD 傳統入口網站的 [ServiceNow] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2. 在 [您希望使用者如何登入 ServiceNow] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3. 在 [設定應用程式設定]  頁面上，執行下列步驟：
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")
   
    a. 在 [ServiceNow 登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入 ServiceNow 應用程式的 URL：`https://<instance-name>.service-now.com`。
   
    b. 在 [識別碼] 文字方塊中，使用下列模式輸入使用者用來登入 ServiceNow 應用程式的 URL：`https://<instance-name>.service-now.com`。
   
    c. 按一下 [下一步] 

4. 若要讓 Azure AD 自動設定 ServiceNow 以便進行 SAML 型驗證，請在 [自動設定單一登入] 表單上輸入您的 ServiceNow 執行個體名稱、系統管理員使用者名稱和系統管理員密碼，然後按一下 [設定]。 請注意，提供的系統管理員使用者名稱必須在 ServiceNow 中指派 **security_admin** 角色，才能運作。 否則，若要手動設定 ServiceNow 以使用 Azure AD 做為 SAML 識別提供者，請按一下 [手動設定應用程式以進行單一登入]，然後按 [下一步] 並完成下列步驟。
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "設定應用程式 URL")

5. 在 [在 ServiceNow 設定單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

6. 以系統管理員身分登入您的 ServiceNow 應用程式。

7. 遵循下一個步驟，啟動 [整合 - 多個提供者單一登入安裝程式] 外掛程式︰
   
    a. 在左側導覽窗格中，移至 [系統定義] 區段，然後按一下 [外掛程式]。
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "啟動外掛程式")
   
    b. 搜尋*整合 - 多個提供者單一登入安裝程式*。
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "啟動外掛程式")
   
    c. 選取外掛程式。 按一下滑鼠右鍵並選取 [啟動/升級]。
   
    d. 按一下 [啟用] 按鈕。

8. 在左側的導覽窗格中，按一下 [屬性] 。  
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "設定應用程式 URL")

9. 在 [多個提供者 SSO 內容]  對話方塊上，執行下列步驟：
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "設定應用程式 URL")
   
    a. 針對 [啟用多個提供者 SSO]，選取 [是]。
   
    b. 針對 [啟用偵錯記錄有多個提供者 SSO 整合]，選取 [是]。
   
    c. 在 [使用者資料表上的欄位...] 文字方塊中，輸入 **user_name**。
   
    d. 按一下 [儲存] 。

10. 在左側的導覽窗格中，按一下 [x509 憑證] 。
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "設定單一登入")

11. 在 [X.509 憑證] 對話方塊中，按一下 [新增]。
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "設定單一登入")

12. 在 [X.509 憑證]  對話方塊中，執行下列步驟：
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "設定單一登入")
    
     a. 按一下 [新增] 。
    
     b. 在 [名稱] 文字方塊中，輸入您的設定名稱 (例如：**TestSAML2.0**)。
    
     c. 選取 [使用中] 。
    
     d. 針對 [格式]，選取 [PEM]。
    
     e. 針對 [類型]，選取 [信任存放區憑證]。
    
     f. 在記事本中開啟您的 Base64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [PEM 憑證] 文字方塊。
    
     g. 按一下 [更新] 。

13. 在左側的導覽窗格中，按一下 [識別提供者] 。
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "設定單一登入")

14. 在 [身分識別提供者] 對話方塊中，按一下 [新增]：
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "設定單一登入")

15. 在 [身分識別提供者] 對話方塊中，按一下 [SAML2 更新 1]：
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "設定單一登入")

16. 在 [SAML2 Update1 內容] 對話方塊上，執行下列步驟：
    
     ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "設定單一登入")

    a. 在 [名稱] 文字方塊中，輸入您的設定名稱 (例如：**SAML 2.0**)。

    b. 在 [使用者欄位] 文字方塊中，輸入 **email** 或 **user_name**，這取決於會用哪一個欄位來唯一識別 ServiceNow 部署中的使用者。 

    > [!NOTE] 
    > 移至 Azure 傳統入口網站的 [ServiceNow] > [屬性] > [單一登入] 區段，並將所要的欄位對應至 [nameidentifier] 屬性，即可設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址做為 SAML 權杖中的唯一識別碼。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值

    c. 在 Azure AD 傳統入口網站中，複製 [識別提供者 ID] 值，然後將它貼至 [識別提供者 URL] 文字方塊中。

    d. 在 Azure AD 傳統入口網站中，複製 [驗證要求 URL] 值，然後將它貼至 [識別提供者的 AuthnRequest] 文字方塊中。

    e. 在 Azure AD 傳統入口網站中，複製 [單一登出服務 URL] 值，然後將它貼至 [識別提供者的 SingleLogoutRequest] 文字方塊中。

    f. 在 [ServiceNow 首頁] 文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。

    > [!NOTE] 
    > 串連您的 [ServieNow 租用戶 URL] 和 **/navpage.do** (例如：`https://fabrikam.service-now.com/navpage.do`)，就是 ServiceNow 執行個體首頁。

    g. 在 [對象識別碼/核發者] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。

    h. 在 [對象 URL] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。 

    i. 在 [IDP 的 SingleLogoutRequest 通訊協定繫結] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。

    j. 在 [名稱識別碼原則] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。

    k. 取消選取 [建立 AuthnContextClass]。

    l. 在 **AuthnContextClassRef 方法**中，輸入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。 只有您是僅限雲端組織時才需要此值。 如果您使用內部部署 ADFS 或 MFA 進行驗證，則不應該設定這個值。 

    m. 在 [時鐘誤差] 文字方塊中，輸入 **60**。

    n. 針對**單一登入指令碼**，選取 **MultiSSO_SAML2_Update1**。

    o. 針對 **x509 憑證**，選取您在上一個步驟中建立的憑證。

    p. 按一下 [提交] 。 

1. 在 Azure AD 傳統入口網站上，選取單一登入設定確認，然後按一下 [下一步] 。 
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "設定單一登入")

2. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "設定單一登入")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>為 ServiceNow Express 設定 Azure AD 單一登入
1. 在 Azure AD 傳統入口網站的 [ServiceNow] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749323.png "設定單一登入")

2. 在 [您希望使用者如何登入 ServiceNow] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749324.png "設定單一登入")

3. 在 [設定應用程式設定]  頁面上，執行下列步驟：
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "設定應用程式 URL")
   
    a. 在 [ServiceNow 登入 URL] 文字方塊中，使用下列模式輸入使用者用來登入 ServiceNow 應用程式的 URL：`https://<instance-name>.service-now.com`。
   
    b. 在 [簽發者 URL] 文字方塊中，使用下列模式輸入使用者用來登入 ServiceNow 應用程式的 URL：`https://<instance-name>.service-now.com`。
   
    c. 按一下 [下一步] 

4. 按一下 [手動設定應用程式以進行單一登入]，然後按 [下一步] 並完成下列步驟。
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "設定應用程式 URL")

5. 在 [在 ServiceNow 設定單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC749325.png "設定單一登入")

6. 以系統管理員身分登入您的 ServiceNow Express 應用程式。

7. 在左側導覽窗格中按一下 [單一登入]。  
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "設定應用程式 URL")

8. 在 [單一登入] 對話方塊中，按一下右上方的 [設定] 圖示，然後設定下列屬性︰
   
    ![設定應用程式 URL](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "設定應用程式 URL")
   
    a. 將 [啟用多個提供者 SSO] 切換到右邊。
   
    b. 將 [啟用偵錯記錄以供多個提供者 SSO 整合] 切換到右邊。
   
    c. 在 [使用者資料表上的欄位...] 文字方塊中，輸入 **user_name**。
9. 在 [單一登入] 對話方塊上，按一下 [新增憑證]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "設定單一登入")
10. 在 [X.509 憑證]  對話方塊中，執行下列步驟：
    
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "設定單一登入")
    
    a. 在 [名稱] 文字方塊中，輸入您的設定名稱 (例如：**TestSAML2.0**)。
    
    b. 選取 [使用中] 。
    
    c. 針對 [格式]，選取 [PEM]。
    
    d. 針對 [類型]，選取 [信任存放區憑證]。
    
    e. 從您下載的憑證建立 Base64 編碼的檔案。
    
    > [!NOTE]
    > 如需詳細資訊，請參閱 [如何將二進位憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。
    > 
    > 
    
    f. 在記事本中開啟您的 Base64 編碼的憑證，將其內容複製到剪貼簿，然後貼到 [PEM 憑證] 文字方塊。
    
    g. 按一下 [更新] 。
11. 在 [單一登入] 對話方塊上，按一下 [新增 IdP]。
    
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "設定單一登入")
12. 在 [新增識別提供者] 對話方塊的 [設定識別提供者] 之下，執行下列步驟：
    
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "設定單一登入")

    a. 在 [名稱] 文字方塊中，輸入您的組態名稱 (例如：**SAML 2.0**)。

    b. 在 Azure AD 傳統入口網站中，複製 [識別提供者 ID] 值，然後將它貼至 [識別提供者 URL] 文字方塊中。

    c. 在 Azure AD 傳統入口網站中，複製 [驗證要求 URL] 值，然後將它貼至 [識別提供者的 AuthnRequest] 文字方塊中。

    d. 在 Azure AD 傳統入口網站中，複製 [單一登出服務 URL] 值，然後將它貼至 [識別提供者的 SingleLogoutRequest] 文字方塊中。

    e. 針對 [識別提供者憑證]，選取您在上一個步驟中建立的憑證。


1. 按一下 [進階設定]，然後在 [其他識別提供者屬性] 之下，執行下列步驟︰
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "設定單一登入")
   
    a. 在 [IDP 的 SingleLogoutRequest 通訊協定繫結] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**。
   
    b. 在 [名稱識別碼原則] 文字方塊中，輸入 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**。    
   
    c. 在 **AuthnContextClassRef 方法**中，輸入 **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**。
   
    d. 取消選取 [建立 AuthnContextClass]。

2. 在 [其他服務提供者屬性] 之下，執行下列步驟︰
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "設定單一登入")
   
    a. 在 [ServiceNow 首頁] 文字方塊中，輸入您的 ServiceNow 執行個體首頁的 URL。
   
    > [!NOTE]
    > 串連您的 [ServieNow 租用戶 URL] 和 **/navpage.do** (例如：`https://fabrikam.service-now.com/navpage.do`)，就是 ServiceNow 執行個體首頁。
    > 
    > 
   
    b. 在 [對象識別碼/核發者] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。
   
    c. 在 [對象 URI] 文字方塊中，輸入您的 ServiceNow 租用戶 URL。 
   
    d. 在 [時鐘誤差] 文字方塊中，輸入 **60**。
   
    e. 在 [使用者欄位] 文字方塊中，輸入 **email** 或 **user_name**，這取決於會用哪一個欄位來唯一識別 ServiceNow 部署中的使用者。
   
    > [!NOTE]
    > 移至 Azure 傳統入口網站的 [ServiceNow] > [屬性] > [單一登入] 區段，並將所要的欄位對應至 [nameidentifier] 屬性，即可設定 Azure AD 以發出 Azure AD 使用者識別碼 (使用者主體名稱) 或電子郵件地址做為 SAML 權杖中的唯一識別碼。 所選屬性儲存在 Azure AD 中的值 (例如使用者主體名稱) 必須符合所輸入欄位 (例如 user_name) 儲存在 ServiceNow 中的值
    > 
    > 
   
    f. 按一下 [儲存] 。 

3. 在 Azure AD 傳統入口網站上，選取單一登入設定確認，然後按一下 [下一步] 。 
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "設定單一登入")

4. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "設定單一登入")

## <a name="configuring-user-provisioning"></a>設定使用者佈建
本節的目的是要說明如何對 ServiceNow 啟用 Active Directory 使用者帳戶的使用者佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 在 Azure 傳統管理入口網站中的 [ServiceNow] 應用程式整合頁面上，按一下 [設定使用者佈建]。 
   
    ![使用者佈建](./media/active-directory-saas-servicenow-tutorial/IC769498.png "使用者佈建")

2. 在 [輸入您的 ServiceNow 認證來啟用自動使用者佈建] 頁面上，提供以下組態設定：
   
     a. 在 [ServiceNow 執行個體名稱] 字方塊中，輸入 ServiceNow 執行個體名稱。
   
     b. 在 [ServiceNow 管理使用者名稱] 文字方塊中，輸入 ServiceNow 管理員帳戶的名稱。
   
     c. 在 [ServiceNow 管理員密碼] 文字方塊中，輸入這個帳戶的密碼。
   
     d. 按一下 [驗證]  來驗證您的組態。
   
     e. 按 [下一步] 按鈕以開啟 [後續步驟] 頁面。
   
     f. 如果您想要將所有使用者佈建到此應用程式，請選取 [自動將此目錄中的所有使用者帳戶佈建到這個應用程式]。 
   
    ![後續步驟](./media/active-directory-saas-servicenow-tutorial/IC698804.png "後續步驟")
   
     g. 在 [後續步驟] 頁面上，按一下 [完成] 來儲存您的組態。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在傳統入口網站中建立名稱為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按一下 [下一步] 。

6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
   ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
   b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
   c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
   d. 在 [角色] 清單中選取 [使用者]。
   
   e. 按一下 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下 [完成]。   

### <a name="creating-a-servicenow-test-user"></a>建立 ServiceNow 測試使用者
在本節中，您要在 ServiceNow 中建立名為 Britta Simon 的使用者。 在本節中，您要在 ServiceNow 中建立名為 Britta Simon 的使用者。 如果您不知道如何在 ServiceNow 或 ServiceNow Express 帳戶中新增使用者，請連絡 ServiceNow 支援小組。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
在本節中，您會把 ServiceNow 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 ServiceNow，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [ServiceNow]。
   
    ![設定單一登入](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 

4. 在 [所有使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。

當您在 [存取面板] 中按一下 [ServiceNow] 圖格時，應該會自動登入您的 ServiceNow 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
