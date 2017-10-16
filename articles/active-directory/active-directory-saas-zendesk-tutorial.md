---
title: "教學課程：Azure Active Directory 與 Zendesk 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Zendesk 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教學課程：Azure Active Directory 與 Zendesk 整合

在本教學課程中，您會了解如何整合 Zendesk 與 Azure Active Directory (Azure AD)。

Zendesk 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Zendesk 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Zendesk (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Zendesk 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Zendesk 單一登入的訂用帳戶


> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。


## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Zendesk
2. 設定並測試 Azure AD 單一登入


## <a name="adding-zendesk-from-the-gallery"></a>從資源庫新增 Zendesk
若要設定將 Zendesk 整合到 Azure AD 中，您需要從資源庫將 Zendesk 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Zendesk，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **Zendesk**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. 在結果窗格中，選取 Zendesk，然後按一下新增 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 Zendesk 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Zendesk 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 Zendesk 中相關使用者之間的連結關聯性。

建立此連結關聯性的方法，就是指派 Azure AD 中**使用者名稱**的值作為 Zendesk 中 **Username** 的值。

若要設定及測試與 Zendesk 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Zendesk 測試使用者](#creating-a-zendesk-test-user)** - 讓 Zendesk 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Zendesk 應用程式中設定單一登入。

**若要設定與 Zendesk 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Zendesk] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. 在 [Zendesk 網域與 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入值：`https://<subdomain>.zendesk.com`

    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，使用下列模式將值輸入：`https://<subdomain>.zendesk.com`

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的登入 URL 和識別碼 URL 來更新這些值。 請連絡 [Zendesk 支援小組](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)以取得這些值。 

4. Zendesk 需要特定格式的 SAML 判斷提示。 沒有任何必要 SAML 屬性，但您可以選擇性依照下列步驟，從 [使用者屬性] 區段新增屬性： 

     ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. 按一下 [檢視和編輯所有其他屬性] 核取方塊。
     
    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b.這是另一個 C# 主控台應用程式。 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. 在 [名稱] 文字方塊中，輸入屬性名稱 (例如 **emailaddress**)。
    
    d. 從 [值] 清單中選取屬性值 (作為 **user.mail**)。
    
    e. 按一下 [確定]。
 
    > [!NOTE] 
    > 您可以使用擴充屬性來新增預設不在 Azure AD 中的屬性。 按一下[可以在 SAML 中設定的使用者屬性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，以取得 **Zendesk**接受的完整 SAML 屬性清單。 

5. 在 [SAML 簽署憑證] 區段上，複製憑證的 [指紋] 值。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. 在 [Zendesk 組態] 區段上，按一下 [設定 Zendesk] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL 和 SAML 單一登入服務 URL]。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Zendesk 公司網站。

8. 按一下 Admin 。

9. 在左側導覽窗格中按一下 設定，然後按一下安全性。

10. 在 [安全性]  頁面上執行下列步驟： 
   
     ![安全性](./media/active-directory-saas-zendesk-tutorial/ic773089.png "安全性")

    ![單一登入](./media/active-directory-saas-zendesk-tutorial/ic773090.png "單一登入")

     a. 按一下 [系統管理員和代理程式] 索引標籤。

     b.這是另一個 C# 主控台應用程式。 選取 [單一登入 (SSO) 和 SAML]，然後選取 [SAML]。

     c. 在 [SAML SSO URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 單一登入服務 URL] 值。 

     d. 在 [遠端登出 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。
        
     e. 在 [憑證指紋] 文字方塊中，貼上您從 Azure 入口網站複製的憑證 [指紋] 值。
     
     f. 按一下 [儲存] 。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。 

### <a name="creating-a-zendesk-test-user"></a>建立 Zendesk 測試使用者

若要讓 Azure AD 使用者登入 **Zendesk**，必須將他們佈建到 **Zendesk** 中。  
視應用程式中指派的角色而定，這是預期的行為：

 1. [使用者] 帳戶會在登入時自動佈建。
 2. 登入之前，必須在 **Zendesk** 中手動佈建 [代理程式] 和 [系統管理員] 帳戶。
 
**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **Zendesk** 租用戶。

2. 選取 [客戶清單]  索引標籤。

3. 選取 [使用者] 索引標籤，然後按一下 [新增]。
   
    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/ic773632.png "新增使用者")
4. 輸入您要佈建之現有 Azure AD 帳戶的電子郵件地址，然後按一下儲存 。
   
    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/ic773633.png "新增使用者")

> [!NOTE]
> 您可以使用任何其他的 Zendesk 使用者帳戶建立工具或 Zendesk 提供的 API，佈建 AAD 使用者帳戶。


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Zendesk 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 Zendesk，請執行以下步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Zendesk]。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Zendesk 圖格時，應該會自動登入您的 Zendesk 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png
