---
title: "教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SilkRoad Life Suite 之間的單一登入。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合
本教學課程旨在說明如何整合 SilkRoad Life Suite 與 Azure Active Directory (Azure AD)。 

SilkRoad Life Suite 與 Azure AD 整合提供下列優點： 

* 您可以在 Azure AD 中控制可存取 SilkRoad Life Suite 的人員 
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SilkRoad Life Suite 單一登入 (SSO)

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定與 SilkRoad Life Suite 的 Azure AD 整合，您需要下列項目：

* Azure AD 訂用帳戶
* 已啟用 SilkRoad Life Suite SSO 的訂用帳戶

>[!NOTE]
>若要測試本教學課程中的步驟，我們不建議使用生產環境。 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD SSO。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫加入 SilkRoad Life Suite 
2. 設定並測試 Azure AD SSO

## <a name="add-silkroad-life-suite-from-the-gallery"></a>從資源庫加入 SilkRoad Life Suite
若要設定 SilkRoad Life Suite 與 Azure AD 整合，您需要從資源庫將 SilkRoad Life Suite 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 SilkRoad Life Suite，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。 
   
    ![Active Directory][1]

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式][2]

4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入 **SilkRoad Life Suite**。
   
    ![應用程式][5]

7. 在結果窗格中，選取 [SilkRoad Life Suite]，然後按一下 [完成] 以加入應用程式。
   
    ![應用程式][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 SilkRoad Life Suite 搭配運作的 Azure AD SSO。

若要讓 SSO 能夠運作，Azure AD 必須知道 SilkRoad Life Suite 與 Azure AD 中互相對應的使用者。 換句話說，Azure AD 使用者和 SilkRoad Life Suite 中的相關使用者之間必須建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 SilkRoad Life Suite 中 **Username** 的值。

若要設定及測試對 SilkRoad Life Suite 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SilkRoad Life Suite 測試使用者](#creating-a-silkroad-life-suite-test-user)** - 使 SilkRoad Life Suite 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
本節的目標是要在 Azure 傳統入口網站中啟用 Azure AD SSO，並在您的 SilkRoad Life Suite 應用程式中設定 SSO。

**若要使用 SilkRoad Life Suite 設定 Azure AD 單一登入，請執行下列步驟：**

1. 以系統管理員身分登入您的 SilkRoad 公司網站。 

  >[!NOTE] 
  > 若要取得用於設定與 Microsoft Azure AD 的同盟存取的 SilkRoad Life Suite 驗證應用程式，請連絡 SilkRoad 支援人員或您的 SilkRoad 服務代表。
  > 

2. 移至 [服務提供者]，然後按一下 [同盟詳細資料]。 
   
    ![Azure AD 單一登入][10] 

3. 按一下 [下載同盟中繼資料] ，然後將資料檔儲存在您的電腦中。
   
    ![Azure AD 單一登入][11] 

4. 在 Azure 傳統入口網站的 [SilkRoad Life Suite] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][6] 

5. 在 [您希望使用者如何登入 SilkRoad Life Suite] 頁面上，選取 [Azure AD 單一登入]，然後按 [下一步]。
   
    ![Azure AD 單一登入][7] 

6. 在 [設定應用程式設定]  對話方塊頁面上，執行下列步驟：
   
    ![Azure AD 單一登入][8]   
 1. 在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 SilkRoad Life Suite 網站的 URL (例如：*https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*)。  
 2. 開啟下載的 **Silkroad** 中繼資料檔案。 
 3. 找到 **AssertionConsumerService** 標籤，然後複製 **Location** 屬性。         
   
    ![Azure AD 單一登入][21] 
 4. 請將該值貼入 [回覆 URL] 文字方塊。  
 5. 按一下 [下一步] 。

6. 在 [設定在 SilkRoad Life Suite 單一登入]  頁面上，執行下列步驟：
   
    ![Azure AD 單一登入][9]  
 1. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。  
 2. 按一下 [下一步] 。

7. 在您的 **SilkRoad** 應用程式中，按一下 [驗證來源]。
   
    ![Azure AD 單一登入][12] 

8. 按一下 [加入驗證來源] 。 
   
    ![Azure AD 單一登入][13] 

9. 在 [加入驗證來源]  區段中，執行下列步驟： 
   
    ![Azure AD 單一登入][14]  
 1. 在 [選項 2 - 中繼資料檔案] 下，按一下 [瀏覽] 來上傳下載的中繼資料檔案。  
 2. 按一下 [使用檔案資料建立身分識別提供者]。

10. 在 [驗證來源] 區段中，按一下 [編輯]。 
    
     ![Azure AD 單一登入][15] 

11. 在 [編輯驗證來源]  對話方塊中，執行下列步驟： 
    
     ![Azure AD 單一登入][16] 
 1. 對 [已啟用] 選取 [是]。   
 2. 在 [IdP 描述] 文字方塊中，輸入您的組態描述 (例如：*Azure AD SSO*)。  
 3. 在 [IdP 名稱] 文字方塊中，輸入組態特定的名稱 (例如：*Azure SP*)。  
 4. 按一下 [儲存] 。

12. 停用所有其他驗證來源。 
    
     ![Azure AD 單一登入][17]

13. 在 Azure 傳統入口網站的 [單一登入確認] 頁面上，按一下 [完成]。  
    
     ![Azure AD 單一登入][18]

14. 在 [單一登入確認] 頁面上，按一下 [完成]。
    
     ![Azure AD 單一登入][19]

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][20]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟： 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. 針對 [使用者類型]，選取 [您組織中的新使用者]。  
 2. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。 
 3. 按一下 [下一步] 。

6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟： 
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. 在 [名字] 文字方塊中，輸入 **Britta**。    
 2. 在 [姓氏] 文字方塊中，輸入 **Simon**。 
 3. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。 
 4. 在 [角色] 清單中選取 [使用者]。
 5. 按一下 [下一步] 。

7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. 記下 [新密碼] 的值。 
 2. 按一下頁面底部的 [新增] 。   

### <a name="create-a-silkroad-life-suite-test-user"></a>建立 SilkRoad Life Suite 測試使用者
本節目標是在 SilkRoad Life Suite 中建立名為 Britta Simon 的使用者。 Britta 必須具備符合 Britta 在 Azure AD 中 *電子郵件位址*的 SSO 識別碼 (有時稱為 **AuthParam** )。

**若要在 SilkRoad Life Suite 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

- 要求您的 SilkRoad Life Suite 支援小組建立一個使用者，其 **SSO ID** 屬性與 Azure AD 中 Britta Simon 的 **emailaddress** 屬性的值相同。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 SilkRoad Life Suite 的存取權，讓她能夠使用 Azure SSO。

![指派使用者][200] 

**若要將 Britta Simon 指派到 SilkRoad Life Suite，請執行以下步驟：**

1. 在 Azure 傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][201] 

2. 在應用程式清單中，選取 [SilkRoad Life Suite] 。
   
    ![指派使用者][202] 

3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][203] 

4. 在 [使用者] 清單中，選取 [Britta Simon] 。

5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][205]

### <a name="test-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD SSO 組態。  

當您在 [存取面板] 中按一下 [SilkRoad Life Suite] 磚時，您應該會自動登入您的 SilkRoad Life Suite 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png





