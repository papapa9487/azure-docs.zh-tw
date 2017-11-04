---
title: "教學課程：Azure Active Directory 與 Cezanne HR Software 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Cezanne HR Software 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>教學課程：Azure Active Directory 與 Cezanne HR Software 整合

在此教學課程中，您將了解如何整合 Cezanne HR Software 與 Azure Active Directory (Azure AD)。

Cezanne HR Software 與 Azure AD 整合提供下列優點。 您可以：

- 在 Azure AD 中控制可存取 Cezanne HR Software 的人員。
- 讓使用者使用他們的 Azure AD 帳戶自動登入 Cezanne HR Software 單一登入 (SSO)。
- 在 Azure 入口網站中集中管理您的帳戶。

若要了解軟體即服務 (SaaS) 應用程式與 Azure AD 的整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Cezanne HR Software 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 已啟用 Cezanne HR Software SSO 的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，不建議您使用生產環境。

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD SSO。 

本教學課程中說明的案例由二個主要建置組塊組成：

* 從資源庫新增 Cezanne HR Software
* 設定並測試 Azure AD SSO

## <a name="add-cezanne-hr-software-from-the-gallery"></a>從資源庫新增 Cezanne HR Software
若要設定 Cezanne HR Software 與 Azure AD 整合，您需要從資源庫將 Cezanne HR Software 新增到受管理的 SaaS 應用程式清單。

若要從資源庫新增 Cezanne HR Software，請執行下列步驟：

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左側窗格中，選取 [Azure Active Directory] 按鈕。 

    ![[Azure Active Directory] 按鈕][1]

2. 選取 [企業應用程式] > [所有應用程式]。

    ![[所有應用程式] 連結][2]
    
3. 若要新增應用程式，請在 [所有應用程式] 對話方塊的頂端，選取 [新增應用程式]。

    ![[新增應用程式] 按鈕][3]

