---
title: "教學課程：Azure Active Directory 與 QPrism 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 QPrism 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: a380e9c07710e0c79a44e036f9c5bd72a731fe40
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>教學課程：Azure Active Directory 與 QPrism 整合

在本教學課程中，您會了解如何將 QPrism 與 Azure Active Directory (Azure AD) 進行整合。

QPrism 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 QPrism 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 QPrism (單一登入)。
- 您可以集中管理您的帳戶：Azure 入口網站。

如需有關 SaaS 應用程式與 Azure AD 整合的詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 QPrism 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 QPrism 單一登入的訂用帳戶

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 QPrism
2. 設定並測試 Azure AD 單一登入

## <a name="add-qprism-from-the-gallery"></a>從資源庫新增 QPrism
若要設定將 QPrism 整合到 Azure AD 中，您需要從資源庫將 QPrism 新增到受管理的 SaaS 應用程式清單。

**從資源庫新增 QPrism：**

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增應用程式，請在對話方塊的頂端，選取 [新增應用程式]。

    ![新增應用程式按鈕][3]

4. 在 [搜尋] 方塊中，輸入**QPrism**，然後從結果面板中選取 **QPrism**。 然後按一下 [新增] 來新增應用程式。

    ![結果清單中的 QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 "Britta Simon" 的測試使用者身分，使用 QPrism 設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 QPrism 與 Azure AD 中互相對應的使用者。 換句話說，必須有 Azure AD 使用者和 QPrism 中相關使用者之間的連結關聯性。

若要建立此關聯性，在 QPrism 中將 Azure AD 中**使用者名稱**的值，指派為 **Username** 的值。

若要設定及測試與 QPrism 搭配運作的 Azure AD 單一登入，請完成下列建構元素：

1. [設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)，讓您的使用者能夠使用此功能。
2. [建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)，以使用 Britta Simon 測試 Azure AD 單一登入。
3. [建立 QPrism 測試使用者](#create-a-qprism-test-user)，使 QPrism 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
4. [指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. [測試單一登入](#test-single-sign-on)，驗證設定是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 QPrism 應用程式中設定單一登入。

1. 在 Azure 入口網站的 [QPrism] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結][4]

2. 若要啟用單一登入，請在 [單一登入] 對話方塊中，於 [模式] 選取 [SAML 登入]。
 
    ![單一登入對話方塊](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. 在 [QPrism 網域與 URL] 區段中，執行下列操作：

    ![QPrism 網域與 URL 單一登入資訊](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<customer domain>.qmyzone.com/login`

    b. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > 這些都不是真正的值。 使用實際「識別碼」的及「登入 URL」來更新這些值。 請連絡 [QPrism 用戶端支援小組](mailto:qsupport-ce@quatrro.com)以取得這些值。 

4. 若要產生**中繼資料** URL，請執行下列動作：

    a. 選取 [應用程式註冊]。
    
    ![設定單一登入的應用程式註冊](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. 選取 [端點] 以開啟 [端點] 對話方塊。  
    
    ![設定單一登入端點](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. 選取複製按鈕複製 [同盟中繼資料文件] URL，並將它貼到 [記事本]。
    
    ![設定單一登入端點](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. 現在，移至 [QPrism] 的屬性頁面，使用 [複製] 複製 [應用程式識別碼]。 然後將它貼到 [記事本]。
 
    ![設定單一登入的應用程式識別碼](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. 使用下列模式產生**中繼資料 URL**︰`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. 若要在 **QPrism** 端設定單一登入，請將**中繼資料 URL** 傳送給 [QPrism 支援小組](mailto:qsupport-ce@quatrro.com)。 他們會確保兩端的 SAML 單一登入連線已正確設定。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [設定] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

   ![建立 Azure AD 測試使用者][100]

**在 Azure AD 中建立測試使用者：**

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. 若要開啟 [使用者] 對話方塊，請在 [所有使用者] 對話方塊的頂端，選取 [新增]。

    ![[新增] 按鈕](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. 在 [使用者] 對話方塊中，執行下列動作：

    ![[使用者] 對話方塊](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. 在 [名稱] 方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 方塊中，輸入使用者 Britta Simon 的電子郵件地址。

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 選取 [ **建立**]。
 
### <a name="create-a-qprism-test-user"></a>建立 QPrism 測試使用者

在本節中，您要在 QPrism 中建立名為 Britta Simon 的使用者。 請與 [QPrism 支援小組](mailto:qsupport-ce@quatrro.com)合作，在 QPrism 平台中加入使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。 

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 QPrism 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者角色][200] 

**將 Britta Simon 指派給 QPrism：**

1. 在 Azure 入口網站中，開啟應用程式檢視，然後瀏覽至目錄檢視。 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取[QPrism]。

    ![應用程式清單中的 QPrism 連結](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202]

4. 選取 [新增] 。 然後，在 [新增指派] 下，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，選取 [選取]。

7. 在 [新增指派] 下，選取 [指派]。
    
### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

在存取面板中，當您選取 QPrism 圖示時，您應該會自動登入 QPrism 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他資源

* [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

