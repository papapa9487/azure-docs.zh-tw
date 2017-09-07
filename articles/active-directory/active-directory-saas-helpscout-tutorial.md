---
title: "教學課程：Azure Active Directory 與 Help Scout 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Help Scout 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教學課程：Azure Active Directory 與 Help Scout 整合

在本教學課程中，您會了解如何將 Help Scout 與 Azure Active Directory (Azure AD) 整合。

Help Scout 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Help Scout 的人員。
- 您可以使用單一登入和使用者的 Azure AD 帳戶，將您的使用者自動登入 Help Scout。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

若要深入了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Help Scout 整合，您需要下列項目：

- Azure AD 訂用帳戶
- Help Scout 訂用帳戶，並開啟單一登入 

> [!NOTE]
> 若您要測試本教學課程中的步驟，不建議您在生產環境中進行測試。

在本教學課程中測試步驟的建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，可以[取得一個月的免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 

本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Help Scout。
2. 設定和測試 Azure AD 單一登入。

## <a name="add-help-scout-from-the-gallery"></a>從資源庫新增 Help Scout
若要設定將 Help Scout 與 Azure AD 整合，在資源庫中，請將 Help Scout 新增到受管理的 SaaS 應用程式清單。

若要從資源庫新增 Help Scout：

1. 在 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [Azure Active Directory]。 

    ![Azure Active Directory 按鈕][1]

2. 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![[企業應用程式] 頁面][2]
    
3. 若要新增新的應用程式，請選取 [新增應用程式]。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Help Scout**。 在搜尋結果中，選取 [Help Scout]，然後選取 [新增]。

    ![結果清單中的 Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 Britta Simon 的測試使用者作為基礎，設定及測試與 Help Scout 搭配運作的 Azure AD 單一登入。

為使單一登入運作，Azure AD 必須知道 Help Scout 中互相對應的 Azure AD 使用者。 必須建立 Azure AD 使用者和 Help Scout 中相關使用者之間的連結關聯性。

若要建立連結關聯性，在 Help Scout 中，將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值。

若要使用 Help Scout 來設定並測試 Azure AD 單一登入，您需要完成下列工作：

1. [設定 Azure AD 單一登入](#set-up-azure-ad-single-sign-on)。 設定要使用這項功能的使用者。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)。 使用 Britta Simon 使用者來測試 Azure AD 單一登入。
3. [建立 Help Scout 測試使用者](#create-a-help-scout-test-user)。 在 Help Scout 中建立 Britta Simon 的對應項目，且該項目與 Azure AD 中代表使用者的項目連結。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)。 將 Britta Simon 設定為使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)。 驗證組態是否能運作。

### <a name="set-up-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中設定 Azure AD 單一登入。 然後，您要在 Help Scout 應用程式中設定單一登入。

若要使用 Help Scout 來設定 Azure AD 單一登入：

1. 在 Azure 入口網站的 [Help Scout] 應用程式整合頁面上，選取 [單一登入]。
 
    ![設定單一登入連結][4]

2. 在 [單一登入] 頁面上，選取 [SAML 登入] 作為 [模式]。
 
    ![單一登入對話方塊](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. 如果您需要以 IDP 起始模式設定應用程式，請在 [Help Scout 網域和 URL] 下，完成下列步驟：

    1. 在 [識別碼] 方塊中，輸入具有下列模式的 URL：`urn:auth0:helpscout:<instancename>`

    2. 在 [回覆 URL] 方塊中，輸入具有下列模式的 URL：`https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Help Scout 網域與 URL 單一登入資訊](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. 如果您需要以 SP 起始模式來設定應用程式，請選取 [顯示進階 URL 設定] 核取方塊，然後執行下列動作：

    * 在 [登入 URL] 方塊中，輸入具有下列格式的 URL：`https://secure.helpscout.net/members/login/`

    ![Help Scout 網域與 URL 單一登入資訊](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > 這些 URL 中的值僅供示範。 請使用實際的識別碼 URL 和回覆 URL 來更新這些值。 若要取得這些值，請連絡 [Help Scout 支援小組](mailto:help@helpscout.com)。 

5. 在 [SAML 簽署憑證] 下，選取 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. 若要在 Help Scout 端設定單一登入，您必須將已下載的中繼資料 XML 傳送給 [Help Scout 支援小組](mailto:help@helpscout.com)。 Help Scout 支援小組會套用此設定，在兩端正確設定 SAML 單一登入連線。

> [!TIP]
> 當您設定應用程式時，在 [Azure 入口網站](https://portal.azure.com)中即可閱讀這些指示的簡要版本！ 選取 [Active Directory] > [企業應用程式] 新增此應用程式之後，選取 [單一登入] 索引標籤。您可以透過頁面底部的 [設定] 區段來存取內嵌的文件。 如需詳細資訊，請參閱 [Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

![建立 Azure AD 測試使用者][100]

若要在 Azure AD 中建立測試使用者：

1. 在 Azure 入口網站的左側功能表中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請選取 [使用者和群組]，然後選取 [所有使用者]。

    ![選取 [使用者和群組]，然後選取 [所有使用者]](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請在 [所有使用者] 頁面頂端選取 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，完成下列步驟：

    1. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    2. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    3. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    4. 選取 [ **建立**]。

        ![[使用者] 對話方塊](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>建立 Help Scout 測試使用者

本節的目標是在 Help Scout 中建立名為 Britta Simon 的使用者。 Help Scout 支援預設啟用的 Just-In-Time (JIT) 佈建。

在本節中，沒有任何需要完成的動作或工作。 如果 Help Scout 中還沒有使用者，當您嘗試存取 Help Scout 時，就會建立新的使用者。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Help Scout 的使用者帳戶存取權授與使用者 Britta Simon，讓她能夠使用 Azure AD 單一登入。

![指派使用者角色][200] 

若要將 Britta Simon 指派給 Help Scout：

1. 在 Azure 入口網站中，開啟應用程式檢視，然後移至目錄檢視。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Help Scout]。

    ![應用程式清單中的 Help Scout 連結](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![使用者和群組連結][202]

4. 選取 [新增] 。 然後在 [新增指派] 頁面上，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 頁面上，選取使用者清單中的 [Britta Simon]。

6. 在 [使用者和群組] 頁面上，選取 [選取]。

7. 在 [新增指派] 頁面上，選取 [指派]。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [Help Scout] 圖格時，應該會自動登入您的 Help Scout 應用程式。

如需存取面板的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


