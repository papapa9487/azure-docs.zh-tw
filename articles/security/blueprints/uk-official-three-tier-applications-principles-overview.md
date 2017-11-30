---
title: "Azure Blueprint Automation - 國家網路安全中心的雲端安全性準則概觀"
description: "Azure Blueprint Automation - 國家網路安全中心的雲端安全性準則概觀"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 49652fd9-b8c6-4a88-bc5e-0b58a0260ed6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: jomolesk
ms.openlocfilehash: 0be18e2c2354ea8f766eb48db793c906e565a201
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="national-cyber-security-centre-cloud-security-principles-overview"></a>國家網路安全中心的雲端安全性準則概觀


> [!NOTE]
> 這些安全性準則是由英國國家網路安全中心 (NCSC) 所定義。 如需每個安全性準則的測試程序和指引相關資訊，請參閱 [NCSC 文件](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)。



## <a name="ncsc-cloud-security-principle-1"></a>NCSC 雲端安全性準則 1
### <a name="data-in-transit-protection"></a>傳輸中的資料保護
使用者資料傳輸網路應受到充分保護，以免遭受竄改和竊聽。

這應透過以下組合來達成：

- 網路保護 - 拒絕攻擊者攔截資料的能力
- 加密 - 拒絕攻擊者讀取資料的能力


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 會將資源設定為只使用安全通訊協定進行通訊。 應用程式閘道的 WAF 元件會設定為接受外部通訊者透過 HTTPS/TLS 使用，並只能透過 HTTPS/TLS 來與後端集區通訊。 遠端桌面服務會設定為使用安全連線。 VPN 用來保護 AppGateway 與 Azure 之間的安全 Web 流量。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Azure 會依照 CESG/NCSC 的建議，使用業界標準的傳輸層安全性 (TLS) 1.2 通訊協定搭配 2048 位元的 RSA/SHA256 加密金鑰，來加密客戶與雲端之間的通訊，以及內部 Azure 系統與資料中心之間的通訊。 例如，當系統管理員使用 Microsoft Azure 入口網站來管理其組織的服務時，在入口網站與系統管理員裝置之間傳輸的資料會透過加密的 TLS 通道傳送。 當電子郵件使用者使用標準網頁瀏覽器來連線到 Outlook.com 時，HTTPS 連線會提供安全的通道來接收及傳送電子郵件。<br /> <br /> Azure 提供各種選項給客戶保護自己的資料和流量。 Azure 內建的憑證管理功能，賦予系統管理員靈活彈性，可為管理系統、個別服務、安全殼層 (SSH) 工作階段、虛擬私人網路 (VPN) 連線、遠端桌面 (RDP) 連線及其他功能設定憑證和加密金鑰。 <br /><br /> 開發人員可以使用 Microsoft .NET Framework 內建的密碼編譯服務提供者 (CSP) 來存取進階加密標準 (AES) 演算法，以及安全雜湊演算法 (SHA-2) 功能，以處理驗證數位簽章等工作。 Azure Key Vault 藉由將密碼編譯金鑰和密碼儲存在硬體安全性模組 (HSM) 中，協助客戶來保護它們。 |


 ## <a name="ncsc-cloud-security-principle-2"></a>NCSC 雲端安全性準則 2
### <a name="asset-protection-and-resilience"></a>資產保護和復原
使用者資料以及儲存或處理該資料的資產都應受到保護，以免遭受實體竄改、遺失、損壞或奪取。

請考量下列層面：

1. 實體位置和法律管轄權
2. 資料中心安全性
3. 待用資料保護
4. 資料清理
5. 設備處置
6. 實體恢復力和可用性


 ## <a name="ncsc-cloud-security-principle-21"></a>NCSC 雲端安全性準則 2.1
### <a name="physical-location-and-legal-jurisdiction"></a>實體位置和法律管轄權
若要了解未經您的同意即可存取您的資料的合法情況，您必須識別儲存、處理及管理資料的位置。
相對於英國法規，您也需要了解如何強制執行服務中的處理資料控制。 不當保護使用者資料可能會導致法律和法規制裁或聲譽受損。


**職責：**`Customer`

> [!NOTE]
> Azure 服務會依照區域部署，客戶可以將特定 Azure 服務設定為只在單一區域儲存客戶資料。 Microsoft Azure 提供一份全球可用的資料中心清單，以提供全球性的可用性和可靠性。 所有 Azure 資料中心都已通過 ISO/IEC 27001:2013 認證。 英國地區是由 2 個區域所組成：英國南部和英國西部。

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會提示系統管理員哪個區域要部署 Azure 資源。 建議的部署區域為英國南部或英國西部。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 不適用 |


 ## <a name="ncsc-cloud-security-principle-22"></a>NCSC 雲端安全性準則 2.2
### <a name="datacentre-security"></a>資料中心安全性
用來提供雲端服務的位置需要實體保護，以免遭受未經授權的存取、竄改、竊取或系統重新設定。 不適當的保護可能會導致遺失外洩、竄改或遺失。


**職責：**`Microsoft Azure`


