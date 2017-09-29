---
title: "在 Azure AD 中自動化 SaaS 應用程式使用者佈建 | Microsoft Docs"
description: "簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和取消佈建
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS 應用程式的自動化使用者佈建是什麼？
Azure Active Directory (Azure AD) 可讓您自動化在雲端 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式中建立、維護和移除使用者身分識別，例如 Dropbox、Salesforce、ServiceNow 等等。

**以下是這項功能可讓您執行的一些範例：**

* 當人員加入您的小組或組織時，在正確的系統中為新的人員自動建立新帳戶。
* 當人員離開小組或組織時，在正確的系統中自動停用帳戶。
* 確保您的應用程式和系統中的身分識別，可根據目錄或您人力資源系統中的變更保持在最新狀態。
* 將非使用者物件 (例如群組) 佈建至支援的應用程式。

**自動化使用者佈建也包含下列功能：**

* 比對來源和目標系統之間現有身分識別的功能。
* 自訂選項，協助 Azure AD 符合您組織目前使用的應用程式和系統之目前設定。
* 佈建錯誤的選用電子郵件警示。
* 報告和活動記錄檔，協助監視與疑難排解。

## <a name="why-use-automated-provisioning"></a>為何要使用自動化佈建？
一些使用這項功能的常見動機包括：

* 為了避免成本、效率不彰，以及與手動佈建程序相關聯的人為錯誤。
* 為了免除裝載及維護自訂開發的佈建解決方案和指令碼的相關成本
* 為了在使用者離開組織時，立即從主要 SaaS 應用程式移除使用者的身分識別以保護您的組織。
* 為了輕鬆將大量使用者匯入特定的 SaaS 應用程式或系統中。
* 為了享受讓佈建方案以您針對 Azure AD 單一登入定義的相同應用程式存取原則執行的便利性。


## <a name="how-does-automatic-provisioning-work"></a>自動化佈建如何運作？
    
**Azure AD 佈建服務**會藉由連接到每個應用程式廠商所提供的使用者管理 API 端點，將使用者佈建至 SaaS 應用程式和其他系統。 這些使用者管理 API 端點可以讓 Azure AD 以程式設計方式建立、更新和移除使用者。 針對所選應用程式，佈建服務也可以建立、更新和移除其他與身分識別相關的物件，例如群組和角色。 

