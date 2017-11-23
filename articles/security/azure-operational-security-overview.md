---
title: "Azure 作業安全性概觀 | Microsoft Docs"
description: "本文提供 Azure 作業安全性概觀。"
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
ms.openlocfilehash: 6ee60c5f9a8049523f05a1f3afd6604768dde602
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security-overview"></a>Azure 作業安全性概觀
Azure 作業安全性是指使用者可在 Microsoft Azure 中用來保護其資料、應用程式和其他資產的服務、控制及功能。 [Azure 作業安全性](https://docs.microsoft.com/azure/security/azure-operational-security)的基礎架構涵蓋透過 Microsoft 特有之各種功能獲得的知識，包括 Microsoft 安全性開發週期 (SDL)、Microsoft 安全性回應中心方案，以及對網路安全性威脅型態的深層認知。

本＜Azure 作業安全性概觀＞一文著重於下列領域︰

- Azure Operations Management Suite
-   Azure 資訊安全中心
-   Azure 監視器
-   Azure 網路監看員
-   Azure 儲存體分析
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
IT 部門負責管理資料中心基礎結構、應用程式和資料，包括這些系統的安全性與穩定性。 不過，要在日趨複雜的 IT 環境中取得安全性深入解析，通常需要組織將多個安全性和管理系統的資料加以併用。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是 Microsoft 的雲端式 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。

OMS 是雲端式 IT 管理解決方案，包含許多供應項目，例如 IT 自動化、安全性與合規性、Log Analytics 以及備份與復原。 因此，它是您管理上的完美輔助工具，並且可保護您內部部署和雲端中的 IT 基礎結構。

OMS 的核心功能是由在 Azure 中執行的一組服務所提供。 每個服務會提供特定的管理功能，您可以合併服務以達成不同的管理案例。 其中包括：

-   Log Analytics
-   自動化
-   備份
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 藉由將受控資源中的資料收集到中央儲存機制，以提供 OMS 監視服務。 此資料可能包含事件、效能資料或透過 API 提供的自訂資料。 所收集的資料即可用於警示、分析和匯出。 這個方法可讓您合併各種來源的資料，以便結合來自 Azure 服務與現有內部部署環境的資料。 它也可清楚劃分資料的收集與對該資料採取的動作，而讓所有類型的資料可以使用所有的動作。

### <a name="automation"></a>自動化
Microsoft [Azure 自動化](https://docs.microsoft.com/azure/automation/automation-intro)可讓使用者將雲端和企業環境中執行的手動、長時間執行、易發生錯誤且重複性高的工作加以自動化。 它可以節省時間並提高日常管理工作的可靠性，甚至將它們排程為定期自動執行。 您可以使用 Runbook 自動執行程序，或使用「期望狀態設定」自動進行組態管理。

### <a name="backup"></a>備份
[Azure 備份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)是以 Azure 為基礎的服務，可用來備份 (或保護) 和還原 Microsoft Cloud 中的資料。 Azure 備份將以一個可靠、安全及具成本競爭力的雲端架構解決方案，取代您現有的內部部署或異地備份解決方案。 Azure 備份提供多個元件，您可以下載並部署在適當的電腦、伺服器或雲端中。 您部署的元件或代理程式，取決於您想要保護的項目。 所有 Azure 備份的元件 (無論您要保護的是內部部署或雲端資料) 都可以將資料備份至 Azure 中的復原服務保存庫。 請參閱 [Azure 備份元件資料表](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use)。

### <a name="site-recovery"></a>站台復原
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 藉由協調內部部署虛擬和實體機器至 Azure 或次要網站的複寫，來提供商務持續性。 如果主要網站無法使用，您會容錯移轉到次要位置，讓使用者可以繼續工作，並且在系統回到正常運作時容錯回復。 智慧型和有效的威脅偵測。

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) 是 Microsoft 的全方位「身分識別即服務」(IDaaS) 解決方案，它能夠：

-   讓 IAM 做為雲端服務
-   提供集中式存取管理、單一登入 (SSO) 及報告功能
-   支援應用程式資源庫中 [數千個應用程式](https://azure.microsoft.com/marketplace/active-directory/) (包括 Salesforce、Google Apps、Box、Concur 等) 的整合式存取管理。

Azure AD 也包含一組完整的[身分識別管理功能](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)，包括[多重要素驗證](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、[裝置註冊]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)、[自助式密碼管理](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)、[自助式群組管理](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)、[特殊權限的帳戶管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)、[角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)、[應用程式使用量監視](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)、[豐富的稽核](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)，以及[安全性監視和警示](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)。

藉由 Azure Active Directory，您為合作夥伴和客戶 (企業或消費者) 發佈的所有應用程式都可享有相同的身分識別與存取管理功能。 這可讓您大幅降低營運成本。

## <a name="azure-security-center"></a>Azure 資訊安全中心
[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-get-started)利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

[資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine)可協助您保護 Azure 中的虛擬機器資料，方法為提供虛擬機器的安全性設定可見性，並監視威脅。 資訊安全中心可以監視虛擬機器以取得︰

-   具有建議設定規則的作業系統 (OS) 安全性設定
-   系統安全性和重大更新遺失
-   端點保護建議
-   磁碟加密驗證
-   網路型攻擊

Azure 資訊安全中心會使用[角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)，以提供可在 Azure 中指派給使用者、群組與服務的[內建角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

資訊安全中心會評估資源的組態，以識別安全性問題與弱點。 在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與資源相關的項目。

>[!Note]
>請參閱[Azure 資訊安全中心的權限](https://docs.microsoft.com/azure/security-center/security-center-permissions)以深入了解角色與資訊安全中心允許的動作。

資訊安全中心會使用 Microsoft Monitoring Agent (這是 Operations Management Suite 和 Log Analytics 服務所用的相同代理程式)。 從這個代理程式收集的資料會儲存在與您的 Azure 訂用帳戶相關聯的現有 Log Analytics [工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)或新的工作區中 (將 VM 的地理位置納入考量)。

## <a name="azure-monitor"></a>Azure 監視器
您雲端應用程式中的效能問題可能會對企業產生影響。 透過多個互連的元件和頻繁的發行，隨時都可能導致效能降低。 此外，如果您正在開發應用程式，您的使用者通常會探索到您在測試時未發現的問題。 您應該立即知道這些問題，並具備可用以診斷和修復問題的工具。

[Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)是用以監視 Azure 上執行之服務的基本工具。 它會提供關於服務輸送量及周遭環境之基礎結構等級的資料。 如果您正在 Azure 中管理所有的應用程式，決定是否要相應增加或減少資源，Azure 監視器接著就能提供可讓您用來做為起點的項目。

除此之外，您還可以使用監視資料來取得應用程式的深入解析。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。 其中包括：

-   Azure 活動記錄檔
-   Azure 診斷記錄
-   度量
-   Azure 診斷

### <a name="azure-activity-log"></a>Azure 活動記錄檔
它是一個記錄，能讓您了解訂用帳戶中的資源所執行之作業。 [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)先前稱為「稽核記錄」或「作業記錄」，因為它會報告訂用帳戶中控制層面的事件。

### <a name="azure-diagnostic-logs"></a>Azure 診斷記錄
[Azure 診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是由資源所發出，提供有關該資源之作業的豐富、經常性資料。 這些記錄的內容會依資源類型而有所不同。

例如，Windows 事件系統記錄是適用於 VM、Blob 和資料表的一個診斷記錄類別，而佇列記錄則是適用於儲存體帳戶的診斷記錄類別。

診斷記錄不同於[活動記錄 (之前稱為稽核記錄或作業記錄)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)。 活動記錄能讓您了解訂用帳戶中的資源所執行之作業。 診斷記錄能讓您了解資源自行執行的作業。

### <a name="metrics"></a>度量
Azure 監視器可讓您取用遙測來查看您 Azure 工作負載的效能與健全狀況。 Azure 遙測資料最重要的類型是由大多數 Azure 資源所發出的度量 (也稱為效能計數器)。 Azure 監視器提供數種方式，來設定及取用這些[計量](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)以進行監視與疑難排解。

### <a name="azure-diagnostics"></a>Azure 診斷
它是 Azure 中可對部署的應用程式啟用診斷資料收集的功能。 您可以使用來自各種不同來源的診斷延伸模組。 目前支援 [Azure 雲端服務 Web 和背景工作角色](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)、執行 Microsoft Windows 的 [Azure 虛擬機器](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)，以及 [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)。


## <a name="network-watcher"></a>網路監看員
客戶可藉由安排和組合各種個別的網路資源 (例如 VNet、ExpressRoute、應用程式閘道、負載平衡器等等)，在 Azure 中建置端對端網路。 每個網路資源都可進行監視。

端對端網路可能會有複雜的組態和資源互動，而產生需要透過網路監看員來進行案例式監視的複雜案例。

[網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)會簡化 Azure 網路的監視和診斷。 網路監看員所提供的診斷和視覺效果工具可讓您擷取 Azure 虛擬機器上的遠端封包、使用流程記錄深入了解您的網路流量，並診斷 VPN 閘道與連線。

網路監看員目前具有下列功能︰

- [拓撲](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview)：提供網路層級檢視，顯示資源群組中網路資源之間的各種相互連線和關聯。
-   [變數封包擷取](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)：擷取進出虛擬機器的封包資料。 進階篩選選項和微調控制項 (例如能夠設定時間和大小限制) 可讓您靈活擷取資料。 封包資料可以 .cap 格式儲存在 Blob 存放區或本機磁碟上。
-   [IP 流量驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - 以流量資訊 5-tuple 封包參數 (目的地 IP、來源 IP、目的地連接埠、來源連接埠和通訊協定) 作為基礎，檢查是否允許或拒絕封包。 如果封包遭到安全性群組拒絕，則會傳回拒絕封包的規則和群組。
-   [下一個躍點](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)：決定在 Azure 網路網狀架構中路由傳送之封包的下一個躍點，讓您得以診斷任何設定錯誤的使用者定義路由。
-   [安全性群組檢視](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview)：取得要在 VM 上套用的有效和已套用安全性規則。
-   [NSG 流程記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)：網路安全性群組的流程記錄可讓您擷取群組中安全性規則所允許或拒絕之流量的相關記錄。 流程是由 5個 Tuple 資訊定義的，這些資訊分別是來源 IP、目的地 IP、來源連接埠、目的地連接埠和通訊協定。
-   [虛擬網路閘道和連線疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)：能夠針對虛擬網路閘道和連線進行疑難排解。
-   [網路訂用帳戶限制](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)：可讓您根據限制檢視網路資源使用量。
-   [設定診斷記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – 提供單一窗格以供啟用或停用資源群組中網路資源的診斷記錄。

若要進一步了解如何設定網路監看員，請參閱[設定網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="developer-operations-devops"></a>開發人員作業 (DevOps)
在 DevOps 應用程式開發之前，小組是負責收集軟體程式的商務需求，並撰寫程式碼。 接著，個別的 QA 小組會在隔離式開發環境中測試程式，如果已達成要求，就會釋出程式碼以供作業進行部署。 開發小組會進一步分割成諸如網路服務與資料庫等的定址接收器群組。 每次軟體程式「擲回牆上」給獨立的小組時，就會新增瓶頸。

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) 可讓小組以更快速、成本較低的方式提供更安全、更高品質的解決方案。 使用軟體與服務時，客戶預期能獲得動態且可靠的經驗。  小組必須快速地反覆查看軟體更新、測量更新的影響，並以新的開發反覆項目快速回應，從而解決問題或提供更多的值。  諸如 Microsoft Azure 的雲端平台已移除傳統的瓶頸，並協助基礎結構獲得更高價值。 在每一項業務中，都是以軟體支配作為業務成果的主要區分及要素。 沒有任何組織、開發人員或 IT 工作者可以或應該避免 DevOps 行動。

成熟的 DevOps 從業人員會採用下列幾種做法。 這些做法[需要人員](https://www.visualstudio.com/learn/what-is-devops-culture/)以業務情節作為基礎來制定策略。  工具可協助將各種做法自動化：

-   [靈活的規劃與專案管理](https://www.visualstudio.com/learn/what-is-agile/)技術，可用來規劃工作並加以隔離為短期衝刺、管理小組產能，並協助小組快速地適應瞬息萬變的業務需求。
-   [版本控制，通常搭配 Git](https://www.visualstudio.com/learn/what-is-git/)，可讓位於全球各地的小組共用來源，並與軟體開發工具整合，從而將發行管線自動化。
-   [持續整合](https://www.visualstudio.com/learn/what-is-continuous-integration/)會驅動進行中的合併和程式碼測試，以便及早找出缺失。  其他優點包括減少對抗合併問題的時間，以及開發小組的快速意見反應。
-   [持續傳遞](https://www.visualstudio.com/learn/what-is-continuous-delivery/)軟體解決方案到生產環境與測試環境，有助於組織快速修正錯誤，並回應日益變遷的商業需求。
-   執行中應用程式的[監視](https://www.visualstudio.com/learn/what-is-monitoring/)包括應用程式健康情況的生產環境以及客戶的使用方式，能協助組織產生假設並快速驗證或反駁策略。  擷取豐富的資料並以各種記錄格式加以儲存。
-   [基礎結構即程式碼 (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/)是一種做法，可用來將建立及卸除網路和虛擬機器加以自動化並驗證，從而協助提供安全且穩定的應用程式裝載平台。
-   [微服務](https://www.visualstudio.com/learn/what-are-microservices/)架構可用來將業務使用案例隔離為小型的可重複使用服務。  此架構能提升延展性和效率。

## <a name="next-steps"></a>後續步驟
若要深入了解 OMS 安全性和稽核解決方案，請參閱下列文章：

- [Operations Management Suite | 安全性與合規性](https://www.microsoft.com/cloud-platform/security-and-compliance)。
- [在 Operations Management Suite 安全性和稽核解決方案內監視及回應安全性警示](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts)。
- [在 Operations Management Suite 安全性和稽核解決方案內監視資源](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources)。
