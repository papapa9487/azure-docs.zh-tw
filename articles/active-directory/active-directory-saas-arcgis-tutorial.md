---
title: "教學課程：Azure Active Directory 與 ArcGIS Online 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 ArcGIS Online 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>教學課程：Azure Active Directory 與 ArcGIS Online 整合

在本教學課程中，您會了解如何將 ArcGIS Online 與 Azure Active Directory (Azure AD) 整合。

將 ArcGIS Online 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 ArcGIS Online 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 ArcGIS Online (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

如果您想要了解有關 SaaS 應用程式與 Azure AD 之整合的更多詳細資料，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 ArcGIS Online 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 ArcGIS Online 單一登入功能的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以在 [這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 ArcGIS Online
2. 設定並測試 Azure AD 單一登入

## <a name="adding-arcgis-online-from-the-gallery"></a>從資源庫新增 ArcGIS Online
若要設定將 ArcGIS Online 整合到 Azure AD 中，您需要從資源庫將 ArcGIS Online 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 ArcGIS Online，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新應用程式] 按鈕來新增新的應用程式。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **ArcGIS Online**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. 在結果面板中，選取 ArcGIS Online，然後按一下新增 按鈕以新增該應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 ArcGIS Online 搭配運作的 Azure AD 單一登入。

若要讓單一登入能夠運作，Azure AD 必須知道 ArcGIS Online 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者與 ArcGIS Online 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法，就是將 ArcGIS Online 中 [使用者名稱] 的值指派為 Azure AD 中 [使用者名稱] 的值。

若要設定及測試與 ArcGIS Online 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 ArcGIS Online 測試使用者](#creating-an-arcgis-online-test-user)** - 在 ArcGIS Online 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的 Britta Simon 對應項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 ArcGIS Online 應用程式中設定單一登入。

**若要設定與 ArcGIS Online 搭配運作的 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [ArcGIS Online] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，於 [模式] 選取 [SAML 登入]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. 在 [ArcGIS Online 網域及 URL] 區段上，執行下列步驟：

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    在 [登入 URL] 文字方塊中，以下列模式輸入值：`https://<company>.maps.arcgis.com`

    > [!NOTE] 
    > 這不是真正的值。 使用實際的「登入 URL」來更新此值。 請連絡 [ArcGIS Online 用戶端支援小組](http://support.esri.com/)以取得此值。 

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. 按一下 [儲存]  按鈕。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ArcGIS 公司網站。

7. 按一下 [編輯設定]。

    ![編輯設定](./media/active-directory-saas-arcgis-tutorial/ic784742.png "編輯設定")

8. 按一下 [安全性] 。

    ![安全性](./media/active-directory-saas-arcgis-tutorial/ic784743.png "安全性")

9. 在 [企業登入] 下方，按一下 [設定識別提供者]。

    ![企業登入](./media/active-directory-saas-arcgis-tutorial/ic784744.png "企業登入")

10. 在 [設定識別提供者]  組態頁面上，執行下列步驟：
   
    ![設定識別提供者](./media/active-directory-saas-arcgis-tutorial/ic784745.png "設定識別提供者")
   
    a. 在 [名稱] 文字方塊中，輸入您的組織名稱。

    b.這是另一個 C# 主控台應用程式。 針對 [用來提供企業識別提供者之中繼資料的方法]，選取 [檔案]。

    c. 若要上傳您下載的中繼資料檔，請按一下 [選擇檔案] 。

    d. 按一下 [設定識別提供者]。

> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本！  從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要按一下 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以從以下連結閱讀更多有關內嵌文件功能的資訊：[Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 Britta Simon 的「電子郵件地址」。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-an-arcgis-online-test-user"></a>建立 ArcGIS Online 測試使用者

若要讓 Azure AD 使用者能夠登入 ArcGIS Online，必須將他們佈建到 ArcGIS Online。  
就 ArcGIS Online 而言，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 登入您的 **ArcGIS** 租用戶。

2. 按一下 [邀請成員]。
   
    ![邀請成員](./media/active-directory-saas-arcgis-tutorial/ic784747.png "邀請成員")

3. 選取 [自動新增成員而不傳送電子郵件]，然後按 [下一步]。
   
    ![自動新增成員](./media/active-directory-saas-arcgis-tutorial/ic784748.png "自動新增成員")

4. 在 [成員]  對話頁面上，執行下列步驟：
   
     ![新增並檢閱](./media/active-directory-saas-arcgis-tutorial/ic784749.png "新增並檢閱")
    
     a. 輸入您想要佈建之有效 AAD 帳戶的 [電子郵件]、[名字] 和 [姓氏]。
  
     b.這是另一個 C# 主控台應用程式。 按一下 [新增並檢閱]。
5. 檢閱您已輸入的資料，然後按一下新增成員。
   
    ![新增成員](./media/active-directory-saas-arcgis-tutorial/ic784750.png "新增成員")
        
    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，並依照連結在啟用其帳戶前進行確認。

### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 ArcGIS Online 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 ArcGIS Online，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [ArcGIS Online]。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [ArcGIS Online] 圖格時，應該會自動登入您的 ArcGIS Online 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

