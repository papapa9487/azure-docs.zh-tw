---
title: "有哪些新功能？ Azure Active Directory 版本資訊 | Microsoft Docs"
description: "了解 Azure Active Directory (Azure AD) 的新功能，包括最新的版本資訊、已知問題、錯誤 (bug) 修正、已被取代的功能，以及即將進行的變更。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 93b80744f68546e37934c987f45d7e2747d64f83
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 的新增功能？




> 在您偏好的 RSS 摘要讀取程式中訂閱此[摘要](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us)，以便隨時收到 Azure Active Directory 的最新訊息。



我們正持續改善 Azure Active Directory。 為了讓您取得最新的開發消息，本主題提供您下列相關資訊：

-   最新版本 
-   已知問題 
-   錯誤修正 
-   已被取代的功能 
-   方案變更 

請定期再次瀏覽這個網頁，因為我們會按月進行更新。


## <a name="october-2017"></a>2017 年 10 月

**類型：**方案變更  
**服務類別：**報告  
**產品功能：**身分識別生命週期管理  


在 `https://graph.windows.net/<tenant-name>/reports/` 節點下淘汰 Azure AD 報告 (beta 版) API

Azure 入口網站可讓您使用：

- 新的 Azure Active Directory 管理主控台 
- 活動和安全報告的新 API
 
因為這些新功能，**/reports** 端點下的報告 API 將在 2017 年 12 月 10 日停用。 

---

**類型：**固定   
**服務類別：**My Apps  
**產品功能：**SSO  


Azure Active Directory 針對呈現 HTML 使用者名稱和密碼欄位的應用程式，支援自動登入欄位偵測。  [如何自動擷取應用程式的登入欄位](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)記錄下列步驟。 您可以在藉由在 [Azure 入口網站](http://aad.portal.azure.com) 的 [企業應用程式] 頁面上新增 [非資源庫] 應用程式來尋找此功能。 此外，您可以在此新應用程式上將 [單一登入] 模式設為 [密碼型單一登入]，輸入 web URL，然後再儲存頁面。
 
因為服務問題，這項功能已暫時停用一段時間。 此問題已解決，並可重新使用自動登入欄位偵測。

---

**類型：**新功能  
**服務類別：**MFA  
**產品功能：**身分識別安全性與保護  


在我們存在的世界中，多重要素驗證 (MFA) 是保護您組織的不可或缺環節。 Microsoft 的身分識別小組是不斷進化的多重要素驗證，能獲得更靈活的認證和更順暢的體驗。 現在我很高興宣佈此旅程中的兩個重要步驟： 

- 將多要素挑戰結果直接整合到 Azure AD 登入報表，包括以程式設計方式存取 MFA 結果

- 在 Azure 入口網站中，將 MFA 設定更深入整合到核心 Azure AD 設定體驗

使用此公開預覽，MFA 的管理和報告屬於核心 Azure AD 設定體驗整體的一部分，可讓您管理 Azure AD 體驗中的 MFA 管理入口網站功能。

如需詳細資訊，請參閱[在 Azure 入口網站中多重要素驗證報告的參考資訊](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**類型：**新功能  
**服務類別：**使用規定  
**產品功能：**控管  


**Azure AD 使用規定**提供一種簡單的方法，將資訊呈現給終端使用者。 這確保使用者看到合法或合規性需求的相關免責聲明。

在下列案例中，您可以使用 Azure AD 使用規定：

- 用於貴組織中所有使用者的一般使用規定。 

- 以使用者屬性為基礎的特定使用規定 (例如， 醫生與護士或國內員工與國際員工，由動態群組完成)。 

- 用於存取高商業影響應用程式 (例如 Salesforce) 的特定使用規定。

如需詳細資訊，請參閱 [Azure Active Directory 使用規定](active-directory-tou.md)。


---
**類型：**新功能  
**服務類別：**PIM  
**產品功能：**Privileged Identity Management  


您現在可以利用 Azure Active Directory Privileged Identity Management (PIM) 來管理、控制和監視組織內，對 Azure 資源 (預覽) 的存取行為。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 所有在 Azure 入口網站內利用 Azure 角色型存取控制 (RBAC) 功能的資源，可善用 Azure AD PIM 所提供的絕佳安全性和生命週期管理功能。更多 Azure AD 角色的功能將於稍後推出。

如需詳細資訊，請參閱 [適用於 Azure 資源的 PIM](privileged-identity-management/azure-pim-resource-rbac.md)。


---
**類型：**新功能  
**服務類別：**存取權檢閱  
**產品功能：**控管  


存取權檢閱 (預覽) 可讓組織有效地管理群組成員資格和企業應用程式的存取權： 

- 您可以重新認證來賓使用者存取權，方法為使用應用程式的存取權和群組的成員資格的存取權檢閱。 存取權檢閱提供的深入資訊可讓檢閱者有效地決定來賓是否應該具有連續存取權。

- 您可以利用存取權檢閱，重新認證員工對應用程式的存取權和群組成員資格。

您可以將存取權檢閱控制項收集到與組織相關的程式，以追蹤合規性或與風險相關之應用程式的檢閱。

如需詳細資訊，請參閱 [Azure AD 存取權檢閱](active-directory-azure-ad-controls-access-reviews-overview.md)。


---
**類型：**新功能  
**服務類別：**My Apps  
**產品功能：**SSO  


**從 My Apps 和 Office 365 啟動器隱藏協力廠商應用程式的能力**

您現在可以更好管理您透過新的**隱藏應用程式**屬性，在使用者入口網站顯示的應用程式。 這有助於為後端服務或重複磚顯示應用程式磚，並且結束使用者的應用程式啟動器凌亂的情況。 切換位於第三方應用程式的屬性區段上，並標示為**給使用者看得見？**。 您也可以透過 PowerShell 以程式設計方式隱藏應用程式。 

如需詳細資訊，請參閱[從 Azure Active Directory 的使用者體驗中隱藏第三方應用程式](active-directory-coreapps-hide-third-party-app.md)。 


**可用的內容？**

 在轉換至新管理主控台的過程中，我們製作了 2 個新 API 來擷取可用的 Azure AD 活動記錄。 除了提供更豐富的稽核和登入活動外，新的 API 集還提供更豐富的篩選和排序功能。 現在可透過 Microsoft Graph 的 Identity Protection 風險事件 API 來存取先前透過安全性報告所使用的資料。


## <a name="september-2017"></a>2017 年 9 月

**類型：**已變更的功能  
**服務類別：**Microsoft Identity Manager  
**產品功能：**身分識別生命週期管理  


自 2017 年 9 月 25 日起，可使用 Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) 的 Hotfix 彙總套件 (建置 4.4.1642.0)。 此彙總套件：

- 解決問題並新增增強功能
- 是取代所有 MIM 2016 SP1 更新，最多到組建 4.4.1459.0 Microsoft Identity Manager 2016 的累計更新。 
- 必須要有 **Microsoft Identity Manager 2016 組建 4.4.1302.0。** 

如需詳細資訊，請參閱 [Microsoft Identity Manager 2016 SP1 有適用的 Hotfix 彙總套件 (組建 4.4.1642.0)](https://support.microsoft.com/en-us/help/4021562)。 

---
