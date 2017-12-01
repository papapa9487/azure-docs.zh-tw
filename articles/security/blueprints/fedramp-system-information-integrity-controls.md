---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 系統與資訊完整性"
description: "適用於 FedRAMP 的 Web 應用程式 - 系統與資訊完整性"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>系統與資訊完整性 (SI)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-si-1"></a>NIST 800-53 控制措施 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>系統與資訊完整性原則和程序

**SI-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、責任、管理承諾、組織實體間之協調合作及合規性的系統與資訊完整性原則，以及可協助實作系統與資訊完整性原則和相關系統與資訊完整性控制措施的程序；並檢閱和更新目前的系統與資訊完整性原則 (依 [指派：組織定義的頻率]) 及系統與資訊完整性程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統與資訊完整性原則和程序可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-2a"></a>NIST 800-53 控制措施 SI-2.a

#### <a name="flaw-remediation"></a>瑕疵補救

**SI-2.a** 組織會識別、報告及更正資訊系統的缺陷。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 OMS 自動化與控制解決方案，以追蹤此架構中所部署之 Windows 虛擬機器的更新狀態。 在 OMS 儀表板上，[更新管理] 磚會顯示所有已部署 Windows 伺服器的瑕疵補救狀態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-2b"></a>NIST 800-53 控制措施 SI-2.b

#### <a name="flaw-remediation"></a>瑕疵補救

**SI-2.b** 組織會針對安裝之前的有效性和潛在副作用，測試與瑕疵補救相關的軟體和韌體更新。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的資源上，針對安裝之前的有效性和潛在副作用，測試與瑕疵補救相關的更新。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-2c"></a>NIST 800-53 控制措施 SI-2.c

#### <a name="flaw-remediation"></a>瑕疵補救

**SI-2.c** 組織會在更新發行的 [指派：組織定義的時間週期] 內，安裝安全性相關的軟體和韌體更新。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 根據預設，這個 Azure Blueprint (藍圖) 所部署的 Windows 虛擬機器會設定為從 Windows Update 服務接收自動更新。 這個解決方案也會部署 OMS 自動化與控制解決方案，可透過此解決方案來建立更新部署，以便在需要時將修補程式部署到 Windows 伺服器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-2d"></a>NIST 800-53 控制措施 SI-2.d

#### <a name="flaw-remediation"></a>瑕疵補救

**SI-2.d** 組織會將瑕疵補救併入組織設定管理程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責將瑕疵補救納入設定管理。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 控制措施 SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>瑕疵補救 | 集中管理

**SI-2 (1)** 組織會集中管理瑕疵補救程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 OMS 自動化與控制解決方案，以追蹤此架構中所部署之 Windows 虛擬機器的更新狀態。 在 OMS 儀表板上，[更新管理] 磚會顯示所有已部署 Windows 伺服器的瑕疵補救狀態。 更新部署可以建立來在需要時將修補程式部署到 Windows 伺服器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 控制措施 SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>瑕疵補救 | 自動化的瑕疵補救狀態

**SI-2 (2)** 組織會依 [指派：組織定義的頻率] 來採用自動化機制，以判斷關於瑕疵補救之資訊系統元件的狀態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 OMS 自動化與控制解決方案，以追蹤此架構中所部署之 Windows 虛擬機器的更新狀態。 對於每部受管理的 Windows 電腦，每天會掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否變更，若是如此，則會起始合規性掃描。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 控制措施 SI-2 (3).a

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>瑕疵補救 | 針對更正動作補救缺陷 / 效能評定的時機

**SI-2 (3).a** 組織會測量瑕疵識別與瑕疵補救之間的時間。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責補救客戶部署資源內的瑕疵。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 控制措施 SI-2 (3).b

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>瑕疵補救 | 針對更正動作補救缺陷 / 效能評定的時機

