---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 系統與通訊保護"
description: "適用於 FedRAMP 的 Web 應用程式 - 系統與通訊保護"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 02e2d07eb29d0d5d436afed1cdab4fe710674a8c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-communications-protection-sc"></a>系統與通訊保護 (SC)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 控制措施 SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>系統與通訊保護原則和程序

**SC-1** 組織開發、記錄系統與通訊保護原則，並散佈給 [指派：組織定義的人員或角色]，這些原則滿足用途、領域、角色、職責和管理承諾、組織實體間的協調及合規性；程序則是加速系統實作與通訊保護原則及關聯的系統與通訊保護控制措施；以及檢閱和更新目前的系統與通訊保護原則 [指派：組織定義的頻率] 和系統與通訊保護程序 [指派：組織定義的頻率]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統和系統與通訊保護原則與程序，可能就足以因應此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 控制措施 SC-2

#### <a name="application-partitioning"></a>應用程式分割

**SC-2** 資訊系統將使用者功能 (包括使用者介面服務) 與資訊系統管理功能作區隔。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 透過強制執行邏輯存取控制和系統架構，將使用者功能與系統管理功能作區隔。 使用者功能僅限於客戶部署的 Web 應用程式介面。 系統管理功能的介面不同於使用者介面。 所有管理連線都會流經位於管理子網路的安全防禦主機 (Jumpbox)，並設有網路安全性群組規則以視情況限制對生產資源的存取。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 控制措施 SC-3

#### <a name="security-function-isolation"></a>安全性功能隔離

**SC-3** 資訊系統將安全性功能與非安全性功能隔離。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 透過將私用虛擬位址空間指派給每個處理序，為每個執行處理序維護個別的執行網域。 此外，此解決方案會實作可在需要時隔離安全性功能的架構和存取控制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 控制措施 SC-4

#### <a name="information-in-shared-resources"></a>共用資源中的資訊

**SC-4** 資訊系統防止透過共用系統資源傳輸未經授權和非預期資訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 作業系統會管理資源 (例如記憶體、儲存體)，使得只有具備適當權限的使用者與角色可以存取資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 控制措施 SC-5

#### <a name="denial-of-service-protection"></a>阻斷服務保護

**SC-5** 資訊系統藉由採取 [指派： 組織定義的安全性預防措施]，以防止或限制下列阻斷服務攻擊類型的影響：[指派：組織定義的阻斷服務攻擊類型，或是這類資訊的來源參考]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署應用程式閘道，其中包括 Web 應用程式防火牆及負載平衡功能。 支援 Web 層、資料庫層與 Active Directory 的已部署虛擬機器，是部署在可調整的可用性設定組中。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 控制措施 SC-6

#### <a name="resource-availability"></a>資源可用性

**SC-6** 資訊系統藉由依 [選取項目 (一或多個); 優先順序; 配額; [指派：組織定義的安全性預防措施]] 配置 [指派：組織定義的資源]，以保護資源的可用性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 每個 Windows 處理序都提供執行程式所需的資源。 資源優先順序是由作業系統管理的。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 控制措施 SC-7.a

#### <a name="boundary-protection"></a>界限保護

**SC-7.a** 資訊系統監視和控制外部系統界限及系統內關鍵界限的通訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署應用程式閘道、負載平衡器和設定網路安全性群組規則，來控制外部界限和內部子網路之間的資訊交換。 OMS Log Analytics 會收集應用程式閘道、負載平衡器和網路安全性群組事件及診斷記錄，以允許客戶監視。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 控制措施 SC-7.b

#### <a name="boundary-protection"></a>界限保護

**SC-7.b** 資訊系統為 [選取項目：實體; 邏輯] 上和內部組織網路有區隔之可公開存取系統元件實作子網路。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 在具有個別 Web 子網路、資料庫子網路、Active Directory 子網路及管理子網路的架構中部署資源。 套用至個別子網路的網路安全性群組規則以邏輯方式區隔了子網路，將子網路之間的流量限制為系統和管理功能所需流量 (例如，外部流量無法存取資料庫、管理或 Active Directory 子網路)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 控制措施 SC-7.c

#### <a name="boundary-protection"></a>界限保護