|||
|---|---|
| **客戶** | 對於 Azure 資料中心的任何系統資源，客戶沒有實體存取權；Microsoft Azure 會實作及管理資料中心安全性保護措施。 此準則是從 Microsoft Azure 繼承而來。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 會代表客戶實作此準則。 Microsoft Azure 會在分散各地的 Microsoft 設備中執行，並與其他 Microsoft 線上服務共用空間和公用程式。 每個設備都已設計為全天候執行，並採用各種產業標準措施來協助保護作業，以免遭遇停電、實體入侵及網路中斷。 這些資料中心均符合實體安全性和可用性的業界標準 (例如 ISO 27001)。 它們是由 Microsoft 作業人員管理、監視及進行系統管理。 <br /> <br /> Azure 客戶可以確信所有 Azure 資料中心都已採行實體安全性控制，因為 Azure 在所有資料中心持有符合 ISO/IEC 27001:2013 標準的憑證。 英國地區是由 2 個區域所組成：英國南部和英國西部。 |


 ## <a name="ncsc-cloud-security-principle-23"></a>NCSC 雲端安全性準則 2.3
### <a name="data-at-rest-protection"></a>待用資料保護
若要確保具有基礎結構實體存取權但未經授權的對象無法使用資料，服務中保留的使用者資料應受到保護 (不論它保留在何種儲存媒體上)。 若為採取適當的措施，資料可能會在已捨棄、遺失或遭竊的媒體上不慎外洩。


**職責：**`Shared`

> [!NOTE]
> Microsoft Azure 提供給客戶的所有加密解決方案都能輕易與 Azure Key Vault 整合，以便輕鬆管理加密金鑰。

|||
|---|---|
| **客戶** | 使用 Azure 儲存體服務加密 (SSE) 可保護此 Azure Blueprint 解決方案所部署之所有 blob 儲存體的機密性與完整性。 SSE 會使用 256 位元 AES 加密來保護 Azure 儲存體帳戶內的待用資料。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Azure 提供各式各樣的加密功能，賦予客戶靈活彈性來選擇最符合本身需求的解決方案。 Azure Key Vault 會協助客戶以輕鬆且符合成本效益的方式，來控管雲端應用程式和服務用來加密資料的金鑰。 Azure 磁碟加密讓客戶能夠加密虛擬機器。 Azure 儲存體服務加密可以加密所有放入客戶儲存體帳戶中的資料。 |


 ## <a name="ncsc-cloud-security-principle-24"></a>NCSC 雲端安全性準則 2.4
### <a name="data-sanitisation"></a>資料清理
佈建、移轉和解除佈建資源的程序應不會導致未經授權存取使用者資料。

不當清理資料可能會導致：

- 服務提供者無限期保留使用者資料
- 重複使用資源時，服務的其他使用者可以存取使用者資料
- 使用者資料在已捨棄、遺失或遭竊的媒體上遺失或外洩。


**職責：**`Microsoft Azure`


|||
|---|---|
| **客戶** | Microsoft Azure 會提供在 Azure 中永久刪除資料的相關契約保證。 因此，此準則是從 Microsoft Azure 繼承而來。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 對於符合「FIPS-199 中等」的資料分類，Microsoft Azure 會遵循 NIST SP800-88r1 處置程序。 對於支援 NIST 的磁碟機，NIST 會提供安全清除方法 (透過硬碟韌體)。 對於無法抹除的硬碟，Microsoft 會將它們銷毀，讓資訊不可能復原 (例如，弄碎、切碎、粉碎或焚化)。 系統會依照資產類型來決定適當的處置方法， 並且保留銷毀的記錄。 所有 Microsoft Azure 服務都會利用已核准的媒體儲存體和處置管理服務。 <br />  <br /> 在服務訂用帳戶到期或終止時，客戶可以連絡 Microsoft 並告訴他們是否： <br /><br /> (1) 停用客戶的帳戶，然後刪除客戶資料；或 <br /> (2) 在客戶的訂用帳戶到期或終止之後，將儲存於線上服務的資料保留在功能受限的帳戶中至少 90 天 (「保留期」)，以便客戶擷取資料。 在保留期到期後，Microsoft 會停用客戶的帳戶並刪除所有資料。 快取或備份複本將會在保留期結束的 30 天內清除。 |


 ## <a name="ncsc-cloud-security-principle-25"></a>NCSC 雲端安全性準則 2.5
### <a name="equipment-disposal"></a>設備處置
當用來傳遞服務的設備達到其使用年限的結尾時，應該以不會危害服務或服務中所儲存使用者資料之安全性的方式來處置。


**職責：**`Microsoft Azure`


|||
|---|---|
| **客戶** | 對於 Azure 資料中心的任何系統資源，客戶沒有實體存取權；Microsoft Azure 會實作及管理設備處置程序。 此準則是從 Microsoft Azure 繼承而來。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 會代表客戶實作此準則。 在系統的生命週期結束時，Microsoft 作業人員會遵循嚴格的資料處理程序和硬體處置程序，協助確保不受信任的對象無法存取可能包含客戶資料的硬體。 對於屬於「FIPS-199 中等安全性」的資料分類，Microsoft Azure 會遵循 NIST SP800-88r1 處置程序。 對於支援 NIST 的磁碟機，NIST 會提供安全清除方法 (透過硬碟韌體)。 對於無法抹除的硬碟，Microsoft 會將它們銷毀，讓資訊不可能復原 (例如，弄碎、切碎、粉碎或焚化)。 系統會依照資產類型來決定適當的處置方法， 並且保留銷毀的記錄。 所有 Microsoft Azure 服務都會利用已核准的媒體儲存體和處置管理服務。 |


 ## <a name="ncsc-cloud-security-principle-26"></a>NCSC 雲端安全性準則 2.6
