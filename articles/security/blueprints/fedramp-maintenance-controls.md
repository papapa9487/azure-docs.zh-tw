---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 維護"
description: "FedRAMP 的 Web 應用程式- 維護"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a0546f6e10b04bbfdb5b02e5c0bbe6d907c76e72
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="maintenance-ma"></a>維護 (MA)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-ma-1"></a>NIST 800-53 控制措施 MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>系統維護原則和程序

**MA-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、職責、管理承諾、組織實體間之協調合作及合規性的系統維護原則，以及可協助實作系統維護原則和相關系統維護控制措施的程序；並檢閱和更新目前的系統維護原則 (依 [指派：組織定義的頻率]) 與系統維護程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護原則和程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800-53 控制措施 MA-2.a

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.a** 組織會根據製造商或廠商規格和/或組織需求，來排定、執行、記載及檢閱資訊系統元件上的維護和修復記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責執行受控制維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 控制措施 MA-2.b

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.b** 組織會核准和監視所有維護活動，不論維護活動是在現場執行的還是從遠端執行的，且不論是在現場為設備提供服務還是移至另一個位置。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責執行受控制維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800-53 控制措施 MA-2.c

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.c** 組織會要求 [指派：組織定義的人員或角色] 明確地核准將資訊系統或系統元件從組織設施移除，以進行異地維護或修復。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會要求需要轉移至異地的資產 (例如網路裝置或伺服器) 必須取得明確的資產擁有者核准。 |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 控制措施 MA-2.d

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.d** 組織會先清理設備以從相關媒體移除所有資訊，然後才從組織設施移除該設備來進行異地維護或修復。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 的「資產保護標準」定義了將資產異地轉移所需的資產處理預防措施。 「資產保護標準」會要求資料儲存體資產必須先依據 NIST SP 800-88「媒體清理指導方針」(Guidelines for Media Sanitization) 進行清理/清除，才能離開資料中心。 |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800-53 控制措施 MA-2.e

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.e** 組織會檢查所有可能受影響的安全性控制項，以確認這些控制項在進行維護或修復動作之後仍可正常運作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責執行受控制維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800-53 控制措施 MA-2.f

#### <a name="controlled-maintenance"></a>受控制維護

**MA-2.f** 組織會將 [指派：組織定義的維護相關資訊] 納入組織維護記錄中。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責執行受控制維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-2-2a"></a>NIST 800-53 控制措施 MA-2 (2).a

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>受控制維護 | 自動化維護活動

**MA-2 (2).a** 組織會採用自動化機制來排定、管理及記載文件的維護和修復。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責將維護活動自動化。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-2-2b"></a>NIST 800-53 控制措施 MA-2 (2).b

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>受控制維護 | 自動化維護活動

**MA-2 (2).b** 組織會產生所要求、排定、進行中及已完成之所有維護和修復動作的最新、正確與完整記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責將維護活動自動化。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800-53 控制措施 MA-3

#### <a name="maintenance-tools"></a>維護工具

**MA-3** 組織會核准、控制及監視資訊系統維護工具。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 使用數個工具來完成維護。 軟體維護工具的核准、控制及維護會透過 Microsoft Azure 變更與發行程序來執行。 <br /> 「網站服務」小組會維護已核准在資料中心內使用的維護工具詳細目錄 (請參閱 MA-3)。 維護人員將受引導來使用所提供的維護工具。 若要使用不是由資料中心提供的工具，必須獲得資料中心管理部門核准。 實體手工具 (螺絲起子、板手等) 則可從此控制措施豁免。 <br /> 每項設施皆包含一個受管制的實體保險箱或有進出管制的房間來儲存特製化維護工具，例如 Fluke Ether Scope 網路分析儀、Fluke 光纖通道測試器、乙太網路音頻探測器等。「網站服務」小組會執行例行性詳細目錄檢查，以確認所有工具的狀態。 存取徽章讀取器記錄中會追蹤保險箱或維護儲藏室的使用情況，進行調查時會提供這些記錄。 |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800-53 控制措施 MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>維護工具 | 檢查工具

**MA-3 (1)** 組織會檢查維護人員攜入設施中的維護工具，以了解是否有不當或未經授權的修改。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 的「網站服務」小組會維護已核准在資料中心內使用的維護工具詳細目錄 (如需進一步的詳細資料，請參閱 MA-3)。 維護人員將受引導來使用所提供的維護工具。 若要使用不是由資料中心提供的工具，必須獲得 DCM 核准。 |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800-53 控制措施 MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>維護工具 | 檢查媒體

**MA-3 (2)** 組織會先檢查包含診斷和測試程式的媒體是否有惡意程式碼，然後才在資訊系統中使用這些媒體。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 禁止在未經資料中心管理部門核准的情況下，於 Microsoft Azure 資料中心的生產環境中使用行動運算或儲存媒體。 在 Microsoft Azure 資料中心的生產環境中，禁止使用個人擁有的媒體。 <br /> Microsoft Azure 已實作程序，會先對膝上型電腦進行檢查，然後才在 Microsoft Azure 資料中心的生產環境中使用這些電腦。 安全人員已受訓練來查問在生產環境中使用膝上型電腦的人員，以確認這些膝上型電腦已經過且通過檢查。 |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800-53 控制措施 MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>維護工具 | 防止未經授權的移除

