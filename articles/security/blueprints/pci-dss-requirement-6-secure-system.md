---
title: "Azure 付款處理藍圖 - 安全系統需求"
description: "PCI DSS 需求 6"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 24c8d90d3fec27258165472e99ba3d36ffcba733
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 規範之環境的安全系統需求 
## <a name="pci-dss-requirement-6"></a>PCI DSS 需求 6

**開發及維護安全系統和應用程式**這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。若要了解每個需求的測試程序和指導方針相關資訊，請參閱 PCI DSS。

> [!NOTE]
> These requirements are defined by the <bpt id="p1">[</bpt>Payment Card Industry (PCI) Security Standards Council<ept id="p1">](https://www.pcisecuritystandards.org/pci_security/)</ept> as part of the <bpt id="p2">[</bpt>PCI Data Security Standard (DSS) Version 3.2<ept id="p2">](https://www.pcisecuritystandards.org/document_library?category=pcidss&amp;document=pci_dss)</ept>. Please refer to the PCI DSS for information on testing procedures and guidance for each requirement.

不懷好意的人會使用安全性弱點取得系統的存取權限。 許多這些弱點可由廠商提供的安全性修補程式修正，這些安全性修補程式必須由管理系統的實體進行安裝。 所有系統都必須具備所有適當的軟體修補程式，以保護持卡人資料免受惡意人士和惡意軟體的利用與危害。

> [!NOTE]
> 適當的軟體修補程式是指經過充分評估及測試，以確定修補程式不會與現有安全性設定有所衝突的那些修補程式。 針對內部開發的應用程式，許多弱點可以透過使用標準系統開發程序和安全的程式碼撰寫技術來避免。

## <a name="pci-dss-requirement-61"></a>PCI DSS 需求 6.1

**6.1** 使用可信賴外部來源取得安全性弱點資訊以建立識別安全性弱點的程序，並對新探索到的安全性弱點指派風險排名 (例如，「高」、「中」或「低」)。

> [!NOTE]
> 風險排名應該以業界最佳做法與潛在影響考量為基礎。 例如，排名弱點的準則可能包括 CVSS 基本分數的考量和/或依廠商的分類，和/或受影響的系統類型。 
> 
> 評估弱點與指派風險等級的方法，會根據組織的環境和風險評定策略而異。 風險排名應至少識別被視為對環境有「高風險」的所有弱點。 除了風險排名之外，如果弱點對環境造成迫切威脅、影響關鍵系統和/或在未解決的情況下可能導致潛在的危害，則可能被視為「重大」弱點。 關鍵系統的範例可能包括安全性系統、面向公眾的裝置與系統、資料庫，以及儲存、處理或傳輸持卡人資料的其他系統。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 已建立並實作程序，以掃描範圍界限中 Hypervisor 主機上的弱點。 弱點掃描會在伺服器作業系統、資料庫與網路裝置上，透過適當的弱點掃描工具執行。 弱點掃描至少每季進行。 Microsoft Azure 與獨立的技術顧問簽約，以執行 Microsoft Azure 界限的滲透測試。 紅隊活動也會定期執行，而結果會用於進行安全性改善。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用具有 WAF 的應用程式閘道並啟用 OWASP 規則集，來降低安全性弱點的風險。 如需詳細資訊，請參閱 [PCI 指導方針 - 降低安全性弱點的風險](payment-processing-blueprint.md#application-gateway)。|



## <a name="pci-dss-requirement-62"></a>PCI DSS 需求 6.2

**6.2** 透過安裝由廠商提供的適當安全性修補程式，確保已保護所有系統元件和軟體免受已知弱點威脅。 在重大安全性修補程式發行一個月內進行安裝。

> [!NOTE]
> 重大安全性修補程式應根據「需求 6.1」中定義的風險排名程序進行識別。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 必須負責確保已保護所有網路裝置和 Hypervisor OS 軟體免受已知弱點威脅，方法是安裝由廠商提供的適當安全性修補程式。 除非客戶要求不使用該服務，否則皆存在修補程式管理程序，以確保防止作業系統層級弱點並及時進行補救。 每月會針對實際執行伺服器進行掃描，以驗證修補程式合規性。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 是 PaaS 服務解決方案。 Azure 提供所有服務修補程式的維護。|



## <a name="pci-dss-requirement-63"></a>PCI DSS 需求 6.3

**6.3** 安全地開發內部與外部軟體應用程式 (包括應用程式的網頁型系統管理存取權)，如下所示：
- 符合 PCI DSS (例如，安全驗證及記錄)
- 符合業界標準和/或最佳做法
- 在整個軟體開發生命週期併入資訊安全性 

> [!NOTE]
> 這適用於所有內部開發軟體，以及由協力廠商開發的定制或自訂軟體。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 應用程式與端點是根據符合 DSS 需求的 Microsoft 安全性開發週期 (SDL) 方法進行開發。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 的設計方式是遵循可用於保護 CHD 的業界最佳做法。 部署指導方針提供安全性措施的詳細資料，而且記錄已啟用。|



### <a name="pci-dss-requirement-631"></a>PCI DSS 需求 6.3.1

**6.3.1** 在應用程式變成作用中或發行給客戶之前，移除開發、測試和/或自訂應用程式帳戶、使用者識別碼和密碼。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 針對生產環境部署之前的主要版本，會由 Azure 開發小組外部指定之「安全性顧問」執行「最終安全性檢閱 (FSR)」，以確保僅發行準備好實際執行的應用程式。 此最終檢閱的一部分是確保所有的測試帳戶和測試資料都已移除。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 提供一個已記錄且隔離的暫存服務。 每個網路層都有專用的網路安全性群組 [NSG]。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-632"></a>PCI DSS 需求 6.3.2

**6.3.2** 在發行至生產環境或客戶之前檢閱自訂程式碼，以識別任何潛在的程式碼撰寫弱點 (使用手動或自動程序)，至少包含下列：
- 程式碼變更是由原始的程式碼作者以外的人員，以及了解程式碼檢閱技巧和安全程式碼撰寫實務的人員進行檢閱。
- 程式碼檢閱可確保程式碼是根據安全編碼方針進行開發
- 發行前實作適當的修正
- 程式碼檢閱結果在發行前會由管理進行檢閱並核准 

> [!NOTE]
> 做為系統開發生命週期的一部分，此程式碼檢閱的需求適用於所有自訂程式碼 (內部與公開兩者)。 
>
> 程式碼檢閱可由知識豐富的內部人員或協力廠商進行。 面向公眾的 Web 應用程式也受到其他控制措施限制，以解決實作之後的持續威脅和弱點，如「PCI DSS 需求 6.6」所定義。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 應用程式與端點是根據 Microsoft 安全性開發週期 (SDL) 方法進行開發。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 提供一個已記錄且隔離的暫存服務。 每個網路層都有專用的網路安全性群組 [NSG]。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](payment-processing-blueprint.md#network-security-groups)。|



## <a name="pci-dss-requirement-64"></a>PCI DSS 需求 6.4

**6.4** 遵循適用於系統元件所有變更的變更控制流程與程序。 流程必須包括下列項目 (請參閱需求 6.4.1 至 6.4.6)。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft 遵循軟體開發中有關安全性考量的 NIST 指導方針，因為資訊安全性必須從系統初始就整合到 SDLC 中。 Microsoft SDL 中安全性做法的持續整合可以：<ul><li>提早識別及降低安全性弱點和錯誤設定</li><li>察覺由必要安全性控制措施所造成之潛在軟體程式碼撰寫挑戰</li><li>識別共用的安全性服務以及重複使用安全性最佳做法工具，如此可以透過經過驗證的方法與技術改善安全性狀態</li><li>強制執行 Microsoft 的完整風險管理計畫</li></ul>Microsoft Azure 已建立變更與發行管理程序，以控制重大變更的實作，包括：<ul><li>已規劃之變更的識別與文件</li><li>產品規劃期間之商務目標、優先順序和案例的識別</li><li>功能/元件設計的規格</li><li>根據預先定義準則/檢查清單評定整體風險/影響的作業整備檢閱</li><li>視情況而定，根據適用於 DEV (開發)、INT (整合測試)、STAGE (生產前) 和 PROD (生產) 環境的允入/允出準則進行的測試、授權和變更管理。 客戶必須負責自己在 Microsoft Azure 中裝載的應用程式。</li></ul> |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 示範提供一個已記錄且隔離的暫存服務。 <br /><br />每個網路層都有專用的網路安全性群組 [NSG]。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](payment-processing-blueprint.md#network-security-groups)。<br /><br />變更會使用 Operations Management Suite 加以記錄，而 Runbook 用來收集記錄檔。 [Operations Management Suite (OMS)](/azure/operations-management-suite/) 能提供廣泛的變更記錄。 可對變更進行檢閱及驗證以確保正確性。 如需更具體的指導方針，請參閱 [PCI 指導方針 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-641"></a>PCI DSS 需求 6.4.1

**6.4.1** 將開發/測試環境與實際執行環境區隔，並透過存取控制強制執行區隔。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 提供一個已記錄且隔離的暫存服務。 每個網路層都有專用的網路安全性群組 [NSG]。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-642"></a>PCI DSS 需求 6.4.2

**6.4.2** 開發/測試環境與實際執行環境之間的責任區隔

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 提供一個已記錄且隔離的暫存服務。 每個網路層都有專用的網路安全性群組 [NSG]。 如需詳細資訊，請參閱 [PCI 指導方針 - 網路安全性群組](payment-processing-blueprint.md#network-security-groups)。|



### <a name="pci-dss-requirement-643"></a>PCI DSS 需求 6.4.3

**6.4.3** 產品資料 (即時 PAN) 不會用於測試或開發。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 沒有即時主要帳戶號碼 (PAN) 資料。|



### <a name="pci-dss-requirement-644"></a>PCI DSS 需求 6.4.4

**6.4.4** 在系統變成作用中或進入實際執行之前移除測試資料和帳戶。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 沒有任何測試帳戶。|



### <a name="pci-dss-requirement-645"></a>PCI DSS 需求 6.4.5

**6.4.5** 適用於安全性修補程式和軟體修改實作的變更控制程序必須包括下列項目：
- **6.5.4.1** 影響文件。
- **6.5.4.2** 由已授權之合作對象核准的記載變更。
- **6.5.4.3** 功能測試，以驗證變更不會對系統安全性造成不良影響。
- **6.5.4.4** 退出程序。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 是 PaaS 服務解決方案。 而 Azure 提供所有服務修補程式的維護。|



### <a name="pci-dss-requirement-646"></a>PCI DSS 需求 6.4.6

**6.4.6** 重大變更完成時，必須在所有新的或已變更的系統和網路上實作所有相關的 PCI DSS 需求，並適當地更新文件。

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。


**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 是 PaaS 服務解決方案。 而 Azure 提供所有服務修補程式的維護。|



## <a name="pci-dss-requirement-65"></a>PCI DSS 需求 6.5

**6.5** 解決軟體開發程序中的常見程式碼撰寫弱點，如下所示：
- 至少每年以最新安全程式碼撰寫技術訓練開發人員，包括如何避免常見的程式碼撰寫弱點。
- 根據安全程式碼撰寫準則開發應用程式。

> [!NOTE]
> 此版本的 PCI DSS 發行時，6.5.1 到 6.5.10 中列出的弱點是目前的業界最佳做法。 不過，由於適用於弱點管理的業界最佳做法已更新 (例如，OWASP 指南、SANS CWE Top 25、CERT 安全程式碼撰寫等)，必須針對這些需求使用目前的最佳做法。 
> 
> [!NOTE]
> 下面的需求 6.5.1 到 6.5.6 適用於所有的應用程式 (內部或外部)。 下面的需求 6.5.7 到 6.5.10 適用於 Web 應用程式與應用程式介面 (內部或外部)。 

- **6.5.1** 插入缺陷，特別是 SQL 插入。 也請考慮 OS 命令插入、LDAP 與 XPath 插入缺陷，以及其他插入缺陷。
- **6.5.2** 緩衝區溢位
- **6.5.3** 不安全的密碼編譯儲存體
- **6.5.4** 不安全的通訊
- **6.5.5** 不適當的錯誤處理
- **6.5.6** 弱點識別程序中所識別之所有「高風險」弱點 (如「PCI DSS 需求 6.1」中所定義)
- **6.5.7** 跨網站指令碼處理 (XSS)
- **6.5.8** 不適當的存取控制 (例如不安全的直接物件參考、限制 URL 存取失敗、目錄周遊、限制使用者存取函式失敗)
- **6.5.9** 跨網站偽造要求 (CSRF)
- **6.5.10** 中斷驗證和工作階段管理

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 6.4](#pci-dss-requirement-6-4) 的＜Microsoft Azure＞一節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 示範提供安全開發、DFD 和威脅模型的指導方針，以說明安全開發做法。|



## <a name="pci-dss-requirement-66"></a>PCI DSS 需求 6.6

**6.6** 針對面向公眾的 Web 應用程式，透過下列任一方法，以持續不斷的方式解決新的威脅和弱點，並確保已保護這些應用程式免受已知攻擊威脅：

- 透過手動或自動應用程式弱點安全性評定工具或方法，至少每年以及在任何變更之後，檢閱面向公眾的 Web 應用程式 

   > [!NOTE]
   > 這個評定與針對需求 11.2 所執行的弱點掃描不同。 

- 安裝自動技術解決方案，以偵測並防止面向公眾的 Web 應用程式所面臨的網頁型攻擊 (例如，Web 應用程式防火牆)，以持續檢查所有流量。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 在應用程式部署到實際執行環境之前，Microsoft Azure 會在執行 SDL 程序期間測試面向公眾的 Web 應用程式。 此外，Microsoft 會定期掃描實際執行環境中的所有面向公眾的 Web 應用程式，以偵測任何可能的弱點。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 參考方案會使用具有 WAF 的應用程式閘道並啟用 OWASP 規則集，來降低安全性弱點的風險。 如需詳細資訊，請參閱 [PCI 指導方針 - 降低安全性弱點的風險](payment-processing-blueprint.md#application-gateway)。|



## <a name="pci-dss-requirement-67"></a>PCI DSS 需求 6.7

**6.7** 確保適用於開發與維護安全系統和應用程式的安全性原則和作業程序，都已記載、使用中，且所有受影響的合作對象都已了解。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 參考方案會使用具有 WAF 的應用程式閘道並啟用 OWASP 規則集，來降低安全性弱點的風險。 如需詳細資訊，請參閱 [PCI 指導方針 - 降低安全性弱點的風險](payment-processing-blueprint.md#application-gateway)。|




