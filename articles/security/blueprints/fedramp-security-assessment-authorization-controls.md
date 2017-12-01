---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 安全性評定和授權"
description: "適用於 FedRAMP 的 Web 應用程式 - 安全性評定和授權"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 47c5914d-0d76-498a-9298-b3d9040791f8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 24c3e53d12f1479ba2d6b17bf28ebb7e5912206e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="security-assessment-and-authorization-ca"></a>安全性評定和授權 (CA)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-ca-1"></a>NIST 800-53 控制措施 CA-1

#### <a name="security-assessment-and-authorization-policy-and-procedures"></a>安全性評定和授權原則及程序

**CA-1** 組織會開發、記錄和傳播安全性評定與授權原則給 [指派：組織定義的人員或角色]，這些原則能處理組織實體間的目的、範圍、角色、責任、管理承諾及協調，以及合規性；以及能加速實作安全評定與授權原則及關聯連的安全性評定與授權控制措施的程序；以及依 [指派：組織定義的頻率] 檢閱和更新目前的安全性評定與授權原則；以及依 [指派：組織定義的頻率] 檢閱和更新安全性評定與授權程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權原則和程序可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-2a"></a>NIST 800-53 控制措施 CA-2.a

#### <a name="security-assessments"></a>安全性評定

**CA-2.a** 組織會開發描述評定範圍的安全性評定計畫，包括受評定的安全性控制措施及控制措施增強功能；用來判斷安全性控制措施效果的評定程序；以及評定環境、評定小組及評定角色和責任。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責開發適用於客戶部署系統的安全性評定計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-2b"></a>NIST 800-53 控制措施 CA-2.b

#### <a name="security-assessments"></a>安全性評定

**CA-2.b** 組織會依 [指派：組織定義的頻率] 評定資訊系統及其作業之環境中的安全性控制措施，以判斷控制措施在符合已建立之安全性需求的情況下正確實作、以預期方式運作並產生所需結果的程度。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在客戶部署資源上評定於 CA-02.a 中定義的安全性控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-2c"></a>NIST 800-53 控制措施 CA-2.c

#### <a name="security-assessments"></a>安全性評定

**CA-2.c** 組織會產生記錄評定結果的安全性評定報告。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責產生安全性評定報告。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-2d"></a>NIST 800-53 控制措施 CA-2.d

#### <a name="security-assessments"></a>安全性評定

**CA-2.d** 組織會將安全性控制措施評定的結果提供給 [指派：組織定義的個人或角色]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責將安全性評定結果提供給必要的個人/角色。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-2-1"></a>NIST 800-53 控制措施 CA-2 (1)

#### <a name="security-assessments--independent-assessors"></a>安全性評定 | 獨立評定者

**CA-2 (1)** 組織會雇用具有 [指派：組織定義的獨立層級] 的評定者或評定小組，以進行安全性控制措施評定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責雇用獨立評定者或評定小組以進行安全性控制措施評定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-2-2"></a>NIST 800-53 控制措施 CA-2 (2)

#### <a name="security-assessments--specialized-assessments"></a>安全性評定 | 特製化評定

**CA-2 (2)** 組織會依 [指派：組織定義的頻率] 將下列項目包含為安全性控制措施評定的一部分：[選取項目：已宣布、未宣布]、[選取項目 (一或多個)：深入監視；弱點掃描；惡意使用者測試；內部威脅評定；效能/負載測試；[指派：組織定義的其他安全性評定形式]]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責選取其他測試以包含為安全性控制措施評定的一部份。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-2-3"></a>NIST 800-53 控制措施 CA-2 (3)

#### <a name="security-assessments--external-organizations"></a>安全性評定 | 外部組織

**CA-2 (3)** 組織會在評定符合 [指派：組織定義的需求] 的情況下，接受由 [指派：組織定義的外部組織] 針對 [指派：組織定義的資訊系統] 所執行之評定的結果。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權程序可以針對由遵循 FedRAMP 的外部組織 (例如第三方評定組織 (3PAO) 或其他機構) 針對雲端服務供應項目所執行之評定的結果提出接受與否。 Azure 是由 FedRAMP 核准之第三方評定組織 (3PAO) 進行評估，以驗證是否符合 FedRAMP 安全性控制措施和其他需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-3a"></a>NIST 800-53 控制措施 CA-3.a