### <a name="physical-resilience-and-availability"></a>實體恢復力和可用性
服務有不同程度的恢復力，這會影響其在發生失敗、事件或攻擊時正常運作的能力。 沒有可用性保證的服務可能會無法使用，有可能會延長服務期間，但不理會對您的業務所造成的影響。


**職責：**`Shared`

> [!NOTE]
> 如果客戶藉由在另一個地理位置的資料中心啟用 Azure Site Recovery 和資料替代儲存體，並適當地設定 Microsoft Azure，Microsoft Azure 即可支援客戶部署的資源持續運作。

|||
|---|---|
| **客戶** | 客戶需負責建立替代儲存地點。 客戶控制實作聲明應陳述客戶在發生事件時的運作能力。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 在不同的地理位置 (英國南部和英國西部) 具有英國國家網路安全中心 (NCSC) 核可的資料中心，以便提供恢復力和可用性。 使用 Azure Site Recovery 服務在替代地區保留容量會是客戶的職責。 當他們設定 Azure Site Recovery 以後，Azure 會在無縫轉換至替代處理地點的過程中啟動並停止客戶的服務。 |


 ## <a name="ncsc-cloud-security-principle-3"></a>NCSC 雲端安全性準則 3
### <a name="separation-between-users"></a>使用者區隔
惡意或遭到入侵的服務使用者應該無法影響其他人的服務或資料。

影響使用者區隔的因素包括：

- 實作區隔控制的位置 - 這深受服務模型 (例如 IaaS、PaaS、SaaS) 所影響
- 與您共用服務的對象 - 這取決於部署模型 (例如公用、私人或社群雲端)
- 在區隔控制的實作中提供的保證層級

> [!NOTE]
> 在 IaaS 服務中，您應考慮計算、儲存體和網路元件所提供的區隔。 此外，以 IaaS 為基礎的 SaaS 和 PaaS 服務可能會繼承 IaaS 基礎架構的某些區隔屬性。

**職責：**`Microsoft Azure`