**SI-2 (3).b** 組織會建立用以採取修正動作的 [指派：組織定義的效能評定]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會依賴企業層級適用於瑕疵補救程序的效能評定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-3a"></a>NIST 800-53 控制措施 SI-3.a

#### <a name="malicious-code-protection"></a>惡意程式碼防護

**SI-3.a** 組織會在系統進入和結束點採用惡意程式碼防護機制，以偵測並杜絕惡意程式碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-3b"></a>NIST 800-53 控制措施 SI-3.b

#### <a name="malicious-code-protection"></a>惡意程式碼防護

**SI-3.b** 根據組織設定管理原則和程序，每當有新版本可供使用時，組織就會更新惡意程式碼防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 這個擴充功能會設定為當版本變成可供使用時，自動更新反惡意程式碼軟體引擎與保護簽章。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-3c"></a>NIST 800-53 控制措施 SI-3.c

#### <a name="malicious-code-protection"></a>惡意程式碼防護

**SI-3.c** 組織會設定惡意程式碼防護機制，依 [指派：組織定義的頻率] 執行資訊系統的定期掃描，並在根據組織安全性原則下載、開啟或執行檔案時，於 [選取 (一或多個)；端點；網路進入/結束點] 即時掃描來自外部來源的檔案，並 [選取 (一或多個)：封鎖惡意程式碼；隔離惡意程式碼；傳送警示給系統管理員；[指派：組織定義的動作]] 以回應惡意程式碼偵測。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 這個擴充功能會設定來執行即時和定期掃描 (每週)、自動更新反惡意程式碼軟體引擎和保護簽章，以及執行自動補救動作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-3d"></a>NIST 800-53 控制措施 SI-3.d

#### <a name="malicious-code-protection"></a>惡意程式碼防護

**SI-3.d** 組織會在惡意程式碼偵測和根除期間處理誤判的回條，以及對於資訊系統可用性所產生的潛在影響。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責提供保護以防範惡意程式碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 控制措施 SI-3 (1)

#### <a name="malicious-code-protection--central-management"></a>惡意程式碼防護 | 集中管理

**SI-3 (1)** 組織會集中管理惡意程式碼防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 Azure OMS 提供一項集中功能，來檢閱反惡意程式碼軟體解決方案的目前狀態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 控制措施 SI-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>惡意程式碼防護 | 自動更新

**SI-3 (2)** 資訊系統會自動更新惡意程式碼防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 這個擴充功能會設定為當版本變成可供使用時，自動更新反惡意程式碼軟體引擎與保護簽章。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 控制措施 SI-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>惡意程式碼防護 | 非簽章型偵測

