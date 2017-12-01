---
title: "Azure Blueprint (藍圖) 自動化 - 適用於 FedRAMP 的 Web 應用程式"
description: "Azure Blueprint (藍圖) 自動化 - 適用於 FedRAMP 的 Web 應用程式"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d0521d68bab8bd0b7db53a512da6d37033abd85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation---web-applications-for-fedramp"></a>Azure Blueprint (藍圖) 自動化 - 適用於 FedRAMP 的 Web 應用程式

## <a name="overview"></a>概觀

[聯邦風險與授權管理計畫 (FedRAMP)](https://www.fedramp.gov) \(英文\) 為美國政府層面的計劃，提供雲端產品與服務的安全評估、授權及持續監視的標準方法。 此＜Azure Blueprint (藍圖) 自動化 - 適用於 FedRAMP 的 Web 應用程式＞提供的指導方針可用來部署符合 FedRAMP 規範的基礎結構即服務 (IaaS) 環境，且此環境適合網際網路面向的簡易 Web 應用程式。 此解決方案將一般參考架構的 Azure 資源部署和設定自動化，其中示範的方式讓客戶能符合特定的安全性和合規性需求，並且作為客戶在 Azure 上建置及設定他們自己的解決方案之基礎。 此解決方案會根據 NIST SP 800-53 實作 FedRAMP 高標準基準的控制措施子集。 如需 FedRAMP 高標準需求和此解決方案的詳細資訊，請參閱 [FedRAMP 高標準需求 - 高層級概觀](fedramp-controls-overview.md)。 ***注意：此解決方案是部署到 Azure Government。***

此架構的目的是作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。 未經修改即將應用程式部署至此環境，會無法完全符合 FedRAMP 高標準基準的需求。 請注意：
- 此架構提供了一個基準，可協助客戶以符合 FedRAMP 規範的方式來使用 Azure。
- 客戶須負責為任何使用架構建置的解決方進行置適當的安全性與合規性評量，因為需求可能會隨每個客戶的實作細節而有所不同。 

如需此解決方案運作方式簡短概觀，請觀賞說明及示範其部署的此[影片](https://aka.ms/fedrampblueprintvideo) \(英文\)。

如需部署指示，請按一下[這裡](https://aka.ms/fedrampblueprintrepo) \(英文\)。

## <a name="solution-components"></a>方案元件

此「Azure Blueprint (藍圖) 自動化」使用預先設定的安全性措施，自動部署 IaaS Web 應用程式參考架構，以協助客戶符合 FedRAMP 需求的規範。 解決方案包含引導資源部署與設定的 Azure Resource Manager 範本和 PowerShell 指令碼。 已附上 Azure Blueprint (藍圖) [合規性文件](#compliance-documentation)，指出繼承自 Azure 安全性措施及已部署且符合 NIST SP 800-53 安全性措施的資源與設定，因此可讓組織快速追蹤合規性義務。

## <a name="architecture-diagram"></a>架構圖表

此解決方案會部署具有資料庫後端之 IaaS Web 應用程式的參考架構。 架構包含 Web 層、資料層、Active Directory 基礎結構、應用程式閘道及負載平衡器。 部署到 Web 層和資料層的虛擬機器是設定在可用性設定組中，而 SQL Server 是設定在適用於高可用性的 AlwaysOn 可用性群組中。 虛擬機器已加入網域，而且使用 Active Directory 群組原則來強化作業系統層級的安全性與合規性設定。 管理 Jumpbox (防禦主機) 提供可讓系統管理員存取部署資源的安全連線。

![替代文字](images/fedramp-architectural-diagram.png?raw=true " 適用於符合 FedRAMP 規範之環境的 IaaS Web 應用程式藍圖自動化")

此解決方案會使用下列 Azure 服務。 部署架構的詳細資料位於[部署架構](#deployment-architecture)一節中。

* **Azure 虛擬機器**
    - (1) 管理/防禦 (Windows Server 2016 Datacenter)
    - (2) Active Directory 網域控制站 (Windows Server 2016 Datacenter)
    - (2) SQL Server 叢集節點 (Windows Server 2012 R2 上的 SQL Server 2016)
    - (1) SQL Server 見證 (Windows Server 2016 Datacenter)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
* **可用性設定組**
    - (1) Active Directory 網域控制站
    - (1) SQL 叢集節點和見證
    - (1) Web/IIS
* **Azure 虛擬網路**
    - (1) /16 虛擬網路
    - (5) /24 子網路
    - DNS 設定設為這兩個網域控制站
* **Azure 負載平衡器**
    - (1) SQL 負載平衡器
* **Azure 應用程式閘道**
    - (1) WAF 應用程式閘道已啟用
      - 防火牆模式：防止
      - 規則集：OWASP 3.0
      - 接聽程式：連接埠 443
* **Azure 儲存體**
    - (7) 異地備援儲存體帳戶
* **Azure 備份**
    - (1) 復原服務保存庫
* **Azure 金鑰保存庫**
    - (1) Key Vault
* **Azure Active Directory**
* **Azure Resource Manager**
* **Azure Log Analytics**
* **Azure 自動化**
    - (1) 自動化帳戶
* **Operations Management Suite**
    - (1) OMS 工作區

## <a name="deployment-architecture"></a>部署架構

下一節會詳細說明開發和實作元素。

### <a name="network-segmentation-and-security"></a>網路分割和安全性

#### <a name="application-gateway"></a>應用程式閘道

透過使用已啟用 Web 應用程式防火牆 (WAF) 和 OWASP 規則集的應用程式閘道，此架構可減少安全性弱點帶來的風險。 其他功能包括：

- [端對端 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- 啟用 [SSL 卸載](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- 停用 [TLS v1.0 和 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web 應用程式防火牆](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) \(WAF 模式\)
- 使用 OWASP 3.0 規則集的[防止模式](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

#### <a name="virtual-network"></a>虛擬網路

此架構會定義位址空間為 10.200.0.0/16 的私人虛擬網路。

#### <a name="network-security-groups"></a>網路安全性群組

此解決方案在虛擬網路內，將資源部署在具有個別的 Web 子網路、資料庫子網路、Active Directory 子網路及管理子網路的架構中。 套用至個別子網路的網路安全性群組規則以邏輯方式區隔了子網路，將子網路之間的流量限制為系統和管理功能所需的流量。

請參閱使用此解決方案部署之[網路安全性群組](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)的設定。 組織可以使用[此文件](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)當作指南來編輯上述的檔案，以設定網路安全性群組。

每個子網路都有專用的網路安全性群組 (NSG)：
- 1 個 NSG 用於應用程式閘道 (LBNSG)
- 1 個 NSG 用於 Jumpbox (MGTNSG)
- 1 個 NSG 用於主要和備份網域控制站 (ADNSG)
- 1 個 NSG 用於 SQL Server 及檔案共用見證 (SQLNSG)
- 1 個 NSG 用於 Web 層 (WEBNSG)

#### <a name="subnets"></a>子網路

每個子網路都與其相對應的 NSG 關聯。

### <a name="data-at-rest"></a>待用資料

此架構使用數個加密措施來保護待用資料。

#### <a name="azure-storage"></a>Azure 儲存體

為符合待用資料的加密需求，所有儲存體都會使用[儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

#### <a name="sql-database"></a>SQL Database

SQL Database 設定成使用[透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)，它會執行即時的資料和記錄檔的加密和解密，以保護待用資訊。 TDE 能保證已儲存的資料不會遭到未經授權的存取。 

#### <a name="azure-disk-encryption"></a>Azure 磁碟加密

「Azure 磁碟加密」可用來加密 Windows IaaS 虛擬機器磁碟。 [Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)利用 Windows 的 BitLocker 功能來提供 OS 和資料磁碟的磁碟區加密。 此解決方案與 Azure Key Vault 整合，以協助控制和管理磁碟加密金鑰。

### <a name="logging-and-auditing"></a>記錄與稽核

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 提供系統、使用者活動及系統健康情況的廣泛記錄。 

- **活動記錄：**  [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。
- **診斷記錄：**  [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)是每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄、Azure 儲存體記錄、Key Vault 稽核記錄，以及應用程式閘道存取和防火牆記錄。
- **記錄封存：**Azure 活動記錄和診斷記錄可以連線到 Azure Log Analytics 進行處理、儲存及顯示在儀表板。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。

### <a name="secrets-management"></a>祕密管理

此解決方案使用 Azure Key Vault 來管理金鑰和祕密。

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 
- 此解決方案與 Azure Key Vault 整合，以管理 IaaS 虛擬機器磁碟加密金鑰和祕密。

### <a name="identity-management"></a>身分識別管理

下列技術可在 Azure 環境中提供身分識別管理功能。
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的多租用戶雲端式目錄和身分識別管理服務。
- 可以使用 Azure AD 執行對客戶部署之 Web 應用程式的驗證。 如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。  
- [Azure 角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 可以對 Azure 進行精確且專注的存取權管理。 訂用帳戶存取限於訂用帳戶系統管理員，而對資源的存取可根據使用者角色加以限制。
- 部署的 IaaS Active Directory 執行個體提供適用於部署 IaaS 虛擬機器之 OS 層級的身分識別管理。
   
### <a name="compute-resources"></a>計算資源

#### <a name="web-tier"></a>Web 層

此解決方案將 Web 層虛擬機器部署在[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中。 可用性設定組可確保虛擬機器會分散到多個各自獨立的硬體叢集中，以提高可用性。

#### <a name="database-tier"></a>資料庫層

此解決方案將資料庫層虛擬機器部署在可用性設定組中當作 [AlwaysOn 可用性群組](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。 Always On 可用性群組功能提供高可用性與災害復原功能。 

#### <a name="active-directory"></a>Active Directory

解決方案部署的所有虛擬機器都已加入網域，而且已使用 Active Directory 群組原則來強化作業系統層級的安全性與合規性設定。 Active Directory 虛擬機器部署在可用性設定組中。


#### <a name="jumpbox-bastion-host"></a>Jumpbox (防禦主機)

管理 Jumpbox (防禦主機) 提供可讓系統管理員存取部署資源的安全連線。 與 Jumpbox 虛擬機器所在之管理子網路相關聯的 NSG 只允許 TCP 連接埠 3389 上的 RDP 連線。 

### <a name="malware-protection"></a>惡意程式碼防護

適用於虛擬機器的 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) 提供即時防護功能，能幫助識別及移除病毒、間諜軟體及其他惡意軟體，具有可設定的警示，可在已知惡意或垃圾軟體嘗試在虛擬機器上安裝或執行時發出警示。

### <a name="patch-management"></a>修補程式管理

根據預設，這個藍圖自動化所部署的 Windows 虛擬機器會設定為從 Windows Update 服務接收自動更新。 這個解決方案也會部署 OMS Azure 自動化解決方案，可透過此解決方案來建立更新部署，以在需要時將修補程式部署到 Windows 伺服器。

### <a name="operations-management"></a>Operations Management

#### <a name="log-analytics"></a>Log Analytics

Log Analytics 是 [Operations Management Suite (OMS)](https://azure.microsoft.com/services/log-analytics/) 中的一個服務，可讓您收集及分析 Azure 和內部部署環境中的資源所產生的資料。

#### <a name="oms-solutions"></a>OMS 解決方案

下列 OMS 解決方案已預先安裝，作為此解決方案的一部分：
- [AD 評估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [反惡意程式碼評估](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure 自動化](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [安全性和稽核](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [SQL 評估](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [更新管理](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [代理程式健全狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Azure 活動記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [變更追蹤](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>合規性文件

### <a name="customer-responsibility-matrix"></a>客戶責任對照表

[客戶責任對照表](https://aka.ms/blueprinthighcrm) (Excel 活頁簿) 列出 FedRAMP 高標準基準要求的所有安全性措施。 對照表中針對每個措施 (或措施的子項目)，指出該措施的實作義務是屬於 Microsoft、屬於客戶，或是雙方共同承擔。 

### <a name="control-implementation-matrix"></a>措施實作對照表

[措施實作對照表](https://aka.ms/blueprintwacim) (Excel 活頁簿) 列出 FedRAMP 高標準基準要求的所有安全性措施。 對照表中針對客戶責任對照表中指定為客戶責任的每個措施 (或措施的子項目)，指出 1) 藍圖自動化是否實作該措施，以及 2) 該實作如何符合措施需求的說明。 此內容也可以在[這裡](fedramp-controls-overview.md)取得。

## <a name="deploy-the-solution"></a>部署解決方案

此 Azure Blueprint (藍圖) 解決方案包含 JSON 設定檔和 PowerShell 指令碼，它們由 Azure Resource Manager 的 API 服務處理以在 Azure 中部署資源。 詳細的部署指示可於[這裡](https://aka.ms/fedrampblueprintrepo) \(英文\) 取得。 ***注意：此解決方案是部署到 Azure Government。***

#### <a name="quickstart"></a>快速入門
1. 將[這個](https://aka.ms/fedrampblueprintrepo) GitHub 存放庫複製或下載到您的本機工作站。

2. 執行預先部署的 PowerShell 指令碼：azure-blueprint/predeploy/Orchestration_InitialSetup.ps1。

3. 按一下下方的按鈕，登入 Azure 入口網站，然後輸入必要的 ARM 範本參數，再按一下 [購買]。

    [![部署至 Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>免責聲明

- 此文件僅供參考之用。 Microsoft 對本文件中的資訊不做任何明示、暗示或成文之擔保。 這份文件係依「現狀」提供。 本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更，恕不另行通知。 閱讀這份文件的客戶須自行承擔使用風險。  
- 本文件未提供給客戶任何 Microsoft 產品或解決方案中任何智慧財產的任何法定權利。  
- 客戶可以複製並使用這份文件，供內部參考之用。  
- 本文件的某些建議可能會導致資料、網路或 Azure 計算資源使用量增加，並可能增加客戶的 Azure 授權或訂用帳戶成本。  
- 此架構的目的是作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。
- 此文件編製做為參考資料，且不應以此文件定義為客戶能符合特定合規性需求和法規的所有手段。 對於核准的客戶實作，客戶應向其組織尋求法務支援。
