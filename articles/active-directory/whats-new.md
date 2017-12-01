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
ms.date: 11/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9de535b2fb70181c68fb698e847dd8361bf54385
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
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

## <a name="november-2017"></a>2017 年 11 月
 


**類型：**方案變更  
**服務類別：**ACS  
**產品功能：**存取控制服務 

**淘汰 ACS**

Microsoft Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 將於 2018 年底淘汰。  未來幾週，我們將會提供詳細的排程和高階移轉指引等詳細資訊。 在此同時，您可以在這個頁面上發表意見與關於 ACS 的任何問題，我們的小組成員將會協助解答。

---



**類型：**方案變更  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護


**限制瀏覽器對 Intune Managed Browser 的存取** 


透過此行為，您將能透過使用 Intune Managed Browser 作為已核准的應用程式，來限制瀏覽器對 Office 365 和其他已連線至 Azure AD 之雲端應用程式的存取。 

這項變更可讓您針對以應用程式為基礎的條件式存取設定下列條件：

**用戶端應用程式：**瀏覽器

**變更的影響為何？**

目前，使用此條件將會封鎖存取。 當此行為的預覽可用時，所有存取都會要求使用 Managed Browser 應用程式。 

請在即將推出的部落格和版本資訊中，查看有關此功能及其他內容的資訊。 

如需詳細資訊，請參閱 [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)。

 
---


 
**類型：**方案變更  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護


**針對 Azure AD 應用程式型條件式存取之新的已核准用戶端應用程式**


已計畫將下列應用程式新增至[已核准的用戶端應用程式](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)清單：

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/) \(英文\)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


如需詳細資訊，請參閱：

- [已核准的用戶端應用程式需求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory 應用程式型條件式存取](active-directory-conditional-access-mam.md)


---


**類型：**新功能    
**服務類別：**使用規定  
**產品功能：**控管/合規性



**針對多種語言的使用規定支援**


系統管理員現在可以建立包含多個 PDF 文件的新使用規定 (TOU)。 您能以相對應的語言標記這些 PDF 文件。 針對適用範圍內的使用者，系統會根據其喜好設定顯示相符語言的 PDF。 如果沒有相符項目，則會顯示預設語言。


---
 


**類型：**新功能  
**服務類別：**SSPR  
**產品功能：**使用者驗證


**即時密碼回寫用戶端狀態**
 

您現在可以檢閱內部部署密碼回寫用戶端的狀態。 此選項可於 [[密碼重設](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)] 頁面的 [內部部署整合] 區段中取得。 

如果您在連線至內部部署回寫用戶端時遇到問題，將會看見包含下列資訊的錯誤訊息：

- 無法連線至內部部署回寫用戶端之原因的資訊 
- 可協助您解決該問題之文件的連結。 


