---
title: "教學課程：Azure Active Directory 與 SuccessFactors 整合 | Microsoft Docs"
description: "了解如何使用 SuccessFactors 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e85a38ccbe25263ac42bc76351416b023fb77c87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>教學課程：Azure Active Directory 與 SuccessFactors 整合
本教學課程旨在說明如何整合 SuccessFactors 與 Azure Active Directory (Azure AD)。

SuccessFactors 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SuccessFactors 的人員
* 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SuccessFactors (單一登入)
* 您可以在 Azure 傳統入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件
若要設定 Azure AD 與 SuccessFactors 的整合，您需要下列項目：

* 有效的 Azure 訂閱
* SuccessFactors 中的租用戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。
> 
> 

若要測試本教學課程中的步驟，您應該遵循這些建議：

* 除非必要，否則您不應使用生產環境，。
* 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 SuccessFactors
2. 設定並測試 Azure AD 單一登入

## <a name="adding-successfactors-from-the-gallery"></a>從資源庫新增 SuccessFactors
若要設定 SuccessFactors 與 Azure AD 的整合，您需要從資源庫將 SuccessFactors 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 SuccessFactors，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![設定單一登入][1]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![設定單一登入][2]
4. 按一下頁面底部的 [新增]  。
   
    ![應用程式][3]
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![設定單一登入][4]
6. 在 [搜尋方塊] 中，輸入 **SuccessFactors**。
   
    ![設定單一登入][5]
7. 在結果窗格中，選取 SuccessFactors，然後按一下完成 加入應用程式。
   
    ![設定單一登入][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 SuccessFactors 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 SuccessFactors 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 SuccessFactors 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指派為 SuccessFactors 中 **Username** 的值。

若要設定及測試對 SuccessFactors 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SuccessFactors 測試使用者](#creating-a-successfactors-test-user)** - 使 SuccessFactors 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入
在本節中，您會在傳統入口網站中啟用 Azure AD 單一登入，並在您的 SuccessFactors 應用程式中設定單一登入。

**若要使用 SuccessFactors 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 傳統入口網站的 [SuccessFactors] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入][7]
2. 在 [要如何讓使用者登入 SuccessFactors] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入][8]
3. 在 [設定應用程式 URL] 頁面上，執行下列步驟，然後按 [下一步]。
   
    ![設定單一登入][9]
   
    a. 在 [登入 URL] 文字方塊中，以下列其中一個模式輸入 URL︰ 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. 在 [回覆 URL] 文字方塊中，以下列其中一個模式輸入 URL： 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. 按一下 [下一步] 。 

    > [!NOTE]
    > 請注意這些不是真正的值。 您必須使用實際的「登入 URL」及「回覆 URL」來更新這些值。 若要取得這些值，請連絡 [SuccessFactors 支援小組](https://www.successfactors.com/en_us/support.html)。

1. 在 [設定在 SuccessFactors 單一登入] 頁面上，按一下 [下載憑證]，然後在本機電腦上儲存憑證檔案。
   
    ![設定單一登入][10]

2. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **SuccessFactors 系統管理入口網站** 。

3. 造訪 [應用程式安全性] 和原生 [單一登入功能]。 

4. 在 [重設權杖] 中放入任何值，然後按一下 [儲存權杖] 以啟用 SAML SSO。
   
    ![在應用程式端設定單一登入][11]

    > [!NOTE] 
    > 此值只是做為 on/off 開關。 如果儲存了任何值，SAML SSO 為 ON。 如果儲存了空白值，SAML SSO 為 OFF。

1. 原生以下螢幕擷取畫面，並執行下列動作。
   
    ![在應用程式端設定單一登入][12]
   
    a. 選取 [SAML v2 SSO] 選項按鈕
   
    b. 設定 SAML 判斷提示方名稱 (例如 SAML 簽發者 + 公司名稱)。
   
    c. 在 [SAML 簽發者] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [簽發者 URL] 值。
   
    d. 選取 [回應 (客戶產生的/IdP/AP)] 做為 [需要必要簽章]。
   
    e. 選取 [啟用] 做為 [啟用 SAML 旗標]。
   
    f. 選取 [否] 做為 [登入要求簽章 (SF 產生的/SP/RP)]。
   
    g. 選取 [瀏覽器/後置設定檔] 做為 [SAML 設定檔]。
   
    h. 選取 [否] 做為 [強制執行憑證有效期間]。
   
    i. 複製所下載憑證檔案的內容，然後將它貼至 [SAML 驗證憑證] 文字方塊中。

    > [!NOTE] 
    > 憑證內容必須有開始憑證和結束憑證標籤。

1. 瀏覽至 [SAML V2]，然後執行下列步驟：
   
    ![在應用程式端設定單一登入][13]
   
    a. 選取 [是] 做為 [支援 SP 起始的全域登出]。
   
    b. 在 [全域登出服務 URL (LogoutRequest 目的地)] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [遠端登出 URL] 值。
   
    c. 選取 [否] 做為 [要求 SP 必須加密所有 NameID 元素]。
   
    d. 選取 [未指定] 做為 [NameID 格式]。
   
    e. 選取 [是] 做為 [啟用 SP 起始的登入 (AuthnRequest)]。
   
    f. 在 [以全公司簽發者身分傳送要求] 文字方塊中，放入得自 Azure AD 應用程式組態精靈的 [遠端登入 URL] 值。
2. 如果您想要讓登入使用者名稱不區分大小寫，請執行下列步驟。
   
    a. 造訪 [公司設定] \(靠近底部)。
   
    b. 選取 [啟用不區分大小寫使用者名稱] 附近的核取方塊。
   
    c. 按一下 [儲存]。
   
    ![設定單一登入][29]

    > [!NOTE] 
    > 如果您嘗試啟用此功能，系統會檢查它是否會建立重複的 SAML 登入名稱。 例如，如果客戶有 User1 和 user1 的使用者名稱。 取消區分大小寫功能將會讓這些名稱重複。 系統會提供您錯誤訊息，並將不會啟用此功能。 客戶需要變更其中一個使用者名稱，使其拼寫方式實際上不同。 

1. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下完成 來關閉 設定單一登入 對話方塊。
   
    ![應用程式][14]
2. 在 [單一登入確認] 頁面上，按一下 [完成]。
   
    ![應用程式][15]

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在傳統入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][16]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]。
   
    ![建立 Azure AD 測試使用者][17]
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要顯示使用者清單，請按一下頂端功能表的 [使用者] 。
   
    ![建立 Azure AD 測試使用者][18]
