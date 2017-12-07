---
title: "適用於 PCI DSS 相容環境的付款處理藍圖"
description: "PCI DSS 需求"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Azure Blueprint (藍圖) 自動化：適用於 PCI DSS 相容環境的付款處理

## <a name="overview"></a>概觀

「適用於 PCI DSS 相容環境的付款處理」可引導您部署符合 PCI DSS 規範的平台即服務 (PaaS) 環境，以處理敏感的支付卡資料。 此藍圖將示範常見的參考架構，並融入可輕鬆採用 Microsoft Azure 的設計。 此藍圖會說明端對端解決方案，以符合組織需要雲端方法來減少部署負擔和成本的需求。

此藍圖設計為符合嚴格的「支付卡產業資料安全標準」(PCI DSS 3.2)，適用於收集、儲存和擷取支付卡資料。 其中會示範如何在安全且相容的多層式環境中正確處理信用卡資料　(包含卡片號碼、到期日和驗證資料)，並將該環境會部署為端對端且以 Azure 為基礎的 PaaS 解決方案。 如需有關 PCI DSS 3.2 需求和此解決方案的詳細資訊，請參閱　[PCI DSS 需求 - 高階概觀](pci-dss-requirements-overview.md)。

此藍圖的目的是作為基礎，讓客戶更加瞭解特定需求，不應完全未經修改就用於生產環境中。 不作修改地將應用程式部署至此環境，會無法完全符合自訂解決方案之 PCI DSS 相容解決方案的需求。 請注意：
- 此藍圖提供了一個基準，可協助客戶透過與 PCI DSS 相容的方式來使用 Microsoft Azure。
- 若要與 PCI DSS 相容，實際客戶解決方案需要由公認的合格安全性評估機構 (QSA) 加以證明。
- 客戶須負責為任何使用基礎結構建置的解決方案安置適當的安全性與合規性檢閱，因為客戶的實作和所在地區可能有不同特性，因此可能會有不同需求。  