如需詳細資訊，請參閱[內部部署整合](active-directory-passwords-how-it-works.md#on-premises-integration)。

 
---
 
**類型：**新功能  
**服務類別：**Azure AD  
**產品功能：**身分識別安全性與保護



**Azure AD 應用程式型條件式存取** 


您現在可以使用 [Azure AD 應用程式型條件式存取](active-directory-conditional-access-mam.md)，限制支援 Intune 應用程式防護的[已核准用戶端應用程式](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)對 Office 365 和其他已連線至 Azure AD 之雲端應用程式的存取。 系統會使用 Intune 應用程式防護原則，來設定及保護這些用戶端應用程式上的公司資料。

透過結合[應用程式型](active-directory-conditional-access-mam.md)和[裝置型](active-directory-conditional-access-policy-connected-applications.md)的條件式存取原則，您將能彈性地保護個人及公司裝置上的資料。

下列條件及控制項現已能搭配應用程式型條件式存取使用：

**支援的平台條件**

- iOS
- Android

**用戶端應用程式條件**

- 行動裝置應用程式和桌面用戶端

**存取控制**

- 需要已核准的用戶端應用程式


如需詳細資訊，請參閱 [Azure Active Directory 應用程式型條件式存取](active-directory-conditional-access-mam.md)。

 
---



**類型：**新功能  
**服務類別：**裝置註冊和管理  
**產品功能：**身分識別安全性與保護

 

**在 Azure 入口網站中管理 Azure AD 裝置**


您現在可以在單一位置找到所有已連線至 Azure AD 的裝置，以及與裝置相關的活動。 現已推出在 Azure 入口網站中管理所有裝置身分識別和設定的新系統管理體驗。 在這個版本中，您將可以︰

- 檢視所有可供在 Azure AD 中進行條件式存取的裝置

- 檢視屬性，包括您的混合式已加入 Azure AD 的裝置

- 尋找已加入 Azure AD 之裝置的 BitLocker 金鑰、透過 Intune 管理裝置等功能。

- 管理 Azure AD 裝置的相關設定


如需詳細資訊，請參閱[使用 Azure 入口網站管理裝置](device-management-azure-portal.md)。



 
---


**類型：**新功能    
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護 



**支援使用 macOS 作為 Azure AD 條件式存取的裝置平台** 
 

您現在可以在 Azure AD 條件式存取原則中，將 macOS 包含為裝置平台條件 (或是將它排除)。 除了將 macOS 新增為支援的裝置平台之外，您也可以：

- **使用 Intune 註冊並管理 macOS 裝置**：和其他平台 (例如 iOS 和 Android) 類似，會有適用於 macOS 的公司入口網站應用程式，以用來進行統一註冊。 適用於 macOS 的新公司入口網站應用程式，可讓您透過 Intune 註冊裝置，並將它登錄至 Azure AD。
 
- **確保 macOS 裝置會遵守組織在 Intune 中定義的合規性原則**：您現在可以在 Azure 入口網站上的 Intune 中，設定適用於 macOS 裝置的合規性原則。 
  
- **限制只有符合規範的 macOS 裝置可以存取 Azure AD 中的應用程式**：條件式存取原則撰寫具有 macOS 作為個別的裝置平台選項。 此選項可讓您針對在 Azure 中設定的目標應用程式，撰寫 macOS 特定的條件式存取原則。

如需詳細資訊，請參閱：

- [使用 Intune 為 macOS 裝置建立裝置合規性原則](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)


 
---


**類型：**新功能    
**服務類別：**MFA  
**產品功能：**使用者驗證


**Azure MFA 的 NPS 擴充功能** 


Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 利用 NPS 擴充功能，您可以在現有驗證流程中新增通話、簡訊或電話應用程式驗證，而不必安裝、設定及維護新的伺服器。 

這個擴充功能是針對想要保護 VPN 連線但無須部署 Azure MFA Server 的組織所建立。 NPS 擴充功能是用於在 RADIUS 和以雲端為基礎的 Azure MFA 之間作為配接器，為聯盟或同步處理的使用者提供第二個驗證因素。


如需詳細資訊，請參閱[將現有的 NPS 基礎結構與 Azure Multi-Factor Authentication 整合](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---


**類型：**新功能    
**服務類別：**使用者管理  
**產品功能：**目錄 


**還原或永久移除已刪除的使用者**


在 Azure AD 系統管理中心中，您現在可以：

- 還原已刪除的使用者 
- 永久刪除使用者 


**試用此功能：**

1. 在 Azure AD 系統管理中心中，選取 [管理] 區段中的 [[所有使用者](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users)]。 

2. 從 [顯示] 清單，選取 [最近刪除的使用者]。 

4. 選取一或多個最近刪除的使用者，然後還原或是永久刪除他們。

 
---



 
**類型：**已變更的功能  
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護

**針對 Azure AD 應用程式型條件式存取之新的已核准用戶端應用程式**


已將下列應用程式新增至[已核准的用戶端應用程式](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)清單：

- Microsoft Planner

- Microsoft Azure 資訊保護 


如需詳細資訊，請參閱：

- [已核准的用戶端應用程式需求](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory 應用程式型條件式存取](active-directory-conditional-access-mam.md)


---



**類型：**已變更的功能    
**服務類別：**條件式存取  
**產品功能：**身分識別安全性與保護


**在條件式存取原則中的控制之間加入 'OR' 的能力** 
 
已推出針對條件式存取控制項加入 'OR' 的能力 (需要其中一個選取的控制項)。 此功能可讓您建立在存取控制項之間具有 **OR** 的原則。 例如，您可以使用此功能來建立要求使用者使用多重要素驗證登入**或**使用符合規範裝置的原則。

如需詳細資訊，請參閱 [Azure Active Directory 條件式存取中的控制項](active-directory-conditional-access-controls.md)。

 
---



**類型：**已變更的功能    
**服務類別：**身分識別保護  
**產品功能：**身分識別安全性與保護

**即時風險事件的彙總**

為了改善您的系統管理體驗，Azure AD Identity Protection 中於同一天內源自於相同 IP 位址的即時風險事件，現已會針對每個風險事件類型進行彙總。 此項變更會限制系統所顯示的風險事件量，但並不會對使用者安全性產生任何變更。

基礎即時偵測仍會在使用者每次登入時運作。 如果您有設定登入風險安全性原則以進行 MFA 或封鎖存取，它仍然會在每次發生具風險的登入時觸發。

 
---
 




## <a name="october-2017"></a>2017 年 10 月

**類型：**方案變更  
**服務類別：**報告  
**產品功能：**身分識別生命週期管理  


**即將過時的 Azure AD 報告**

Azure 入口網站可讓您使用：

- 新的 Azure Active Directory 管理主控台 
- 活動和安全報告的新 API
 
因為這些新功能，**/reports** 端點下的報告 API 將在 2017 年 12 月 10 日停用。 

---

**類型：**固定   
**服務類別：**我的應用程式  
**產品功能：**SSO  


**自動登入欄位偵測**


Azure Active Directory 針對呈現 HTML 使用者名稱和密碼欄位的應用程式，支援自動登入欄位偵測。  [如何自動擷取應用程式的登入欄位](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)記錄下列步驟。 您可以在藉由在 [Azure 入口網站](http://aad.portal.azure.com) 的 [企業應用程式] 頁面上新增 [非資源庫] 應用程式來尋找此功能。 此外，您可以在此新應用程式上將 [單一登入] 模式設為 [密碼型單一登入]，輸入 web URL，然後再儲存頁面。
 
因為服務問題，這項功能已暫時停用一段時間。 此問題已解決，並可重新使用自動登入欄位偵測。

---

**類型：**新功能  
**服務類別：**MFA  
**產品功能：**身分識別安全性與保護  


**新的 MFA 功能**

多重要素驗證 (MFA) 是保護您組織不可或缺的環節。 為了使認證更具彈性，並讓使用體驗更加順暢，我們已新增下列功能： 

- 將多要素挑戰結果直接整合到 Azure AD 登入報表，包括以程式設計方式存取 MFA 結果

- 在 Azure 入口網站中，將 MFA 設定更深入地整合至 Azure AD 設定體驗

在此公開預覽中，MFA 管理和報告功能是核心 Azure AD 設定體驗相當重要的一部分。 對這兩項功能的彙總，可讓您在 Azure AD 體驗內管理 MFA 管理入口網站功能。

如需詳細資訊，請參閱[在 Azure 入口網站中多重要素驗證報告的參考資訊](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**類型：**新功能  
**服務類別：**使用規定  
**產品功能：**控管  


**使用規定的推出**

「Azure AD 使用規定」能提供將資訊呈現給使用者的簡易方式。 這確保使用者看到合法或合規性需求的相關免責聲明。

在下列案例中，您可以使用 Azure AD 使用規定：

- 用於貴組織中所有使用者的一般使用規定。 

- 以使用者屬性為基礎的特定使用規定 (例如， 醫生與護士或國內員工與國際員工，由動態群組完成)。 

- 用於存取高商業影響應用程式 (例如 Salesforce) 的特定使用規定。

如需詳細資訊，請參閱 [Azure Active Directory 使用規定](active-directory-tou.md)。


---
**類型：**新功能  
**服務類別：**PIM  
**產品功能：**Privileged Identity Management  

**針對 Privileged Identity Management 的增強功能**

您現在可以利用 Azure Active Directory Privileged Identity Management (PIM) 來管理、控制和監視下列項目針對組織內 Azure 資源 (預覽) 的存取行為：

- 訂用帳戶
- 資源群組
- 虛擬機器。 

所有在 Azure 入口網站內利用 Azure 角色型存取控制 (RBAC) 功能的資源，都可以運用 Azure AD PIM 所提供的安全性和生命週期管理功能。

如需詳細資訊，請參閱 [適用於 Azure 資源的 PIM](privileged-identity-management/azure-pim-resource-rbac.md)。


---
**類型：**新功能  
**服務類別：**存取權檢閱  
**產品功能：**控管  

**存取權檢閱的推出**


存取權檢閱 (預覽) 可讓組織有效地管理群組成員資格和企業應用程式的存取權： 

- 您可以重新認證來賓使用者存取權，方法為使用應用程式的存取權和群組的成員資格的存取權檢閱。 存取權檢閱提供的深入資訊可讓檢閱者有效地決定來賓是否應該具有連續存取權。

- 您可以利用存取權檢閱，重新認證員工對應用程式的存取權和群組成員資格。

您可以將存取權檢閱控制項收集到與組織相關的程式，以追蹤合規性或與風險相關之應用程式的檢閱。

如需詳細資訊，請參閱 [Azure AD 存取權檢閱](active-directory-azure-ad-controls-access-reviews-overview.md)。


---
**類型：**新功能  
**服務類別：**我的應用程式  
**產品功能：**SSO  


**從 [我的應用程式] 和 Office 365 啟動器隱藏協力廠商應用程式**

您現在可以更好管理您透過新的**隱藏應用程式**屬性，在使用者入口網站顯示的應用程式。 隱藏應用程式有助於解決因後端服務的應用程式磚或是重複磚的出現，而造成使用者的應用程式啟動器凌亂的情況。 該切換位於協力廠商應用程式的屬性區段上，並標示為 [讓使用者可以看到?] 您也可以透過 PowerShell 以程式設計方式隱藏應用程式。 

如需詳細資訊，請參閱[從 Azure Active Directory 的使用者體驗中隱藏第三方應用程式](active-directory-coreapps-hide-third-party-app.md)。 


**可用的內容？**

 作為轉換至新系統管理主控台過程的一部分，有 2 個可擷取 Azure AD 活動記錄的新 API 現已可用。 新的 API 集合除了能提供更豐富的稽核和登入活動外，還提供更豐富的篩選和排序功能。 現在可透過 Microsoft Graph 的 Identity Protection 風險事件 API 來存取先前透過安全性報告所使用的資料。


## <a name="september-2017"></a>2017 年 9 月

**類型：**已變更的功能  
**服務類別：**Microsoft Identity Manager  
**產品功能：**身分識別生命週期管理  


**適用於 Microsoft Identity Manager 的 Hotfix**

自 2017 年 9 月 25 日起，可使用 Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) 的 Hotfix 彙總套件 (建置 4.4.1642.0)。 此彙總套件：

- 解決問題並新增增強功能
- 是取代所有 MIM 2016 SP1 更新，最多到組建 4.4.1459.0 Microsoft Identity Manager 2016 的累計更新。 
- 必須要有 **Microsoft Identity Manager 2016 組建 4.4.1302.0。** 

如需詳細資訊，請參閱 [Microsoft Identity Manager 2016 SP1 有適用的 Hotfix 彙總套件 (組建 4.4.1642.0)](https://support.microsoft.com/en-us/help/4021562)。 

---