4. 若要開啟 [新增使用者] 對話方塊，請按一下底部工具列上的 [新增使用者]。
   
    ![建立 Azure AD 測試使用者][19]
5. 在 [告訴我們這位使用者]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者][20]
   
    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。
   
    b. 在 [使用者名稱] 文字方塊中，輸入 **BrittaSimon**。
   
    c. 按一下 [下一步] 。
6. 在 [使用者設定檔]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者][21]
   
    a. 在 [名字] 文字方塊中，輸入 **Britta**。  
   
    b. 在 [姓氏] 文字方塊中，輸入 **Simon**。
   
    c. 在 [顯示名稱] 文字方塊中，輸入 **Britta Simon**。
   
    d. 在 [角色] 清單中選取 [使用者]。
   
    e. 按一下 [下一步] 。
7. 在 [取得暫時密碼] 對話方塊頁面上，按一下 [建立]。
   
    ![建立 Azure AD 測試使用者][22]
8. 在 [取得暫時密碼]  對話方塊頁面上，執行下列步驟：
   
    ![建立 Azure AD 測試使用者][23]
   
    a. 記下 [新密碼] 的值。
   
    b. 按一下頁面底部的 [新增] 。  

### <a name="creating-a-successfactors-test-user"></a>建立 SuccessFactors 測試使用者
若要讓 Azure AD 使用者能夠登入 SuccessFactors，必須將他們佈建到 SuccessFactors。  
SuccessFactors 需以手動方式佈建。

若要在 SuccessFactors 建立使用者，您需要連絡 [SuccessFactors 支援小組](https://www.successfactors.com/en_us/support.html)。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者
本節的目標是授與 Britta Simon 對 SuccessFactors 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][24]

**若要將 Britta Simon 指派到 SuccessFactors，請執行下列步驟：**

1. 在傳統入口網站中，若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![指派使用者][25]
2. 在應用程式清單中，選取 [SuccessFactors] 。
   
    ![設定單一登入][26]
3. 在頂端的功能表中，按一下 [使用者] 。
   
    ![指派使用者][27]
4. 在 [使用者] 清單中，選取 [Britta Simon] 。
5. 在底部的工具列中，按一下 [指派] 。
   
    ![指派使用者][28]

### <a name="testing-single-sign-on"></a>測試單一登入
本節的目標是要使用「存取面板」來測試您的 Azure AD 單一登入組態。

當您在存取面板中按一下 [SuccessFactors] 圖格時，應該會自動登入您的 SuccessFactors 應用程式。

## <a name="additional-resources"></a>其他資源
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png