**SC-7.c** 與外部網路連線或只透過受管理介面的資訊系統，由根據組織安全性架構規劃的界限保護裝置所組成的。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署應用程式閘道，以管理連線到客戶部署之 Web 應用程式的外部連線。 管理存取的外部連線僅限於部署在管理子網路中的防禦主機/Jumpbox ，並套用網路安全性規則以限制只有經授權的 IP 位址可進行外部連線。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 控制措施 SC-7 (3)

#### <a name="boundary-protection--access-points"></a>界限保護 | 存取點

**SC-7 (3)** 組織限制連線到資訊系統的外部網路連線數目。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署兩個公用 IP 位址：一個與應用程式閘道關聯，一個與管理防禦主機/Jumpbox 關聯。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 控制措施 SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>界限保護 | 外部電信服務

**SC-7 (4).a** 組織為每個外部電信服務實作受管理介面。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署兩個公用 IP 位址：一個與應用程式閘道關聯，一個與管理防禦主機/Jumpbox 關聯。 這些介面的管理是透過軟體定義的網路功能來達成。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 控制措施 SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>界限保護 | 外部電信服務

**SC-7 (4).b** 組織建立每個受管理介面的流量流程原則。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署兩個公用 IP 位址：一個與應用程式閘道關聯，一個與管理防禦主機/Jumpbox 關聯。 這些介面的管理是透過軟體定義的網路功能來達成。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 控制措施 SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>界限保護 | 外部電信服務

**SC-7 (4).c** 組織保護透過每個介面傳輸之資訊的機密性和完整性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的 Web 應用程式閘道會設定 HTTPS 接聽程式，確保通訊工作階段的機密性和完整性。 與 Jumpbox 的遠端桌面連線也會加密，以提供機密性和完整性。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 控制措施 SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>界限保護 | 外部電信服務

**SC-7 (4).d** 組織記錄流量流程原則的每個例外，含支援的任務/商務需求和該需求的持續時間。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶不負責資料中心的運作 (包含電信服務)。 所有電信服務均由 Microsoft Azure 提供和管理。 此控制措施繼承自 Azure。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 控制措施 SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>界限保護 | 外部電信服務

**SC-7 (4).e** 組織檢閱流量流程原則的例外 [指派：組織定義的頻率]，並移除明確的任務/商務需求不再支援的例外。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶不負責資料中心的運作 (包含電信服務)。 所有電信服務均由 Microsoft Azure 提供和管理。 此控制措施繼承自 Azure。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 控制措施 SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>界限保護 | 預設為拒絕/依例外允許

**SC-7 (5)** 資訊系統在受管理介面預設為拒絕網路通訊流量，而且是依例外狀況允許網路通訊流量 (亦即全部拒絕、依例外允許)。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 套用至此 Azure Blueprint (藍圖) 部署之網路安全性群組的規則集，會設定為使用「預設為拒絕」配置。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 控制措施 SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>界限保護 | 防止遠端裝置的分割通道

**SC-7 (7)** 資訊系統結合遠端裝置，以防止裝置同時與系統建立非遠端連線，以及經由一些其他連線與外部網路中的資源交換資訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級遠端裝置設定原則可能可以預防分割通道。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 控制措施 SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>界限保護 | 將流量路由傳送到經驗證的 Proxy 伺服器

**SC-7 (8)** 資訊系統透過經驗證的 Proxy 伺服器在受管理的介面將 [指派：組織定義的內部通訊流量] 路由傳送至 [指派：組織定義的外部網路]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責將客戶定義的資訊透過經驗證的 Proxy 路由傳送到外部網路。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 控制措施 SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>界限保護 | 防止未經授權的資訊流出

**SC-7 (10)** 組織預防跨管理介面未經授權的資訊流出。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責防止跨管理介面未經授權的資訊流出。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 控制措施 SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>界限保護 | 主機型保護

**SC-7 (12)** 組織在 [指派：組織定義的資訊系統元件] 實作 [指派：組織定義的主機型界限保護機制]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署的虛擬機器設為啟用主機型防火牆。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 控制措施 SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>界限保護 | 安全性工具隔離/機制/支援元件

**SC-7 (13)** 組織藉由實作實體區隔的子網路和存取系統其他元件的受管理介面，將 [指派：組織定義的資訊安全性工具、機制和支援元件] 與其他內部資訊系統元件隔離。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署資源的架構中，客戶部署的資訊安全工具和支援元件位在個別的管理子網路。 網路安全性群組規則以邏輯方式區隔子網路。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 控制措施 SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>界限保護 | 失效安全

