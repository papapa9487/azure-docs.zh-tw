---
title: "教學課程︰在 Azure 中設定 Google Apps 來自動佈建使用者 | Microsoft Docs"
description: "了解如何將使用者帳戶從 Azure AD 自動佈建和取消佈建至 Google Apps。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: jeedes
ms.openlocfilehash: 026eca7fc8f17602ba44ba38db138d645c9d7713
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>教學課程︰設定 Google Apps 來自動佈建使用者

本教學課程旨在說明如何將使用者帳戶從 Azure Active Directory (Azure AD) 自動佈建和取消佈建至 Google Apps。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶。
*   Google Apps for Work 或 Google Apps for Education 的有效租用戶。 您可以使用免費試用帳戶來使用任何服務。
*   Google Apps 中具有小組管理員權限的使用者帳戶。

## <a name="assign-users-to-google-apps"></a>指派使用者至 Google Apps

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

您設定並啟用佈建服務之前，需要決定 Azure AD 中的哪些使用者或群組需要 Google Apps 應用程式的存取權。 完成決定後，可以遵循[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示，將這些使用者指派給 Google Apps 應用程式。

> [!IMPORTANT]
> 建議將單一 Azure AD 使用者指派給 Google Apps，以測試佈建設定。 可以稍後再指派額外的使用者和群組。

> 將使用者指派給 Google Apps 時，請在指派對話方塊中選取 [使用者] 或 [群組] 角色。 「**預設存取**」角色不適用於佈建。

## <a name="enable-automated-user-provisioning"></a>啟用自動使用者佈建

本節將引導您進行將 Azure AD 連線至 Google Apps 的使用者帳戶佈建 API 的流程。 也會協助您設定佈建服務，根據 Azure AD 中的使用者和群組指派來建立、更新和停用 Google Apps 中指派的使用者帳戶。

>[!TIP]
>您也可以遵循 [Azure 入口網站](https://portal.azure.com)中的指示，選擇啟用 Google Apps 的 SAML 型單一登入。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

> [!NOTE]
> 自動化使用者佈建至 Google Apps 還有另一個可行的選項，即是使用 [Google Apps Directory Sync](https://support.google.com/a/answer/106368?hl=en)。 GADS 會將您的內部部署 Active Directory 身分識別佈建至 Google Apps。 但是，本教學課程中的解決方案則會將您的 Azure Active Directory (雲端) 使用者和啟用郵件功能的群組佈建至 Google Apps。 

1. 使用您的系統管理員帳戶登入 [Google Apps 管理主控台](http://admin.google.com/)，然後選取 [安全性]。 如果您沒有看到連結，它可能隱藏在畫面底部的 [其他控制項] 功能表之下。
   
    ![選取安全性。][10]

2. 在 [安全性] 頁面上，選取 [API 參考]。
   
    ![選取 API 參考。][15]

3. 選取 [啟用 API 存取] 。
   
    ![選取 API 參考。][16]

    > [!IMPORTANT]
    > 對於您想要佈建至 Google Apps 的每位使用者，其使用者名稱在 Azure Active Directory 中*必須*繫結至自訂網域。 例如，Google Apps 不會接受類似 bob@contoso.onmicrosoft.com 的使用者名稱。 另一方面，則接受 bob@contoso.com。 您可以在 Azure AD 中編輯現有使用者的內容，來變更其網域。 下列步驟包含如何為 Azure Active Directory 與 Google Apps 兩者設定自訂網域的指示。
      
4. 如果您尚未將自訂網域名稱新增至 Azure Active Directory，請按照下列步驟執行：
  
    a. 在 [Azure 入口網站](https://portal.azure.com) 的左方瀏覽窗格中，選取 [Active Directory]。 在目錄清單中，選取您的目錄。 

    b. 在左方瀏覽窗格上，選取 [網域名稱]，然後選取 [新增]。
     
     ![網域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![新增網域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. 在 [網域名稱]  欄位中輸入您的網域名稱。 這個網域名稱應該與您要用於 Google Apps 的網域名稱相同。 然後選取 [新增網域] 按鈕。
     
     ![網域名稱](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. 選取 [下一步] 移至驗證頁面。 若要確認您擁有此網域，請根據此頁面提供的值，編輯網域的 DNS 記錄。 您可以選擇使用 [MX 記錄] 或 [TXT 記錄] 進行確認，視您選取的 [記錄類型] 選項而定。 
    
    如需有關如何向 Azure AD 驗證網域名稱的更完整指示，請參閱「[將您自己的網域名稱新增至 Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)」。
     
     ![網域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. 對於所有您想要新增至目錄的網域，重複上述步驟。

    > [!NOTE]
    針對使用者佈建，Google Apps 自訂網域必須符合來源 Azure AD 的網域名稱。 如果不相符，您可以藉由實作屬性對應的自訂來解決問題。


5. 您已經向 Azure AD 驗證所有網域，但是您還必須向 Google Apps 再驗證一次。 對於尚未向 Google Apps 註冊的每個網域，請採取下列步驟：
   
    a. 在 [Google Apps 管理主控台](http://admin.google.com/)中，選取 [網域]。
     
     ![選取網域][20]

    b. 選取 [新增網域或網域別名]。
     
     ![新增網域][21]

    c. 選取 [新增另一個網域]，然後輸入您想要新增的網域名稱。
     
     ![輸入您的網域名稱][22]

    d. 選取 [繼續並驗證網域擁有權]。 然後依照步驟以驗證您擁有網域名稱。 如需有關如何向 Google Apps 驗證您的網域的完整指示，請參閱 [向 Google Apps 驗證您的站台擁有權](https://support.google.com/webmasters/answer/35179)。

    e. 對於您想要新增至 Google Apps 的任何其他網域，重複上述步驟。
     
     > [!WARNING]
     > 如果您變更 Google Apps 租用戶的主要網域，而且如果您已經使用 Azure AD 來設定單一登入，則必須重複[步驟 2：啟用單一登入](#step-two-enable-single-sign-on)下的步驟 #3。
       
6. 在 [Google Apps 管理主控台](http://admin.google.com/)中，選取 [管理角色]。
   
     ![選取 Google Apps][26]

7. 決定您想要用來管理使用者佈建的管理帳戶。 對於該帳戶的 [管理角色]，編輯該角色的 [權限]。 務必啟用所有 [管理 API 權限]，以便讓此帳戶可以用來佈建。
   
     ![選取 Google Apps][27]
   
    > [!NOTE]
    > 如果您要設定生產環境，最佳做法是特別針對此步驟在 Google Apps 中建立管理帳戶。 這些帳戶必須有相關聯的管理角色，該角色還要具備必要的 API 權限。
     
8. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory] > [企業應用程式] > [所有應用程式] 區段。

9. 如果您已將 Google Apps 設定為單一登入，請使用 [搜尋] 欄位搜尋您的 Google Apps 執行個體。 否則，請選取 [新增]，然後在應用程式庫中搜尋 [Google Apps]。 從搜尋結果中選取 **Google Apps**，然後將它新增至您的應用程式清單。

10. 選取您的 Google Apps 執行個體，然後選取 [佈建] 索引標籤。

11. 將 [佈建模式] 設定為 [自動]。 

     ![佈建](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. 在 [系統管理員認證] 區段下，選取 [授權]。 這會在新的瀏覽器視窗中開啟 Google Apps 授權對話方塊。

13. 確認您想要授與 Azure Active Directory 權限來變更您的 Google Apps 租用戶。 選取 [接受]。
    
     ![確認權限。][28]

14. 在 Azure 入口網站中，選取 [測試連線]，以確保 Azure AD 可以連線至您的 Google Apps 應用程式。 如果連線失敗，請確定您的 Google Apps 帳戶具有小組系統管理員權限。 然後再試一次**授權**步驟。

15. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址。 然後選取核取方塊。

16. 選取 [儲存]。

17. 在 [對應] 區段下，選取 [同步處理 Azure Active Directory 使用者至 Google Apps]。

18. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Google Apps 的使用者屬性。 做為 [比對] 屬性的屬性會用來比對 Google Apps 中的使用者帳戶，以進行更新作業。 選取 [儲存] 認可任何變更。

19. 若要對 Google Apps 啟用 Azure AD 佈建服務，請在 [設定] 中，將 [佈建狀態] 變更為 [開啟]。

20. 選取 [ **儲存**]。

此流程會對 [使用者和群組] 區段中指派給 Google Apps 的任何使用者和群組，啟動首次同步處理。 初始同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘便會發生一次。 

您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告。 這些報告會描述在 Google Apps 應用程式上佈建服務所執行的所有動作。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