**SI-3 (7)** 資訊系統會實作非簽章型惡意程式碼偵測機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會針對所有已部署的 Windows 虛擬機器部署主機型反惡意程式碼軟體防護，其會使用 Microsoft Antimalware 虛擬機器擴充功能來實作。 這個擴充功能會設定來執行啟發式偵測。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4a"></a>NIST 800-53 控制措施 SI-4.a

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.a** 組織會監視資訊系統，根據 [指派：組織定義的監視目標] 偵測攻擊和潛在攻擊的跡象，以及未經授權的本機、網路和遠端連線。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Log Analytics 及 OMS 安全性與稽核解決方案。 這個解決方案會提供安全性狀態、攻擊及潛在攻擊跡象的全面性檢視。 [安全性與稽核] 儀表板會使用所有已部署 OMS 解決方案提供的資料，以提供已部署資源之安全性狀態的概要深入解析。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4b"></a>NIST 800-53 控制措施 SI-4.b

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.b** 組織會透過 [指派：組織定義的技術和方法] 來識別未經授權使用資訊系統。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 OMS 安全性與稽核解決方案。 身分識別和存取網域提供一個儀表板及資訊系統識別狀態的概觀，包括嘗試登入失敗次數和目前已登入的帳戶數目。 此儀表板中可用的資訊可協助識別潛在的可疑活動。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4c"></a>NIST 800-53 控制措施 SI-4.c

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.c** 組織會在資訊系統中策略性地部署監視裝置，以收集組織所判定的基本資訊，並在系統內的特定位置追蹤組織感興趣的特定類型交易。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Log Analytics 及 OMS 安全性與稽核解決方案。 [安全性與稽核] 儀表板會使用可跨已部署 OMS 解決方案取得的資料，來提供對於已部署資源之安全性狀態的深入了解，包括深入探索 VM 作業系統監視資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4d"></a>NIST 800-53 控制措施 SI-4.d

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.d** 組織會保護取自入侵監視工具的資訊，以防止未經授權的存取、修改和刪除。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 在這個 Azure Blueprint (藍圖) 內使用邏輯存取控制項來保護監視資訊，以防止未經授權的存取、修改和刪除。 Azure Active Directory 會使用角色型群組成員資格，強制執行已核准的邏輯存取。 只限需要那些權限的使用者，才能檢視監視資訊和使用監視工具。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4e"></a>NIST 800-53 控制措施 SI-4.e

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.e** 每當對於組織運作和資產、個人、其他組織或國家出現提高風險的跡象時，組織就會根據執法資訊、智慧資訊或其他可靠來源的資訊，提高資訊系統監視活動的層級。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4f"></a>NIST 800-53 控制措施 SI-4.f

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.f** 組織會根據適用的聯邦法律、行政命令、指示、原則或法規，取得關於資訊系統監視活動的法律意見。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-4g"></a>NIST 800-53 控制措施 SI-4.g

#### <a name="information-system-monitoring"></a>資訊系統監視

**SI-4.g** 組織會依 [選取 (一或多個)：視需要；[指派：組織定義的頻率]]，將 [指派：組織定義的資訊系統監視資訊] 提供給 [指派：組織定義的人員或角色]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 控制措施 SI-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>資訊系統監視 | 全系統的入侵偵測系統

**SI-4 (1)** 組織會將個別的入侵偵測工具連線到資訊全系統入侵偵測系統並加以設定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 控制措施 SI-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>資訊系統監視 | 進行即時分析的自動化工具

**SI-4 (2)** 組織會採用自動化工具，以支援接近即時的事件分析。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Log Analytics 及各種 OMS 解決方案，包括安全性與稽核解決方案。 Log Analytics 會跨已部署的資源提供接近即時的事件分析。 OMS 解決方案可跨解決方案網域，提供安全性狀態的全面性檢視。 OMS 會使用可跨已部署 OMS 解決方案取得的資料，來提供對於已部署資源之安全性狀態的深入了解。 OMS 可以設定為根據已定義的準則產生警示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 控制措施 SI-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>資訊系統監視 | 輸入和輸出通訊流量

**SI-4 (4)** 資訊系統會依 [指派：組織定義的頻率] 來監視傳入和傳出通訊流量，以找出不尋常或未經授權的活動或狀況。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 控制措施 SI-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>資訊系統監視 | 系統產生的警示

**SI-4 (5)** 資訊系統會在發生以下洩露或潛在的洩露跡象時，警示 [指派：組織定義的人員或角色]：[指派：組織定義的洩露跡象]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署各種 OMS 解決方案，包括安全性與稽核解決方案。 Log Analytics 會跨已部署的資源提供接近即時的事件分析。 OMS 解決方案可跨解決方案網域，提供安全性狀態的全面性檢視。 OMS 可以設定為根據已定義的準則產生警示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 控制措施 SI-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>資訊系統監視 | 分析通訊流量異常

**SI-4 (11)** 組織會在資訊系統的外部界限上，以及選取的 [指派：系統 (例如子網路、子系統) 內組織定義的內部點] 上，分析輸出通訊流量以探索異常。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責分析客戶部署資源的通訊流量異常。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 控制措施 SI-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>資訊系統監視 | 無線入侵偵測