如需快速了解此解決方案的運作方式，請觀賞說明和示範其部署的此[影片](https://aka.ms/pciblueprintvideo)。

## <a name="solution-components"></a>方案元件

基礎架構是由下列元件組成：

- **架構圖**。 該圖顯示 Contoso Webstore 解決方案中所用的參考架構。
- **部署範本**。 在此部署中，透過在安裝期間指定設定參數，系統會使用 [Azure Resource Manager 範本](/azure/azure-resource-manager/resource-group-overview#template-deployment)來將架構的元件自動部署至 Microsoft Azure。
- **自動化部署指令碼**。 這些指令碼可協助您部署端對端解決方案。 指令碼包括：
    - 模組安裝和[全域管理員](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)安裝指令碼會用來進行安裝，以及確認必要的 PowerShell 模組和全域管理員角色已正確設定。
    - 安裝 PowerShell 指令碼會用來部署端對端解決方案，並透過 .zip 檔案和 .bacpac 檔案提供，其中包含預先建置的示範 Web 應用程式與 [SQL 資料庫的範例](https://github.com/Microsoft/azure-sql-security-sample)。 內容。 您可以在[付款處理藍圖代碼存放庫][code-repo]檢閱此解決方案的原始程式碼。 

## <a name="architectural-diagram"></a>架構圖

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>使用者案例

藍圖可解決下列使用案例。

> 此案例說明虛構網路商店如何將他們的支付卡處理程序移至以 Azure 為基礎的 PaaS 解決方案。 解決方案會處理付款資料等基本使用者資訊的收集。 該解決方案不會處理包含持卡人資料的付款作業；一旦收集資料後，客戶會負責開始及完成與付款處理器的交易。 如需詳細資訊，請參閱[實作的檢閱和指引](https://aka.ms/pciblueprintprocessingoverview)。

### <a name="use-case"></a>使用案例
名為 Contoso Webstore 的小型網路商店已準備好將他們的付款系統移至雲端。 他們已選取 Microsoft Azure 來主控購買處理程序，並允許店員收集客戶的信用卡付款資訊。

系統管理員正在尋找可快速部署的解決方案，以在雲端產生的解決方案中達成他的目標。 他將使用此概念證明 (POC) 與他的專案關係人一起討論 Azure 如何收集、儲存和擷取支付卡資料，同時又能符合嚴格的支付卡產業資料安全標準 (PCI DSS) 需求。

> 您將負責為任何使用此 POC 所用結構建置的解決方案安置適當的安全性與合規性檢閱，因為您的實作和所在地區可能有不同特性，因此可能會有不同需求。 PCI DSS 需要您直接與公認的合格安全性評估機構合作，來為您可實際執行的解決方案提供證明。

### <a name="elements-of-the-foundational-architecture"></a>基本架構的元素

基本架構的設計包含下列虛構元素：

網域站台 `contosowebstore.com`

用來說明使用案例的使用者角色，並提供使用者介面的深入解析。

#### <a name="role-site-and-subscription-admin"></a>角色：站台和訂用帳戶管理員

|Item      |範例|
|----------|------|
|使用者名稱： |`adminXX@contosowebstore.com`|
| 名稱： |`Global Admin Azure PCI Samples`|
|使用者類型：| `Subscription Administrator and Azure Active Directory Global Administrator`|

- 管理員帳戶無法讀取未遮罩的信用卡資訊。 所有動作皆會記錄。
- 管理員帳戶無法管理或登入 SQL Database。
- 管理員帳戶可以管理 Active Directory 和訂用帳戶。

#### <a name="role-sql-administrator"></a>角色：SQL 系統管理員

|Item      |範例|
|----------|------|
|使用者名稱： |`sqlAdmin@contosowebstore.com`|
| 名稱： |`SQLADAdministrator PCI Samples`|
| 名字： |`SQL AD Administrator`|
|姓氏： |`PCI Samples`|
|使用者類型：| `Administrator`|

- sqladmin 帳戶無法檢視未篩選的信用卡資訊。 所有動作皆會記錄。
- sqladmin 帳戶可以管理 SQL 資料庫。

#### <a name="role-clerk"></a>角色：店員

|Item      |範例|
|----------|------|
|使用者名稱：| `receptionist_EdnaB@contosowebstore.com`|
| 名稱： |`Edna Benson`|
| 名字：| `Edna`|
|姓氏：| `Benson`|
| 使用者類型： |`Member`|

Edna Benson 是接待員和業務經理。 她負責確保客戶資訊正確且收費已完成。 Edna 是登入的使用者，負責 Contoso Webstore 測試網站的所有互動。 Edna 有以下權利︰ 

- Edna 可以建立和讀取客戶資訊
- Edna 可以修改客戶資訊。
- Edna 可以覆寫或取代信用卡號碼、到期日和 CVV 資訊。

> 在 Contoso Webstore 中，使用者會自動設定為 **Edna** 使用者，以便測試已部署的環境功能。

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - 估算價格

此基本架構和範例 Web 應用程式使用每月計費結構，而使用量的費用為每小時計費，這些都是決定解決方案大小時須考慮的項目。 您可以使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)來估算這些成本。 從 2017年 9 月開始，此解決方案的每月費用預估為 ~ $2500，這包含 ASE v2 每月 $1000 的使用費。 這些費用會因為使用量不同而有所差異，而且可能會有所變更。 客戶應在部署時估算每月費用，可取得更準確的估計值。 

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於＜[部署架構](#deployment-architecture)＞一節中。

>- 應用程式閘道
>- Azure Active Directory
>- App Service Environment v2
>- OMS Log Analytics
>- Azure 金鑰保存庫
>- 網路安全性群組
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure 資訊安全中心
>- Azure Web 應用程式
>- Azure 自動化
>- Azure 自動化 Runbook
>- Azure DNS
>- Azure 虛擬網路
>- Azure 虛擬機器
>- Azure 資源群組和原則
>- Azure Blob 儲存體
>- Azure Active Directory 角色型存取控制 (RBAC)

## <a name="deployment-architecture"></a>部署架構

下一節會詳細說明開發和實作元素。

### <a name="network-segmentation-and-security"></a>網路分割和安全性

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>應用程式閘道

透過使用已啟用 Web 應用程式防火牆 (WAF) 和 OWASP 規則集的應用程式閘道，基礎架構可減少安全性弱點帶來的風險。 其他功能包括：

- [端對端 SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 啟用[ SSL 卸載](/azure/application-gateway/application-gateway-ssl-portal)
- 停用 [TLS v1.0 和 v1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 應用程式防火牆](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (WAF)
- 使用 OWASP 3.0 規則集的[預防模式](/azure/application-gateway/application-gateway-web-application-firewall-portal)
- 啟用[診斷記錄](/azure/application-gateway/application-gateway-diagnostics)
- [自訂健康情況探查](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。

#### <a name="virtual-network"></a>虛擬網路

基本架構會定義位址空間為 10.0.0.0/16 的私人虛擬網路。

#### <a name="network-security-groups"></a>網路安全性群組

每個網路層都有專用的網路安全性群組 (NSG)：
- 適用於防火牆和應用程式閘道 WAF 的 DMZ 網路安全性群組
- 適用於管理 jumpbox 的 NSG (防禦主機)
- 適用於應用程式環境的 NSG

每個 NSG 都具有特定連接埠及通訊協定，開放給安全且正確的解決方案作業使用。 如需詳細資訊，請參閱 [PCI 指引 - 網路安全性群組](#network-security-groups)。

每個 NSG 都具有特定連接埠及通訊協定，開放給安全且正確的解決方案工作使用。 此外，以下設定會針對每個 NSG 啟用：
- 啟用的[診斷記錄和事件](/azure/virtual-network/virtual-network-nsg-manage-log)會儲存在儲存體帳戶 
- 將 OMS Log Analytics 連線至 [NSG 的診斷](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>子網路
 確認每個子網路都與對應的 NSG 相關聯。

#### <a name="custom-domain-ssl-certificates"></a>自訂網域 SSL 憑證
 使用自訂網域 SSL 憑證來啟用 HTTPS 流量。

### <a name="data-at-rest"></a>待用資料

架構會使用加密、資料庫稽核及其他量值來保護待用資料。

#### <a name="azure-storage"></a>Azure 儲存體

為符合加密的待用資料需求，所有 [Azure 儲存體](https://azure.microsoft.com/services/storage/)都會使用[儲存體服務加密](/azure/storage/storage-service-encryption)。

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database 執行個體會使用下列資料庫安全性量值：

- [AD 驗證和授權](/azure/sql-database/sql-database-aad-authentication)
- [SQL 資料庫稽核](/azure/sql-database/sql-database-auditing-get-started)
- [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [防火牆規則](/azure/sql-database/sql-database-firewall-configure)，可用於 ASE 背景工作集區與用戶端 IP 管理
- [SQL 威脅偵測](/azure/sql-database/sql-database-threat-detection-get-started)
- [Always Encrypted 資料行](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL Database 動態資料遮罩](/azure/sql-database/sql-database-dynamic-data-masking-get-started)，使用部署後 PowerShell 指令碼

### <a name="logging-and-auditing"></a>記錄與稽核

[Operations Management Suite (OMS)](/azure/operations-management-suite/) 可以提供所有系統和使用者活動的大量記錄給 Contoso Webstore ，包括持卡人資料記錄。 為確保正確性，您可以檢閱及驗證變更。 

- **活動記錄：**  [活動記錄](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。
- **診斷記錄：**[診斷記錄](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄、Azure Blob 儲存體、資料表和佇列記錄。
- **防火牆記錄：**  應用程式閘道會提供完整的診斷和存取記錄。 防火牆記錄可供已啟用 WAF 的應用程式閘道資源使用。
- **記錄封存：**  所有診斷記錄會設定為寫入到集中且加密的 Azure 儲存體帳戶進行封存，並包含定義的保留期間 (2 天)。 接著，記錄會連線至 Azure Log Analytics 以進行處理、儲存及進行儀表板管理。 [Log Analytics](https://azure.microsoft.com/services/log-analytics) 是種 OMS 服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。

### <a name="encryption-and-secrets-management"></a>加密和密碼管理

Contoso Webstore 會將所有信用卡資料加密，並使用 Azure Key Vault 管理金鑰，防止 CHD 遭到擷取。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) 會用來加密所有客戶持卡人資料、到期日和 CVV。
- 資料會使用 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)和 BitLocker 來儲存在磁碟上。

### <a name="identity-management"></a>身分識別管理

下列技術可在 Azure 環境中提供身分識別管理功。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。 解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 SQL Database 的使用者。
- 應用程式的驗證是使用 Azure AD 執行。 如需詳細資訊，請參閱 [整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。 此外，資料庫資料行加密也會使用 Azure AD 向 Azure SQL Database 驗證應用程式。 如需詳細資訊，請參閱 [Always Encrypted：保護 SQL Database 中的敏感性資料](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)。 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) 會偵測影響您組織身分識別的潛在弱點，並為偵測到的組織身分識別相關可疑活動設定自動回應，以及調查可疑事件並採取適當動作來解決這些可疑事件。
- [Azure 角色型存取控制 (RBAC)](/azure/active-directory/role-based-access-control-configure) 可以對 Azure 進行精確且專注的存取權管理。 可存取訂用帳戶的身分會限制為訂用帳戶系統管理員，而可存取 Azure Key Vault 的身分則是限制為所有使用者。

若要了解使用 Azure SQL Database 安全性功能的詳細資訊，請參閱 [Contoso 診所示範應用程式](https://github.com/Microsoft/azure-sql-security-sample)範例。
   
### <a name="web-and-compute-resources"></a>Web 與計算資源

#### <a name="app-service-environment"></a>App Service 環境

[Azure App Service](/azure/app-service/) 是受管理的服務，適用於部署 Web 應用程式。 Contoso Webstore 應用程式會部署為 [App Service Web 應用程式](/azure/app-service-web/app-service-web-overview)。

[Azure App Service Environment (ASE v2)](/azure/app-service/app-service-environment/intro) 是 App Service 的功能，可提供完全隔離和專用的環境，以便安全地大規模執行 App Service 應用程式。 此基礎結構會使用此進階服務方案來符合 PCI DSS 的規範。

ASE 已經過隔離，可執行只有單一客戶的應用程式，且一律會部署到虛擬網路。 客戶對於輸入和輸出的應用程式網路流量都有更細微的控制，且應用程式可以透過虛擬網路建立與內部部署公司資源的高速安全連線。

下列控制項/設定可允許此架構使用 ASE：

- 在安全的虛擬網路和網路安全性規則內主控
- 使用自我簽署的 ILB 憑證設定 ASE，以進行 HTTPS 通訊
- [內部負載平衡模式](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (模式 3)
- 停用 [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) - 對於 PCI DSS 而言，這是已被取代的 TLS 通訊協定
- 變更 [TLS 加密](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- 控制[輸入流量的 N/W 連接埠](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – 限制資料](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- 允許 [SQL Database 流量](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (防禦主機)

當 App Service 環境受到保護且鎖定時，會需要一個機制來允許任何可能必要的 DevOps 發行或變更，例如使用 Kudu 監視 Web 應用程式的功能。 虛擬機器會在 NAT 負載平衡器後方受到保護，讓您可在 TCP 3389 以外的連接埠上連線到虛擬機器。 

虛擬機器已透過下列設定建立為 jumpbox (防禦主機)：

-   [反惡意程式碼軟體擴充功能](/azure/security/azure-security-antimalware)
-   [OMS 擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure 診斷擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   使用 Azure Key Vault 的 [Azure 磁碟加密](/azure/security/azure-security-disk-encryption)(遵循 Azure Government、PCI DSS和 HIPAA 等其他需求)。
-   [自動關閉原則](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)，可減少不使用虛擬機器資源時的耗用量。

### <a name="security-and-malware-protection"></a>安全性和惡意程式碼防護

[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)可提供所有 Azure 資源安全性狀態的集中檢閱。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，以及快速找出任何需要注意的資源。  

[Azure Advisor](/azure/advisor/advisor-overview) 是個人化的雲端顧問，可協助您依最佳做法來最佳化您的 Azure 部署。 它可分析您的資源組態和使用量遙測，然後建議可協助您改善 Azure 資源的成本效益、效能、高可用性和安全性的解決方案。

適用於 Azure 雲端服務和虛擬機器的 [Microsoft Antimalware](/azure/security/azure-security-antimalware) 是一項即時保護功能，能夠協助識別並移除病毒、間諜軟體及其他惡意軟體，且具有可設定的警示，可在已知的惡意或垃圾軟體嘗試在您的 Azure 系統上安裝或執行時發出警示。

### <a name="operations-management"></a>作業管理

#### <a name="application-insights"></a>Application Insights

使用 [Application Insights](https://azure.microsoft.com/services/application-insights/) 來透過應用程式效能管理和立即分析，取得可操作的深入資訊。

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Operations Management Suite (OMS) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。

#### <a name="oms-solutions"></a>OMS 解決方案

您應該考慮和設定這些額外的 OMS 解決方案：
- [活動 Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [使用 Azure 網路分析](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL 分析](/azure/log-analytics/log-analytics-azure-sql)
- [變更追蹤](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [金鑰保存庫分析](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [服務對應](/azure/operations-management-suite/operations-management-suite-service-map)
- [安全性和稽核](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [反惡意程式碼](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [更新管理](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>資訊安全中心整合

預設部署的目的是提供資訊安全中心建議的基準，並指出狀況良好且安全的設定狀態。 您可以在 Azure 資訊安全中心啟用資料收集。 如需詳細資訊，請參閱 [Azure 資訊安全中心 - 使用者入門](/azure/security-center/security-center-get-started)。

## <a name="deploy-the-solution"></a>部署解決方案

部署此解決方案的元件可在 [PCI 藍圖原始程式碼存放庫][code-repo]中取得。 基本架構的部署需要透過 Microsoft PowerShell v5 執行多個步驟。 若要連線至網站，您必須提供自訂網域名稱 (例如 contoso.com)。 這會使用步驟 2 中的 `-customHostName`參數來指定。 如需詳細資訊，請參閱[針對 Azure Web Apps 購買自訂網域名稱](/azure/app-service-web/custom-dns-web-site-buydomains-web-app)。 自訂網域名稱並不是成功部署和執行解決方案的必要項目，但是您會無法連線至用於示範的網站。

指令碼會將網域使用者新增至您指定的 Azure AD 租用戶。 我們建議您建立新的 Azure AD 租用戶，以作為測試使用。

如果您在部署期間遇到任何問題，請參閱[常見問題和疑難排解](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md)。

強烈建議您使用 PowerShell 的全新安裝來部署解決方案。 或者，請確認您已使用可正確執行安裝指令碼的最新模組。 在此範例中，我們會登入 jumpbox (防禦主機)，並執行下列命令。 請注意，這會啟用自訂網域命令。


1. 安裝所需的模組，並正確地設定系統管理員角色。
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    如需詳細的使用方式指示，請參閱[指令碼指示 - 安裝系統管理帳戶和權限](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md)。
    
2. 安裝解決方案更新管理 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    如需詳細的使用方式指示，請參閱[指令碼指示 - 部署和設定 Azure 資源](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。
    
3. OMS 記錄和監視。 一旦部署方案後，[Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) 工作區即可開啟，並可使用解決方案存放庫中的範例範本來說明如何設定監視儀表板。 如範例 OMS 的範本，請參考 [omsDashboards 資料夾](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md)。 請注意必須在 OMS 中收集資料，範本才能正確部署。 根據網站活動，這可能需要花費一小時以上。
 
    設定您的 OMS 記錄時，請考慮加入這些資源：
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>威脅模型

適用於 Contoso Webstore 的資料流程圖 (DFD) 和範例威脅模型[付款處理藍圖威脅模型](https://aka.ms/pciblueprintthreatmodel)。

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>客戶責任矩陣

客戶須保留一份[責任摘要矩陣](https://aka.ms/pciblueprintcrm32)，其中會概述屬於客戶責任的 PCI DSS 需求與屬於 Microsoft Azure 責任的 PCI DSS 需求。

## <a name="pci-compliance-review"></a>PCI 合規性檢閱 

解決方案會由 Coalfire systems, Inc.(PCI-DSS 的合格安全性評估機構) 檢閱。 [PCI 合規性檢閱和實作指引](https://aka.ms/pciblueprintprocessingoverview)提供稽核員對解決方案的檢閱，以及將藍圖轉換為實際可執行部署的考量。

## <a name="disclaimer-and-acknowledgements"></a>免責聲明與認知

*2017 年 9 月*

- 此文件僅供參考之用。 Microsoft 和 AVYAN 對本文件中的資訊不做任何明示、暗示或成文之擔保 這份文件係依「現狀」提供。 本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更，恕不另行通知。 讀取這份文件的客戶須自行承擔使用風險。  
- 本文件未提供給客戶任何 Microsoft 或 Avyan 產品或解決方案中任何智慧財產的任何法定權利。  
- 客戶可以複製並使用這份文件，供內部參考之用。  

  > [!NOTE]
  > 本文件的某些建議可能會導致資料、網路或 Azure 計算資源使用量增加，並可能增加客戶的 Azure 授權或訂用帳戶成本。  

- 這份文件中的解決方案旨在作為基本架構，請勿毫無修改地直接用於生產環境。 達到 PCI 合規性需要客戶向合格安全性評估機構進行諮詢。  
- 此頁面上的所有客戶名稱、交易記錄和任何相關資料皆屬虛構，僅為此基本架構而建立，並僅供示範用。 並未影射或關聯任何真實的人、事、物。  
- 此解決方案由 Microsoft 與 Avyan Consulting 共同開發，而且經過 [MIT 授權](https://opensource.org/licenses/MIT)許可。
- 此解決方案已由 Coalfire (Microsoft 的 PCI DSS 稽核員) 檢閱。 [PCI 合規性檢閱](https://aka.ms/pciblueprintcrm32)提供解決方案獨立的第三方檢閱，以及需要解決的要素。 

### <a name="document-authors"></a>文件作者

- *Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (Avyan Consulting)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "程式碼存放庫"
