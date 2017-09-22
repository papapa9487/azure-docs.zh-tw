---
title: "教學課程︰以 Azure Active Directory 設定 ServiceNow 來自動佈建使用者 | Microsoft Docs"
description: "了解如何將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ServiceNow。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: e338804a0c5fcb09dfdfd9bf18ae4d0466cd9a82
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>教學課程︰以 Azure Active Directory 設定 ServiceNow 來自動佈建使用者

本教學課程旨在說明您需要在 ServiceNow 和 Azure AD 中執行的步驟，以將使用者帳戶從 Azure AD 自動佈建和取消佈建至 ServiceNow。

## <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

*   Azure Active Directory 租用戶。
*   您必須擁有 ServiceNow for Work 或 ServiceNow for Education 的有效租用戶。 您可以使用免費試用帳戶來使用任何服務。
*   具有小組系統管理員權限的 ServiceNow 使用者帳戶。

## <a name="assigning-users-to-servicenow"></a>將使用者指派給 ServiceNow

Azure Active Directory 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用佈建服務之前，您必須決定 Azure AD 中的哪些使用者及/或群組代表需要 ServiceNow 應用程式存取權的使用者。 一旦決定後，您可以將這些使用者指派給 ServiceNow 應用程式，方法是遵循以下指示：[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   建議將單一 Azure AD 使用者指派給 ServiceNow，以測試佈建設定。 其他使用者及/或群組可能會稍後再指派。
>*   將使用者指派給 ServiceNow 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="enable-automated-user-provisioning"></a>啟用自動使用者佈建

本節會引導您將 Azure AD 連線至 ServiceNow 的使用者帳戶佈建 API，以及根據 Azure AD 中的使用者和群組指派，設定佈建服務以在 ServiceNow 中建立、更新和停用指派的使用者帳戶。

> [!TIP]
>您也可以選擇啟用 ServiceNow 的 SAML 型單一登入，請遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示。 可以獨立設定自動佈建的單一登入，雖然這兩個功能彼此補充。

### <a name="configure-automatic-user-account-provisioning"></a>設定使用者帳戶自動佈建

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Azure Active Directory > 企業應用程式 > 所有應用程式] 區段。

2. 如果您已將 ServiceNow 設定為單一登入，請使用 [搜尋] 欄位搜尋您的 ServiceNow 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [ServiceNow]。 從搜尋結果中選取 ServiceNow，並將它新增至您的應用程式清單。

3. 選取您的 ServiceNow 執行個體，然後選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。 

    ![佈建](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. 在 [管理員認證] 區段底下，執行下列步驟：
   
    a. 在 [ServiceNow 執行個體名稱] 字方塊中，輸入 ServiceNow 執行個體名稱。

    b. 在 [ServiceNow 管理員使用者名稱] 文字方塊中，輸入管理員的使用者名稱。

    c. 在 [ServiceNow 管理員密碼] 文字方塊中，輸入管理員的密碼。

6. 在 Azure 入口網站中，按一下 [測試連線]，以確保 Azure AD 可以連線至您的 ServiceNow 應用程式。 如果連線失敗，請確定您的 ServiceNow 帳戶具有小組系統管理員權限，並再試一次「管理員認證」步驟。

7. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選核取方塊。

8. 按一下 [儲存]。

9. 在 [對應] 區段下，選取 [同步處理 Azure Active Directory 使用者至 ServiceNow]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 ServiceNow 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 ServiceNow 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可任何變更。

11. 若要對 ServiceNow 啟用 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]

12. 按一下 [儲存]。

這會對 [使用者和群組] 區段中指派給 ServiceNow 的任何使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續的同步處理花費較多時間執行，只要服務正在執行，大約每 20 分鐘便會發生一次。 您可以使用 [同步處理詳細資料] 區段來監視進度，並遵循連結來佈建活動報告，其會描述您 ServiceNow 應用程式上的佈建服務所執行之所有動作。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-servicenow-tutorial.md)