**SI-4 (14)** 組織會採用無線入侵偵測系統來識別 Rogue 無線裝置，以及偵測對於資訊系統的攻擊嘗試和潛在的洩漏/缺口。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | Azure 資料中心不允許任何客戶控制的硬體 (包括無線裝置)。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 每季都會定期監視 Rrogue 無線訊號，如 AC-18 中所討論。 <br /> Microsoft Azure 會代表 PaaS 和 IaaS 客戶實作此控制措施。 |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 控制措施 SI-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>資訊系統監視 | 將監視資訊相互關聯

**SI-4 (16)** 組織會將來自整個資訊系統中所採用之監視工具的資訊相互關聯。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Log Analytics 及各種 OMS 解決方案，包括安全性與稽核解決方案。 OMS 會使用可跨已部署 OMS 解決方案取得的資料，來提供對於已部署資源之安全性狀態的深入了解。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 控制措施 SI-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>資訊系統監視 | 分析流量 / 掩飾外洩

**SI-4 (18)** 組織會在資訊系統 (例如系統周邊) 的外部界限上，以及選取的 [指派：系統 (例如子系統和子網路) 內組織定義的內部點] 上，分析輸出通訊流量以偵測掩飾資訊外洩的情況。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責分析客戶部署資源的通訊流量。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 控制措施 SI-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>資訊系統監視 | 個人造成更大的風險

**SI-4 (19)** 組織會針對由 [指派：組織定義的來源] 識別為造成風險層級提高的人員，實作 [指派：組織定義的其他監視]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視會造成更高風險的人員。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 控制措施 SI-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>資訊系統監視 | 具特殊權限的使用者

**SI-4 (20)** 組織會針對具特殊權限的使用者實作 [指派：組織定義的其他監視]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視具特殊權限的使用者。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 控制措施 SI-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>資訊系統監視 | 未經授權的網路服務

**SI-4 (22)** 資訊系統會偵測尚未經過 [指派：組織定義的授權或核准程序] 和 [選取 (一或多個)：稽核；警示 [指派：組織定義的人員或角色]] 授權或核准的網路服務。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責偵測未經授權的網路服務。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 控制措施 SI-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>資訊系統監視 | 主機型裝置

**SI-4 (23)** 組織會在 [指派：組織定義的資訊系統元件] 上實作 [指派：組織定義的主機型監視機制]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會收集來自已部署資源的監視資料，包括來自主機型監視功能的資料。 Microsoft Monitoring Agent 會安裝於所有 Windows 虛擬機器上，以收集 Log Analytics 和其他 OMS 解決方案所使用的監視資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 控制措施 SI-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>資訊系統監視 | 洩露跡象

**SI-4 (24)** 資訊系統會探索、收集、發佈及使用洩露跡象。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責探索、收集、發佈及使用客戶部署資源的洩漏跡象。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-5a"></a>NIST 800-53 控制措施 SI-5.a

#### <a name="security-alerts-advisories-and-directives"></a>安全性警示、建議及指示

**SI-5.a** 組織會持續不斷地接收來自 [指派：組織定義的外部組織] 的資訊系統安全性警示、建議及指示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源 (包含應用程式、作業系統、資料庫及軟體) 管理安全性警示、建議及指示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-5b"></a>NIST 800-53 控制措施 SI-5.b

#### <a name="security-alerts-advisories-and-directives"></a>安全性警示、建議及指示

**SI-5.b** 組織會在必要時產生內部安全性警示、建議及指示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源管理安全性警示、建議及指示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-5c"></a>NIST 800-53 控制措施 SI-5.c

#### <a name="security-alerts-advisories-and-directives"></a>安全性警示、建議及指示

**SI-5.c** 組織會將安全性警示、建議及指示散佈給：[選取 (一或多個)：[指派：組織定義的人員或角色]；[指派：組織內組織定義的元素]；[指派：組織定義的外部組織]]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源管理安全性警示、建議及指示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-5d"></a>NIST 800-53 控制措施 SI-5.d

