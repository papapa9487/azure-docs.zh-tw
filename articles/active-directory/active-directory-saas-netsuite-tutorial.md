---
title: "教學課程：Azure Active Directory 與 Netsuite 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Netsuite 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: f2184f426c0e60ea2b6ce029b80d3d63821794d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>教學課程：Azure Active Directory 與 Netsuite 整合

在本教學課程中，您會了解如何整合 Netsuite 與 Azure Active Directory (Azure AD)。

Netsuite 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Netsuite 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Netsuite (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Netsuite 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Netsuite 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Netsuite
2. 設定並測試 Azure AD 單一登入

## <a name="adding-netsuite-from-the-gallery"></a>從資源庫新增 Netsuite
若要設定將 Netsuite 整合到 Azure AD 中，您需要從資源庫將 Netsuite 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Netsuite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **Netsuite**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. 在結果面板中，選取 Netsuite，然後按一下新增 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Netsuite 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Netsuite 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Netsuite 中的相關使用者之間，建立連結關聯性。

建立此連結關聯性的方法，就是將 Azure AD 中**使用者名稱**的值指派為 Netsuite 中 **Username** 的值。

若要設定及測試與 Netsuite 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Netsuite 測試使用者](#creating-a-netsuite-test-user)** - 使 Netsuite 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Netsuite 應用程式中設定單一登入。

**若要設定與 Netsuite 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Netsuite] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. 在 [Netsuite 網域及 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    在 [回覆 URL] 文字方塊中，使用下列模式輸入 URL：`https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > 這些不是真正的值。 請使用實際的「回覆 URL」來更新這些值。 請連絡 [Netsuite 支援小組](http://www.netsuite.com/portal/services/support.shtml)以取得這些值。
 
4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. 在 [Netsuite 組態] 區段上，按一下 [設定 Netsuite] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. 在瀏覽器中開啟新索引標籤，以系統管理員的身分登入您的 Netsuite 公司站台。

8. 在頁面頂端的工具列上，按一下 [設定]，然後按一下 [設定管理員]。

    ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. 從 [設定工作] 清單中，選取 [整合]。

    ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. 在 [管理驗證] 區段中，按一下 [SAML 單一登入]。

    ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. 在 [SAML 設定]  頁面上，執行下列步驟：
   
    a. 從 [設定登入]的 [快速參考] 區段複製 [SAML 單一登入服務 URL] 值，然後貼到 Netsuite 的 [識別提供者登入頁面] 欄位中。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b.這是另一個 C# 主控台應用程式。 在 Netsuite 中，選取 [主要驗證方法] 。

    c. 對標示為 [SAMLV2 身分識別提供者中繼資料] 的欄位，選取 [上傳 IDP 中繼資料檔案]。 然後按一下 [瀏覽] ，上傳您從 Azure 入口網站下載的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. 按一下 [提交] 。

12. 在 Azure AD 中，按一下 [檢視及編輯所有其他使用者屬性] 核取方塊並新增屬性。

    ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. 在 [屬性名稱] 欄位中，輸入 `account`。 在 [屬性值]  欄位中，輸入您的 Netsuite 帳戶識別碼。 這個值是常數，並會隨著帳戶變更。 如何找出帳戶識別碼的指示如下：

      ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. 在 Netsuite 中，從頂端導覽功能表按一下 [設定]。

    b.這是另一個 C# 主控台應用程式。 然後，在左方導覽功能表的 [設定工作] 區段下，選取 [整合] 區段，然後按一下 [Web 服務喜好設定]。

    c. 複製您的 Netsuite 帳戶識別碼，並將它貼到 Azure AD 中的 [屬性值]  欄位。

    ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. 在使用者可以執行單一登入 Netsuite 之前，必須先在 Netsuite 中指派適當的權限。 請依照下列指示來指派這些權限。

    a. 在頂端的瀏覽功能表上，按一下 [設定]，然後按一下 [設定管理員]。
      
      ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b.這是另一個 C# 主控台應用程式。 在左方的瀏覽功能表上，選取 [使用者/角色]，然後按一下 [管理角色]。
      
      ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. 按一下 [新角色] 。

    d. 輸入新角色的 [名稱]，然後選取 [僅單一登入] 核取方塊。
      
      ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. 按一下 [儲存] 。

    f. 在頂端的功能表中，按一下 [權限] 。 然後按一下 [設定] 。
      
       ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. 選取 設定 SAM 單一登入，然後按一下新增。

    h. 按一下 [儲存] 。

    i. 在頂端的瀏覽功能表上，按一下 [設定]，然後按一下 [設定管理員]。
      
       ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. 在左方的瀏覽功能表上，選取 [使用者/角色]，然後按一下 [管理使用者]。
      
       ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. 選取測試使用者。 然後按一下 [編輯] 。
      
       ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. 在 [角色] 對話方塊中，選取您建立的角色，然後按一下 [新增] 。
      
       ![設定單一登入](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. 按一下 [儲存] 。
    
> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 

### <a name="creating-a-netsuite-test-user"></a>建立 Netsuite 測試使用者

本節會在 Netsuite 中建立名為 Britta Simon 的使用者。 Netsuite 支援預設啟用的 Just-In-Time 佈建。
在這一節沒有您需要進行的動作項目。 如果 Netsuite 中還沒有使用者，當您嘗試存取 Netsuite 時，就會建立新的使用者。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Netsuite 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Netsuite，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Netsuite]。

    ![設定單一登入](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

若要測試您的單一登入設定，請開啟位於 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 的存取面板，登入測試帳戶，然後按一下 [Netsuite]。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定使用者佈建](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

