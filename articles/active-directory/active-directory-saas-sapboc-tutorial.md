---
title: "教學課程：Azure Active Directory 與 SAP Business Object Cloud 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 SAP Business Object Cloud 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a09e965b6fc9b89023c09092860fcf79773a4518
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>教學課程：Azure Active Directory 與 SAP Business Object Cloud 整合

在本教學課程中，您將了解如何整合 SAP Business Object Cloud 與 Azure Active Directory (Azure AD)。

SAP Business Object Cloud 與 Azure AD 整合有下列優點：

- 您可以在 Azure AD 中控制可存取 SAP Business Object Cloud 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SAP Business Object Cloud (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要進行 Azure AD 與 SAP Business Object Cloud 整合的設定，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 SAP Business Object Cloud 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 SAP Business Object Cloud
2. 設定並測試 Azure AD 單一登入

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>從資源庫新增 SAP Business Object Cloud
若要進行 SAP Business Object Cloud 與 Azure AD 整合的設定，您需要從資源庫將 SAP Business Object Cloud 新增至受管理 SaaS 應用程式清單中。

**若要從資源庫新增 SAP Business Object Cloud，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **SAP Business Object Cloud**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. 在結果面板中，選取 [SAP Business Object Cloud]，然後按一下 [新增] 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您將以名為 "Britta Simon" 的測試使用者身分，設定及測試與 SAP Business Object Cloud 搭配運作的 Azure AD 單一登入。

為了讓單一登入正常運作，Azure AD 必須知道 Azure AD 使用者在 SAP Business Object Cloud 中的對應使用者是誰。 換句話說，必須在 Azure AD 使用者與 SAP Business Object Cloud 的相關使用者之間建立連結關聯性。

在 SAP Business Object Cloud 中，將 Azure AD 中 [使用者名稱] 的值指派為 [使用者名稱] 的值，以建立連結關聯性。

若要設定及測試與 SAP Business Object Cloud 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 SAP Business Object Cloud 測試使用者](#creating-an-sap-business-object-cloud-test-user)** - 使 SAP Business Object Cloud 中 Britta Simon 的對應使用者連結到該使用者在 Azure AD 中的代表身分。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您將在 Azure 入口網站中啟用 Azure AD 單一登入，並在 SAP Business Object Cloud 應用程式中設定單一登入。

**若要設定 SAP Business Object Cloud 的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [SAP Business Object Cloud] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. 在 [SAP Business Object Cloud 網域及 URL] 區段中，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. 在 [登入 URL] 文字方塊中，使用下列模式輸入 URL︰ 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b.這是另一個 C# 主控台應用程式。 在 [識別碼] 文字方塊中，使用下列模式輸入 URL：
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > 這些都不是真正的值。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [SAP Business Object Cloud 用戶端支援小組](https://www.sap.com/product/analytics/cloud-analytics.support.html)，以取得登入 URL，以及可下載管理主控台 (本教學課程後面部分將說明) 裡 SAP Business Object Cloud 中繼資料的識別碼。 
 
4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將中繼資料檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 SAP Business Object Cloud 公司網站。

7. 移至 [功能表] > [系統] > [管理]
    
    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. 在 [安全性] 索引標籤上，按一下 [Edit – pen icon]\(編輯 - 畫筆圖示\)。
    
    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. 選取 [SAML 單一登入 (SSO)] 做為驗證方法。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. 按一下 [下載]，以下載服務提供者中繼資料。 從檔案取出 **entityID** 的值，然後貼到 Azure 入口網站上，[SAP Business Object Cloud 網域及 URL] 區段中的 [識別碼] 文字方塊。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. 在執行**上傳識別提供者中繼資料**步驟時，按一下 [上傳..] 按鈕，上傳從 Azure 入口網站下載的中繼資料檔案。 

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. 從清單中選取您想要用來實作的適用**使用者屬性**，以對應至識別提供者。 使用 [Custom SAML Mapping]\(自訂 SAML 對應\) 選項，以在 [使用者] 頁面中輸入自訂屬性，或選取 [電子郵件] 或 [使用者識別碼] 做為使用者屬性。 例如 此處的 [電子郵件] 已是選取狀態，因為我們已將使用者識別碼宣告與 Azure 入口網站上 [使用者屬性] 區段中的 userprincipalname 屬性對應，該屬性提供了唯一的使用者電子郵件，系統會將該電子郵件透過至每個成功的 SAML 回應傳送至 SAP Business Object Cloud 應用程式。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. 在 [Login Credential (Email)]\(登入認證 (電子郵件)\) 文字方塊中，輸入您的電子郵件，然後按一下 [驗證帳戶] 按鈕，讓系統可將您的登入認證加入您的帳戶。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. 按一下 [儲存] 圖示

    ![儲存](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後按一下 [所有使用者]。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，按一下對話方塊頂端的 [新增]。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>建立 SAP Business Object Cloud 測試使用者

若要讓 Azure AD 使用者能夠登入 SAP Business Object Cloud，您必須將他們佈建至 SAP Business Object Cloud。 針對 SAP Business Object Cloud，必須以手動方式進行佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 SAP Business Object Cloud 公司網站。

2. 移至 [功能表] > [安全性] > [使用者]

    ![新增員工](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. 在 [使用者] 頁面上，按一下 **“+”**，以新增使用者詳細資料，並執行下列步驟：

    ![邀請人員](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. 在 [使用者識別碼] 文字方塊中，輸入使用者的使用者識別碼，例如 Britta。

    b. 在 [名字] 文字方塊中，輸入使用者的名字，例如 Britta。

    c. 在 [姓氏] 文字方塊中，輸入使用者的姓氏，例如 Simon。

    d. 在 [顯示名稱] 文字方塊中，輸入使用者的全名，例如 Britta Simon。

    e. 在 [電子郵件] 文字方塊中，輸入使用者的電子郵件地址，例如 brittasimon@contoso.com。

    f. 為使用者選取適當的角色。

      ![角色](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. 按一下 [儲存] 圖示。 


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您將把 SAP Business Object Cloud 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派到 SAP Business Object Cloud，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [SAP Business Object Cloud]。

    ![設定單一登入](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

按一下存取面板中的 [SAP Business Object Cloud] 圖格，應會自動登入您的 SAP Business Object Cloud 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png