4. 在 [搜尋] 方塊中，輸入 **Cezanne HR Software**。

    ![搜尋方塊](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. 在結果清單中，選取 [Cezanne HR Software]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Cezanne HR Software 搭配運作的 Azure AD SSO。

若要讓單一登入能夠運作，Azure AD 必須知道 Cezanne HR Software 與 Azure AD 中互相對應的使用者。 換句話說，您必須建立 Azure AD 使用者和 Cezanne HR Software 中相關使用者之間的連結關聯性。

若要建立連結關聯性，請將 Cezanne HR Software **使用者名稱**值指派為 Azure AD **Username** 值。

若要使用 Cezanne HR Software 設定並測試 Azure AD SSO，請完成下列構成要素。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

在本節中，您可以藉由執行下列步驟，在 Azure 入口網站中啟用 Azure AD SSO，並在 Cezanne HR Software 應用程式中設定 SSO：

1. 在 Azure 入口網站的 [Cezanne HR Software] 應用程式整合頁面上，按一下 [單一登入]。

    ![[單一登入] 命令][4]

2. 若要啟用 SSO，請在 [單一登入] 對話方塊中，於 [模式] 選取 [SAML 登入]。
 
    ![[模式] 方塊](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. 在 [Cezanne HR Software 網域與 URL] 下，執行下列動作：

    ![[Cezanne HR Software 網域與 URL] 區段](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. 在 [登入 URL] 方塊中，輸入具有下列語法的 URL：`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. 在 [回覆 URL] 方塊中，輸入具有下列語法的 URL：`https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > 上述值並非真正的值。 使用實際的回覆 URL 與登入 URL 更新這些值。 若要取得這些值，請連絡 [Cezanne HR Software 用戶端支援小組](mailto:info@cezannehr.com)。

4. 在 [SAML 簽署憑證] 下，選取 [憑證 (Base64)]，然後將憑證檔案儲存到您的電腦。

    ![[SAML 簽署憑證] 區段](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. 選取 [ **儲存**]。

    ![[儲存] 按鈕](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. 在 [Cezanne HR Software 設定] 下，選取 [設定 Cezanne HR Software] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [SAML Entity ID] 與 [SAML 單一登入服務] URL。

    ![[Cezanne HR Software 設定] 區段](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Cezanne HR Software 租用戶。

8. 在左側窗格中，選取 [系統設定]。 選取 [安全性設定] > [單一登入設定]。

    ![[單一登入設定] 連結](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. 在 [允許使用者使用下列的單一登入 (SSO) 服務來登入] 窗格中，選取 [SAML 2.0] 核取方塊，然後選取 [進階組態] 選項。

    ![單一登入服務選項](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. 選取 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. 在 [SAML 2.0 識別提供者] 下，執行下列動作：

    ![[SAML 2.0 識別提供者] 區段](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. 在 [顯示名稱] 方塊中，輸入您的識別提供者名稱。

    b. 在 [實體識別碼] 方塊中，貼上您從 Azure 入口網站複製的 **SAML 實體識別碼**。 

    c. 在 [SAML 繫結] 清單方塊中，選取 [POST]。

    d. 在 [Security Token Service 端點] 方塊中，貼上您從 Azure 入口網站複製的 **SAML 單一登入服務** URL。 
    
    e. 在 [使用者識別碼屬性名稱] 方塊中，輸入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。
    
    f. 若要上傳從 Azure AD 下載的憑證，請選取 [上傳] 按鈕。
    
    g. 選取 [確定] 。 

12. 選取 [ **儲存**]。

    ![[儲存] 按鈕](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> 當您設定應用程式時，您可以在 [Azure 入口網站](https://portal.azure.com)中閱讀精簡版的先前說明。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，選取 [單一登入] 索引標籤。然後從 [設定] 區段來存取內嵌的文件。 

若要深入了解內嵌文件功能，請參閱 [Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在本節中，您會在 Azure 入口網站中建立測試使用者 Britta Simon。

![測試使用者 Britta Simon][100]

若要在 Azure AD 中建立測試使用者，請執行下列動作：

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory] 按鈕。

    ![[Azure Active Directory] 按鈕](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請選取 [使用者和群組] > [所有使用者]。
    
    ![[所有使用者] 連結](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    [所有使用者] 對話方塊隨即開啟。

3. 若要開啟 [使用者] 對話方塊，請選取 [新增]。
 
    ![[新增] 按鈕](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. 在 [使用者] 對話方塊中，執行下列動作：
 
    ![[使用者] 對話方塊](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中產生的值。

    d. 選取 [ **建立**]。
 
### <a name="create-a-cezanne-hr-software-test-user"></a>建立 Cezanne HR Software 測試使用者

若要讓 Azure AD 使用者能夠登入 Cezanne HR Software，必須將他們佈建到 Cezanne HR Software 中。 Cezanne HR Software 需以手動的方式佈建。

請執行下列步驟來佈建使用者帳戶：

1.  以系統管理員身分登入您的 Cezanne HR Software 公司網站。

2.  在左側窗格中，選取 [系統設定] > [管理使用者] > [新增使用者]。

    ![新增使用者](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "連結 新增使用者")

3.  在 [人員詳細資料] 下，執行下列動作：

    ![人員詳細資料](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "區段 新增使用者")
    
    a. 將 [內部使用者] 設為 [OFF]。
    
    b. 在 [名字] 方塊中，輸入使用者的名字，例如，**Britta**。  
 
    c. 在 [姓氏] 方塊中，輸入使用者的姓氏，例如，**Simon**。
    
    d. 在 [電子郵件] 方塊中，輸入使用者的電子郵件地址，例如 Brittasimon@contoso.com。

4.  在 **[帳戶資訊]** 下，執行下列動作：

    ![帳戶資訊](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "區段 新增使用者")
    
    a. 在 [使用者名稱] 方塊中，輸入使用者的電子郵件地址，例如 Brittasimon@contoso.com。
    
    b. 在 [密碼] 方塊中，輸入使用者的密碼。
    
    c. 在 [安全性角色]中，選取 [專業 HR]。
    
    d. 選取 [確定] 。

5. 在 [單一登入] 索引標籤的 [SAML 2.0 識別碼] 區段中，選取 [新增]。

    ![新增](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png " 按鈕 使用者")

6. 在 [識別提供者] 清單方塊中，選取您的識別提供者。 在 [使用者識別碼] 方塊中，輸入測試使用者 Britta Simon 帳戶的電子郵件地址。

    ![識別提供者 和 使用者識別碼 方塊](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "[使用者]")
    
7. 選取 [ **儲存**]。

    ![儲存](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "按鈕 使用者")

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會把 Cezanne HR Software 的存取權授予 Britta Simon，讓 Britta Simon 能夠使用 Azure SSO。

![測試使用者存取][200] 

1. 在 Azure 入口網站中，開啟應用程式檢視，然後移至目錄檢視。 選取 [企業應用程式] > [所有應用程式]。

    ![[所有應用程式] 連結][201] 

2. 在應用程式清單中，選取 [Cezanne HR Software] 。

    ![[應用程式] 清單](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. 在左側功能表中，選取 [使用者和群組]。

    ![指派使用者][202] 

4. 選取 [新增] 。 然後，在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][203]

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，選取 [選取]。

7. 在 [新增指派] 對話方塊中，選取 [指派]。
    
### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD SSO 組態。

當您在 [存取面板] 中選取 [Cezanne HR Software] 圖格時，應該會自動登入您的 Cezanne HR Software 應用程式。

## <a name="next-steps"></a>後續步驟

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

