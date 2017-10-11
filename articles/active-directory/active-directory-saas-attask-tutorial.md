---
title: "教學課程： Azure Active Directory 整合與@Task|Microsoft 文件"
description: "了解如何設定 Azure Active Directory 與 @Task 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: ebb19ca6cbaf04106fbce937d95651e709854cfd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>教學課程：Azure Active Directory 與 @Task 整合
本教學課程旨在說明如何整合 @Task 與 Azure Active Directory (Azure AD)。  
@Task 與 Azure AD 整合提供下列優點： 

* 您可以在 Azure AD 中控制可存取 @Task 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 @Task (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 Azure AD 整合@Task，您需要下列項目：

* Azure AD 訂用帳戶
* 啟用 @Task 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。 

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。  
本教學課程中說明的案例由三個主要建置組塊組成：

1. 從資源庫新增 @Task 
2. 設定並測試 Azure AD 單一登入

## <a name="adding-task-from-the-gallery"></a>從資源庫新增 @Task
若要設定將 @Task 整合到 Azure AD 中，您需要從資源庫將 @Task 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 @Task，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1] 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2] 
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3] 
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4] 
6. 在搜尋方塊中，輸入 **@Task**。
   
    ![應用程式][5] 
7. 在結果窗格中，選取 [@Task]，然後按一下 [完成] 以新增應用程式。
   
    ![應用程式][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節的目標是要說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 @Task 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道與 @Task 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 @Task 中相關使用者之間的連結關聯性。   
建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 @Task 中 **Username** 的值。

設定和測試 Azure AD 單一登入與@Task，您必須完成下列的建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 @Tasktest 使用者](#creating-a-halogen-software-test-user)** - 在 @Taskthat 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表 Britta Simon 的項目連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD 單一登入，並在您的 @Task 應用程式中設定單一登入。

**若要設定 Azure AD 單一登入與@Task，執行下列步驟：**

1. 在 Azure 傳統入口網站的 **@Task** 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 
2. 在 [要如何讓使用者登入 @Task] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![Azure AD 單一登入][7] 
3. 在 [設定應用程式設定]  對話方塊頁面上，執行下列步驟：
   
    ![設定 App 設定][8] 
   
     a. 在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 @Task 應用程式的 URL (例如：*https://<Tenant name>.attask-ondemand.com*。
   
     b. 按一下 [下一步] 。
4. 在 [設定在 @Task 單一登入] 頁面上，按一下 [下載中繼資料]，將中繼資料檔儲存在您的本機電腦中，然後按 [下一步]。
   
    ![何謂 Azure AD Connect][9] 
5. 以系統管理員身分登入您的 @Task 公司網站。
6. 移至 [單一登入組態] 。
7. 在 [單一登入]  對話方塊中，執行下列步驟
   
    ![設定單一登入][23]
   
    a. 針對 [類型]，選取 [SAML 2.0]。
   
    b. 選取 [服務提供者識別碼]。
   
    c. 在 Azure 傳統入口網站上，複製 [遠端登入 URL]，然後將它貼到 [遠端登入 URL] 文字方塊中。
   
    d. 在 Azure 傳統入口網站上，複製 [單一登出服務 URL]，然後將它貼到 [登出 URL] 文字方塊中。
   
    e. 在 Azure 傳統入口網站上，複製 [變更密碼 URL]，然後將它貼到 [變更密碼 URL] 文字方塊中。
   
    f. 按一下 [儲存] 。
8. 在 Azure 傳統入口網站中，選取單一登入設定確認，然後按 [下一步] 。 
   
    ![何謂 Azure AD Connect][10]
9. 在 [單一登入確認] 頁面上，按一下 [完成]。  
   
    ![何謂 Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節目標是在 Azure 傳統入口網站中建立名為 Britta Simon 的測試使用者。  

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟： 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按一下 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟： 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。

    e. 按一下 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下 [完成]。   

### <a name="creating-an-task-test-user"></a>建立 @Task 測試使用者
本節目標是在 @Task 中建立名為 Britta Simon 的使用者。

**若要建立使用者，在呼叫許 Simon @Task，執行下列步驟：**

1. 以系統管理員身分登入您的 @Task 公司網站。
2. 在頂端的功能表中，按一下 [人員] 。
3. 按一下 [新增人員] 。 
4. 在 [新增人員] 對話方塊上，執行下列步驟：
   
    ![建立 @Task 測試使用者][21] 
   
    a. 在 [名字] 文字方塊中，輸入 "Britta"。
   
    b. 在 [姓氏] 文字方塊中，輸入 "Simon"。
   
    c. 在 [電子郵件地址] 文字方塊中，輸入 Azure Active Directory 中 Britta Simon 的電子郵件地址。
   
    d. 按一下 [新增人員] 。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節目標是授與 Britta Simon 對 @Task 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要指派至許 Simon @Task，執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 
2. 在應用程式清單中，選取 [@Task] 。
   
    ![指派使用者][202] 
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用存取面板來測試您的 Azure AD 單一登入組態。  
當您在存取面板中按一下 @Task 圖格時，應該會自動登入您的 @Task 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






