---
title: "教學課程：Azure Active Directory 與 TOPdesk - Secure 整合 | Microsoft Docs"
description: "了解如何使用 TOPdesk - Secure 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>教學課程：Azure Active Directory 與 TOPdesk - Secure 整合
本教學課程的目的是要示範 Azure 與 TOPdesk - Secure 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂用帳戶
* 已啟用 TOPdesk - Secure 單一登入功能的訂用帳戶

完成本教學課程之後，您指派給 TOPdesk - Secure 的 Azure AD 使用者就能夠從您的 TOPdesk - Secure 公司網站 (服務提供者起始登入)，或使用 [存取面板](active-directory-saas-access-panel-introduction.md)來單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1. 啟用 TOPdesk - Secure 的應用程式整合
2. 設定單一登入
3. 設定使用者佈建
4. 指派使用者

![案例](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "案例")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>啟用 TOPdesk - Secure 的應用程式整合
本節的目的是要說明如何啟用 TOPdesk - Secure 的應用程式整合。

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>若要啟用 TOPdesk - Secure 的應用程式整合，請執行下列步驟：
1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
    ![應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "應用程式")

4. 按一下頁面底部的 [新增]  。
   
    ![新增應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "新增應用程式")

5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
    ![從資源庫新增應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "從資源庫新增應用程式")

6. 在**搜尋方塊**中，輸入 **TOPdesk - Secure**。
   
    ![應用程式資源庫](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "應用程式資源庫")

7. 在結果窗格中，選取 TOPdesk - Secure，然後按一下完成 以新增應用程式。
   
    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## <a name="configuring-single-sign-on"></a>設定單一登入
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TOPdesk - Secure。  
設定 TOPdesk - Secure 的單一登入需要您上傳標誌的圖示檔。 若要取得圖示檔，請連絡 TOPdesk 支援小組。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟：
1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。
2. 在 [TOPdesk] 功能表中按一下 [設定]。
   
    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "設定")

3. 按一下 [登入設定] 。
   
    ![登入設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "登入設定")

4. 展開 登入設定 功能表，然後按一下一般。
   
    ![一般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "一般")

5. 在 [SAML 登入] 組態區段的 **Secure** 區段中，執行下列步驟：
   
    ![技術設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "技術設定")
   
    a. 按 [下載]  來下載公用中繼資料檔案，然後再將它儲存在本機電腦上。
   
    b. 開啟此中繼資料檔案，然後找到 **AssertionConsumerService** 節點。
    
    ![判斷提示取用者服務](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "判斷提示取用者服務")
   
    c. 複製 **AssertionConsumerService** 值。  
      
    > [!NOTE]
    > 在本教學課程稍後的＜ **設定應用程式 URL** ＞一節中，您將需要這個值。
    > 
    > 

6. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **Azure 傳統入口網站** 。

7. 在 [TOPdesk - Secure] 應用程式整合頁面上，按一下 [設定單一登入] 以開啟 [設定單一登入] 對話方塊。
   
    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "設定單一登入")

8. 在 [要如何讓使用者登入 TOPdesk - Secure] 頁面上，選取 [Microsoft Azure AD 單一登入]，然後按 [下一步]。
   
    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "設定單一登入")

9. 在 [設定應用程式 URL]  頁面上，執行下列步驟：
   
    ![設定應用程式 URL](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "設定應用程式 URL")
   
    a. 在 [TOPdesk - Secure 登入 URL] 文字方塊中，輸入使用者用來登入 TOPdesk - Secure 應用程式的 URL (例如："*https://qssolutions.topdesk.net*")。
   
    b. 在 [TOPdesk - Public 回覆 URL] 文字方塊中，貼上 **TOPdesk - Secure AssertionConsumerService URL** (例如："*https://qssolutions.topdesk.net/tas/public/login/saml*")。
   
    c. 按一下 [下一步] 。

10. 在 [設定在 TOPdesk - Secure 單一登入] 頁面上，若要下載您的中繼資料檔，請按一下 [下載中繼資料]，然後將檔案儲存在本機電腦上。
    
    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "設定單一登入")

11. 若要建立憑證檔案，請執行下列步驟：
    
    ![憑證](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "憑證")
    
    a. 開啟下載的中繼資料檔案。
    b. 展開 **RoleDescriptor** 節點，其具有 **fed:ApplicationServiceType** 的 **xsi:type**。
    c. 複製 **X509Certificate** 節點的值。
    d. 儲存複製的 **X509Certificate** 值到本機電腦的檔案中。

12. 在您 TOPdesk - Secure 公司網站的 [TOPdesk] 功能表上，按一下 [設定]。
    
    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "設定")

13. 按一下 [登入設定] 。
    
    ![登入設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "登入設定")

14. 展開 登入設定 功能表，然後按一下一般。
    
    ![一般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "一般")

15. 在 **Public** 區段中，按一下 [加入]。
    
    ![新增](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "新增")

16. 在 [SAML 組態輔助程式]  對話頁面上，執行下列步驟：
    
    ![SAML 組態輔助程式](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML 組態輔助程式")
    
    a. 若要上傳您下載的中繼資料檔，請在 [同盟中繼資料] 下按一下 [瀏覽]。

    b. 若要上傳您的憑證檔案，請在 [憑證 (RSA)] 下按一下 [瀏覽]。

    c. 若要上傳您從 TOPdesk 支援小組取得的標誌檔案，請在 [標誌圖示] 下按一下 [瀏覽]。

    d. 在 [使用者名稱] 屬性文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。

    e. 在 [顯示名稱]  文字方塊中，輸入您的組態名稱。

    f. 按一下 [儲存] 。

17. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下完成 來關閉 設定單一登入 對話方塊。
    
    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "設定單一登入")

## <a name="configuring-user-provisioning"></a>設定使用者佈建
若要讓 Azure AD 使用者可以登入 TOPdesk - Secure，則必須將他們佈建到 TOPdesk - Secure。  
TOPdesk - Secure 需以手動的方式佈建。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，請執行下列步驟：
1. 以系統管理員身分登入您的 **TOPdesk - Secure** 公司網站。
2. 在上方功能表中按一下 [TOPdesk] \> [新增] \> [支援檔案] \> [操作員]。
   
    ![操作員](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "操作員")

3. 在 [新增操作員]  對話方塊上，執行下列步驟：
   
    ![新增操作員](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "新增操作員")
   
    a. 按一下 [一般] 索引標籤。
   
    b. 在 [一般] 區段的 [姓氏] 文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的姓氏。
   
    c. 在 [位置] 區段中選取該帳戶的 [網站]。
   
    d. 在 [TOPdesk 登入] 區段的 [登入名稱] 文字方塊中，輸入使用者的登入名稱。
   
    e. 按一下 [儲存] 。

> [!NOTE]
> 您可以使用任何其他的 TOPdesk - Secure 使用者帳戶建立工具或 TOPdesk - Secure 提供的 API 來佈建 AAD 使用者帳戶。
> 
> 

## <a name="assigning-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>若要指派使用者給 TOPdesk - Secure，請執行下列步驟：
1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [TOPdesk - Secure] 應用程式整合頁面上，按一下 [指派使用者]。
   
    ![指派使用者](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "指派使用者")

3. 選取測試使用者，按一下 指派，然後按一下是 以確認指派。
   
    ![是](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