|||
|---|---|
| **客戶** | Microsoft Azure 會確保隔離每個使用者，以防止惡意或遭到入侵的使用者影響其他人的服務或資料。 因此，此準則是從 Microsoft Azure 繼承而來。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 由於客戶雲端伺服器是虛擬的，所以實體區隔範例不再適用。 Microsoft Azure 的設計是為了協助識別及對抗多租用戶環境中固有的風險。 在使用 Active Directory 和特別為了多租用戶服務開發之功能的 Azure 使用者中，其資料儲存和處理會以邏輯方式區隔，這麼做是為了確保另一個組織無法存取共用 Azure 資料中心儲存的使用者資料。 <br /> <br /> 基本上，任何共用雲端架構都會為每個使用者進行隔離，以防止惡意或遭到入侵的使用者影響其他人的服務或資料。 <br /> <br /> 如需 Microsoft 租用戶區隔的詳細資訊，請參閱 [Azure Blueprint - NCSC 雲端安全性準則 - 客戶職責列表](https://aka.ms/blueprintuk-gcrm)中的完整說明。 |


 ## <a name="ncsc-cloud-security-principle-4"></a>NCSC 雲端安全性準則 4
### <a name="governance-framework"></a>治理架構
服務提供者中應具有安全性治理架構，可協調及指示其對於服務與其資訊的管理。 在此架構之外部署的任何技術控制基本上都會遭到推翻。
具備有效的治理架構可確保程序、人員、實體和技術控制能在服務的存留期內繼續運作。 它也應該因應服務變更、技術發展和新威脅的出現。


**職責：**`Microsoft Azure`


|||
|---|---|
| **客戶** | Microsoft Azure 為 Azure 服務維護一份記載的安全性治理架構。 因此，此準則是從 Microsoft Azure 繼承而來。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft 合規性架構包含一套標準方法，可用於定義合規性網域、決定指定的小組或資產所適用的目標，以及詳細說明將網域控制目標套用到一組指定的業界標準、法規或業務需求時，如何達成這些目標。 此架構會將控制對應至多項法規標準，讓 Microsoft 能使用一組常用的控制來設計和建置服務，因而簡化現今各種法規的合規性並在未來隨之發展。 <br /> <br /> 此外，Microsoft 合規性程序讓客戶能更輕鬆地達成多項服務的合規性，並有效地符合其不斷變動的需求。 再者，安全性增強技術和有效的安全性程序讓 Microsoft 得以維護及擴充一組豐富的第三方認證。 這些認證可協助客戶向其客戶、稽核機構及主管機關證明合規性整備程度。 <br /> <br />  Azure 符合一組廣泛的國際性及地區性產業特定合規性標準，例如 ISO 27001、FedRAMP、SOC 1 和 SOC 2。 這些標準內含的嚴格安全性控制合規性會經由嚴格的第三方稽核進行驗證，以證明 Azure 服務努力符合世界級的業界標準、認證、證明和授權。 <br /> <br /> Azure 的合規性策略是協助客戶達成業務目標以及業界標準和法規。 安全性安全性架構包含測試和稽核階段、安全性分析、風險管理最佳做法以及安全性標竿分析，以取得憑證和證明。 <br /> <br /> 如需 Microsoft 遵循合規性架構的詳細資訊，請參閱 [Azure Blueprint - NCSC 雲端安全性準則 - 客戶職責列表](https://aka.ms/blueprintuk-gcrm)中的完整說明。|


 ## <a name="ncsc-cloud-security-principle-5"></a>NCSC 雲端安全性準則 5
### <a name="operational-security"></a>作業安全性
此服務需要安全地運作和管理，才能夠阻止、偵測或防禦攻擊。 良好的運作安全性應該不需要複雜、官僚、耗時或昂貴的程序。

有四個需要考量的元素：

- 組態和變更管理 - 您應確保系統的變更已經過適當的測試及授權。 變更不得意外地改變安全性屬性
- 弱點管理 - 您應該識別及緩和組成元件中的安全性問題
- 防護監視 - 您應該採取相關措施，以偵測攻擊和服務上未經授權的活動
- 事件管理 - 確保您可回應事件，並且復原安全、可用的服務




 ## <a name="ncsc-cloud-security-principle-51"></a>NCSC 雲端安全性準則 5.1
### <a name="configuration-and-change-management"></a>組態和變更管理
您應該精確掌握構成服務的資產，以及其組態和相依性。
應該識別及管理可能影響服務安全性的變更。 應該偵測未經授權的變更。
若未有效地管理變更，可能會在不知情的狀況下將安全性弱點引入服務。 即使在察覺這項弱點的情況下，也不可能完全加以緩和。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 構成此 Azure Blueprint 的 Azure Resource Manager 範本及隨附資源即代表所部署架構的「組態即程式碼」基準。 此解決方案會透過 GitHub 來提供，可用於組態控制。 <br /> <br /> Azure Active Directory 帳戶權限會藉由使用角色型存取控制來實作，方法是將使用者指派給可嚴格控制哪些使用者能夠檢視和控制所部署資源的角色。 實作 Active Directory 帳戶權限時，會使用角色型存取控制將使用者指派給安全性群組。 這些安全性群組可控制使用者能夠對作業系統組態執行哪些動作。 客戶可以延伸這些角色型配置來滿足任務需求。 <br /> <br /> 為了符合此準則，客戶需要進一步設定以便在生產環境中使用。 因此，這些組態必須是客戶變更管理程序的一部分。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 會每年審查和更新組態設定以及硬體、軟體和網路裝置的基準組態。 從開發和/或測試環境進入生產環境之前，變動會經過開發、測試及核准。 <br /> <br />Microsoft Azure 會利用 Microsoft Azure 軟體元件 (例如 OS、Fabric、RDFE、XStore 等) 的變更和發行程序，以及硬體和網路裝置元件的 Bootstrap 設定程序，在進入 Microsoft Azure 生產環境之前套用基準組態，如下所示。 <br /> <br /> 在部署生產服務之前，Azure 安全性與合規性小組及服務小組會在測試過程中檢閱以 Azure 為基礎之服務所需的基準組態。 |


 ## <a name="ncsc-cloud-security-principle-52"></a>NCSC 雲端安全性準則 5.2
### <a name="vulnerability-management"></a>弱點管理
服務提供者應採行管理程序來識別、分級及緩和弱點。 若未這麼做，使用公開已知方法和工具的服務，很快就會變得容易遭受攻擊。


**職責：**`Customer`


|||
|---|---|
| **客戶** | 客戶需負責在客戶部署的資源 (包括應用程式、作業系統、資料庫及軟體) 上掃描弱點。 客戶實作聲明應陳述用來執行弱點掃描的工具。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 安全性更新管理可協助系統抵禦已知的弱點。 Azure 使用整合式部署系統，管理 Microsoft 軟體安全性更新的散發與安裝。 Azure 也能夠提取 Microsoft Security Response Centre (MSRC) 的資源，以便隨時識別、監視、回應及解決安全性事件和雲端弱點。 |


 ## <a name="ncsc-cloud-security-principle-53"></a>NCSC 雲端安全性準則 5.3
### <a name="protective-monitoring"></a>防護監視
不會有效監視攻擊、誤用和故障的服務，不可能會偵測攻擊 (成功與不成功)。 因此，它將無法快速回應您的環境和資料可能遭受的危害。


**職責：**`Customer`


|||
|---|---|
| **客戶** | 客戶需負責監視客戶部署的資源 (包括應用程式、作業系統、資料庫及軟體)。 客戶控制實作聲明應陳述用來監視、偵測及回應攻擊、誤用及故障的機制。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 安全性已定義主動監視的需求。 服務小組會根據這些需求來設定主動監視工具。 主動監視工具包括 Monitoring Agent (MA) 和 System Centre Operations Manager (SCOM)，其已設定成在需要立即採取行動的情況下，對 Microsoft Azure 安全性人員提供即時警示。 |


 ## <a name="ncsc-cloud-security-principle-54"></a>NCSC 雲端安全性準則 5.4
### <a name="incident-management"></a>事件管理
除非採行仔細預先規劃的事件管理程序，否則可能在發生事件時做出不好的決策，進而可能對使用者造成不良的影響。
這些程序不需要很複雜，也不需要大量的說明，但良好的事件管理可將服務的安全性、可靠性及環境問題對於使用者的影響降到最低。


**職責：**`Shared`

> [!NOTE]
> 在客戶安全性事件可能會影響 Microsoft Azure 安全性狀態的情況下，客戶需負責通知 Microsoft Azure。

|||
|---|---|
| **客戶** | 客戶需針對客戶部署的資源 (包括應用程式、作業系統、資料庫及軟體)，負責建立事件管理程序。 客戶實作聲明應陳述如何回報事件和警示、支援即時事件回應，以及將資訊轉送給 PGA 和其他相關的 HMG 組織。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft 已實作安全性事件管理程序，以便在發生事件時協調回應。 <br /> <br /> 如果 Microsoft 察覺到任何未經授權存取其設備或設施中儲存的任何客戶資料，或未經授權存取這類設備或設施導致遺失、洩漏或改變客戶資料，Microsoft 已宣稱它會： <br /> <br />   - 立即將安全性事件通知客戶； <br /> - 立即調查安全性事件，並將詳細的安全性事件資訊提供給客戶；以及 <br /> - 採取合理和立即的步驟來降低影響，並將安全性事件所造成的損害降到最低。  <br />  <br /> 已建立事件管理架構，其中包含定義的角色和配置的職責。 Windows Azure 安全性事件管理 (WASIM) 小組負責管理安全性事件，包括必要時提升及確保專家小組的參與程度。 Azure Operations Manager 需在其他功能的支援下，負責監督安全性和隱私權事件的調查與解決。 <br /> <br /> 如需 Microsoft 事件回應程序的詳細資訊，請參閱 [Azure Blueprint - NCSC 雲端安全性準則 - 客戶職責列表](https://aka.ms/blueprintuk-gcrm)中的完整說明。 |


 ## <a name="ncsc-cloud-security-principle-6"></a>NCSC 雲端安全性準則 6
### <a name="personnel-security"></a>人員安全性
在服務提供者的人員有權存取您的資料和系統的情況下，您必須對其可信賴度抱持高度信心。 由適當訓練所支援的徹底篩選，可降低由服務提供者的人員造成意外或惡意洩露的可能性。
服務提供者應要求相關人員接受安全性篩選和定期安全性訓練。 這些角色的人員應該了解他們的職責。 提供者應該清楚說明其篩選和管理具特殊權限角色之人員的方式。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 客戶需負責篩選個人，並且為個人提供定期安全性訓練與客戶部署資源的存取權。 客戶實作聲明應陳述角色的篩選準則和安全性訓練的頻率。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 操作 Azure 服務及提供客戶支援的 Microsoft Azure 人員 (或是協助平台作業、疑難排解和技術支援的 Microsoft 轉包人員) 會經歷 Microsoft 標準背景 (或同等) 檢查來評估員工的教育、雇用及刑事歷程記錄。 背景檢查大致與英國政府的 BPSS/BS7858 需求一致。 並未特別包含正式身份檢查。  <br /> <br /> Microsoft 會在其員工和轉包人員合約中納入不得公開的條款。 所有適任的 Microsoft 員工和轉包人員都會參與 Microsoft Azure 贊助的安全性訓練方案，告知他們其對於資訊安全性的職責。 <br /> <br /> 疑似破壞安全性和/或違反資訊安全性原則的 Microsoft Azure 服務人員或轉包人員，需接受調查和懲處 (最嚴重的懲處包括終止聘雇)。 如果情況允許，Microsoft 可能會交由執法機關起訴。 <br /> <br /> 為了補充此背景檢查和安全性教育系統，Microsoft 部署各種預防性、防禦性和反應性控制，協助防範未經授權的開發人員及/或系統管理活動，包括以下機制： <br />  <br /> - 嚴格的敏感性資料存取控制，包括要求進行以智慧卡為基礎的雙因子驗證，以執行敏感性作業。 <br /> - 增強獨立偵測惡意活動的控制組合。 <br /> - 多個層級的監視、記錄及報告。 |


 ## <a name="ncsc-cloud-security-principle-7"></a>NCSC 雲端安全性準則 7
### <a name="secure-development"></a>安全開發
應為了識別及緩和安全性威脅來設計及開發服務。 不容易遭受安全性問題的服務，可能會危害您的資料、造成服務損失或讓其他惡意活動。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 所部署的虛擬機器會執行 Windows 作業系統。 Windows 會為核心系統檔案提供即時的檔案完整性驗證、保護及復原，這些檔案會透過可啟用即時完整性檢查的 Windows 資源保護 (WRP) 功能，安裝為 Windows 或已獲授權之 Windows 系統更新的一部分。 <br /> <br /> Windows 已採取適當的保護，防止在受限制的記憶體位置執行程式碼：不執行 (NX)、位址空間配置隨機載入 (ASLR) 及資料執行防止 (DEP)。 <br /> <br /> 此 Azure Blueprint 會針對使用 Microsoft Windows Defender.實作的所有已部署 Windows 虛擬機器，部署主機型反惡意程式碼防護。 Windows Defender 會設定為當版本可供使用時，自動更新反惡意程式碼引擎與保護簽章。 <br /> <br /> 為了符合此準則，客戶需要進一步設定以便在生產環境中使用。 因此，這些組態必須是客戶安全開發程序的一部分。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft 安全性開發週期 (SDL) 提供有效的威脅模型化程序來識別軟體和服務的威脅與弱點。 威脅模型化是一項小組演練，成員包含作業管理員、方案/專案管理員、開發人員和測試人員，並說明針對解決方案設計所執行的重要安全性分析工作。 小組成員可在建立服務和專案以及使用新功能進行更新時，使用 SDL 威脅模型化工具來建立所有服務和專案的模型。 威脅模型涵蓋在攻擊面暴露的所有程式碼以及由第三方撰寫和授權的所有程式碼，而且會考量所有的信任界限。 在安全性威脅影響客戶之前，STRIDE 系統 (詐騙、竄改、否認、資訊洩漏、阻絕服務及提高權限) 用於在設計過程中協助提早識別及解決這些威脅。 |


 ## <a name="ncsc-cloud-security-principle-8"></a>NCSC 雲端安全性準則 8
### <a name="supply-chain-security"></a>供應鏈安全性
服務提供者應確保其供應鏈以令人滿意的方式支援服務要求實作的所有安全性準則。
雲端服務通常會依賴第三方產品與服務。 因此，如果未實作此準則，供應鏈洩漏可能會破壞服務的安全性，並影響其他安全性準則的實作。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 客戶需針對其 Azure 訂用帳戶中使用的任何第三方購置軟體和作業系統，負責提供安全的供應鏈文件。 客戶實作聲明應陳述遵循此供應鏈文件所識別之程序的例外狀況。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft 雲端供應鏈 (MCSC) 團隊包含六個特殊小組，每個小組都會盡力保護 Azure 以免遭遇供應鏈的威脅。  <br />  <br /> - 採購 <br /> - 客戶作業 <br /> - 部署品質 <br /> - 供應商關係管理 <br /> - 備用品 <br />  <br /> 如需 Microsoft MCSC 團隊的詳細資訊，請參閱 [Azure Blueprint - NCSC 雲端安全性準則 - 客戶職責列表](https://aka.ms/blueprintuk-gcrm)中的完整說明。 |


 ## <a name="ncsc-cloud-security-principle-9"></a>NCSC 雲端安全性準則 9
### <a name="secure-user-management"></a>安全的使用者管理
您的提供者應提供工具給您使用，才能安全地管理您使用其服務。 管理介面和程序都是安全障礙的重要一環，可防止未經授權存取及變更您的資源、應用程式和資料。

請考量下列層面：

- 管理介面和支援管道的使用者驗證
- 管理介面中的區隔和存取控制



 ## <a name="ncsc-cloud-security-principle-91"></a>NCSC 雲端安全性準則 9.1
### <a name="authentication-of-users-to-management-interfaces-and-within-support-channels"></a>管理介面和支援管道的使用者驗證
為了維護安全服務，使用者必須先經過適當的驗證，才能執行管理活動、報告錯誤或要求變更服務。
這些活動可以透過服務管理 Web 入口網站，或透過其他管道 (例如電話或電子郵件) 進行。 它們可能包括佈建新的服務元素、管理使用者帳戶及管理使用者資料等功能。
服務提供者必須確保可能有安全性影響的所有管理要求是透過安全且經過驗證的管道來執行。 如果使用者未經過強式驗證，詐騙者可能會成功執行特殊權限的動作，進而破壞服務或資料的安全性。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 當系統管理員存取 Microsoft Azure 入口網站來管理其組織的 Azure 服務時，在入口網站與系統管理員裝置之間傳輸的資料會使用 2048 位元 RSA/SHA256 加密金鑰 (如 CESG/NCSC 建議)、透過加密的傳輸層安全性 (TLS) 通道傳送。  <br /> <br /> 此 Azure Blueprint 採用 Windows 驗證、遠端桌面和 BitLocker。 這些元件可以設定為依賴 FIPS 140 驗證的密碼編譯模組。 <br /> <br /> 這個 Azure Blueprint 會藉由將使用者指派至角色，使用 Azure Active Directory 所強制的角色型存取控制來強制邏輯存取授權，Active Directory 是將使用者指派給安全性群組和 Windows OS 層級控制。 指派給使用者或群組的 Azure Active Directory 角色可以資源、群組或訂用帳戶層級控制 Azure 內的資源邏輯存取。 Active Directory 安全性群組可控制 OS 層級之資源和功能的邏輯存取。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 客戶會透過 Azure 入口網站管理其 Azure 資源，該入口網站可供存取所有的虛擬機器、資料庫、雲端服務，以及針對客戶帳戶設定的其他資源。 Azure 入口網站的 Web 存取會使用 2048 位元 RSA/SHA256 加密金鑰 (如 CESG/NCSC 建議)、由符合業界標準的傳輸層安全性 (TLS) 1.2 連線保護。 客戶能夠使用角色型存取控制，為特定的使用者和群組提供 Azure 管理資源的有限權限。 |


 ## <a name="ncsc-cloud-security-principle-92"></a>NCSC 雲端安全性準則 9.2
### <a name="separation-and-access-control-within-management-interfaces"></a>管理介面中的區隔和存取控制
許多雲端服務是透過 web 應用程式或 API 來管理。 這些介面是服務安全性的重要一環。 如果使用者並未在管理介面中適當地區隔，一位使用者可能會影響服務，或修改另一個使用者的資料。
特殊權限的管理帳戶或許能夠存取大量資料。 將個別使用者的權限限制於絕對必要的權限，有助於限制惡意使用者、遭盜用的認證或遭入侵的裝置所造成的損害。
角色型存取控制會提供一個機制來達成此目的，對於管理大型部署的使用者而言，可能是特別重要的功能。
對較不容易存取的網路 (例如社群，而非公用網路) 公開管理介面，可讓攻擊者更難以觸達和進行攻擊，因為他們必須先取得其中一個這類網路的存取權。 我們會根據準則 11，提供對不同類型的網路公開介面的風險評估指引。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署應用程式閘道、負載平衡器和設定網路安全性群組規則，來控制外部界限和內部子網路之間的資訊交換。 透過強制執行邏輯存取控制和系統架構，區隔使用者功能與系統管理功能。 系統管理功能的介面不同於使用者介面。 所有管理連線都經由位於管理子網路的安裝防禦主機 (Jumpbox)，並設有網路安全性群組規則以視情況限制生產資源的存取。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 如「使用者區隔」所述，區隔已內建於 Azure 的核心。 Azure Active Directory (Azure AD 或 AAD) 可用於為每個向 Azure 入口網站進行驗證的使用者，提供僅只他們有權查看及管理之資源的存取權。 因此，透過一般 Azure 入口網站來管理不同的客戶帳戶時，這些帳戶會彼此嚴格區隔。 |


 ## <a name="ncsc-cloud-security-principle-10"></a>NCSC 雲端安全性準則 10
### <a name="identity-and-authentication"></a>身分識別和驗證
只有通過驗證且經過授權的個人才能存取服務介面。
這些介面的弱式驗證可能使您的系統遭到未經授權存取，導致您的資料遭竊或修改、您的服務變更或阻斷服務攻擊。重要的是，驗證應透過安全的通道進行。
Importantly, authentication should occur over secure channels. 電子郵件、HTTP 或電話容易遭到攔截和社交工程攻擊。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 運用 Active Directory 進行帳戶管理。 此 Azure Blueprint 所部署資源的存取權受 Azure Active Directory、Active Directory 及 Windows 作業系統的內建 Kerberos 功能保護，能防範重新執行攻擊。 在 Kerberos 驗證中，用戶端傳送的驗證器包含其他資料，例如加密的 IP 清單、用戶端時間戳記以及票證存留期。 如果重新執行封包，就會檢查時間戳記。 如果時間戳記早於或和先前的驗證器相同，該封包會遭到拒絕。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Azure 提供可協助追蹤身分識別，以及將它與使用中的身分識別存放區進行整合的服務。 Azure AD 是一項適用於雲端的完整身分識別和存取管理服務，可協助保護內部部署和雲端應用程式中的資料存取。 Azure AD 也會合併核心目錄服務、進階身分識別管理、安全性及應用程式存取管理，以簡化使用者和群組的管理。 |


 ## <a name="ncsc-cloud-security-principle-11"></a>NCSC 雲端安全性準則 11
### <a name="external-interface-protection"></a>外部介面保護
應識別服務的所有外部或信任度愈低的介面，並加以適當防禦。
如果有些暴露的介面是私密的 (例如管理介面)，則洩露的影響可能更重大。
您可以使用不同模式來連線到讓您的企業系統暴露於各級風險的雲端服務。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 使用個別的 Web 子網路、資料庫子網路、Active Directory 子網路及管理子網路來部署架構中的資源。 套用至個別子網路的網路安全性群組規則以邏輯方式區隔了子網路，將子網路之間的流量限制為系統和管理功能所需流量 (例如，外部流量無法存取資料庫、管理或 Active Directory 子網路)。  <br /> <br /> 部署應用程式閘道，以管理客戶所部署 Web 應用程式的外部連線。 管理存取的外部連線僅限於部署在管理子網路中的 Jumpbox (防禦主機)，並套用網路安全性規則以限制只有經授權的 IP 位址可進行外部連線。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft 會運用一種名為「紅隊作業」的方法，透過滲透測試來改善 Azure 安全性控管和流程。 「紅隊」是 Microsoft 內的一群全職人員，負責對 Microsoft 基礎結構、平台和應用程式 (但非一般客戶的應用程式或資料) 執行目標式和持續性攻擊。 <br /> <br /> 如需 Microsoft 紅隊作業的詳細資訊以及藍隊作業的說明，請參閱 [Azure Blueprint - NCSC 雲端安全性準則 - 客戶職責列表](https://aka.ms/blueprintuk-gcrm)中的完整說明。  |


 ## <a name="ncsc-cloud-security-principle-12"></a>NCSC 雲端安全性準則 12
### <a name="secure-service-administration"></a>安全的服務管理
用於雲端服務管理的系統將會有該服務的高度存取權限。 其洩露會有重大影響，包括規避安全性控制及竊取或操控大量資料。
在認可管理系統對於攻擊者有很高價值的同時，管理系統的設計、實作和管理應遵循企業的良好做法。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 客戶負責確保有安全的工作站可管理其 Azure訂用帳戶和客戶部署的資源 (包含應用程式、作業系統、資料庫及軟體)。 客戶實作聲明應陳述用來降低客戶部署的資源遭受攻擊之風險的機制。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Microsoft Azure 作業人員必須使用安全的管理工作站 (SAW；也稱為特殊權限的存取工作站，或 PAW)。 SAW 方法是完善建議作法的延伸，可使用個別的系統管理員和使用者帳戶做為系統管理人員。 這種作法會使用個別指派的系統管理帳戶，該帳戶與使用者的標準使用者帳戶完全區隔。 SAW 會為敏感性帳戶提供可靠的工作站，並以該帳戶分隔作法為基礎。 |


 ## <a name="ncsc-cloud-security-principle-13"></a>NCSC 雲端安全性準則 13
### <a name="audit-information-for-users"></a>稽核使用者的資訊
您應會取得監視您的服務和其內含資料所需的稽核記錄。 您可使用的稽核資訊類型會直接影響您在合理的時間範圍內，偵測及回應不當或惡意活動的能力。


**職責：**`Shared`


|||
|---|---|
| **客戶** | 此 Azure Blueprint 所稽核的事件包括由 Azure 活動記錄，針對已部署的資源、OS 層級記錄及 Active Directory 記錄進行稽核的事件。 這些事件記錄包括足以判斷事件發生時機、事件來源、事件結果的相關資訊，以及支援調查安全性事件的其他詳細資訊。 客戶可以選取要稽核以符合關鍵需求的其他事件。 在 Azure 入口網站中，可取得所有 Azure 資源的稽核記錄。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | Azure Log Analytics 會在組織的系統和網路中發生事件時立即收集其記錄，防止被任何人竄改，並且讓多部電腦之間的資料相互關聯，以便執行不同類型的分析。 Azure 可讓客戶執行安全性事件產生和集合，從 Azure IaaS 和 PaaS 角色至其訂用帳戶中的中央儲存體。 這些收集的事件可以匯出到進行中監視的內部部署安全性資訊和事件管理 (SIEM) 系統。 資料傳輸至儲存體之後，有許多選項可檢視診斷資料。 <br /> <br /> Azure 內建的診斷有助於偵錯。 對於 Azure 中部署的應用程式，預設會啟用一組作業系統安全性事件。 客戶可以新增、移除或修改可藉由自訂作業系統稽核原則加以稽核的事件。 <br /> <br /> 概括而言，在 Azure 中使用 IaaS 部署以 Windows 為基礎的 VM 時，使用 Windows 事件轉送 (WEF) 或更進階的 Azure 診斷來開始收集記錄相當容易而簡單。 此外，可將 Azure 診斷設定為從 PaaS 角色執行個體收集記錄和事件。 使用以 IaaS 為基礎的 VM 時，客戶只需以啟用 Windows Server 的相同方式來設定和啟用所需的安全性事件，即可在其內部部署的資料中心記錄稽核。 如果 Web 應用程式是主要應用程式且部署在 Azure 中，也可以對其啟用 IIS 記錄功能。 客戶一律可在位於所選支援地理位置的儲存體帳戶中儲存安全性資料，以符合資料自主性需求。 |


 ## <a name="ncsc-cloud-security-principle-14"></a>NCSC 雲端安全性準則 14
### <a name="secure-use-of-the-service"></a>安全使用服務
如果您不當使用服務，則雲端服務的安全性及其內含的資料可能會遭到破壞。 因此，您在使用服務時有一些職責，讓您的資料受到適當的保護。
您的職責範圍會根據雲端服務的部署模型，以及您打算使用服務的情況而有所不同。 個別服務的特定功能可能也有關係。 例如，除了雲端安全性準則所涵蓋的一般考量以外，內容傳遞網路保護私密金鑰的方式，或雲端付款提供者偵測詐騙交易的方式都是重要的安全性考量。  
採用 IaaS 和 PaaS 供應項目，您需負責資料和工作負載安全性的重要層面。 例如，如果您購買 IaaS 計算執行個體，您通常需負責安裝新式作業系統、安全地設定該作業系統、安全地部署任何應用程式，以及透過套用修補程式或執行必要維護來維護該執行個體。


**職責：**`Customer`

> [!NOTE]
> 客戶可以使用 Azure 資訊安全中心，藉由提升的 Azure 資源可見度及安全性管控，協助預防、偵測及回應威脅。 Azure 資訊安全中心能提供 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助偵測原先可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

|||
|---|---|
| **客戶** | 構成此 Azure Blueprint 的 Azure Resource Manager 範本及隨附的資源，都會遵循深度防禦安全性方法。 為了符合此準則，客戶需要進一步設定以便在生產環境中使用 (例如資料庫管理軟體、Web 應用程式部署)。 |
| **提供者&nbsp;(Microsoft&nbsp;Azure)** | 不適用 |

## <a name="disclaimer"></a>免責聲明

 - 此文件僅供參考之用。 Microsoft 對本文件中的資訊不做任何明示、暗示或成文之擔保。 這份文件係依「現狀」提供。 本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更，恕不另行通知。 讀取這份文件的客戶須自行承擔使用風險。
 - 本文件未提供給客戶任何 Microsoft 產品或解決方案中任何智慧財產的任何法定權利。
 - 客戶可以複製並使用這份文件，供內部參考之用。
 - 本文件的某些建議可能會導致資料、網路或 Azure 計算資源使用量增加，並可能增加客戶的 Azure 授權或訂用帳戶成本。
 - 此架構的目的是作為基礎，讓顧客進行調整以符合其特定需求，不應將未修改的原始架構用於生產環境中。
 - 此文件編制做為參考資料，且不應以此文件定義為客戶能符合特定法務遵循需求和法規的所有手段。 對於核准的客戶實作，客戶應向其組織尋求法務支援。