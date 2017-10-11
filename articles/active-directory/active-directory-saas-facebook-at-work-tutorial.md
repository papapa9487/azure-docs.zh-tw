---
title: "教學課程：Azure Active Directory 與 Workplace by Facebook 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 Workplace by Facebook 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教學課程：Azure Active Directory 與 Workplace by Facebook 整合

在本教學課程中，您會了解如何將 Workplace by Facebook 與 Azure Active Directory (Azure AD) 整合。

將 Workplace by Facebook 與 Azure AD 整合可提供下列優點：

- 您可以在 Azure AD 中控制可存取 Workplace by Facebook 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Workplace by Facebook (單一登入)。
- 您可以集中管理您的帳戶：Azure 入口網站。

如需軟體即服務 (SaaS) 應用程式與 Azure AD 整合的更多詳細資訊，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Workplace by Facebook 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Workplace by Facebook (SSO) 單一登入的訂用帳戶

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述
在本教學課程中，您會在測試環境中測試 Azure AD SSO。 本教學課程中說明的案例由二個主要建置組塊組成：

1. 從資源庫新增 Workplace by Facebook。
2. 設定和測試 Azure AD 單一登入。

## <a name="add-workplace-by-facebook-from-the-gallery"></a>從資源庫新增 Workplace by Facebook
若要設定將 Workplace by Facebook 整合到 Azure AD 中，從資源庫將 Workplace by Facebook 新增到受管理的 SaaS 應用程式清單中。

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]。 

    ![Azure Active Directory 按鈕][1]

2. 瀏覽至 [企業應用程式] > [所有應用程式]。

    ![企業應用程式刀鋒視窗][2]
    
3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式]。

    ![新增應用程式按鈕][3]

4. 在搜尋方塊中，輸入 **Workplace by Facebook**，從結果中選取 [Workplace by Facebook]。 然後選取 [新增]，以新增應用程式。

    ![結果清單中的 Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入
在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Workplace by Facebook 搭配運作的 Azure AD SSO。

若要讓 SSO 能夠運作，Azure AD 必須知道 Workplace by Facebook 與 Azure AD 中互相對應的使用者。 換句話說，應在 Azure AD 使用者與 Workplace by Facebook 中的相關使用者之間建立連結關聯性。

建立此關聯性的方法，就是將 Azure AD 中的**使用者名稱**值指派為 Workplace by Facebook 中 **Username** 的值。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD SSO，然後在 Workplace by Facebook 應用程式中設定 SSO。

1. 在 Azure 入口網站的 [Workplace by Facebook] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結][4]