**MA-3 (3)** 組織會防止對包含組織資訊的維護設備進行未經授權的移除，方法包括確認該設備上沒有任何組織資訊；清理或銷毀該設備；將該設備保留在設施內；或向 [指派：組織定義的人員或角色] 取得明確授權從設施移除該設備的豁免權。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會採用保留在設施內且不會移除的資料中心特定維護工具。 每項設施皆包含一個受管制的實體保險箱或儲藏室來儲存維護工具，例如 Fluke Ether Scope 網路分析儀、Fluke 光纖通道測試器、乙太網路音頻探測器等。保險箱或儲藏室的使用會在 DCAT 中受到管制，以防止發生未經授權使用維護工具的情況。 <br /> 在維護期間，會由 MA-4 中的控制措施保護組織資訊。 若要存取組織資訊，使用者必須擁有具權限的帳戶和驗證器。 |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 控制措施 MA-4.a

#### <a name="nonlocal-maintenance"></a>非本機維護

**MA-4.a** 組織會核准及監視非本機維護和診斷活動。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的作業系統上執行非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800-53 控制措施 MA-4.b

#### <a name="nonlocal-maintenance"></a>非本機維護

**MA-4.b** 組織允許使用非本機維護和診斷工具，但僅限在符合組織原則及資訊系統安全性計畫中所記載的情況下。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的作業系統上執行非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 控制措施 MA-4.c

#### <a name="nonlocal-maintenance"></a>非本機維護

**MA-4.c** 組織會在建立非本機維護和診斷工作階段時，採用增強式驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的作業系統上執行非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800-53 控制措施 MA-4.d

#### <a name="nonlocal-maintenance"></a>非本機維護

**MA-4.d** 組織會維護非本機維護和診斷活動的記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的作業系統上執行非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800-53 控制措施 MA-4.e

#### <a name="nonlocal-maintenance"></a>非本機維護

**MA-4.e** 組織會在非本機維護完成時，終止工作階段和網路連線。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署的作業系統上執行非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 控制措施 MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>非本機維護 | 記載非本機維護

**MA-4 (2)** 組織會在資訊系統的安全性計畫中，記載用於建立及使用非本機維護和診斷連線的原則與程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署作業系統的安全性計畫中，記載非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 控制措施 MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>非本機維護 | 同等級安全性/清理

**MA-4 (3)** 組織會要求從實作與所服務系統上同等級安全性功能的資訊系統執行非本機維護和診斷服務；或先從資訊系統移除要服務的元件再進行非本機維護或診斷服務、先清理元件 (與組織資訊相關的部分) 再從組織設施中移除元件，以及在執行服務之後，先檢查並清理元件 (與潛在惡意軟體相關的部分) 再將元件重新連線到資訊系統。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責從具備同等級安全性的資訊系統，執行客戶所部署作業系統的所有非本機維護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 控制措施 MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>非本機維護 | 密碼編譯保護

**MA-4 (6)** 資訊系統會實作密碼編譯機制，以保護非本機維護和診斷通訊的完整性與機密性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在執行客戶所部署作業系統的非本機維護和診斷時，實作密碼編譯機制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800-53 控制措施 MA-5.a

#### <a name="maintenance-personnel"></a>維護人員

**MA-5.a** 組織會建立維護人員授權程序，並維護一份已獲授權之維護組織或人員的清單。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護人員授權和戒護程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800-53 控制措施 MA-5.b

#### <a name="maintenance-personnel"></a>維護人員

**MA-5.b** 組織會確保在資訊系統上執行維護的非受戒護人員擁有必要的存取授權。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護人員授權和戒護程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800-53 控制措施 MA-5.c

#### <a name="maintenance-personnel"></a>維護人員

**MA-5.c** 組織會為組織人員指定必要的存取授權和技術能力，以監督未具備必要存取授權之人員的維護活動。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護人員授權和戒護程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-5-1a"></a>NIST 800-53 控制措施 MA-5 (1).a

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>維護人員 | 沒有適當存取權的個人

**MA-5 (1).a** 組織會針對使用缺乏適當安全性許可或非美國公民的維護人員情況，實作包括下列需求的程序：未擁有所需存取授權、許可或正式存取核准的維護人員在於資訊系統上執行維護和診斷活動時，會由已獲完全認可、擁有適當存取授權且技術上合格的已核准組織人員進行戒護和監督；在未擁有所需存取授權、許可或正式存取核准的人員起始維護或診斷活動之前，會先清理資訊系統內的所有揮發性資訊儲存元件，以及將所有非揮發性儲存媒體從系統移除或中斷連線並加以保護。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護人員授權和戒護程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ma-5-1b"></a>NIST 800-53 控制措施 MA-5 (1).b

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>維護人員 | 沒有適當存取權的個人

**MA-5 (1).b** 組織會在無法從系統清理、移除資訊系統元件或與其中斷連線時，開發並實作替代的安全性保護。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級系統維護人員授權和戒護程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800-53 控制措施 MA-6

#### <a name="timely-maintenance"></a>及時維護

**MA-6** 組織會在故障的 [指派：組織定義的時間週期] 內，取得 [指派：組織定義的資訊系統元件] 的維護支援和/或零件。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 資料中心會保有常駐的維護人員，以支援重要的資料中心基礎結構系統及資料中心操作。 小組已識別出重要的安全性與技術系統元件，並於現場保有備品。 重要系統是採用 N+1 的組態設計，而服務則是採用彈性設計。 這可讓資料中心管理小組在發生服務中斷或應變計畫情況時，達成復原目標。 針對重要的資訊系統服務，會從多個資料中心進行佈建，以防止因其中一個資料中心發生事件而導致服務中斷。 客戶應用程式須負責部署至多個資料中心，以提供備援和復原能力。 |
