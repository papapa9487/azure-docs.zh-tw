---
title: "Azure 付款處理藍圖 - 測試需求"
description: "PCI DSS 需求 11"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的測試需求 
## <a name="pci-dss-requirement-11"></a>PCI DSS 需求 11

**定期測試安全性系統和流程**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

惡意人士和研究人員會不斷找出弱點，並藉以引入新軟體。 應時常測試系統元件、流程和自訂軟體，以確保安全性控制會繼續反映變動的環境。

## <a name="pci-dss-requirement-111"></a>PCI DSS 需求 11.1

**11.1** 實作流程來測試無線存取點 (802.11) 是否存在，並每季偵測及識別所有已授權和未經授權的無線存取點。

> [!NOTE]
> 流程中可能使用的方法包括 (但不限於) 無線網路掃描、系統元件和基礎結構的實際/邏輯檢查、網路存取控制 (NAC) 或無線 IDS/IPS。
無論使用何種方法，這些方法必須足夠用來偵測及識別授權和未經授權的裝置。


**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 不允許在 Azure 的網路環境中使用無線連線。 內部安全性小組會每季定期掃描 Rogue 無線訊號，並調查和移除 Rogue 訊號。 客戶無法在 Azure 環境中部署無線網路技術。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 解決方案中不會實作無線及 SNMP。|



### <a name="pci-dss-requirement-1111"></a>PCI DSS 需求 11.1.1