#### <a name="security-alerts-advisories-and-directives"></a>安全性警示、建議及指示

**SI-5.d** 組織會根據既有的時間範圍來實作安全性指示，或通知發行組織不符合規範的程度。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源管理安全性警示、建議及指示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 控制措施 SI-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>安全性警示、建議及指示 | 自動化的警示和建議

**SI-5 (1)** 組織會採用自動化機制，讓安全性警示和建議資訊可在整個組織內取得。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源管理安全性警示、建議及指示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-6a"></a>NIST 800-53 控制措施 SI-6.a

#### <a name="security-function-verification"></a>安全性功能驗證

**SI-6.a** 資訊系統會驗證 [指派：組織定義的安全性功能] 正常運作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源 (包含應用程式、作業系統、資料庫及軟體) 驗證安全性功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 控制措施 SI-6.b

#### <a name="security-function-verification"></a>安全性功能驗證

**SI-6.b** 資訊系統會以 [選取 (一或多個)：[指派：組織定義的系統過渡狀態]；根據具特殊權限之使用者的命令；[指派：組織定義的頻率]] 執行此驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源驗證安全性功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 控制措施 SI-6.c

#### <a name="security-function-verification"></a>安全性功能驗證

**SI-6.c** 資訊系統會通知 [指派：組織定義的人員或角色]，安全性驗證測試失敗。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源驗證安全性功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-6d"></a>NIST 800-53 控制措施 SI-6.d

#### <a name="security-function-verification"></a>安全性功能驗證

**SI-6.d** 資訊系統會在探索到異常時，[選取 (一或多個)：將資訊系統關機；將資訊系統重新啟動；[指派：組織定義的替代動作]]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源驗證安全性功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-7"></a>NIST 800-53 控制措施 SI-7

#### <a name="software-firmware-and-information-integrity"></a>軟體、韌體及資訊完整性

**SI-7** 組織會採用完整性驗證工具，來偵測對於 [指派：組織定義的軟體、韌體及資訊] 所做的未經授權變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 會為核心系統檔案提供即時的檔案完整性驗證、保護及復原，這些檔案會透過 Windows 資源保護 (WRP) 功能，安裝為 Windows 或已獲授權之 Windows 系統更新的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 控制措施 SI-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>軟體、韌體及資訊完整性 | 完整性檢查

**SI-7 (1)** 資訊系統會 [選取 (一或多個)：在啟動時；在 [指派：組織定義的過渡狀態或安全性相關事件] 上；[指派：組織定義的頻率]] 執行 [指派：組織定義的軟體、韌體及資訊] 的完整性檢查。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 會為核心系統檔案提供即時的檔案完整性驗證、保護及復原，這些檔案會透過 Windows 資源保護 (WRP) 功能，安裝為 Windows 或已獲授權之 Windows 系統更新的一部分。 WRP 會啟用即時完整性檢查。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 控制措施 SI-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>軟體、韌體及資訊完整性 | 自動通知完整性違規

**SI-7 (2)** 組織會採用自動化工具，在完整性驗證期間探索到差異時，向 [指派：組織定義的人員或角色] 提供通知。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 會為核心系統檔案提供即時的檔案完整性驗證、保護及復原，這些檔案會透過 Windows 資源保護 (WRP) 功能，安裝為 Windows 或已獲授權之 Windows 系統更新的一部分。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 控制措施 SI-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>軟體、韌體及資訊完整性 | 自動回應完整性違規

**SI-7 (5)** 資訊系統會在探索到完整性違規時，自動 [選取 (一或多個)：將資訊系統關機；將資訊系統重新啟動；實作 [指派：組織定義的安全性保護]]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責自動回應客戶部署資源內的完整性違規。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 控制措施 SI-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>軟體、韌體及資訊完整性 | 整合偵測和回應