#### <a name="system-interconnections"></a>系統互相連線

**CA-3.a** 組織會透過互相連線安全性協議授權從資訊系統連線至其他資訊系統。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權程序可以處理系統互相連線的授權。 注意：FedRAMP 並不需要在 CSP 和美國聯邦機構之間具有 ISA。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-3b"></a>NIST 800-53 控制措施 CA-3.b

#### <a name="system-interconnections"></a>系統互相連線

**CA-3.b** 組織會針對每個互相連線，記錄介面特性、安全性需求，以及通訊資訊的性質。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權程序可以建立需求以建立系統互相連線。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-3c"></a>NIST 800-53 控制措施 CA-3.c

#### <a name="system-interconnections"></a>系統互相連線

**CA-3.c** 組織會依 [指派：組織定義的頻率] 檢閱和更新互相連線安全性協議。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權程序可以建立 ISA 檢閱和更新程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-3-3"></a>NIST 800-53 控制措施 CA-3 (3)

#### <a name="system-interconnections--unclassified-non-national-security-system-connections"></a>系統互相連線 | 未分類的非國家安全系統連線

**CA-3 (3)** 組織會禁止在沒有使用 [指派：組織定義的界線保護裝置] 的情況下直接將 [指派：組織定義的未分類、非國家安全系統] 連線至外部網路。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性評定和授權程序可以建立系統互相連線的界線保護需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-3-5"></a>NIST 800-53 控制措施 CA-3 (5)

#### <a name="system-interconnections--restrictions-on-external-system-connections"></a>系統互相連線 | 對外部系統連線的限制

**CA-3 (5)** 組織會運用 [選取項目：allow-all、deny-by-exception、deny-all、permit-by-exception] 原則來決定是否允許 [指派：組織定義的資訊系統] 連線至外部資訊系統。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 系統會部署 Azure 應用程式閘道和網路安全性群組，以限制外部位置連線至由此 Azure Blueprint (藍圖) 所部署的資源。 針對套用至網路安全性群組的規則集，系統會設定為使用 deny-by-default 配置。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-5a"></a>NIST 800-53 控制措施 CA-5.a

#### <a name="plan-of-action-and-milestones"></a>動作計畫和里程碑

**CA-5.a** 組織會針對資訊系統開發動作計畫和里程碑，以記錄組織針對修正在安全性控制措施評定期間所發現的弱點或缺陷所計畫的補救動作，以減少或消除系統中的已知弱點。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署資源 (包含應用程式、作業系統、資料庫及軟體) 開發動作計畫和里程碑 (POA&M)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-5b"></a>NIST 800-53 控制措施 CA-5.b

#### <a name="plan-of-action-and-milestones"></a>動作計畫和里程碑

**CA-5.b** 組織會根據安全性控制措施評定、安全性影響分析及連續監視活動，依 [指派：組織定義的頻率] 更新現有動作計畫和里程碑。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責更新於 CA-05.a 中定義的 POA&M 項目。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-6a"></a>NIST 800-53 控制措施 CA-6.a

#### <a name="security-authorization"></a>安全性授權

**CA-6.a** 組織會將高階主管或經理指派為資訊系統的授權官方人員。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可以在已指派授權官方人員的情況下仰賴企業層級的程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-6b"></a>NIST 800-53 控制措施 CA-6.b

#### <a name="security-authorization"></a>安全性授權

**CA-6.b** 組織會確保授權官方人員在開始作業之前授與資訊系統開始處理的權限。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可以仰賴企業層級系統授權程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-6c"></a>NIST 800-53 控制措施 CA-6.c

#### <a name="security-authorization"></a>安全性授權

