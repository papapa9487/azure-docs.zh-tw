---
title: "教學課程：Azure Active Directory 與 Google Apps整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Google Apps 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 1d92e673a948dd139ff2d4a24f2e602180be43c5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>教學課程：Azure Active Directory 與 Google Apps 整合

在本教學課程中，您會了解如何整合 Google Apps 與 Azure Active Directory (Azure AD)。

Google Apps 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Google Apps 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Google Apps (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Google Apps 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Google Apps 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="video-tutorial"></a>影片教學課程
如何在 2 分鐘內啟用單一登入至 Google Apps：

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>常見問題集
1. **問：Chromebook 及其他 Chrome 裝置是否與 Azure AD 單一登入相容？**
   
    答：是，使用者能夠使用其 Azure AD 認證來登入其 Chromebook 裝置。 若要了解為何使用者可能收到兩次提供認證的提示，請參閱此 [Google Apps 支援文章](https://support.google.com/chrome/a/answer/6060880) 。

2. **問：如果我啟用單一登入，使用者是否將能夠使用其 Azure AD 認證來登入任何 Google 產品 (例如 Google Classroom、GMail、Google 雲端硬碟、YouTube 等)？**
   
    答：是，視您選擇為您組織啟用或停用的 [Google Apps](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) 而定。

3. **問：我是否可以只為一部分 Google Apps 使用者啟用單一登入？**
   
    答：否，開啟單一登入會立即要求您的所有 Google Apps 使用者使用其 Azure AD 認證進行驗證。 由於 Google Apps 不支援使用多個身分識別提供者，因此您 Google Apps 環境的身分識別提供者可以是 Azure AD 或 Google 其中之一，但不可同時是兩者。

4. **問：如果使用者透過 Windows 登入，他們是否會自動向 Google Apps 進行驗證，而不會收到輸入密碼的提示？**
   
    答：有兩個選項可允許這樣的情況。 第一個是，使用者可以透過 [Azure Active Directory Join](active-directory-azureadjoin-overview.md)登入 Windows 10 裝置。 另一個是，使用者可以登入已加入某個內部部署 Active Directory 網域的 Windows 裝置，其中此內部部署 Active Directory 已透過 [Active Directory 同盟服務 (AD FS)](active-directory-aadconnect-user-signin.md) 部署而能夠單一登入到 Azure AD。 這兩個選項都需要您執行下列教學課程的步驟，才能在 Azure AD 與 Google Apps 之間啟用單一登入。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Google Apps
2. 設定並測試 Azure AD 單一登入

## <a name="adding-google-apps-from-the-gallery"></a>從資源庫新增 Google Apps
若要設定將 Google Apps 整合到 Azure AD 中，您需要從資源庫將 Google Apps 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Google Apps，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Google Apps**，從結果面板中選取 [Google Apps]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，設定及測試與 Google Apps 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Google Apps 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Google Apps 中的相關使用者之間，建立連結關聯性。

在 Google Apps 中，將 Azure AD 中 [使用者名稱] 的值指派為 [使用者名稱] 的值，以建立連結關聯性。

若要設定及測試與 Google Apps 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Google Apps 測試使用者](#create-a-google-apps-test-user)** - 使 Google Apps 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，並在您的 Google Apps 應用程式中設定單一登入。

**若要設定與 Google Apps 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Google Apps] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. 在 [Google Apps 網域及 URL] 區段中，執行下列步驟：

    ![Google Apps 網域及 URL 單一登入資訊](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰`https://mail.google.com/a/<yourdomain.com>`

    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：

    | |
    |--|
    | `http://google.com/a/<yourdomain.com>`|
    | `http://google.com`|    
    | `google.com/<yourdomain.com>`|
    | `google.com`|

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Google Apps 用戶端支援小組](https://www.google.com/contact/)以取得這些值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [憑證]，然後將憑證檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. 在 [Google Apps 組態] 區段上，按一下 [設定 Google Apps] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中，複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![Google Apps 設定](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. 在瀏覽器中開啟新索引標籤，然後使用系統管理員帳戶登入 [Google Apps 管理控制台](http://admin.google.com/) 。

8. 按一下 [安全性] 。 如果您沒有看到連結，它可能隱藏在畫面底部的 [其他控制項]  功能表之下。
   
    ![按一下 [安全性]。][10]

9. 在 [安全性] 頁面上，按一下 [設定單一登入 (SSO)]。
   
    ![按一下 [SSO]。][11]

10. 執行下列組態變更：
   
    ![設定 SSO][12]
   
    a. 選取 [使用第三方識別提供者來設定 SSO]。

    b. 在 Google Apps 的 [登入頁面 URL] 欄位中，貼上您從 Azure 入口網站複製的 [單一登入服務 URL] 值。

    c. 在 Google Apps 的 [登出頁面 URL] 欄位中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。 

    d. 在 Google Apps 的 [變更密碼 URL] 欄位中，貼上您從 Azure 入口網站複製的 [變更密碼 URL] 值。 

    e. 在 Google Apps 中，對於 [驗證憑證]，上傳您已從 Azure 入口網站下載的憑證。

    f. 按一下 [儲存變更] 。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 Azure 入口網站的左窗格中，按一下 [Azure Active Directory] 按鈕。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，按一下 [所有使用者] 對話方塊頂端的 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列步驟：

    ![[使用者] 對話方塊](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下 [建立] 。
 
### <a name="create-a-google-apps-test-user"></a>建立 Google Apps 測試使用者

本節目標是要在 Google Apps Software 中建立名為 Britta Simon 的使用者。 Google Apps 支援預設啟用的自動佈建。 在這一節沒有您需要進行的動作。 如果 Google Apps Software 中還沒有使用者，當您嘗試存取 Google Apps Software 時，就會建立新的使用者。

>[!NOTE] 
>如果您需要手動建立使用者，請連絡 [Google 支援小組](https://www.google.com/contact/)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Google Apps 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**若要將 Britta Simon 指派給 Google Apps，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Google Apps]。

    ![應用程式清單中的 Google Apps 連結](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Google Apps 圖格時，應該會自動登入 Google Apps 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