**SC-7 (18)** 資訊系統針對界限保護裝置故障有失效安全的設計。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內，沒有實體界限保護裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會部署地理位置不同的備援閘道伺服器和 SSL VPN。 當閘道系統失敗時，會啟動失效安全並將存取限制為僅限該環境。 若要建立與 Microsoft Azure 環境的連線，使用者必須另行建立連線以連線到由 Microsoft Azure 管理的作用中閘道伺服器。 <br /> 此外，如果 Microsoft Azure 網路裝置 (包括邊緣路由器、存取路由器、負載平衡器、彙總交換器和 TORS) 失敗，受影響的線路會中斷連線，因而啟動失效安全。 Microsoft Azure 網路裝置一旦失敗，不會引起或導致系統外部的資訊進入裝置，或允許未經授權的資訊發行。 內建的備援讓 Microsoft Azure 資產在失敗時不致影響可用性。 |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 控制措施 SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>界限保護 | 動態隔離/區分

**SC-7 (20)** 資訊系統可以將 [指派：組織定義的資訊系統元件] 和系統的其他元件動態隔離/區分。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責確保系統能夠以動態方式隔離客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 控制措施 SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>界限保護 | 資訊系統元件隔離

**SC-7 (21)** 組織採用界限保護機制以區隔 [指派：組織定義的資訊系統元件] 支援的 [指派：組織定義的任務和/或商務功能]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 在具有個別 Web 子網路、資料庫子網路、Active Directory 子網路及管理子網路的架構中部署資源。 套用至個別子網路的網路安全性群組規則以邏輯方式區隔了子網路，將子網路之間的流量限制為系統和管理功能所需的流量。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 控制措施 SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>傳輸機密性和完整性

**SC-8** 資訊系統會保護所傳輸資訊的 [選取項目 (一或多個)：機密性; 完整性]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | SI-8 (1) 實作可滿足此控制措施需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 控制措施 SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>傳輸機密性和完整性 | 密碼編譯或替代實體保護

**SC-8 (1)** 資訊系統會實作密碼編譯機制，以在傳輸期間 [選取項目 (一或多個)：防止未經授權的資訊揭露; 偵測資訊變更]，除非已使用 [指派：組織定義的替代實體預防措施] 保護。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會將資源設定為只使用安全通訊協定進行通訊。 應用程式閘道的 WAF 元件會設定為接受 外部通訊者透過 HTTPS/TLS 通訊，並只能透過 HTTPS/TLS 與後端集區通訊。 SQL Server 設定為只透過 HTTPS/TLS 通訊。 遠端桌面服務設定為使用安全連線。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 控制措施 SC-10

#### <a name="network-disconnect"></a>網路中斷連線

**SC-10** 資訊系統在工作階段結束時或 [指派：組織定義時間期間] 的非使用狀態後，終止與通訊工作階段相關聯的網路連線。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 遠端桌面工作階段的驗證是由 Active Directory 管理的。 一旦停用 Active Directory 中的使用者存取權，遠端工作階段會立即終止。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 控制措施 SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>密碼編譯金鑰的建立和管理

**SC-12** 組織建立和管理按照 [指派：組織定義之金鑰產生、分配、儲存、存取和解構的需求] 在資訊系統內採用的必要密碼編譯所用的密碼編譯金鑰。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署 Azure Key Vault。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 Azure Key Vault 可使用 FIPS 140-2 第 2 級硬體安全性模組 (HSM) 金鑰產生功能來產生金鑰。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 控制措施 SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>密碼編譯金鑰的建立和管理 | 可用性

**SC-12 (1)** 組織在使用者遺失密碼編譯金鑰時維持資訊的可用性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Key vault 用於儲存此 Azure Blueprint (藍圖) 中使用的密碼編譯金鑰和密碼。 Key Vault 簡化了存取和加密資料之金鑰的金鑰管理程序。 下列驗證器會儲存在 Key Vault 中：部署帳戶的 Azure 密碼、虛擬機器系統管理員密碼、SQL Server 服務帳戶密碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 控制措施 SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>密碼編譯金鑰的建立和管理 | 對稱金鑰

