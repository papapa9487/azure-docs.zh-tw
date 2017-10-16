---
title: "教學課程：Azure Active Directory 與 Adaptive Suite 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Adaptive Suite 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 5d7ba2f4c7d814e3aaa1bf804ddc5030380ccb2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>教學課程：Azure Active Directory 與 Adaptive Suite 整合

在本教學課程中，您將了解如何整合 Adaptive Suite 與 Azure Active Directory (Azure AD)。

Adaptive Suite 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Adaptive Suite 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Adaptive Suite (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Adaptive Suite 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Adaptive Suite 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Adaptive Suite
2. 設定並測試 Azure AD 單一登入

## <a name="adding-adaptive-suite-from-the-gallery"></a>從資源庫新增 Adaptive Suite
若要設定 Adaptive Suite 與 Azure AD 整合，您需要從資源庫將 Adaptive Suite 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Adaptive Suite，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，鍵入 **Adaptive Suite**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. 在結果窗格中，選取 Adaptive Suite，然後按一下新增 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Adaptive Suite 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Adaptive Suite 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Adaptive Suite 中的相關使用者之間建立連結關聯性。

在 Adaptive Suite 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 Adaptive Suite 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Adaptive Suite 測試使用者](#creating-an-adaptive-suite-test-user)** - 在 Adaptive Suite 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Adaptive Suite 應用程式中設定單一登入。

**若要設定與 Adaptive Suite 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Adaptive Suite] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. 在 [Adaptive Suite 網域與 URL] 區段上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > 您可以從 Adaptive Suite 的 [SAML SSO 設定] 頁面取得這個值。
    >  

4. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. 在 [Adaptive Suite 設定] 區段上，按一下 [設定 Adaptive Suite] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML 實體識別碼] 和 [SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Adaptive Suite 公司網站。

8. 移至 [管理] 。
   
    ![管理](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "管理")

9. 在 [使用者和角色] 區段中，按一下 [管理 SAML SSO 設定]。
   
    ![管理 SAML SSO 設定](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "管理 SAML SSO 設定")

10. 在 [SAML SSO 設定]  頁面上，執行下列步驟：
   
    ![SAML SSO 設定](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "SAML SSO 設定")

    a. 在 [識別提供者名稱]  文字方塊中，輸入您組態的名稱。
    
    b.這是另一個 C# 主控台應用程式。 將從 Azure 入口網站複製的 [SAML 實體識別碼] 值貼到 [識別提供者實體識別碼] 文字方塊中。
  
    c. 將從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值貼到 [識別提供者 SSO URL] 文字方塊中。
  
    d. 將從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值貼到 [自訂登出 URL] 文字方塊中。
  
    e. 若要上傳您下載的憑證，請按一下 [選擇檔案] 。
  
    f. 選取下列選項：
    * 針對 [SAML 使用者識別碼]，選取 [使用者的 Adaptive Insights 使用者名稱]。
    * 針對 [SAML 使用者識別碼位置]，選取 [主旨 NameID 中的使用者識別碼]。
    * 針對 [SAML NameID 格式]，選取 [電子郵件地址]。
    * 針對 [啟用 SAML]，選取 [允許 SAML SSO 和直接 Adaptive Insights 登入]。
    
    g. 按一下 [儲存] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-an-adaptive-suite-test-user"></a>建立 Adaptive Suite 測試使用者

若要讓 Azure AD 使用者能夠登入 Adaptive Suite，必須將他們佈建到 Adaptive Suite。  

* Adaptive Suite 需以手動方式佈建。

**若要設定使用者佈建，請執行下列步驟：** 

1. 以系統管理員身分登入您的 **Adaptive Suite** 公司網站。
2. 移至 [管理] 。
   
   ![管理](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "管理")
3. 在 [使用者和角色] 區段中，按一下 [新增使用者]。
   
   ![新增使用者](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "新增使用者")
4. 在 [新增使用者]  區段中，執行下列步驟：
   
   ![提交](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "提交")   

   a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 使用者的 [名稱]、[登入]、[電子郵件]、[密碼]。
  
   b.這是另一個 C# 主控台應用程式。 選取 [角色] 。
  
   c. 按一下 [提交] 。

>[!NOTE]
>您可以使用任何其他的 Adaptive Suite 使用者帳戶建立工具或 Adaptive Suite 提供的 API 來佈建 AAD 使用者帳戶。
>  

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Adaptive Suite 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Adaptive Suite，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Adaptive Suite]。

    ![設定單一登入](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

本節的目標是要使用「存取面板」來測試您的 Microsoft Azure AD 單一登入組態。

當您在存取面板中按一下 [Adaptive Suite] 磚時，應該會自動登入您的 Adaptive Suite 應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

