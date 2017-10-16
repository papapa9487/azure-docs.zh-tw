---
title: "教學課程︰以 Azure Active Directory 設定自動使用者佈建的 ZenDesk | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 來自動佈建並取消佈建使用者帳戶至 ZenDesk。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: 1a1414eefd20e6d7c025da08cfd5ae7c45daad33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-zendesk-for-automatic-user-provisioning"></a>教學課程︰設定自動使用者佈建的 ZenDesk


本教學課程旨在說明您需要在 ZenDesk 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ZenDesk。 

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶
*   已啟用 [Enterprise 方案](https://www.zendesk.com/product/pricing/)或更好方案的 ZenDesk 租用戶 
*   ZenDesk 中具有系統管理員權限的使用者帳戶 

> [!NOTE]
> Azure AD 佈建整合仰賴 [ZenDesk REST API](https://developer.zendesk.com/rest_api/docs/core/introduction#the-api)，其可供 ZenDesk 小組在 Essential 方案或更好方案上使用。

## <a name="assigning-users-to-zendesk"></a>將使用者指派給 ZenDesk

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。 

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 ZenDesk 應用程式存取權的使用者。 一旦決定後，您可以依照此處的指示，將這些使用者指派給 ZenDesk 應用程式︰

[將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>將使用者指派給 ZenDesk 的重要秘訣

*   建議將單一 Azure AD 使用者指派給 ZenDesk，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。

*   將使用者指派給 ZenDesk 時，您必須在 [指派] 對話方塊中選取 [使用者] 角色，或另一個有效的應用程式特有角色 (如果有的話)。 [預設存取] 角色不適用於佈建，系統會略過這些使用者。

> [!NOTE]
> 佈建服務是一項新增功能，可讀取 Zendesk 中定義的任何自訂角色，並將它們匯入 Azure AD 中，以便在 [選取角色] 對話方塊中進行選取。 啟用佈建服務並完成一個同步處理週期之後，這些角色就會顯示在 Azure 入口網站。

## <a name="configuring-user-provisioning-to-zendesk"></a>設定使用者佈建至 ZenDesk 

本節會引導您將 Azure AD 連線至 ZenDesk 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 ZenDesk 中建立、更新和停用指派的使用者帳戶。

> [!TIP] 
> 您也可以選擇啟用 ZenDesk 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。


### <a name="configure-automatic-user-account-provisioning-to-zendesk-in-azure-ad"></a>在 Azure AD 中設定自動使用者帳戶佈建至 ZenDesk


1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2. 如果您已經設定單一登入的 ZenDesk，使用 [搜尋] 欄位搜尋您的 ZenDesk 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [ZenDesk]。 從搜尋結果中選取 ZenDesk，並將它新增至您的應用程式清單。

3. 選取您的 ZenDesk 執行個體，然後選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。

    ![ZenDesk 佈建](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. 在 [系統管理員認證] 區段之下，輸入您的 ZenDesk 帳戶所產生的 [系統管理員使用者名稱、權杖金鑰和網域] \(您可以在自己的帳戶之下找到權杖：[系統管理員] > [API] > [設定])。 

    ![ZenDesk 佈建](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

6. 在 Azure 入口網站中，按一下 [測試連線]以確保 Azure AD 可以連線到您的 ZenDesk 應用程式。 如果連線失敗，請確定您的 ZenDesk 帳戶具有系統管理員權限並再試一次步驟 5。

7. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選 [發生失敗時傳送電子郵件通知] 核取方塊。

8. 按一下 [儲存] 。 

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 ZenDesk]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 ZenDesk 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 ZenDesk 中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

11. 若要啟用 ZenDesk 的 Azure AD 佈建服務，在 [設定]區段中，將 [佈建狀態] 變更為 [開啟]

12. 按一下 [儲存] 。 

此選項會啟動在 [使用者和群組] 區段中指派給 ZenDesk 的任何使用者和/或群組之首次同步處理。 初始同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，報告中會描述佈建服務執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)。


## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-enterprise-apps-manage-provisioning.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](active-directory-saas-provisioning-reporting.md)