**SC-12 (2)** 組織使用 [選取項目：符合 NIST FIPS 規範的; NSA 核准的] 金鑰管理技術和程序產生、控制和散發對稱密碼編譯金鑰。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Key Vault 用來產生、控制和散發密碼編譯金鑰。 Azure Key Vault 可使用 FIPS 140-2 第 2 級硬體安全性模組 (HSM) 金鑰產生功能來產生金鑰。 您可以在 Azure Key Vault 內的安全加密容器中儲存和管理金鑰。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 控制措施 SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>密碼編譯金鑰的建立和管理 | 非對稱金鑰

**SC-12 (3)** 組織使用 [選取項目：NSA 核准的金鑰管理技術和程序; 核准的 PKI 類別 3 憑證或前置的金鑰處理內容; 核准的 PKI 類別 3 或類別 4 憑證和保護使用者私密金鑰的硬體安全性權杖] 產生、控制和散發非對稱密碼編譯金鑰。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責產生、控制和散發非對稱密碼編譯金鑰 (如果使用於客戶部署的資源內)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 控制措施 SC-13

#### <a name="cryptographic-protection"></a>密碼編譯保護

**SC-13** 資訊系統根據適用之聯邦法律、行政命令、指令、原則、規定和標準實作 [指派：組織定義的密碼編譯用途和每種用途所需的密碼類型]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 採用 Windows 驗證、遠端桌面和 BitLocker。 這些元件可以設定為依賴 FIPS 140 驗證的密碼編譯模組。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 控制措施 SC-15.a

#### <a name="collaborative-computing-devices"></a>共同作業計算裝置

**SC-15.a** 資訊系統禁止遠端啟用共同作業計算裝置，但以下狀況除外：[指派：組織所定義允許遠端啟用的例外]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 沒有任何共同作業計算裝置部署為此 Azure Blueprint (藍圖) 的一部分。 注意：在部署於 Azure 上的系統範圍內有實體共同作業計算裝置。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 控制措施 SC-15.b

#### <a name="collaborative-computing-devices"></a>共同作業計算裝置

**SC-15.b** 資訊系統為實際坐在裝置前的使用者提供明確的用法指示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 沒有任何共同作業計算裝置部署為此 Azure Blueprint (藍圖) 的一部分。 注意：在部署於 Azure 上的系統範圍內有實體共同作業計算裝置。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 控制措施 SC-17

#### <a name="public-key-infrastructure-certificates"></a>公開金鑰基礎結構憑證

**SC-17** 組織依照 [指派： 組織定義的憑證原則] 發行公開金鑰憑證，或從核准的服務提供者取得公開金鑰憑證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會依賴企業層級的公開金鑰基礎結構以發行憑證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 控制措施 SC-18.a

#### <a name="mobile-code"></a>行動程式碼

**SC-18.a** 組織定義可接受和不接受的行動程式碼和行動程式碼技術。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統與通訊保護程序可能會定義可接受和不接受的行動程式碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 控制措施 SC-18.b

#### <a name="mobile-code"></a>行動程式碼

**SC-18.b** 組織建立使用限制，並實作可接受的行動程式碼和行動程式碼技術的指導方針。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統與通訊保護程序可能會建立使用行動程式碼的限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 控制措施 SC-18.c

#### <a name="mobile-code"></a>行動程式碼

**SC-18.c** 組織在資訊系統內授權、監視和控制行動程式碼的使用。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會依賴企業層級程序來授權、監視和控制行動程式碼的使用。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 控制措施 SC-19.a

#### <a name="voice-over-internet-protocol"></a>VoIP

**SC-19.a** 組織建立使用限制，並實作 VoIP 技術的指導方針，以因應遭惡意使用時造成資訊系統損壞的可能性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 沒有任何 VoIP 技術部署為此 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 控制措施 SC-19.b

#### <a name="voice-over-internet-protocol"></a>VoIP

**SC-19.b** 組織在資訊系統內授權、監視和控制 VoIP 的使用。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 沒有任何 VoIP 技術部署為此 Azure Blueprint (藍圖) 的一部分。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 控制措施 SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>安全的名稱/位址解析服務 (權威來源)

**SC-20.a** 資訊系統除了回應外部名稱/位址解析查詢所傳回的權威名稱解析資料外，還提供額外的資料來源驗證和完整性驗證成品。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責維護安全的名稱和位址解析服務。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 控制措施 SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>安全的名稱/位址解析服務 (權威來源)

