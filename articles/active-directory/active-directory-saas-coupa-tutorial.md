---
title: "教學課程：Azure Active Directory 與 Coupa 整合 | Microsoft Docs"
description: "了解如何使用 Coupa 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>教學課程：Azure Active Directory 與 Coupa 整合
本教學課程的目的是要示範 Azure 與 Coupa 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

* 有效的 Azure 訂閱
* 已啟用 Coupa 單一登入 (SSO) 的訂用帳戶

完成本教學課程之後，您指派給 Coupa 的 Azure AD 使用者就能夠使用使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)單一登入應用程式。

本教學課程中說明的案例由下列建置組塊組成：

* 啟用 Coupa 的應用程式整合
* 設定單一登入
* 設定使用者佈建
* 指派使用者

![案例](./media/active-directory-saas-coupa-tutorial/IC791897.png "案例")

## <a name="enable-the-application-integration-for-coupa"></a>啟用 Coupa 的應用程式整合
本節的目的是要說明如何啟用 Coupa 的應用程式整合。

**若要啟用 Coupa 的應用程式整合，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，按一下左方瀏覽窗格的 [Active Directory] 。
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]  。
   
   ![應用程式](./media/active-directory-saas-coupa-tutorial/IC700994.png "應用程式")
4. 按一下頁面底部的 [新增]  。
   
   ![新增應用程式](./media/active-directory-saas-coupa-tutorial/IC749321.png "新增應用程式")
5. 在 [欲執行動作] 對話方塊上，按一下 [從資源庫中新增應用程式]。
   
   ![從資源庫新增應用程式](./media/active-directory-saas-coupa-tutorial/IC749322.png "從資源庫新增應用程式")
6. 在**搜尋方塊**中，輸入 **Coupa**。
   
   ![應用程式資源庫](./media/active-directory-saas-coupa-tutorial/IC791898.png "應用程式資源庫")
7. 在結果窗格中，選取 Coupa，然後按一下完成 來加入應用程式。
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Coupa。  

設定 Coupa 的單一登入需要您從憑證擷取憑證指紋值。 如果您不熟悉這個程序，請參閱 [如何抓取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

**若要設定單一登入，請執行下列步驟：**

1. 以系統管理員身分登入您的 Coupa 公司網站。
2. 移至 [設定] \> [安全性控制]。
   
   ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制項")
3. 若要將 Coupa 中繼資料檔案下載至您的電腦，按一下 [下載和匯入預存程序的中繼資料] 。
   
   ![Coupa 預存程序中繼資料](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa 預存程序中繼資料")
4. 在不同的瀏覽器視窗中，登入 Azure 傳統入口網站。
5. 在 [Coupa] 應用程式整合頁面上，按一下 [設定單一登入] 來開啟 [設定單一登入] 對話方塊。
   
   ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791902.png "設定單一登入")
6. 在 要如何讓使用者登入 Coupa 頁面上，選取 Microsoft Azure AD 單一登入，然後按一下下一步。
   
   ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791903.png "設定單一登入")
7. 在 [設定應用程式 URL]  頁面上，執行下列步驟：
   
   ![設定應用程式 URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "設定應用程式 URL")   
   1. 在 [登入 URL] 文字方塊中，輸入您的使用者用來登入 Coupa 應用程式的 URL (例如： “*http://company.Coupa.com*”)。
   2. 開啟您已下載的 Coupa 中繼資料檔案，然後複製 **AssertionConsumerService index/URL**。
   3. 在 [Coupa 回覆 URL] 文字方塊中，貼上 **AssertionConsumerService index/URL** 值。
   4. 按一下 [下一步] 。
8. 在 [設定在 Coupa 單一登入] 頁面上，請按一下 [下載中繼資料] 來下載您的中繼資料，然後將檔案儲存在您的本機電腦上。
   
   ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791905.png "設定單一登入")
9. 在 Coupa 公司網站上，請移至 [設定] \> [安全性控制]。
   
   ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制項")
10. 在 [使用 Coupa 認證登入]  區段中，執行下列步驟：  

   ![使用 Coupa 認證登入](./media/active-directory-saas-coupa-tutorial/IC791906.png "使用 Coupa 認證登入") 
   1. 選取 [使用 SAML 登入] 。
   2. 按一下 [瀏覽]  來上傳您下載的 Azure Active Directory 中繼資料檔。
   3. 按一下 [儲存] 。
11. 在 Azure 傳統入口網站上，選取單一登入設定確認，然後按一下完成 來關閉 設定單一登入 對話方塊。
    
   ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791907.png "設定單一登入")
    
## <a name="configure-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者可以登入 Coupa，則必須將他們佈建到 Coupa。  

* Coupa 需以手動的方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Coupa** 公司網站。
2. 在頂端的功能表中，按一下 設定，然後按一下使用者。
   
   ![使用者](./media/active-directory-saas-coupa-tutorial/IC791908.png "使用者")
3. 按一下 [建立] 。
   
   ![建立使用者](./media/active-directory-saas-coupa-tutorial/IC791909.png "建立使用者")
4. 在 [使用者建立]  區段中，執行下列步驟：
   
   ![使用者詳細資料](./media/active-directory-saas-coupa-tutorial/IC791910.png "使用者詳細資料")
   
   1. 在相關的文字方塊中，輸入您要佈建之有效 Azure Active Directory 帳戶的 [登入]、[名字]、[姓氏]、[單一登入識別碼]、[電子郵件] 屬性。
   2. 按一下 [建立] 。   
   >[!NOTE]
   >Azure Active Directory 帳戶的持有者會收到一封包含連結的電子郵件，以在啟用帳戶前進行確認。 
   > 

>[!NOTE]
>您可以使用任何其他的 Coupa 使用者帳戶建立工具或 Coupa 提供的 API 來佈建 AAD 使用者帳戶。 
> 

## <a name="assign-users"></a>指派使用者
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

**若要指派使用者給 Coupa，請執行下列步驟：**

1. 在 Azure 傳統入口網站中建立測試帳戶。
2. 在 [Coupa] 應用程式整合頁面上，按一下 [指派使用者]。
   
   ![指派使用者](./media/active-directory-saas-coupa-tutorial/IC791911.png "指派使用者")
3. 選取測試使用者，按一下 指派，然後按一下是 以確認指派。
   
   ![是](./media/active-directory-saas-coupa-tutorial/IC767830.png "是")

如果要測試您的單一登入設定，請開啟存取面板。 如需 [存取面板] 的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

