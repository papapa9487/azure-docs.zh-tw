---
title: "教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Amazon Web Services (AWS) 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合

在本教學課程中，您將了解如何整合 Amazon Web Services (AWS) 與 Azure Active Directory (Azure AD)。

Amazon Web Services (AWS) 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Amazon Web Services (AWS) 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Amazon Web Services (AWS) (單一登入)
- 您可以在 Azure 入口網站中集中管理您的帳戶

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Amazon Web Services (AWS) 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Amazon Web Services (AWS) 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月試用。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD 單一登入。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Amazon Web Services (AWS)
2. 設定並測試 Azure AD 單一登入

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>從資源庫新增 Amazon Web Services (AWS)
若要設定 Amazon Web Services (AWS) 與 Azure AD 整合，您需要從資源庫將 Amazon Web Services (AWS) 新增到受管理的 SaaS App 清單。

**若要從資源庫新增 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。 

    ![Active Directory][1]

2. 瀏覽至 [企業應用程式]。 然後移至 [所有應用程式]。

    ![應用程式][2]
    
3. 按一下對話方塊頂端的 [新增] 按鈕。

    ![應用程式][3]

4. 在搜尋方塊中，輸入 **Amazon Web Services (AWS)**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. 在結果窗格中，選取 Amazon Web Services (AWS)，然後按一下新增 按鈕以新增應用程式。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定並測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Amazon Web Services (AWS) 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Amazon Web Services (AWS) 中的相關使用者之間建立連結關聯性。

建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 Amazon Web Services (AWS) 中 **Username** 的值。