**11.1.1** 維護授權的無線存取點庫存，包括已記錄的業務正當理由。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 11.1](#pci-dss-requirement-11-1) 的「Microsoft Azure」區段。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 解決方案中不會實作無線及 SNMP。|



### <a name="pci-dss-requirement-1112"></a>PCI DSS 需求 11.1.2

**11.1.2** 在偵測到未授權無線存取點的事件中，實作事件回應程序。


**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 11.1](#pci-dss-requirement-11-1) 的「Microsoft Azure」區段。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 解決方案中不會實作無線及 SNMP。|



## <a name="pci-dss-requirement-112"></a>PCI DSS 需求 11.2

**11.2** 至少每季和當網路中發生重大變更 (例如新系統元件安裝、網路拓撲變更、防火牆規則修改或產品升級) 後，執行內部和外部網路弱點掃描。 

> [!NOTE]
> 多個掃描報告可結合至每季的掃描流程，以說明所有系統皆已掃描，且所有適用弱點皆已解決。 若要驗證正在設法解決且無法補救的弱點，則可能需要其他文件。
> 針對取得初始 PCI DSS 合規性，如果評估者已確認下列項目，則您不用完成四個季度的通過性掃描：1) 最近的掃描結果是通過的掃描、2) 實體已記錄需要每季掃描的原則和程序，以及 3) 掃描結果中註明的弱點已在重新掃描結果中顯示為已修正。 完成初始 PCI DSS 檢閱之後的每年，皆必須執行四個季度的通過性掃描。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 每季會執行內部和外部弱點掃描。 僅限合格人員可執行掃描。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 本身已經過滲透測試 (pen test) 及弱點掃描。 滲透測試結果可以透過使用常見工具 (如 nmap 或 pentest tools.com) 重複進行。滲透測試的結果將提供不明確的易受攻擊面，並且沒有可利用的項目。 此外，[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供弱點資訊與補救方法。|



### <a name="pci-dss-requirement-1121"></a>PCI DSS 需求 11.2.1

**11.2.1** 每季執行內部弱點掃描。 解決弱點並執行重新掃描，以確認所有實體弱點排名 (參考需求 6.1) 中的「高風險」弱點都已解決。 僅限合格人員可執行掃描。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會在基礎結構的範圍中執行弱點掃描。 Microsoft Azure 會在伺服器作業系統、資料庫，以及網路裝置上，透過適當的弱點掃描工具實作弱點掃描。 Azure Web 應用程式會藉由適當的業界掃描解決方案進行掃描。 弱點掃描會每季進行。<br /><br />視需要對所有系統進行重新掃描，直到所有「高風險」弱點 (如同需求 6.1 中所述) 都已解決。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 本身已經過滲透測試 (pen test) 及弱點掃描。 滲透測試結果可以透過使用常見工具 (如 nmap 或 pentest tools.com) 重複進行。滲透測試的結果將提供不明確的易受攻擊面，並且沒有可利用的項目。 此外，[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供弱點資訊與補救方法。|



### <a name="pci-dss-requirement-1122"></a>PCI DSS 需求 11.2.2

**11.2.2** 透過支付卡產業安全標準委員會 (PCI SSC) 核准的核准掃描廠商 (ASV)，每季執行外部弱點掃描。 視需要執行重新掃描，直到掃描成功通過。 

> [!NOTE]
> 必須由支付卡產業安全標準委員會 (PCI SSC) 核准的核准掃描廠商 (ASV)，每季執行外部弱點掃描。
> 請參閱 PCI SSC 網站上發行的 ASV 計畫指南，了解掃描的客戶責任和掃描準備等。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會在可從外部存取的基礎結構範圍中執行外部弱點掃描。 掃描皆由核准的掃描廠商 (ASV) 執行。<br /><br />Microsoft Azure 已訂閱 MSRC/OSSC 的每月修補程式通知，並且至少每季進行弱點掃描。 識別的弱點會根據風險層級上已建立的時間軸進行評估和補救。<br /><br />為識別安全性風險，每一季皆會針對 Microsoft Azure 中具有優先順序的元件進行全面安全性弱點掃描。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 部署 Contoso Webstore 時，此範例中的客戶必須針對持卡人資料環境 (CDE) 中的所有 PaaS 執行個體，每季執行外部弱點掃描，並且需透過支付卡產業安全標準委員會核准的核准掃描廠商 (ASV) 進行。<br /><br />|



### <a name="pci-dss-requirement-1123"></a>PCI DSS 需求 11.2.3

**11.2.3** 在任何重大變更之後，執行內部和外部掃描，以及視需要進行重新掃描。 僅限合格人員可執行掃描。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 結果會回報給專案關係人，而 Azure 安全性小組會追蹤補救措施，直到問題關閉。 Azure 的測試結果可與 NDA 下的客戶共用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須針對其 CDE 中的所有 PaaS 執行個體，每季執行內部和外部弱點掃描和重新掃描 (如有需要)。 在環境範圍內發生重大變更之後，應進行掃描。<br /><br />掃描必須由 ASV 或獨立組織的人員執行。|



## <a name="pci-dss-requirement-113"></a>PCI DSS 需求 11.3

**11.3** 實作包含下列項目的滲透測試方法：
- 以產業接受的滲透測試方法為依據 (例如，NIST SP800-115)
- 包含整個 CDE 周邊和重要系統的範圍
- 包含網路內部和外部的測試
- 包含驗證任何分割和減少範圍控制項的測試
- 定義應用程式層的滲透測試，以至少包含需求 6.5 中所列的弱點
- 定義網路層的滲透測試，以包含支援網路功能和作業系統的元件
- 包含前 12 個月內發生的威脅與弱點檢閱與考量
- 指定保留滲透測試結果和補救活動結果

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會使用第三方滲透測試來驗證服務，此測試會以 OWASP (開放 Web 應用程式安全性專案) 的前十名為基礎，並使用 CREST 認證合格的測試人員。 測試結果會透過風險登錄受到追蹤，並且會定期稽核和檢閱以確保與安全作法一致。 <br /><br />Microsoft 也會對 Microsoft 所管理的基礎結構、服務和應用程式使用 Red Teaming。 在 Red Teaming 和即時網站滲透測試期間，不會故意以一般客戶資料為目標。 測試是針對 Microsoft Azure 基礎結構與平台，以及 Microsoft 自己的應用程式和資料。 裝載於 Azure 的客戶租用戶、應用程式和資料決不會成為目標。<br /><br />Microsoft Azure 採用獨立的評估工具來開發系統評估計劃並實施控制項評估。 控制項評估會每年執行，且會將結果回報給相關合作對象。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 本身已經過滲透測試 (pen test) 及弱點掃描。 滲透測試結果可以透過使用常見工具 (如 nmap 或 pentest tools.com) 重複進行。滲透測試的結果將提供不明確的易受攻擊面，並且沒有可利用的項目。 此外，[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供弱點資訊與補救方法。|



### <a name="pci-dss-requirement-1131"></a>PCI DSS 需求 11.3.1

**11.3.1** 至少每年以及在發生任何重大基礎結構或應用程式更新或修改後 (例如作業系統升級，將子網路新增至環境中或將網頁伺服器新增至環境中時)，執行「外部」滲透測試。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 11.3](#pci-dss-requirement-11-3) 的「Microsoft Azure」區段。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 本身已經過滲透測試 (pen test) 及弱點掃描。 滲透測試結果可以透過使用常見工具 (如 nmap 或 pentest tools.com) 重複進行。滲透測試的結果將提供不明確的易受攻擊面，並且沒有可利用的項目。 此外，[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供弱點資訊與補救方法。|



### <a name="pci-dss-requirement-1132"></a>PCI DSS 需求 11.3.2

**11.3.2** 至少每年以及在發生任何重大基礎結構或應用程式更新或修改後 (例如作業系統升級，將子網路新增至環境中或將網頁伺服器新增至環境中時)，執行「內部」滲透測試。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 與獨立技術顧問簽約，以執行 Microsoft Azure 界限的滲透測試。 Red Team 的活動也會定期執行，而結果會用於進行安全性改善。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 本身已經過滲透測試 (pen test) 及弱點掃描。 滲透測試結果可以透過使用常見工具 (如 nmap 或 pentest tools.com) 重複進行。滲透測試的結果將提供不明確的易受攻擊面，並且沒有可利用的項目。 此外，[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供弱點資訊與補救方法。|



### <a name="pci-dss-requirement-1133"></a>PCI DSS 需求 11.3.3

**11.3.3** 針對滲透測試期間發現的可利用弱點進行修正，並重複測試以確認修正已完成。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 針對已知的安全性弱點，已建立程序來監視 Microsoft Azure 平台元件。 <br /><br /><br /><br />為識別安全性風險，每一季皆會針對 Azure 生產環境中具有優先順序的元件進行全面安全性弱點掃描。 結果會回報給專案關係人，而小組會追蹤補救措施，直到問題關閉。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 提供弱點資訊及補救方法的 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations)，已用來確保 Contoso Webstore 示範 CDE 所有未處理的問題已補救。|



### <a name="pci-dss-requirement-1134"></a>PCI DSS 需求 11.3.4

**11.3.4** 如果使用分割來將 CDE 與其他網路隔離，則至少每年及在變更分割控制項/方法後，執行滲透測試，以驗證分割方法可正常運作而且能發揮效用，並將範圍外的所有系統與 CDE 中的系統隔離。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 針對已知的安全性弱點，已建立程序來監視 Microsoft Azure 平台元件。 <br /><br /><br /><br />為識別安全性風險，每一季皆會針對 Azure 生產環境中具有優先順序的元件進行全面安全性弱點掃描。 結果會回報給專案關係人，而小組會追蹤補救措施，直到問題關閉。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 提供弱點資訊及補救方法的 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations)，已用來確保 Contoso Webstore 示範 CDE 所有未處理的問題已補救。|



### <a name="pci-dss-requirement-11341"></a>PCI DSS 需求 11.3.4.1

**11.3.4.1** 僅適用於服務提供者的其他需求：如果使用分割，請至少每六個月及變更任何分割控制項/方法後，透過在分割控制項上執行滲透測試來確認 PCI DSS 範圍。

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前是屬於最佳作法，該日期之後會變成需求。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 11.3.4](#pci-dss-requirement-11-3-4) 的「Microsoft Azure」區段。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 提供弱點資訊及補救方法的 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations)，已用來確保 Contoso Webstore 示範 CDE 所有未處理的問題已補救。|



## <a name="pci-dss-requirement-114"></a>PCI DSS 需求 11.4

**11.4** 使用入侵偵測和/或入侵防護技術來偵測及/或防止網路入侵。 監視持卡人資料環境周邊及持卡人資料環境中重點位置的所有流量，並針對可疑的危害行為向人員發出警示。
使所有入侵偵測和防護引擎、基準及簽章維持在最新狀態。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會在作業環境中進行事件的即時分析，而系統會產生幾近即時的事件警示，告知可能有危害系統的事件。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 是 PaaS 服務，而且網路入侵偵測與防護屬於 Azure 的責任。 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 會提供入侵警示與補救方法。|



## <a name="pci-dss-requirement-115"></a>PCI DSS 需求 11.5

**11.5** 部署變更偵測機制 (例如，檔案完整性監視工具)，以在重要系統檔案、設定檔案或內容檔案發生未經授權的修改時，向人員發出警示；並將軟體設計為至少每週執行重要檔案的比較。 

> [!NOTE]
> 基於變更偵測的目的，重要的檔案通常不會定期變更，但修改該檔案可能會使系統受到危害，或增加發生危害的風險。 變更偵測機制 (例如檔案完整性監視產品) 通常會隨附針對相關作業系統預先設定的重要檔案。 其他重要檔案，例如用於自訂應用程式的檔案，則必須由實體 (也就是商家或服務提供者) 評估和定義。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 對於可能發生的變更和事件，Microsoft Azure 會進行維修並通知客戶，這些事項可能會影響整個線上服務儀表板的服務安全性或可用性。 當 Microsoft Azure 客戶的安全性承諾和安全性責任有所變更時，會及時更新在 Microsoft Azure 的網站上。<br /><br />僅限獲得授權的系統管理員可安裝或變更 Microsoft Azure 生產環境上的軟體，且須遵循變更管理程序。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 範例是種 PaaS 服務，並已使用 OMS 實作變更偵測。 如需詳細資訊，請參閱 [PCI 指引 - 預先安裝的 OMS 解決方案](payment-processing-blueprint.md#oms-solutions)。<br /><br />|



### <a name="pci-dss-requirement-1151"></a>PCI DSS 需求 11.5.1

**11.5.1** 實作流程以回應任何變更偵測解決方案所產生的警示。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 監視事件規則會提供層級提升的高風險作業和資產監視。 受 Azure 管理的網路裝置會受到監視，以確保其符合建立的安全性標準。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 變更 Contoso Webstore 的警示會由 OMS 實作提供。 如需詳細資訊，請參閱 [PCI 指引 - 預先安裝的 OMS 解決方案](payment-processing-blueprint.md#oms-solutions)。<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>PCI DSS 需求 11.6

**11.6** 確定安全性監視和測試的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 變更 Contoso Webstore 的警示會由 OMS 實作提供。 如需詳細資訊，請參閱 [PCI 指引 - 預先安裝的 OMS 解決方案](payment-processing-blueprint.md#oms-solutions)。<br /><br /><br /><br />|