**CA-6.c** 組織會依 [指派：組織定義的頻率] 更新安全性授權。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可以仰賴企業層級系統授權程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7a"></a>NIST 800-53 控制措施 CA-7.a

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.a** 組織會開發連續監視策略並實作連續監視計畫，其中包含要監視之 [指派：組織定義的計量] 的建立。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7b"></a>NIST 800-53 控制措施 CA-7.b

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.b** 組織會開發連續監視策略，並實作連續監視計畫，其中包含監視 [指派：組織定義的頻率] 的建立，以及支援這些監視之評定 [指派：組織定義的頻率] 的建立。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7c"></a>NIST 800-53 控制措施 CA-7.c

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.c** 組織會開發連續監視策略並實作連續監視計畫，其中包含與組織連續監視策略一致的持續性安全性控制措施評定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7d"></a>NIST 800-53 控制措施 CA-7.d

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.d** 組織會開發連續監視策略並實作連續監視計畫，其中包含與組織連續監視策略 一致，針對組織定義之計量的持續性安全性狀態監視。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7e"></a>NIST 800-53 控制措施 CA-7.e

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.e** 組織會開發連續監視策略並實作連續監視計畫，其中包含針對由評定和監視所產生的安全性相關資訊所進行的相互關聯與分析。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7f"></a>NIST 800-53 控制措施 CA-7.f

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.f** 組織會開發連續監視策略並實作連續監視計畫，其中包含處理安全性相關資訊之分析結果的回應動作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-7g"></a>NIST 800-53 控制措施 CA-7.g

#### <a name="continuous-monitoring"></a>連續監視

**CA-7.g** 組織會開發連續監視策略，並實作連續監視計畫，其中包含向 [指派：組織定義的個人或角色] 報告組織和資訊系統之安全性狀態的 [指派：組織定義的頻率]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可能足以處理此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-7-1"></a>NIST 800-53 控制措施 CA-7 (1)

#### <a name="continuous-monitoring--independent-assessment"></a>連續監視 | 獨立評定

**CA-7 (1)** 組織會雇用具有 [指派：組織定義的獨立層級] 的評定者或評定小組，以持續監視資訊系統中的安全性控制措施。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可以建立針對安全性控制措施之獨立評定的程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-7-3"></a>NIST 800-53 控制措施 CA-7 (3)

#### <a name="continuous-monitoring--trend-analyses"></a>持續監視 | 趨勢分析

**CA-7 (3)** 組織會運用趨勢分析以判斷安全性控制措施的實作、連續監視活動的頻率，以及/或是用於連續監視程序中的活動類型是否須根據經驗資料進行修改。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級連續監視計畫可以針對趨勢分析建立程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-8"></a>NIST 800-53 控制措施 CA-8

#### <a name="penetration-testing"></a>滲透測試

**CA-8** 組織會依 [指派：組織定義的頻率] 針對 [指派：組織定義的資訊系統或系統元件] 進行滲透測試。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可以仰賴企業層級滲透測試評定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ca-8-1"></a>NIST 800-53 控制措施 CA-8 (1)

#### <a name="penetration-testing--independent-penetration-agent-or-team"></a>滲透測試 | 獨立滲透代理人或小組

**CA-8 (1)** 組織會雇用獨立的滲透代理人或滲透小組，以在資訊系統或系統元件上執行滲透測試。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可以仰賴由獨立代理人或小組所執行的企業層級滲透測試評定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-9a"></a>NIST 800-53 控制措施 CA-9.a

#### <a name="internal-system-connections"></a>內部系統連線

**CA-9.a** 組織會授權 [指派：組織定義的資訊系統元件或元件類別] 針對資訊系統的內部連線。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責授權客戶部署資源上 (例如從系統連線至 VM) 的內部連線。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ca-9b"></a>NIST 800-53 控制措施 CA-9.b

#### <a name="internal-system-connections"></a>內部系統連線

**CA-9.b** 組織會針對每個內部連線，記錄介面特性、安全性需求，以及通訊資訊的性質。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責記錄於 CA-09.a 中定義的類別/資源之間所進行之每個內部連線的詳細資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |
