---
title: "教學課程：設定使用者佈建的 Workplace by Facebook | Microsoft Docs"
description: "了解如何從 Azure AD 自動佈建並取消佈建使用者帳戶至 Workplace by Facebook。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>教學課程：設定使用者佈建的 Workplace by Facebook

本教學課程說明從 Azure Active Directory (Azure AD) 自動佈建並取消佈建使用者帳戶至 Workplace by Facebook 所需的步驟。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Workplace by Facebook 的整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Workplace by Facebook (SSO) 單一登入的訂用帳戶

若要測試本教學課程中的步驟，請遵循下列建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以取得[一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="assign-users-to-workplace-by-facebook"></a>將使用者指派給 Workplace by Facebook

Azure AD 會使用稱為「指派」的概念，來判斷哪些使用者應接收對指定應用程式的存取權。 在自動使用者帳戶佈建的內容中，只有「已指派」至 Azure AD 中的應用程式之使用者和群組會進行同步處理。

在設定並啟用佈建服務之前，請決定 Azure AD 中有哪些使用者和群組代表需要存取 Workplace by Facebook 應用程式的使用者。 然後，您可以將這些使用者指派給 Workplace by Facebook 應用程式，方法是遵循[將使用者或群組指派給企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的指示。

>[!IMPORTANT]
>*   將單一 Azure AD 使用者指派給 Workplace by Facebook，以便測試佈建組態。 稍後指派額外的使用者和群組。
>*   將使用者指派給 Workplace by Facebook 時，必須選取有效的使用者角色。 「預設存取」角色不適用於佈建。

## <a name="enable-automated-user-provisioning"></a>啟用自動使用者佈建

本節將引導您將 Azure AD 連線至 Workplace by Facebook 的使用者帳戶佈建 API。 您也了解如何設定佈建服務，以建立、更新和停用 Workplace by Facebook 中已指派的使用者帳戶。 這是以 Azure AD 中的使用者和群組指派為基礎。

>[!Tip]
>您也可以遵循 [Azure 入口網站](https://portal.azure.com)中提供的指示，選擇啟用 Workplace by Facebook 的 SAML 型 SSO。 您可以獨立設定自動佈建的 SSO，雖然這兩個功能彼此補充。

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>在 Azure AD 中設定要佈建到 Workplace by Facebook 的使用者帳戶

Azure AD 支援將指派之使用者的帳戶詳細資料自動同步處理至 Workplace by Facebook 的功能。 這個自動同步處理可讓 Workplace by Facebook 在使用者嘗試第一次登入之前，取得授權使用者存取所需的資料。 當存取在 Azure AD 中被撤銷時，它也會從 Workplace by Facebook 取消佈建使用者。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [Azure Active Directory] > [企業應用程式] > [所有應用程式]。

2. 如果您已經針對 SSO 設定 Workplace by Facebook，使用 [搜尋] 欄位搜尋您的 Workplace by Facebook 執行個體。 否則，請選取 [新增]，並在應用程式庫中搜尋 [Workplace by Facebook]。 從搜尋結果中選取 [Workplace by Facebook]，並將它新增至您的應用程式清單。

3. 選取您的 Workplace by Facebook 執行個體，然後選取 [佈建] 索引標籤。

4. 將 [佈建模式] 設定為 [自動]。 

    ![Workplace by Facebook 佈建選項的螢幕擷取畫面](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. 在 [管理員認證] 區段下，輸入 Workplace by Facebook 系統管理員的 [祕密權杖] 和 [租用戶 URL]。

6. 在 Azure 入口網站中，選取 [測試連線]以確保 Azure AD 可以連線到您的 Workplace by Facebook 應用程式。 如果連線失敗，請確定您的 Workplace by Facebook 帳戶具有小組系統管理員權限。

7. 在 [通知電子郵件] 欄位中，輸入應收到佈建錯誤通知的個人或群組之電子郵件地址，然後勾選核取方塊。

8. 選取 [ **儲存**]。

9. 在 [對應] 區段中，選取 [同步處理 Azure Active Directory 使用者至 Workplace by Facebook]。

10. 在 [屬性對應] 區段中，檢閱從 Azure AD 同步至 Workplace by Facebook 的使用者屬性。 選取為 [比對] 屬性的屬性會用來比對 Workplace by Facebook 中的使用者帳戶以進行更新作業。 若要認可任何變更，請選取 [儲存]。

11. 若要啟用 Workplace by Facebook 的 Azure AD 佈建服務，請在 [設定] 區段中，將 [佈建狀態] 變更為 [開啟]。

12. 選取 [ **儲存**]。

如需有關如何設定自動佈建的詳細資訊，請參閱 [Facebook 文件](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)。

您現在可以建立測試帳戶了。 請等候 20 分鐘以驗證帳戶已同步至 Workplace by Facebook。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
* [設定單一登入](active-directory-saas-facebook-at-work-tutorial.md)

