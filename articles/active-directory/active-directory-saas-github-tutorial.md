---
title: "教學課程：Azure Active Directory 與 GitHub 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 GitHub 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 9dc12bc2e313bcb2000724d035156c5054d14e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>教學課程：Azure Active Directory 與 GitHub 整合

在本教學課程中，您將了解如何整合 GitHub 與 Azure Active Directory (Azure AD)。

GitHub 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 GitHub 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 GitHub (單一登入)
- 您可以在 Azure 管理入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 GitHub 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 GitHub 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 GitHub
2. 設定並測試 Azure AD 單一登入


## <a name="adding-github-from-the-gallery"></a>從資源庫新增 GitHub
若要設定將 GitHub 整合到 Azure AD 中，您需要從資源庫將 GitHub 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 GitHub，請執行下列步驟：**

1. 在 **[Azure 管理入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **GitHub.com**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. 在結果窗格中，選取 GitHub，然後按一下新增 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 GitHub 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 GitHub 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 GitHub 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，就是指派 Azure AD 中**使用者名稱**的值做為 GitHub 中 **Username** 的值。

若要設定及測試與 GitHub 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 GitHub 測試使用者](#creating-a-GitHub-test-user)** - 讓 GitHub 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 管理入口網站中啟用 Azure AD 單一登入，並在您的 GitHub 應用程式中設定單一登入。

**若要設定與 GitHub 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 管理入口網站的 [GitHub] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [SAML 型登入] 做為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. 在 [GitHub 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. 在 [登入 URL] 文字方塊中，輸入下列值：`https://github.com/orgs/<entity-id>/sso`

    b. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > 請注意這些不是真正的值。 您必須使用實際的「登入 URL」及「識別碼」來更新這些值。 在此建議您在 [識別碼] 中使用唯一的字串值。 移至 [GitHub 管理] 區段來擷取這些值。 

4. 在 [使用者屬性] 區段中，選取 [user.mail] 做為 [使用者識別碼]。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. 在 [SAML 簽署憑證] 區段中，按一下 [建立新憑證]。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. 在 [建立新憑證] 對話方塊中，按一下行事曆圖示並選取 [到期日]。 然後按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. 在 [SAML 簽署憑證] 區段中，選取 [啟用新憑證] 並按一下 [儲存] 按鈕。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. 在 [變換憑證] 快顯視窗上，按一下 [確定]。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. 在 [SAML 簽署憑證] 區段上，按一下 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. 在 [GitHub 組態] 區段上，按一下 [設定 GitHub] 以開啟 [設定登入] 視窗。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 GitHub 組織網站。

12. 瀏覽至 [設定]，然後按一下 [安全性]

    ![設定](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. 勾選 [啟用 SAML 驗證]方塊，以顯示單一登入設定欄位。 然後，使用單一登入 URL 值來更新 Azure AD 組態上的單一登入 URL。

    ![設定](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. 設定下列欄位：

    a. **登入 URL**：從 Azure AD 的 [設定 GitHub] 區段中，輸入 [SAML 單一登入服務 URL]

    b. **簽發者**︰從 Azure AD 的 [設定 GitHub] 區段中，輸入 [SAML 實體識別碼]

    c. **公開憑證**：在記事本中開啟已從 Azure AD 下載的憑證並複製內容，包括 "BEGIN CERTIFICATE" 和 "END CERTIFICATE"

    ![設定](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. 按一下 [測試 SAML 組態]，確認 SSO 期間沒有驗證失敗或錯誤。

    ![設定](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. 按一下 [儲存] 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 管理入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 


### <a name="creating-a-github-test-user"></a>建立 GitHub 測試使用者

若要讓 Azure AD 使用者能夠登入 GitHub，則必須將他們佈建到 GitHub。  
GitHub 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 GitHub 公司網站。

2. 按一下 [人員] 。

    ![人員](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "人員")

3. 按一下 [邀請成員]。

    ![邀請使用者](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "邀請使用者")

4. 在 [邀請成員] 對話方塊頁面上，執行下列步驟：

    a. 在 [電子郵件] 文字方塊中，輸入 Britta Simon 帳戶的電子郵件地址。

    ![邀請人員](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "邀請人員")
    
    b. 按一下 [傳送邀請]。

    ![邀請人員](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "邀請人員")

    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 GitHub 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 GitHub，請執行下列步驟：**

1. 在 Azure 管理入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [GitHub.com]。

    ![設定單一登入](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    


### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [GitHub] 圖格時，應該會登入您的 GitHub 應用程式。 您將會以組織帳戶登入，但隨後需要以您的個人帳戶登入。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
