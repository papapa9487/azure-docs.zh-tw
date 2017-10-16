---
title: "教學課程：Azure Active Directory 與 DocuSign 整合 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 與 DocuSign 之間的單一登入。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 3b509ffa934949200277ae431761d2accd4a02d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-docusign-for-user-provisioning"></a>教學課程：設定 DocuSign 的使用者佈建

本教學課程旨在說明您需要在 DocuSign 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 DocuSign。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶。
*   已啟用 DocuSign 單一登入的訂用帳戶。
*   具有小組系統管理員權限的 DocuSign 使用者帳戶。

## <a name="assigning-users-to-docusign"></a>將使用者指派給 DocuSign

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 DocuSign 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 DocuSign 應用程式︰

[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>將使用者指派給 DocuSign 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 DocuSign，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 DocuSign 時，您必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="enable-user-provisioning"></a>啟用使用者佈建

本節會引導您將 Azure AD 連線至 DocuSign 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 DocuSign 中建立、更新和停用指派的使用者帳戶。

> [!Tip]
> 您也可以選擇啟用 DocuSign 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="to-configure-user-account-provisioning"></a>若要設定使用者帳戶佈建：

本節的目的是要說明如何對 DocuSign 啟用 Active Directory 使用者帳戶的使用者佈建。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2. 如果您已經設定單一登入的 DocuSign，使用 [搜尋] 欄位搜尋您的 DocuSign 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [DocuSign]。 從搜尋結果中選取 DocuSign，並將它新增至您的應用程式清單。

3. 選取您的 DocuSign 執行個體，然後選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。 

    ![佈建](./media/active-directory-saas-docusign-provisioning-tutorial/provisioning.png)

5. 在 [管理員認證] 區段下，提供下列組態設定：
   
    a. 在 [管理員使用者名稱] 文字方塊中，輸入在 DocuSign.com 已指派 **System Administrator** 設定檔的 DocuSign 帳戶名稱。
   
    b.這是另一個 C# 主控台應用程式。 在 [管理員密碼] 文字方塊中，輸入這個帳戶的密碼。

6. 在 Azure 入口網站中，按一下 [測試連線]以確保 Azure AD 可以連線到您的 DocuSign 應用程式。

7. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

8. 按一下 [儲存]。

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 DocuSign]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 DocuSign 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 DocuSign 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

11. 若要啟用 DocuSign 的 Azure AD 佈建服務，在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]

12. 按一下 [儲存]。

這會啟動在 [使用者和群組] 區段中指派給 DocuSign 的任何使用者和/或群組之首次同步處理。 初始同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，其會描述您 DocuSign 應用程式上的佈建服務所執行之所有動作。

您現在可以建立測試帳戶了。 請等候 20 分鐘以驗證帳戶已同步至 DocuSign。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-docusign-tutorial.md)