![佈建](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
圖 1：Azure AD 佈建服務**

![輸出佈建](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
圖 2：從 Azure AD 至熱門 SaaS 應用程式的「輸出」使用者佈建工作流程**

![輸入佈建](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
圖 3：從熱門人力資本管理 (HCM) 應用程式至 Azure Active Directory 和 Windows Server Active Directory 的「輸入」使用者佈建工作流程**


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 提供對許多熱門 SaaS 應用程式和人力資源系統的預先整合支援，以及對於實施 [SCIM 2.0 標準](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning)特定部分的應用程式的一般支援。

Azure AD 應用程式資源庫中的所有「精選」應用程式都支援自動化使用者佈建。 [精選應用程式的清單可以在此檢視̂。](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)

為了讓應用程式支援自動化使用者佈建，它必須先提供必要的使用者管理端點，讓外部程式自動化建立、維護及移除使用者。 因此，並非所有 SaaS 應用程式都與此功能相容。 針對支援使用者管理 API 的應用程式，Azure AD 工程小組則是能夠建置與這些應用程式的佈建連接器，這項工作是以目前和潛在客戶的需求來排定優先順序。 

若要連絡 Azure AD 工程小組以要求對於其他應用程式的佈建支援，請透過 [Azure Active Directory 意見反應論壇](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning)提交訊息。 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

對於所選應用程式的 Azure AD 佈建服務設定會從 **[Azure 入口網站](https://potal.azure.com)**開始。 在 [Azure Active Directory] > [企業應用程式] 區段中，選取 [新增]，然後選取 [所有]，並根據您的情況新增下列其中一項：

* [精選應用程式] 區段中的所有應用程式皆支援自動佈建

* 對於自訂開發的 SCIM 整合使用「非資源庫應用程式」選項

![資源庫](./media/active-directory-saas-app-provisioning/gallery.png)

在應用程式管理畫面中，佈建可在 [佈建] 索引標籤中進行設定。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **管理員認證**必須提供給 Azure AD 佈建服務，讓它可連線至應用程式所提供的使用者管理 API。

* 可設定**屬性對應**，指定來源系統中的欄位 (如：Azure AD) 其內容應同步至目標系統中的哪個欄位 (如：ServiceNow)。 如果目標應用程式可支援，則此區段可讓您選擇性地設定群組佈建 (除了使用者帳戶以外)。 「相符的屬性」可讓您選取使用哪些欄位來比對系統之間的帳戶。 「[運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)」可讓您在將值寫入目標系統之前修改及轉換從來源系統取出的值。 如需詳細資訊，請參閱[自訂屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **範圍設定篩選**可告知佈建服務，來源系統中的哪些使用者和群組應該佈建和/或取消佈建到目標系統。 範圍設定篩選中有兩個層面會一起評估，以決定誰會在佈建範圍中：

* **屬性值篩選** - 屬性對應中的「來源物件範圍」功能表可針對特定屬性值進行篩選。 例如，您可以指定只有「部門」屬性為「銷售」的使用者應在佈建範圍中。

* **指派篩選** - 入口網站的 [佈建] > [設定] 區段中的「範圍」功能表可讓您指定是否只有「已指派」的使用者和群組應在佈建範圍中，或者所有 Azure AD 目錄中的使用者都應進行佈建。 有關「指派」使用者和群組的資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)。
    
* **設定**可控制應用程式的佈建服務作業，包括其目前是否正在執行。

* **稽核記錄**可提供 Azure AD 佈建服務所執行的每項作業記錄。 如需詳細資訊，請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

![設定](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>佈建期間會發生什麼事？

1. 當您第一次對應用程式啟用佈建時，會執行下列動作：
   * Azure AD 會嘗試比對 SaaS 應用程式中任何現有使用者與其在目錄中的對應身分識別。 若有使用者符合時，就「不會」  自動啟用單一登入。 為了讓使用者能存取應用程式，必須在 Azure AD 中直接或透過群組成員資格，明確將他們指派至應用程式。
   * 如果您已經指定哪些使用者應指派給應用程式，且 Azure AD 無法找到這些使用者的現有帳戶，則 Azure AD 會在應用程式中為其佈建新帳戶。
2. 完成上述的首次同步處理之後，Azure AD 每隔 20 分鐘會檢查下列變更：
   * 如果新使用者已指派至應用程式 (直接或透過群組成員資格)，則系統會為新使用者在 SaaS 應用程式中佈建新的帳戶。
   * 如果已移除使用者的存取權，則其帳戶在 SaaS 應用程式中會標示為停用 (使用者永遠不會被徹底刪除，以保護您在錯誤設定時不會遺失資料)。
   * 如果使用者最近指派至應用程式，且使用者在 SaaS 應用程式中已有帳戶，則該帳戶會標示為已啟用，且如果某些使用者屬性相較於目錄是過時的，則可能會進行更新。
   * 如果使用者資訊 (例如電話號碼、辦公室位置) 在目錄中已變更，則該資訊也會在 SaaS 應用程式中更新。


## <a name="frequently-asked-questions"></a>常見問題集
**Azure AD 將目錄變更寫入至 SaaS 應用程式的頻率為何？**

首次完整同步處理完成之後，Azure AD 佈建服務通常每隔 20 分鐘會檢查變更一次。 

如果 SaaS 應用程式傳回數個錯誤 (例如無效的系統管理認證)，則 Azure AD 會逐漸降低其頻率，最多每日一次直到修正錯誤為止。 發生這種情況時，Azure AD 佈建作業會進入「隔離」狀態，並在[佈建摘要報告](active-directory-saas-provisioning-reporting.md)中指出這個情況。

**佈建我的使用者需要多久時間？**

首次完整同步處理完成之後，累加變更通常會在 20-40 分鐘內進行。 如果您嘗試佈建大部分的目錄，則時間取決於您擁有的使用者和群組數目。 效能則是取決於佈建服務所用的使用者管理 API 的效能，其用來從來源系統讀取資料以及將資料寫入目標系統。 

如果有許多錯誤 (記錄在[稽核記錄](active-directory-saas-provisioning-reporting.md)中) 且佈建服務已進入「隔離」狀態，效能也會較慢。

**什麼是首次完整同步處理，以及為什麼它需要的時間比後續同步處理更久？**

Azure AD 佈建服務第一次針對指定應用程式執行時，會使用來源目錄中的使用者 (或群組)「快照集」。 此快照集可讓佈建服務減少往返來源和目標 API 的次數，並可讓後續的「差異」同步處理更有效率地運作。 

小型目錄的使用者首次完整同步處理通常可在幾分鐘內完成，但大型目錄可能要數小時。 含有數十萬名使用者的企業目錄可能需要多個小時才能完成首次同步處理。 不過，在首次同步處理之後，後續的「差異」同步處理速度會快很多。

> [!NOTE]
> 對於支援佈建群組和群組成員資格的應用程式，啟用此功能會大幅增加完成完整同步處理所需的時間。 如果不想將群組名稱和群組成員資格佈建到應用程式，您可以在佈建設定的[屬性對應](active-directory-saas-customizing-attribute-mappings.md)中停用此功能。

**我要如何追蹤目前佈建作業的進度？**

請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

**如何得知使用者無法正確佈建？**

所有失敗都會記錄在 Azure AD 稽核記錄中。 如需詳細資訊，請參閱[佈建報告指南](active-directory-saas-provisioning-reporting.md)。

**我要如何提交意見反應給工程小組？**

請透過 [Azure Active Directory 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。


## <a name="related-articles"></a>相關文章
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [自訂使用者佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
* [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [適用於使用者佈建的範圍篩選器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](active-directory-scim-provisioning.md)
* [帳戶佈建通知](active-directory-saas-account-provisioning-notifications.md)
* [如何整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)