**SI-7 (7)** 組織會將未經授權的 [指派：組織定義之對於資訊系統所做的安全性相關變更] 的偵測併入組織事件回應功能。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責保護客戶部署資源的軟體和資訊完整性。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 控制措施 SI-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>軟體、韌體及資訊完整性 | 二進位或機器可執行的程式碼

**SI-7 (14)** 組織禁止使用來自有限或不具保證的來源，而且也未提供任何原始程式碼的二進位或機器可執行程式碼，並且只有在強迫進行任務/操作需求且具備授權官方的核准時，才會提供原始程式碼需求的例外狀況。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統與資訊完整性程序可能會建立需求，以便從某些來源取得二進位或機器可執行程式碼的原始程式碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-8a"></a>NIST 800-53 控制措施 SI-8.a

#### <a name="spam-protection"></a>垃圾郵件防護

**SI-8.a** 組織會在系統進入和結束點採用垃圾郵件防護機制，以偵測來路不明的訊息並採取動作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 不會將任何郵件伺服器部署為這個 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-8b"></a>NIST 800-53 控制措施 SI-8.b

#### <a name="spam-protection"></a>垃圾郵件防護

**SI-8.b** 根據組織設定管理原則和程序，當有新版本可供使用時，組織就會更新垃圾郵件防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 不會將任何郵件伺服器部署為這個 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 控制措施 SI-8 (1)

#### <a name="spam-protection--central-management"></a>垃圾郵件防護 | 集中管理

**SI-8 (1)** 組織會集中管理垃圾郵件防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 不會將任何郵件伺服器部署為這個 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 控制措施 SI-8 (2)

#### <a name="spam-protection--automatic-updates"></a>垃圾郵件防護 | 自動更新

**SI-8 (2)** 資訊系統會自動更新垃圾郵件防護機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 不會將任何郵件伺服器部署為這個 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-10"></a>NIST 800-53 控制措施 SI-10

#### <a name="information-input-validation"></a>資訊輸入驗證

**SI-10** 資訊系統會檢查 [指派：組織定義的資訊輸入] 的有效性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源 (包含應用程式、作業系統、資料庫及軟體) 驗證資訊輸入。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-11a"></a>NIST 800-53 控制措施 SI-11.a

#### <a name="error-handling"></a>處理錯誤

**SI-11.a** 資訊系統會產生錯誤訊息，以提供更正動作所需的資訊，而不會揭露可能遭到敵人惡意探索的資訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會採用商用作業系統和軟體應用程式。 這個軟體會使用業界最佳作法，以確保錯誤訊息中不會揭露機密資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-11b"></a>NIST 800-53 控制措施 SI-11.b

#### <a name="error-handling"></a>處理錯誤

**SI-11.b** 資訊系統只會向 [指派：組織定義的人員或角色] 揭露錯誤訊息。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會採用商用作業系統和軟體應用程式。 這個軟體會使用業界最佳作法，在接收訊息的用法內容中提供適當的錯誤訊息。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-12"></a>NIST 800-53 控制措施 SI-12

#### <a name="information-handling-and-retention"></a>資訊處理和保留

**SI-12** 組織會根據適用的聯邦法律、行政命令、指示、原則、法規、標準及操作需求，處理並保留系統資訊內的資訊，以及從系統輸出的資訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的資源 (包括應用程式、作業系統、資料庫和軟體) 內，處理並保留資訊以及從那些資源輸出的資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-si-16"></a>NIST 800-53 控制措施 SI-16

#### <a name="memory-protection"></a>記憶體保護

**SI-16** 資訊系統會實作 [指派：組織定義的安全性保護] 來保護它的記憶體，以防止未經授權的程式碼執行。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 具有適當的保護，可防止在限制的記憶體位置中執行程式碼：禁止執行 (NX)、位址空間配置隨機載入 (ASLR)，以及資料執行防止 (DEP)。 |
| **提供者 (Microsoft Azure)** | 不適用 |
