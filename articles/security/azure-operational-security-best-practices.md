---
title: "Azure 作業安全性最佳做法 | Microsoft Docs"
description: "本文提供一組 Azure 作業安全性的最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: ced8ecde1f36c49b479c7b253a90614567783663
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security-best-practices"></a>Azure 作業安全性最佳做法
Azure 作業安全性是指使用者可在 Microsoft Azure 中用來保護其資料、應用程式和其他資產的服務、控制及功能。 Azure 作業安全性的基礎架構涵蓋透過 Microsoft 特有之各種功能獲得的知識，包括 Microsoft 安全性開發週期 (SDL)、Microsoft Security Response Center 方案，以及對網路安全性威脅型態的深層認知。

本文討論 Azure 資料庫安全性最佳做法的集合。 這些最佳做法衍生自我們的 Azure 資料庫安全性以及如您本身的客戶體驗。

針對每個最佳做法，我們會說明︰
-   最佳作法是什麼
-   您為何想要啟用該最佳作法
-   如果無法啟用最佳作法，結果可能為何
- 如何學習啟用最佳作法

這篇＜Azure 作業安全性最佳做法＞文章是以共識意見以及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 作為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 作業安全性最佳做法包括：

-   監視、管理及保護雲端基礎結構
-   管理身分識別及實作單一登入 (SSO)
-   追蹤要求、分析使用趨勢及診斷問題
-   使用集中式監視解決方案監視服務
-   預防、偵測及回應威脅
-   以端對端案例為基礎的網路監視
-   使用經證實的 DevOps 工具進行安全的部署

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>監視、管理及保護雲端基礎結構
IT 部門負責管理資料中心基礎結構、應用程式和資料，包括這些系統的安全性與穩定性。 不過，要在日趨複雜的 IT 環境中取得安全性深入解析，通常需要組織將多個安全性和管理系統的資料加以併用。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是 Microsoft 的雲端式 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。

[OMS 安全性與稽核解決方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)可讓 IT 人員主動監視所有資源，此舉有助於將安全性事件的影響降至最低。 OMS 安全性和稽核具備可用來監視資源的安全性網域。

如需 OMS 的詳細資訊，請閱讀 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)一文。

為了協助您預防、偵測及回應威脅，[Operations Management Suite (OMS) 安全性和稽核解決方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)會收集和處理資源相關資料，其中包括：

-   安全性事件記錄檔
-   Windows 事件追蹤 (ETW) 事件
-   AppLocker 稽核事件
-   Windows 防火牆記錄檔
-   Advanced Threat Analytics 事件
-   基準評估結果
-   反惡意程式碼評估結果
-   更新/修補評估結果
-   在代理程式上明確啟用的 Syslog 資料流


## <a name="manage-identity-and-implement-single-sign-on"></a>管理身分識別及實作單一登入
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。

