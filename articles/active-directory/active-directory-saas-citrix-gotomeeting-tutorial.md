---
title: "教學課程：Azure Active Directory 與 GoToMeeting 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 GoToMeeting 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: e8871ea9c3aa9f962bf961a8b44b748392a56ba7
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>教學課程：Azure Active Directory 與 GoToMeeting 整合

在本教學課程中，您會了解如何整合 GoToMeeting 與 Azure Active Directory (Azure AD)。

GoToMeeting 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 GoToMeeting 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 GoToMeeting (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 GoToMeeting 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 GoToMeeting 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 GoToMeeting
2. 設定並測試 Azure AD 單一登入

## <a name="adding-gotomeeting-from-the-gallery"></a>從資源庫新增 GoToMeeting
若要設定 GoToMeeting 與 Azure AD 整合，您需要從資源庫將 GoToMeeting 新增到受管理的 SaaS App 清單。

**若要從資源庫新增 GoToMeeting，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **GoToMeeting**，從結果面板中選取 [GoToMeeting]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 GoToMeeting 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 GoToMeeting 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 GoToMeeting 中的相關使用者之間建立連結關聯性。

在 GoToMeeting 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要設定及測試與 GoToMeeting 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 GoToMeeting 測試使用者](#create-a-gotomeeting-test-user)** - 在 GoToMeeting 中建立一個與 Azure AD 中代表使用者的項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 GoToMeeting 應用程式中設定單一登入。

**若要使用 GoToMeeting 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [GoToMeeting] 應用程式整合分頁上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. 在 [GoToMeeting 網域和 URL] 區段中，按一下 [顯示進階 URL 設定]，並執行下列動作：

    ![GoToMeeting 網域與 URL 單一登入資訊](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_ssourl.png)

    a. 在 [識別碼] 文字方塊中，輸入 URL：`https://authentication.logmeininc.com/saml/sp`

    b. 在 [回覆 URL] 文字方塊中，輸入 URL：`https://authentication.logmeininc.com/saml/acs`

    c. 在 [轉送狀態] 文字方塊中，輸入下列其中一個 URL：

    **若為 GoToMeeting**：`https://global.gotomeeting.com`
    
    **若為 GoToTraining**：`https://global.gototraining.com`

    **若為 GoToWebinar**：`https://global.gotowebinar.com`

    **若為 GoToAssist**：`https://app.gotoassist.com`


4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. 若要產生**中繼資料** URL，執行下列步驟︰

    a. 按一下 [應用程式註冊]。
    
    ![設定單一登入](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b.這是另一個 C# 主控台應用程式。 按一下 [端點] 以開啟 [端點] 對話方塊。  
    
    ![設定單一登入](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. 按一下複製按鈕複製 [同盟中繼資料文件] URL，並將它貼到 [記事本]。
    
    ![設定單一登入](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. 現在，移至 [GoToMeeting] 的屬性頁面，使用 [複製] 按鈕複製 [應用程式識別碼]，並將它貼到 [記事本]。
 
    ![設定單一登入](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. 使用下列模式產生**中繼資料 URL**︰`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. 在 [GoToMeeting 組態] 區段上，按一下 [設定 GoToMeeting] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![GoToMeeting 設定](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. 在不同的瀏覽器視窗中，登入您的 [GoToMeeting 組織中心](https://account.citrixonline.com/organization/administration/)

9. 在 [識別提供者] 索引標籤下，您可以提供所產生的**中繼資料 URL** 或下載的**中繼資料檔** 或 [手動]，來設定 Azure 設定。

10. 若為 [中繼資料 URL]，請執行下列步驟：

    ![GoToMeeting 設定](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. 在 [想要如何設定 SAML IDP？] 中，從下拉式清單選取 [自動]。

    b. 將您在先前步驟產生的**中繼資料 URL** 貼至 [中繼資料 URL] 文字方塊。

    c. 按一下 [儲存] 。

11. 若為 [中繼資料檔]，請執行下列步驟：

    ![GoToMeeting 設定](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. 在 [想要如何設定 SAML IDP？] 中，從下拉式清單選取 [上傳 SAML 中繼資料檔]。

    b. 若要上傳您下載的中繼資料檔，請按一下 [上傳中繼資料檔] 。

    c. 按一下 [儲存] 。

12. 若為 [手動]，請執行下列步驟：

    ![GoToMeeting 設定](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  在 [登入頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。

    b.  在 [登出頁面 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。

    c.  在 [識別提供者實體識別碼] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。

    d. 從下載的中繼資料檔中擷取 X509Certificate，然後按一下 [上傳憑證]，上傳此憑證。

    e. 按一下 [儲存] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下 [建立] 。
 
### <a name="create-a-gotomeeting-test-user"></a>建立 GoToMeeting 測試使用者

本節會在 GoToMeeting 中建立名為 Britta Simon 的使用者。 GoToMeeting 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作項目。 如果 GoToMeeting 中還沒有該使用者，當您嘗試存取 GoToMeeting 時，就會建立新的使用者。

> [!NOTE]
> 如果您需要手動建立使用者，請連絡 [GoToMeeting 支援小組](https://support.logmeininc.com/gotomeeting)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 GoToMeeting 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 GoToMeeting，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [GoToMeeting]。

    ![應用程式清單中的 GoToMeeting 連結](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [GoToMeeting] 圖格時，應該會自動登入您的 GoToMeeting 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定使用者佈建](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