2. 在 [單一登入] 對話方塊中，於 [模式] 選取 [SAML 登入]，以啟用 SSO。
 
    ![單一登入對話方塊](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. 在 [Workplace by Facebook 網域與 URL] 區段中，執行下列步驟：

    a. 在 [登入 URL] 文字方塊中，以下列模式輸入 URL︰`https://<company subdomain>.facebook.com`

    b. 在 [識別碼] 文字方塊中，以下列模式輸入 URL：`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > 這些值僅為範例。 使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Workplace by Facebook 用戶端支援小組](https://workplace.fb.com/faq/)以取得這些值。 

4. 在 [SAML 簽署憑證] 區段中，選取 [憑證 (Base64)]，然後將憑證檔案儲存在您的電腦上。

    ![憑證下載連結](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. 選取 [ **儲存**]。

    ![設定單一登入儲存按鈕](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. 在 [Workplace by Facebook 設定] 區段中，選取 [設定 Workplace by Facebook] 可開啟 [設定登入] 視窗。 從 [快速參考] 區段中複製 [登出 URL、SAML 實體識別碼和 SAML 單一登入服務 URL]。

    ![Workplace by Facebook 設定](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Workplace by Facebook 公司網站。
  
   > [!NOTE] 
   > 在 SAML 驗證過程中，Workplace 可以使用大小最多 2.5 KB 的查詢字串，將參數傳遞給 Azure AD。

8. 在 [公司儀表板] 中，請移至 [驗證] 索引標籤。

9. 在 [SAML 驗證] 下，從下拉式清單中選取 [僅限 SSO]。

10. 將您從 Azure 入口網站的 [Workplace by Facebook 設定] 區段中複製的值輸入對應的欄位中：

    *   在 [SAML URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [單一登入服務 URL] 的值。
    *   在 [SAML 簽發者 URL] 文字方塊中，貼上您從 Azure 入口網站複製的 [SAML 實體識別碼] 值。
    *   在 [SAML 登出重新導向] \(選擇性) 中，貼上您從 Azure 入口網站複製的 [登出 URL] 值。
    *   在記事本中開啟您從 Azure 入口網站下載的 **base-64 編碼憑證**。 將其內容複製到剪貼簿，然後將它貼到 [SAML 憑證] 文字方塊中。

11. 您需要輸入 [SAML 組態] 區段下所列的對象 URL、收件者 URL 和 ACS (判斷提示取用者服務) URL。

12. 捲動到區段的底部，然後選取 [測試 SSO]。 顯示 Azure AD 登入頁面的快顯視窗隨即出現。 若要驗證，請如往常一樣輸入認證。 確保從 Azure AD 傳回的電子郵件地址與您用來登入的 Workplace 帳戶相同。

13. 如果順利完成測試，請捲動到頁面底部並選取 [儲存]。

14. 使用 Workplace 的所有使用者現在都會看到進行驗證的 Azure AD 登入頁面。

您可以選擇設定 SAML 登出 URL，其可用來指向 Azure AD 的登出頁面。 當啟用並設定此設定時，就不會再將使用者導向至 Workplace 登出頁面。 相反地，系統會將使用者重新導向至 [SAML 登出重新導向] 設定中所新增的 URL。


> [!TIP]
> 現在，當您設定此應用程式時，在 [Azure 入口網站](https://portal.azure.com)內即可閱讀這些指示的簡要版本。 從 [Active Directory] > [企業應用程式] 區段新增此應用程式之後，只要選取 [單一登入] 索引標籤，即可透過底部的 [組態] 區段存取內嵌的文件。 您可以在 [Azure AD 內嵌文件]( https://go.microsoft.com/fwlink/?linkid=845985)中閱讀更多有關內嵌文件功能的資訊。

### <a name="configure-reauthentication-frequency"></a>設定重新驗證頻率

您可以將 Workplace 設定為每一天、每三天、每一週、每兩週、每一個月或一律不會提示進行 SAML 檢查。

> [!NOTE] 
>行動應用程式的 SAML 檢查最小值是設定為一週。

您也可以強制所有使用者重設 SAML。 若要這樣做，請使用 [要求所有使用者立即進行 SAML 驗證] 按鈕。


### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

![建立 Azure AD 使用者][100]

1. 在 **Azure 入口網站**的左側窗格中，選取 [Azure Active Directory]。

    ![Azure Active Directory 按鈕](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. 若要顯示使用者清單，請移至 [使用者和群組]，然後選取 [所有使用者]。
    
    ![[使用者和群組] 與 [所有使用者] 連結](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. 若要開啟 [使用者] 對話方塊，請選取 [新增]。
 
    ![[新增] 按鈕](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. 在 [使用者] 對話方塊中，執行下列動作：
 
    ![[使用者] 對話方塊](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. 在 [名稱] 文字方塊中，輸入 **BrittaSimon**。

    b. 在 [使用者名稱] 文字方塊中，輸入 BrittaSimon 的**電子郵件地址**。

    c. 選取 [顯示密碼]，並將它寫下來。

    d. 選取 [ **建立**]。
 
### <a name="create-a-workplace-by-facebook-test-user"></a>建立 Workplace by Facebook 測試使用者

本節會在 Workplace by Facebook 中建立名為 Britta Simon 的使用者。 Workplace by Facebook 支援預設啟用的 Just-In-Time 佈建。

在這一節沒有您需要進行的動作。 如果使用者不存在於 Workplace by Facebook 中，當您嘗試存取 Workplace by Facebook 時，就會建立一個新的。

>[!Note]
>如果您需要手動建立使用者，請連絡 [Workplace by Facebook 用戶端支援小組](https://workplace.fb.com/faq/)。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Workplace by Facebook 的存取權授與 Britta Simon，讓她能夠使用 Azure SSO。

![指派使用者][200] 

1. 在 Azure 入口網站中，開啟應用程式檢視。 移至目錄檢視，移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![指派使用者][201] 

2. 在應用程式清單中，選取 [Workplace by Facebook]。

    ![應用程式清單中的 Workplace by Facebook 連結](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結][202] 

4. 選取 [新增] 。 然後在 [新增指派] 窗格中，選取 [使用者和群組]。

    ![[新增指派] 窗格][203]

5. 在 [使用者和群組] 對話方塊中，選取 [使用者] 清單中的 [Britta Simon]。

6. 在 [使用者和群組] 對話方塊中，選取 [選取]。

7. 在 [新增指派] 對話方塊中，選取 [指派]。
    
### <a name="test-single-sign-on"></a>測試單一登入

如果要測試您的 SSO 設定，請開啟存取面板。
如需詳細資訊，請參閱[存取面板簡介](active-directory-saas-access-panel-introduction.md)。


## <a name="next-steps"></a>後續步驟

* 選取[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)。
* 閱讀[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)。
* 深入了解如何[設定使用者佈建](active-directory-saas-facebook-at-work-provisioning-tutorial.md)。



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