[Azure AD](https://azure.microsoft.com/services/active-directory/) 也包含一組完整的[身分識別管理](https://docs.microsoft.com/azure/security/security-identity-management-overview)功能，包括 [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、裝置註冊、自助式密碼管理、自助式群組管理、特殊權限的帳戶管理、角色型存取控制、應用程式使用量監視、豐富的稽核，以及安全性監視和警示。

下列功能可以協助保護雲端式應用程式的安全、簡化 IT 程序、降低成本，以及協助確保達到公司的規範目標：

-   雲端身分識別和存取管理
-   簡化任何雲端應用程式的使用者存取
-   保護敏感性資料和應用程式
-   讓員工進行自助服務
-   與 Azure Active Directory 整合

### <a name="identity-and-access-management-for-the-cloud"></a>雲端身分識別和存取管理
Azure Active Directory (Azure AD) 是全方位[身分識別與存取管理雲端解決方案](https://www.microsoft.com/cloud-platform/identity-management)，提供您一組完善的功能來管理使用者與群組。 對於內部部署與雲端應用程式 (包括 Office 365 等 Microsoft Web 服務，以及許多非 Microsoft 的軟體即服務 (SaaS) 應用程式) 的存取，也協助提供保護。
若要深入了解如何在 Azure AD 中啟用 Identity Protection，請參閱[啟用 Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)。

### <a name="simplify-user-access-to-any-cloud-app"></a>簡化任何雲端應用程式的使用者存取
[啟用單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)可以簡化從 Windows、Mac、Android 和 iOS 裝置到數千種雲端應用程式的使用者存取。 使用者可以從個人化的 Web 架構存取面板或行動應用程式中，使用其公司認證啟動應用程式。 使用 Azure AD 應用程式 Proxy 模組來超越 SaaS 應用程式，並發佈內部部署 Web 應用程式，進而提供高度安全的遠端存取與單一登入。

### <a name="protect-sensitive-data-and-applications"></a>保護敏感性資料和應用程式
啟用 [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)，藉由提供一層額外的驗證，來防止未經授權的人員存取內部部署和雲端應用程式。 您可以利用安全性監視、警示及機器學習報表來找出矛盾的存取模式，進而達到保護您公司安全及緩解潛在威脅的目的。

### <a name="enable-self-service-for-your-employees"></a>讓員工進行自助服務
將重要的工作委派給您的員工，例如重設密碼、建立及管理群組等等。 您可以透過 Azure AD [啟用自助式密碼變更](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)、重設及自助式群組管理。

### <a name="integrate-with-azure-active-directory"></a>與 Azure Active Directory 整合
將 [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) 與任何其他內部部署目錄擴展到 Azure AD，為所有雲端式應用程式啟用單一登入功能。 使用者屬性可自動從所有種類的內部部署目錄同步處理至雲端目錄。

若要深入了解 Azure Active Directory 的整合及如何啟用此功能，請閱讀[整合您的內部部署目錄與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 一文。

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>追蹤要求、分析使用趨勢及診斷問題
[Azure 儲存體分析](https://docs.microsoft.com/azure/storage/storage-analytics)會執行記錄，並提供儲存體帳戶的計量資料。 您可以使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。

預設會為新的儲存體帳戶啟用儲存體分析度量。 您可以在 Azure 入口網站中啟用記錄及設定度量和記錄；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用 [設定服務屬性] 作業，分別為各個服務啟用儲存體分析。

如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

若要深入了解 Azure Active Directory 的整合及如何啟用此功能，請閱讀 [Enabling and Configuring Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN) (啟用及設定儲存體分析) 一文。

## <a name="monitoring-services"></a>監視服務
雲端應用程式相當複雜，且具有許多移動組件。 監視會提供資料，以確保應用程式持續運作並以健全的狀態執行。 它也可協助您預防潛在問題，或是針對過去所發生的問題進行疑難排解。 除此之外，您還可以使用監視資料來取得應用程式的深入解析。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。

### <a name="monitor-azure-resources"></a>監視 Azure 資源
[Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)是平台服務，提供監視 Azure 資源的單一來源。 您可以使用 Azure 監視器來視覺化、查詢、路由、封存及針對來自 Azure 資源的度量和記錄檔採取行動。 您可以利用監視入口網站刀鋒視窗、[Monitor PowerShell Cmdlets](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)、[Cross-Platform CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) 或 [Azure 監視器 REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來使用此資料。

### <a name="enable-autoscale-with-azure-monitor"></a>啟用 Azure 監視器自動調整規模
啟用 [Azure 監視器自動調整規模](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started)僅適用於虛擬機器擴展集 (VMSS)、雲端服務、App Service 方案及 App Service 環境。

### <a name="manage-roles-permissions-and-security"></a>管理角色權限與安全性
許多團隊需要嚴格[規範對監視資料及設定的存取](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 例如，如果您擁有專門從事監視 (技術支援工程師、devops 工程師) 的團隊成員，或如果您使用受管理的服務提供者，則您可能只要授與他們監視資料的存取權，同時限制他們建立、修改或刪除資源的能力。

本文說明如何在 Azure 中快速將內建的監視 RBAC 角色套用到使用者，或針對需要有限監視權限的使用者建置您自己的自訂角色。 接著會討論 Azure 監視器相關資源的安全性考量，以及如何限制對這些資源所包含的資料進行存取。

## <a name="prevent-detect-and-respond-to-threats"></a>預防、偵測及回應威脅
資訊安全中心威脅偵測的運作方式如下：從 Azure 資源、網路及已連線的合作夥伴解決方案自動收集安全性資訊。 它會分析這項資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。 資訊安全中心的安全性警示會排定優先順序，並提供如何補救威脅的建議。

-   為您的 Azure 訂用帳戶[設定安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies)。
-   使用[資訊安全中心的建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)來協助您保護 Azure 資源。
-   檢閱並管理目前的[安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。

[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

資訊安全中心，提供 Azure 內建的威脅預防、偵測及回應功能，簡單易用又有效。 主要功能：

-   了解雲端安全性狀態
-   掌控雲端安全性
-   輕鬆地部署整合式雲端安全性解決方案
-   偵測威脅並快速回應

### <a name="understand-cloud-security-state"></a>了解雲端安全性狀態
使用 Azure 資訊安全中心，以集中檢視所有 Azure 資源的安全性狀態。 只需看一眼，便可確認適當的安全性控制項是否已就緒並正確設定，以及快速找出任何需要注意的資源。

### <a name="take-control-of-cloud-security"></a>掌控雲端安全性
根據您公司的雲端安全性需求，針對各訂用帳戶的應用程式類型或資料敏感度量身打造，為您的 Azure 訂用帳戶定義[安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies)。 使用原則驅動的建議來指引資源擁有者了解實作必要控制項的過程，讓雲端安全性的不確定性蕩然無存。

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>輕鬆地部署整合式雲端安全性解決方案
[啟用來自 Microsoft 及其合作夥伴的安全性解決方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)，包括領先業界的防火牆以及反惡意程式碼。 使用簡化的佈建來部署安全性解決方案，就連網路變更都為您設定好。 系統會自動收集來自合作夥伴解決方案的安全性事件，以進行分析及發出警示。

### <a name="detect-threats-and-respond-fast"></a>偵測威脅並快速回應
透過經整合的分析導向方法，搶先發現目前及潛藏的雲端威脅。 資訊安全中心藉著結合 Microsoft 全球[威脅情報](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)和專業知識，以及您 Azure 部署內雲端安全性相關事件的深入解析，協助您即早偵測出實際的威脅並減少誤判情形。 雲端安全性警示提供對攻擊活動的深入解析，包括相關事件及受影響的資源，並建議修復問題及快速復原的方式。

## <a name="end-to-end-scenario-based-network-monitoring"></a>以端對端案例為基礎的網路監視
客戶可藉由安排和組合各種個別的網路資源 (例如 VNet、ExpressRoute、應用程式閘道、負載平衡器等等)，在 Azure 中建置端對端網路。 每個網路資源都可進行監視。

[網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>利用封包擷取自動化遠端網路監視作業
使用網路監看員無須登入您的虛擬機器 (VM)，就能監視及診斷網路問題。 您可以設定警示來觸發[封包擷取](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture)，並能存取封包層級的即時效能資訊。 當您發現問題時，可以詳加調查，從而進行更好的診斷。

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>利用流量記錄獲取網路流量的深入解析
使用[網路安全性群組流量記錄](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)更進一步了解您的網路流量模式。 流量記錄所提供的資訊可協助您收集合規性資料，並稽核及監視您的網路安全性設定檔。

### <a name="diagnose-vpn-connectivity-issues"></a>診斷 VPN 連線問題
網路監看員讓您可以[診斷一些最常見的 VPN 閘道及連線問題](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity)。 不僅可以找出問題，還能利用所建立的詳細記錄進行深入調查。

若要深入了解如何設定網路監看員及如何啟用此功能，請閱讀[設定網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)一文。

## <a name="secure-deployment-using-proven-devops-tools"></a>使用經證實的 DevOps 工具進行安全的部署
此 Microsoft Cloud 空間中列出其中一些 Azure DevOps 實作，可提高企業和團隊的生產力和效率。

-   **基礎結構即程式碼 (IaC)：**基礎結構即程式碼是一組技術和實作，可協助 IT 專業人員減輕與每日建置及管理模組化基礎結構相關的負擔。 它可讓 IT 專業人員建立及維護其新式伺服器環境，就像是軟體開發人員建立及維護應用程式程式碼一樣。 在 Azure 中，[Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) 可讓您使用宣告式範本佈建應用程式。 在單一的範本中，您可以部署多個服務及其相依性。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。
-   **持續整合和持續部署：**您可以將 Visual Studio Online Team 專案設定為[自動建置和部署](https://www.visualstudio.com/docs/build/overview)至 Azure Web Apps 或雲端服務。 VSO 會在對 Azure 執行組建之後，將二進位檔自動部署至每個程式碼簽入後面。 此處所述的套件建置程序等同於 Visual Studio 中的 [封裝] 命令，而發佈步驟等同於 Visual Studio 中的 [發佈] 命令。
-   **Release Management：**Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) 是自動化多階段部署及管理發行程序的絕佳解決方案。 建立受管理的持續部署管線以快速、輕鬆且經常發行。 使用 Release Management，我們可以將發行程序大幅自動化，而且可以具有預先定義的核准工作流程。 在內部部署及部署至雲端、擴充，並視需要自訂。
-   **應用程式效能監視：**偵測問題、解決問題並持續改善您的應用程式。 快速診斷即時應用程式中的任何問題。 了解您的使用者與其有無任何關係？ 新增 JS 程式碼和 webconfig 項目是簡單的設定，幾分鐘之內就能在入口網站中看到附有所有詳細資料的結果。[App Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) 協助企業更快偵測問題並進行修正。
-   **負載測試和自動縮放：**我們可以在應用程式中找出效能問題，從而改善部署品質，並確保應用程式一律保持最新狀態，或使用符合商務需求。 請確定您的應用程式可以處理後續上市或行銷活動的流量。 使用 Visual Studio Online 幾乎立即開始執行雲端式[負載測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)。

## <a name="next-steps"></a>後續步驟
- 深入了解 [Azure 作業安全性](https://docs.microsoft.com/azure/security/azure-operational-security)。
- 如需詳細資訊，請參閱 [Operations Management Suite | 安全性與合規性](https://www.microsoft.com/cloud-platform/security-and-compliance)。
- [開始使用 Operations Management Suite 安全性與稽核解決方案](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)。
