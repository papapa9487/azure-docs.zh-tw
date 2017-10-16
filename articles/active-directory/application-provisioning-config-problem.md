---
title: "設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題 | Microsoft Docs"
description: "如何對在為已經列於 Azure AD 應用程式庫中的應用程式設定使用者佈建時所面臨的常見問題進行疑難排解"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 44e344095352f2bc6b27e389fc8be2cdf3e368d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>設定 Azure AD 資源庫應用程式的使用者佈建時遇到的問題

設定應用程式的[自動使用者佈建](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) (如果支援)，需要遵循特定指示來準備應用程式以進行自動佈建。 接著，可以使用 Azure 入口網站來設定佈建服務，將使用者帳戶同步處理至應用程式。

您一開始總是應先尋找為應用程式設定佈建專用的設定教學課程。 然後，遵循這些步驟來設定應用程式和 Azure AD 以建立佈建連接。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)中找到應用程式教學課程清單。

## <a name="how-to-see-if-provisioning-is-working"></a>如何查看佈建是否正常運作 

一旦設定服務之後，就能從下列兩個位置繪製出大多數對服務作業的深入見解：

-   **稽核記錄** - 佈建稽核記錄會記錄佈建服務所執行的所有作業，包括針對位於佈建範圍內的已指派使用者查詢 Azure AD。 查詢目標應用程式以確定那些使用者是否存在，比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。 您可以在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式]&gt;[\[應用程式名稱\]]&gt;[稽核記錄] 索引標籤中存取佈建稽核記錄。基於 [帳戶佈建] 類別來篩選記錄，只顯示該應用程式的佈建事件。

-   **佈建狀態 -** 您可以在 [Azure Active Directory]&gt;[企業應用程式]&gt;[\[應用程式名稱\]]&gt;[佈建] 區段中，於螢幕底部的服務設定下方，看見指定應用程式的最後一個佈建執行摘要。 本節將摘要說明目前會在這兩個系統之間同步處理多少個使用者 (和/或群組)，以及是否發生任何錯誤。 錯誤詳細資料位於稽核記錄中。 請注意，在 Azure AD 和應用程式之間完成一次完整的初始同步處理之前，不會填入佈建資料。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域

如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

* [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
* [無法儲存設定，因為應用程式認證無法運作](#can’t-save-configuration-due-to-app-credentials-not-working)
* [稽核記錄表示「已略過」且未佈建使用者，即使已指派它們](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動

如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式]&gt;[\[應用程式名稱\]]&gt;[佈建] 區段中，將 [佈建狀態] 設為 [啟用]。 不過，在後續重新載入之後，該頁面上並未顯示任何其他狀態詳細資料。 很可能是服務正在執行，但尚未完成初始同步處理。 請檢查上述的**稽核記錄**，以判斷服務正在執行哪些作業，以及是否發生任何錯誤。

>[!NOTE]
>根據 Azure AD 目錄大小和佈建範圍中的使用者數目而定，初始同步處理可能會花費 20 分鐘到數小時。 初始同步處理之後的後續同步處理會更快，因為佈建服務會儲存浮水印，代表兩個系統在初始同步處理之後的狀態，所以會改善後續同步處理的效能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>無法儲存設定，因為應用程式認證無法運作

為了讓佈建能夠運作，Azure AD 需要有效的認證，讓它能夠連接到該應用程式所提供的使用者管理 API。 如果這些認證無效，或您不了解它們，請檢閱設定此應用程式的教學課程，如先前所述。

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>稽核記錄表示「已略過」且未佈建使用者，即使已指派它們

當使用者在稽核記錄中顯示為「已略過」時，請務必讀取記錄訊息中延伸的詳細資料來判斷原因。 下面是常見原因和解決方式：

-   **已設定範圍設定篩選****，這會根據屬性值篩選出使用者**。 如需範圍設定篩選的詳細資訊，請參閱 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>。

-   **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需指派的詳細資訊，請參閱 <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>。

-   **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 這包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程序的詳細資訊，請參閱 <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>。

   * **群組的屬性對應：**除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以對 [佈建] 索引標籤中顯示的群組物件啟用或停用 [對應]，以啟用或停用此功能。如果已啟用佈建群組，請務必檢閱屬性對應，以確保「比對識別碼」使用適當的欄位。 這可以是顯示名稱或電子郵件別名，因為如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組和其成員。

#<a name="next-steps"></a>後續步驟
[自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](active-directory-saas-app-provisioning.md)