若要使用 Amazon Web Services (AWS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
3. **[建立 Amazon Web Services 測試使用者](#creating-an-amazon-web-services-test-user)** - 使 Amazon Web Services (AWS) 中對應的 Britta Simon 連結到她在 Azure AD 中的代表項目。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 驗證組態是否能運作。

### <a name="configuring-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Amazon Web Services (AWS) 應用程式中設定單一登入。

**若要使用 Amazon Web Services (AWS) 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure 入口網站的 [Amazon Web Services (AWS)] 應用程式整合頁面上，按一下 [單一登入]。

    ![設定單一登入][4]

2. 在 [單一登入] 對話方塊上，選取 [以 SAML 為基礎的登入] 作為 [模式]，以啟用單一登入。
 
    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. 在 [Amazon Web Services (AWS) 網域和 URL] 區段中，使用者不需要執行任何步驟，因為應用程式已經與 Azure 預先整合。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. 在 [SAML 簽署憑證] 區段上，按一下 [中繼資料 XML]，然後將 XML 檔案儲存在您的電腦上。
    
    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Amazon Web Services (AWS) 軟體應用程式預期要有特定格式的 SAML 判斷提示。 請設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性] 區段中管理這些屬性的值。 以下螢幕擷取畫面顯示上述的範例。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. 在 [單一登入] 對話方塊的 [使用者屬性] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：
    
    | 屬性名稱  | 屬性值 | 命名空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 角色            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >您需要設定在 Azure AD 中的使用者佈建，以從 AWS 主控台擷取所有角色。 請參閱下面的佈建步驟。

    a. 按一下 [新增屬性] 來開啟 [新增屬性] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. 在 [值] 清單中，選取該列所顯示的值。 如上所述新增 [命名空間] 值。
    
    d. 按一下 [確定] 。

7. 按一下 [儲存] 按鈕以在 Azure 上儲存設定。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web Services (AWS) 公司網站。

9. 按一下 [主控台首頁] 。
   
    ![設定單一登入][11]

10. 從 [安全性、身分識別與合規性] 服務按一下 [IAM]。
   
    ![設定單一登入][12]

11. 按一下 識別提供者，然後按一下建立提供者。
   
    ![設定單一登入][13]

12. 在 [設定提供者]  對話方塊頁面上，執行下列步驟：
   
    ![設定單一登入][14]
 
    a. 針對 [提供者類型]，選取 [SAML]。

    b. 在 [提供者名稱] 文字方塊中，輸入提供者名稱 (例如：*WAAD*)。

    c. 若要上傳您下載的中繼資料檔，請按一下 [選擇檔案]。

    d. 按一下頁面底部的 [新增] 。

13. 在 [驗證提供者資訊] 對話方塊頁面上，按一下 [建立]。 
    
    ![設定單一登入][15]

14. 按一下 角色，然後按一下建立新角色。 
    
    ![設定單一登入][16]

15. 在 [設定角色名稱]  對話方塊上，執行下列步驟： 
    
    ![設定單一登入][17] 

    a. 在 [角色名稱] 文字方塊中，輸入角色名稱 (例如：*TestUser*)。 

    b. 按一下頁面底部的 [新增] 。

16. 在 [選取角色類型]  對話方塊上，執行下列步驟： 
    
    ![設定單一登入][18] 

    a. 選取 [識別提供者存取的角色]。 

    b. 在 [授與 SAML 提供者的 Web 單一登入 (WebSSO) 存取] 區段中，按一下 [選取]。

17. 在 [建立信任]  對話方塊上，執行下列步驟：  
    
    ![設定單一登入][19] 

    a. 針對 SAML 提供者，請選取您先前建立的 SAML 提供者 (例如：WAAD)
  
    b. 按一下頁面底部的 [新增] 。

18. 在 [確認角色信任] 對話方塊上，按 [下一步]。
    
    ![設定單一登入][32]

19. 在 [附加原則] 對話方塊上，按 [下一步]。
    
    ![設定單一登入][33]

20. 在 [檢閱]  對話方塊上，執行下列步驟：
    
    ![設定單一登入][34]
 
    a. 按一下 [建立角色]。

    b. 建立所需數量的角色，並將它們對應至識別提供者。

21. 立即設定使用者佈建，以從 AWS 擷取所有角色。

    a. 在 AWS 主控台中使用您的根帳戶登入

    b.這是另一個 C# 主控台應用程式。 在右上角按一下您的名稱，然後按一下我的安全性認證 選項。 這會開啟一個畫面顯示警告訊息。 按一下 [安全性認證] 按鈕以通過此畫面。
        
       ![設定單一登入][36]

       ![設定單一登入][37]

    c. 在 [存取金鑰] 區段中按一下 [建立新的存取金鑰] 按鈕。 這會產生存取金鑰識別碼和權杖值。
    
       ![設定單一登入][38]

    d. 複製這兩個值，同時加以下載，您才不會遺失它。

    e. 在 Azure 入口網站的 [Amazon Web Services (AWS)] 應用程式整合頁面上，按一下 [佈建]。
        
       ![設定單一登入][35]

    f. 將 [佈建模式] 設定為 [自動]。
        
       ![設定單一登入][39]

    g. 現在於 [clientsecret] 和 [祕密權杖] 中，貼上您從 AWS 主控台複製的對應值。
    
    h. 您可以按一下 [測試連線] 按鈕來測試連線能力。 一旦成功，您就可以啟動佈建連接器。
       
       ![設定單一登入][40]

    i. 現在讓 [佈建狀態] 設定為 [開啟]。 這會開始從應用程式擷取角色。

       ![設定單一登入][41]

    > [!NOTE]
    > Azure AD 佈建服務會每隔一段時間執行一次，以從 AWS 同步處理角色。 您應該會看到 Azure AD 中所有識別提供者附加的 AWS 角色，而且您可以在將應用程式指派給使用者或群組時使用這些角色。

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. 移至 [使用者和群組]，然後按一下 [所有使用者] 以顯示使用者清單。
    
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. 在對話方塊的頂端，按一下 [新增] 以開啟 [使用者] 對話方塊。
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. 在 [使用者]  對話頁面上，執行下列步驟：
 
    ![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b.這是另一個 C# 主控台應用程式。 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼] 並記下 [密碼] 的值。

    d. 按一下 [建立] 。
 
### <a name="creating-an-amazon-web-services-test-user"></a>建立 Amazon Web Services 測試使用者

若要讓 Azure AD 使用者能夠登入 Amazon Web Services (AWS)，必須將他們佈建到 Amazon Web Services (AWS) 中。 Amazon Web Services (AWS) 需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Amazon Web Services (AWS)** 公司網站。

2. 按一下 [主控台首頁]  圖示。 
   
    ![設定單一登入][11]

3. 按一下 [身分識別與存取管理]。 
   
    ![設定單一登入][28]

4. 在儀表板中，按一下 使用者，然後按一下建立新使用者。 
   
    ![設定單一登入][29]

5. 在 [建立使用者] 對話方塊中，執行下列步驟： 
   
    ![設定單一登入][30]   
    
    a. 在 [輸入使用者名稱] 文字方塊中，輸入 Brita Simon 在 Azure AD 中的使用者名稱 (userprincipalname)。

    b. 按一下 [建立]。
        
### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Amazon Web Services (AWS) 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

![指派使用者][200] 

**若要將 Britta Simon 指派給 Amazon Web Services (AWS)，請執行下列步驟：**

1. 在 Azure 入口網站中，開啟應用程式檢視，接著瀏覽至目錄檢視並移至 [企業應用程式]，然後按一下 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Amazon Web Services (AWS)] 。

    ![設定單一登入](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. 在左側功能表中，按一下 [使用者和群組]。

    ![指派使用者][202] 

4. 按一下 [新增] 按鈕。 然後選取 [新增指派] 對話方塊上的 [使用者和群組]。

    ![指派使用者][203]

5. 在 [使用者和群組] 對話方塊上，選取 [使用者] 清單中的 [Britta Simon]。

6. 按一下 [使用者和群組] 對話方塊上的 [選取] 按鈕。

7. 在 [選取角色] 索引標籤上，為使用者選取適當的角色。 所有角色都會以角色名稱和識別提供者名稱顯示。 如此一來，您可以輕鬆地從 AWS 識別角色。

8. 按一下 [新增指派] 對話方塊上的 [指派] 按鈕。
    
### <a name="testing-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Amazon Web Services (AWS)] 圖格時，應該會自動登入您的 Amazon Web 服務 (AWS) 應用程式。 

## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