**SC-20.b** 資訊系統提供指出子區域和 (如果子系支援安全的解析服務) 安全狀態的方法，以在父網域和子網域作為分散式、階層式命名空間的一部分運作時啟用兩者間信任鏈結的驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責維護安全的名稱和位址解析服務。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 控制措施 SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>安全的名稱/位址解析服務 (遞迴或快取解析程式)

**SC-21** 資訊系統在接收自權威來源的名稱/位址解析回應上，要求並執行資料來源驗證及資料完整性驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責將客戶部署的資源設定為在接收來自權威來源的名稱/位址解析回應上，要求並執行資料來源驗證和資料完整性驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 控制措施 SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>名稱/位址解析服務的架構和佈建

**SC-22** 為組織整體提供名稱/位址解析服務的資訊系統支援容錯，並實作內部/外部角色區隔。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責確保為客戶部署的資源提供位址解析服務的系統支援容錯，並實作內部/外部角色區隔。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 控制措施 SC-23

#### <a name="session-authenticity"></a>工作階段真確性

**SC-23** 資訊系統保護通訊工作階段的真確性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署資源 (包括 Azure 入口網站、遠端桌面連線和 Web 應用程式閘道) 的遠端存取安全是使用 TLS 來保護的。 TLS 為工作階段層級的通訊提供真確性。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 控制措施 SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>工作階段真確性 | 登出時使工作階段識別碼失效

**SC-23 (1)** 資訊系統在使用者登出或其他工作階段終止時使工作階段識別碼失效。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署資源 (包括 Azure 入口網站、遠端桌面連線和 Web 應用程式閘道) 的遠端存取安全是使用 TLS 來保護的。 Azure 入口網站和遠端桌面工作階段會在登出時使工作階段識別碼失效。 Web 工作階段的失效是透過 Azure 應用程式閘道 - Web 應用程式防火牆 (WAF) 規則來強制執行的。 WAF 會套用每一工作階段的 Cookie 親和性，並在用戶端處於閒置狀態超過 30 分鐘 (部署後可設定為組織特定規則) 後執行工作階段逾時。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 控制措施 SC-24

#### <a name="fail-in-known-state"></a>在已知狀態失敗

**SC-24** 資訊系統在 [指派：組織定義的失敗類型] 的 [指派：組織定義的已知狀態] 失敗，使 [指派：組織定義的系統狀態資訊] 保持在失敗狀態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責確保客戶部署的資源在已知狀態失敗。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 控制措施 SC-28

#### <a name="protection-of-information-at-rest"></a>待用資訊的保護

**SC-28** 資訊系統會保護 [指派：組織定義的待用資訊] 的 [選取項目 (一或多個)：機密性; 完整性]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | SC-28 (1) 實作可滿足此控制措施需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 控制措施 SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>待用資訊的保護 | 密碼編譯保護

**SC-28 (1)** 資訊系統實作密碼編譯機制，以防止未經授權揭露及修改 [指派：組織定義的資訊系統元件] 上的 [指派：組織定義的資訊]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會實作磁碟加密，以保護待用資訊的機密性和完整性。 適用於 Windows 的 Azure 磁碟加密是使用 Windows 的 BitLocker 功能實作。 SQL Server 設定成使用透明資料加密 (TDE)，它會執行即時的資料和記錄檔的加密和解密，以保護待用資料。 TDE 能保證已儲存的資料不會遭到未經授權的存取。 客戶可選擇實作額外的應用程式層級控制措施，以保護儲存資訊的完整性。 此 Azure Blueprint (藍圖) 部署之所有儲存體 Blob 的機密性與完整性是使用 Azure 儲存體服務加密 (SSE) 來保護的。 SSE 會使用 256 位元 AES 加密來保護 Azure 儲存體帳戶內的待用資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 控制措施 SC-39

#### <a name="process-isolation"></a>處理序隔離

**SC-39** 資訊系統針對每個執行處理序各維護一個執行網域。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的虛擬機器會執行 Windows 作業系統。 Windows 透過將私用虛擬位址空間指派給每個處理序，為每個執行處理序維護個別的執行網域。 |
| **提供者 (Microsoft Azure)** | 不適用 |
