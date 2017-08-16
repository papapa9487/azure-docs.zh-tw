---
title: "教學課程：Azure Active Directory 與 Absorb LMS 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Absorb LMS 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 3c68c3ac7d6be593476d419f8c015931b206eead
ms.contentlocale: zh-tw
ms.lasthandoff: 08/05/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>教學課程：Azure Active Directory 與 Absorb LMS

在本教學課程中，您將了解如何整合 Absorb LMS 與 Azure Active Directory (Azure AD)。

將 Absorb LMS 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Absorb LMS 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Absorb LMS (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要知道與 Azure AD 整合的 SaaS 應用程式詳細資訊，請參閱： [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定與 Absorb LMS 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Absorb LMS 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Absorb LMS
2. 設定並測試 Azure AD 單一登入

## <a name="adding-absorb-lms-from-the-gallery"></a>從資源庫新增 Absorb LMS
若要設定將 Absorb LMS 整合到 Azure AD 中，您需要從資源庫將 Absorb LMS 新增到受管理的 SaaS app 清單。

**若要從資源庫新增 Absorb LMS，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Absorb LMS**，從結果面板中選取 [Absorb LMS]，然後按一下 [新增] 按鈕以新增應用程式。

    ![結果清單中的 Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Absorb LMS 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 Absorb LMS 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 Absorb LMS 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 Absorb LMS 中 [Username] 的值指派為 Azure AD 中 [使用者名稱] 的值。

若要設定及測試與 Absorb LMS 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Absorb LMS 測試使用者](#create-an-absorb-lms-test-user)** - 使 Absorb LMS 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Absorb LMS 應用程式中設定單一登入。

**若要設定與 Absorb LMS 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Absorb LMS] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![單一登入對話方塊](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. 在 [Absorb LMS 網域與 URL] 區段中，執行下列步驟：

    ![Absorb LMS 網域與 URL 單一登入資訊](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：`https://<subdomain>.myabsorb.com/Account/SAML`

    b. 在 [回覆 URL] 文字方塊中，以下列模式輸入 URL：`https://<subdomain>.myabsorb.com/Account/SAML`
     
    > [!NOTE] 
    > 這些都不是真正的值。 請使用實際的識別碼和回覆 URL 更新這些值。 請連絡 [Absorb LMS 用戶端支援小組](https://www.absorblms.com/support)以取得這些值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. 按一下 [儲存]  按鈕。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. 在 [Absorb LMS 組態] 區段上，按一下 [設定 Absorb LMS] 以開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL 和 SAML 單一登入服務 URL]。

    ![Absorb LMS 設定](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Absorb LMS 公司網站。

9. 按一下系統管理介面上的 [帳戶圖示]。 

    ![設定單一登入](./media/active-directory-saas-absorblms-tutorial/1.png)

10. 按一下 [入口網站設定]。

    ![設定單一登入](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. 按一下 [使用者] 索引標籤。

    ![設定單一登入](./media/active-directory-saas-absorblms-tutorial/3.png)

12. 執行下列步驟以存取單一登入設定欄位：

    ![設定單一登入](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. 選取適當**模式**。

    b. 在記事本中開啟您從 Azure 入口網站下載的憑證，移除 **---BEGIN CERTIFICATE---** 和 **---END CERTIFICATE---** 標記，然後在 [金鑰] 文字方塊中貼上其餘內容。
    
    c. 在 [識別碼屬性] 中，選取您已設定為 Azure AD 使用者識別碼的適當屬性 (例如，如果在 Azure AD 中選取 userprinciplename，則這裡會選取使用者名稱)。

    d. 在 [登入 URL] 中，貼上您從 Azure 入口網站的 [設定登入] 視窗所複製的 **SAML 單一登入服務 URL** 值。

    e. 在 [登出 URL] 中，貼上您從 Azure 入口網站的 [設定登入] 視窗所複製的**登出 URL** 值。

13. 啟用 [只允許 SSO 登入]。

    ![設定單一登入](./media/active-directory-saas-absorblms-tutorial/5.png)

14. 按一下 [儲存]。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 測試使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![[新增] 按鈕](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![[使用者] 對話方塊](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。

### <a name="create-an-absorb-lms-test-user"></a>建立 Absorb LMS 測試使用者

若要讓 Azure AD 使用者能夠登入 Absorb LMS，必須將他們佈建到 Absorb LMS。  
就 Absorb LMS 而言，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 Absorb LMS 公司網站。

2. 按一下 [使用者] 索引標籤。

    ![邀請人員](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. 按一下 [使用者] 索引標籤底下的 [使用者]。

    ![邀請人員](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  從 [新增] 下拉式清單選取 [使用者]。

    ![邀請人員](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. 在 [新增使用者] 頁面上，執行下列步驟：

    ![邀請人員](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. 在 [名字] 文字方塊中輸入名字，例如 Britta。

    b. 在 [姓氏] 文字方塊中輸入姓氏，例如 Simon。
    
    c. 在 [使用者名稱] 文字方塊中輸入使用者名稱，例如 Britta Simon。

    d. 在 [密碼] 文字方塊中，輸入 Britta Simon 的密碼。

    e. 在 [確認密碼] 文字方塊中，輸入相同的密碼。
    
    f. 使其 [啟用]。   

6. 按一下 [儲存]。
 
### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Absorb LMS 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200]

**若要將 Britta Simon 指派給 Absorb LMS，請執行以下步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Absorb LMS]。

    ![應用程式清單中的 Absorb LMS 連結](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![[使用者和群組] 連結][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

在存取面板中按一下 [Absorb LMS] 圖格，系統就會自動將您登入 Absorb LMS 應用程式。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://msdn.microsoft.com/library/dn308586)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


