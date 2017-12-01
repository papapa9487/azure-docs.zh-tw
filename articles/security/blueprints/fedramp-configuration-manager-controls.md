---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 組態管理"
description: "適用於 FedRAMP 的 Web 應用程式 - 組態管理"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: e93aa430b7150f07210f5d1f37e2027d95334a59
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="configuration-management-cm"></a>組態管理 (CM)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 控制措施 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>組態管理原則和程序

**CM-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、職責、管理承諾、組織實體間之協調合作及合規性的組態管理原則，以及可協助實作組態管理原則和相關組態管理控制措施的程序；並檢閱和更新目前的組態管理原則 (依 [指派：組織定義的頻率]) 與組態管理程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級組態管理原則和程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 控制措施 CM-2

#### <a name="baseline-configuration"></a>基準組態

**CM-2** 組織會在組態控制措施下，開發、記載及維護資訊系統的目前基準組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 構成這個 Azure Blueprint (藍圖) 的 Azure Resource Manager 範本及隨附資源即代表所部署架構的「組態即程式碼」基準。 此解決方案會透過 GitHub 來提供，可用於組態控制。 此解決方案包含所部署之每個虛擬機器的 Desired State Configuration (DSC) 基準。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 控制措施 CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>基準組態 | 檢閱和更新

**CM-2 (1).a** 組織會依 [指派：組織定義的頻率] 來檢閱和更新資訊系統的基準組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱和更新客戶部署資源 (包含應用程式、作業系統、資料庫及軟體) 的基準組態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 控制措施 CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>基準組態 | 檢閱和更新

**CM-2 (1).b** 組織會在因 [指派：組織定義的情況] 而有必要時，依 [指派：組織定義的頻率] 來檢閱和更新資訊系統的基準組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在必要時，檢閱和更新客戶部署資源的基準組態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 控制措施 CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>基準組態 | 檢閱和更新

**CM-2 (1).c** 組織會在安裝和升級資訊系統元件時，一併檢閱和更新資訊系統的基準組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在必要時，檢閱和更新客戶部署資源的基準組態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 控制措施 CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>基準組態 | 正確性/流通性的自動化支援

**CM-2 (2)** 組織會採用自動化機制來維持最新、完整、正確且隨時可用的資訊系統基準組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 構成這個 Azure Blueprint (藍圖) 的 Azure Resource Manager 範本及隨附資源即代表所部署架構的「組態即程式碼」基準。 此解決方案會透過 GitHub 來提供，可用於組態控制。 在 Azure 入口網站中，會為所有部署的資源提供自動化指令碼，並一律呈現這些資源的最新狀態。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 控制措施 CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>基準組態 | 先前組態的保留

**CM-2 (3)** 組織會保留 [指派：組織定義的資訊系統舊版基準組態] 以支援復原。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責保留客戶部署資源的舊版基準組態。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 控制措施 CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>基準組態 | 設定高風險區域的系統、元件或裝置

**CM-2 (7).a** 組織會向出行到組織視為具有重大風險之地點的個人核發具備 [指派：組織定義的組態] 的 [指派：組織定義的資訊系統、系統元件或裝置]。

**責任：**`Not Applicable`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的實體裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 客戶內容一律不會儲存在 Microsoft Azure (實體位於美國本土內) 外部。 Microsoft Azure 人員不會攜帶 Microsoft Azure 詳細目錄內含的裝置出行。 因此，這項控制措施不適用於 Microsoft Azure。 |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 控制措施 CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>基準組態 | 設定高風險區域的系統、元件或裝置

**CM-2 (7).b** 組織會在個人返回時，於裝置套用 [指派：組織定義的安全性保護]。

**責任：**`Not Applicable`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的實體裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 客戶內容一律不會儲存在 Microsoft Azure 外部，且 Microsoft Azure 人員不會攜帶 Microsoft Azure 詳細目錄內含的裝置出行，因此，這項控制措施並不適用。 |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 控制措施 CM-3.a

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.a** 組織會決定由組態控制的資訊系統變更類型。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責決定哪些類型的客戶部署資源 (包含應用程式、作業系統、資料庫及軟體) 變更是由組態控制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 控制措施 CM-3.b

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.b** 組織會檢閱所提議由組態控制的資訊系統變更，然後依據明確的安全性影響分析考量來核准或拒絕這類變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱所提議由組態控制的客戶部署資源變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 控制措施 CM-3.c

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.c** 組織會記載與資訊系統關聯的組態變更決策。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責記載與客戶部署資源關聯、由組態控制的變更 (請參閱 CM-03.b)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 控制措施 CM-3.d

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.a** 組織會實作由組態控制的資訊系統變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責實作 CM-03.b 中所核准由組態控制的變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 控制措施 CM-3.e

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM 3.e** 組織會依 [指派：組織定義的時間週期] 來保留由組態控制的資訊系統變更記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責保留由組態控制的客戶部署資源變更記錄。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 控制措施 CM-3.f

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.f** 組織會稽核和檢閱與由組態控制之資訊系統變更關聯的活動。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責稽核和檢閱組態變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 控制措施 CM-3.g

#### <a name="configuration-change-control"></a>組態變更控制措施

**CM-3.g** 組織會透過依 [選取項目 (一或多個)：[指派：組織定義的頻率]；[指派：組織定義的組態變更條件]] 召集的 [指派：組織定義的組態變更控制單位 (例如委員會、董事會)] 來協調和提供組態變更控制活動的監督。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責協調和提供組態變更控制活動的監督。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 控制措施 CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).a** 組織會採用自動化機制來記載所提議的資訊系統變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來記載所提議的變更 (請參閱 CM-03.b)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 控制措施 CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).b** 組織會採用自動化機制來通知 [指派：組織定義的核准授權單位] 有關所提議的資訊系統變更，並要求核准該變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來路由傳送所提議的客戶部署資源變更，並要求核准該變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 控制措施 CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).c** 組織會採用自動化機制來醒目提示在 [指派：組織定義的時間週期] 之前尚未核准或拒絕的所提議資訊系統變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來醒目提示未檢閱的變更建議。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 控制措施 CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).d** 組織會採用自動化機制來禁止資訊系統變更，直到收到指定的核准為止。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來禁止實作未核准的客戶部署資源變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 控制措施 CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).e** 組織會採用自動化機制來記載資訊系統的所有變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來記載所有實作的客戶部署資源變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 控制措施 CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>組態控制變更 | 自動化記載/通知/禁止變更

**CM-3 (1).f** 組織會採用自動化機制，以在所核准的資訊系統變更完成時通知 [指派：組織定義的人員]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制，以在所核准的客戶部署資源變更完成時提供通知。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 控制措施 CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>組態變更控制 | 測試/驗證/文件變更

**CM-3 (2)** 組織會先測試、驗證及記載資訊系統變更，然後才在操作系統上實作變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責先測試、驗證及記載客戶部署資源變更，然後才進行實作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 控制措施 CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>組態變更控制措施 | 安全性代表

**CM-3 (4)** 組織會要求資訊安全性代表必須是 [指派：組織定義的組態變更控制單位] 的成員。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責指派一個資訊安全性代表來成為 CM-03.g 中所定義變更控制單位的成員。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 控制措施 CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>組態變更控制 | 密碼編譯管理

**CM-3 (6)** 組織會確保用來提供 [指派：組織定義的安全性保護] 的密碼編譯機制受到組態管理。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責確保該密碼編譯機制受到組態管理。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 控制措施 CM-4

#### <a name="security-impact-analysis"></a>安全性影響分析

**CM-4** 組織會先分析資訊系統變更來判斷潛在的安全性影響，然後才變更實作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責分析所提議的客戶部署資源變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 控制措施 CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>安全性影響分析 | 個別的測試環境

**CM-4 (1)** 組織會先在個別的測試環境中分析資訊系統變更，然後才在操作環境中進行實作，藉此找出因缺陷、弱點、不相容或蓄意惡意而造成的安全性影響。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責先在測試環境中分析所提議的客戶部署資源變更，然後才在操作環境中進行實作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 控制措施 CM-5

#### <a name="access-restrictions-for-change"></a>變更的存取限制

**CM-5** 組織會定義、記載、核准及強制執行與資訊系統變更關聯的實體和邏輯存取限制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Active Directory 帳戶權限會藉由使用角色型存取控制來實作，方法是將使用者指派給可嚴格控制哪些使用者能夠檢視和控制所部署資源的角色。 實作 Active Directory 帳戶權限時，會使用角色型存取控制將使用者指派給安全性群組。 這些安全性群組可控制使用者能夠對作業系統組態採取哪些動作。 客戶可以延伸這些角色型配置來滿足任務需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 控制措施 CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>變更的存取限制 | 自動化存取強制執行/稽核

**CM-5 (1)** 資訊系統會強制執行存取限制並支援稽核強制動作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Active Directory 帳戶權限會藉由使用角色型存取控制來實作，方法是將使用者指派給可嚴格控制哪些使用者能夠檢視和控制所部署資源的角色。 實作 Active Directory 帳戶權限時，會使用角色型存取控制將使用者指派給安全性群組。 這些安全性群組可控制使用者能夠對作業系統組態採取哪些動作。 系統會稽核所有存取和存取嘗試。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 控制措施 CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>變更的存取限制 | 檢閱系統變更

**CM-5 (2)** 組織會依 [指派：組織定義的頻率] 和 [指派：組織定義的情況] 來檢閱資訊系統變更，以判斷是否發生未經授權的變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱客戶部署資源變更，以判斷是否發生未經授權的變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 控制措施 CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>變更的存取限制 | 已簽署的元件

**CM-5 (3)** 如果未驗證是否已使用組織所認可及核准的憑證以數位方式簽署 [指派：組織定義的軟體和韌體元件]，資訊系統就會防止安裝該元件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的虛擬機器會實作 Windows AppLocker，以指定哪些使用者能夠安裝和/或執行特定的應用程式。 此外，還會以數位方式簽署所有 Windows 作業系統更新。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 控制措施 CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>變更的存取限制 | 限制生產環境/操作權限

**CM-5 (5).a** 組織會針對變更生產環境或操作環境內的資訊系統元件和系統相關資訊，限制權限。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責限制在客戶部署生產環境或操作環境內進行變更的權限。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 控制措施 CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>變更的存取限制 | 限制生產環境/操作權限

**CM-5 (5).b** 組織會依 [指派：組織定義的頻率] 來檢閱和重新評估權限。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱和重新評估 CM-05 (05).a 中所定義的權限。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 控制措施 CM-6.a

#### <a name="configuration-settings"></a>組態設定

**CM-6.a** 組織會使用反映與操作需求一致之最嚴格限制模式的 [指派：組織定義的安全性組態檢查清單]，來建立和記載資訊系統內所採用資訊技術產品的組態設定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 包含所部署之每個虛擬機器的 Desired State Configuration (DSC) 基準。 這些宣告式 PowerShell 指令碼會定義並設定作為其套用對象的資源。 客戶可以延伸針對此解決方案所開發之資源包含的基準 DSC 來滿足任務需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 控制措施 CM-6.b

#### <a name="configuration-settings"></a>組態設定

**CM-6.b** 組織會實作組態設定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 包含所部署之每個虛擬機器的 Desired State Configuration (DSC) 基準。 在部署期間，會使用自訂的指令碼虛擬機器擴充功能，將基準自動套用至虛擬機器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 控制措施 CM-6.c

#### <a name="configuration-settings"></a>組態設定

**CM-6.c** 組織會根據 [指派：組織定義的操作需求]，識別、記載及核准任何偏離已確立之 [指派：組織定義的資訊系統元件] 組態設定的情況。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責識別、記載及核准任何偏離已確立之客戶部署資源組態設定的情況。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 控制措施 CM-6.d

#### <a name="configuration-settings"></a>組態設定

**CM-6.d** 組織會根據組織的原則和程序，來監視和控制組態設定變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Automation DSC。 Automation DSC 會讓電腦組態與特定的組織定義組態一致。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 控制措施 CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>組態設定 | 自動化中央管理/應用程式/驗證

**CM-6 (1)** 組織會採用自動化機制來集中管理、套用及驗證 [指派：組織定義的資訊系統元件] 的組態設定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Azure Automation DSC。 Automation DSC 會讓電腦組態與特定的組織定義組態一致，並持續監視是否有變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 控制措施 CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>組態設定 | 因應未經授權的變更

**CM-6 (2)** 組織會採用 [指派：組織定義的安全性保護] 來因應未經授權的 [指派：組織定義的組態設定]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署 Azure Automation DSC。 部分 Azure Operations Management Suite (OMS) (即 Automation DSC) 可以設定成在偵測到設定錯誤時產生警示或進行補救。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 控制措施 CM-7.a

#### <a name="least-functionality"></a>最少的功能

**CM-7.a** 組織會將資訊系統設定成僅提供基本功能。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會設定成針對其預定的目的提供最少的功能。 針對所部署的每個虛擬機器都會包含一個 Desired State Configuration (DSC) 基準。 這些宣告式 PowerShell 指令碼會定義並設定作為其套用對象的資源。 客戶可以延伸針對此解決方案所開發之資源包含的基準 DSC，以進一步限制功能來滿足任務需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 控制措施 CM-7.b

#### <a name="least-functionality"></a>最少的功能

**CM-7.b** 組織會禁止或限制下列函式、連接埠、通訊協定和/或服務的使用：[指派：組織定義之禁止或限制的函式、連接埠、通訊協定和/或服務]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署「Azure 應用程式閘道」和網路安全性群組，以僅限將連接埠和通訊協定用於必要的功能。 客戶可以進一步設定虛擬機器的「應用程式閘道」、網路安全性群組及 DSC 基準，以僅限將函式、連接埠、通訊協定及服務用於提供預定的功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 控制措施 CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>最少的功能 | 定期檢閱

**CM-7 (1).a** 組織會依 [指派：組織定義的頻率] 來檢閱資訊系統，以識別不必要和/或不安全的函式、連接埠、通訊協定及服務。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱客戶部署的資源 (包含應用程式、作業系統、資料庫及軟體)，以識別不必要和/或不安全的函式、連接埠、通訊協定及服務。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 控制措施 CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>最少的功能 | 定期檢閱

**CM-7 (1).b** 組織會停用 [指派：資訊系統內被視為不必要和/或不安全的組織定義函式、連接埠、通訊協定及服務]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責停用被視為不必要或不安全的函式、連接埠、通訊協定及服務。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 控制措施 CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>最少的功能 | 防止程式執行

**CM-7 (2)** 資訊系統會根據 [選取項目 (一或多個)：[指派：與軟體程式使用和限制相關的組織定義原則]；授權軟體程式使用條款和條件的規則] 來防止程式執行。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責根據客戶定義的軟體程式使用原則，來防止程式執行。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 控制措施 CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 已授權的軟體/加入白名單

**CM-7 (5).a** 組織會識別 [指派：已獲授權在資訊系統上執行的組織定義軟體程式]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責識別已授權的軟體程式。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 控制措施 CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 已授權的軟體/加入白名單

**CM-7 (5).b** 組織會採用全部拒絕但依例外允許的原則，來允許在資訊系統上執行已授權的軟體程式。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用全部拒絕但依例外允許的原則，來允許在客戶部署的資源上執行已授權的軟體程式。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 控制措施 CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>最少的功能 | 已授權的軟體/加入白名單

**CM-7 (5).c** 組織會依 [指派：組織定義的頻率] 來檢閱和更新已授權的軟體程式清單。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱和更新已授權的軟體程式清單。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 控制措施 CM-8.a

#### <a name="information-system-component-inventory"></a>資訊系統元件詳細目錄

**CM-8.a** 組織會開發和記載資訊系統元件的詳細目錄，此詳細目錄會正確反映目前的資訊系統、包含 資訊系統授權界限內的所有元件、採用追蹤和報告所需的細微程度，並且包含 [指派：被視為達到有效資訊系統元件責任所需的組織定義資訊]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azure Resource Manager 會提供一個一律最新的已部署資源清單，並可經由自訂來將資源標記和分組以進行詳細目錄管理。 系統會給予此解決方案所部署的資源一個可與系統界限關聯的特定資源標記。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 控制措施 CM-8.b

#### <a name="information-system-component-inventory"></a>資訊系統元件詳細目錄

**CM-8.b** 組織會依 [指派：組織定義的頻率] 來檢閱和更新資訊系統詳細目錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azure Resource Manager 會提供一個一律最新、可供在 Azure 入口網站中檢閱的已部署資源清單。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 控制措施 CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>資訊系統元件詳細目錄 | 安裝/移除時的更新

**CM-8 (1)** 組織會在安裝、移除元件及更新資訊系統時，一併更新資訊系統元件的詳細目錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azure 入口網站中的資源刀鋒視窗會列出所有已部署的資源，提供一個一律反映最新資源部署和移除情況的詳細目錄。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 控制措施 CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>資訊系統元件詳細目錄 | 自動化維護

**CM-8 (2)** 組織會採用自動化機制來協助維持最新、完整、正確且隨時可用的資訊系統元件詳細目錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azure 入口網站中的資源刀鋒視窗會列出所有已部署的資源，提供一個一律反映最新資源部署和移除情況的詳細目錄。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 控制措施 CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>資訊系統元件詳細目錄 | 自動化的未經授權元件偵測

**CM-8 (3).a** 組織會依 [指派：組織定義的頻率]，採用自動化機制來偵測資訊系統內是否有未經授權的硬體、軟體及韌體元件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責採用自動化機制來偵測客戶部署的資源內是否有未經授權的軟體。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 控制措施 CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>資訊系統元件詳細目錄 | 自動化的未經授權元件偵測

**CM-8 (3).b** 組織會在偵測到未經授權的元件時採取下列動作：[選取項目 (一或多個)：停用這類元件的網路存取；隔離元件；通知 [指派：網路定義的人員或角色]]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在偵測到未經授權的軟體時採取動作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 控制措施 CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>資訊系統元件詳細目錄 | 責任資訊

**CM-8 (4)** 組織會在資訊系統元件詳細目錄資訊中包含一個可依 [選取項目 (一或多個)：名稱；位置；角色] 來識別負責管理這些元件之個人的方法。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azurey 資源標籤是可用來針對責任和/或管理目的進行資源分類的索引鍵/值組。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 控制措施 CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>資訊系統元件詳細目錄 | 無重複的元件記述

**CM-8 (5)** 組織會確認資訊系統授權界限內的所有元件都未重複出現在其他資訊系統元件詳細目錄中。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會將所有資源部署到 Azure Resource Manager 資源群組。 Azure Resource Manager 會提供一個一律最新的已部署資源清單。 系統會給予此解決方案所部署的資源一個可與系統界限關聯的特定資源標記。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 控制措施 CM-9.a

#### <a name="configuration-management-plan"></a>組態管理計畫

**CM-9.a** 組織會針對資訊系統開發、記載及實作組態管理計畫，用以處理角色、職責及組態管理流程和程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源開發、記載及實作組態管理計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 控制措施 CM-9.b

#### <a name="configuration-management-plan"></a>組態管理計畫

**CM-9.b** 組織會針對資訊系統開發、記載及實作組態管理計畫，此組態管理計畫會建立程序來識別整個系統開發生命週期的組態項目，以及管理組態項目的組態。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源開發、記載及實作組態管理計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 控制措施 CM-9.c

#### <a name="configuration-management-plan"></a>組態管理計畫

**CM-9.c** 組織會針對資訊系統開發、記載及實作組態管理計畫，用以定義資訊系統的組態項目，以及讓組態項目受到組態管理。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源開發、記載及實作組態管理計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 控制措施 CM-9.d

#### <a name="configuration-management-plan"></a>組態管理計畫

**CM-9.d** 組織會針對資訊系統開發、記載及實作組態管理計畫，用以防止組態管理計畫遭受未經授權的公開和修改。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責針對客戶部署的資源開發、記載及實作組態管理計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 控制措施 CM-10.a

#### <a name="software-usage-restrictions"></a>軟體使用限制

**CM-10.a** 組織會根據合約協議和著作權法來使用軟體及相關文件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 針對這個 Azure Blueprint (藍圖) 所部署的資源會包含 Windows 和 SQL Server 授權。 這是內建的 Azure 功能。 具備現有軟體授權合約的組織可以考慮部署替代的授權模型。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 控制措施 CM-10.b

#### <a name="software-usage-restrictions"></a>軟體使用限制

**CM-10.b** 組織會追蹤受數量授權保護之軟體及相關文件的使用情況，以控制複製和散發。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 針對這個 Azure Blueprint (藍圖) 所部署的資源會包含 Windows 和 SQL Server 授權。 使用者並不需要個別追蹤授權的使用情況。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 控制措施 CM-10.c

#### <a name="software-usage-restrictions"></a>軟體使用限制

**CM-10.c** 組織會控制和記載對等檔案共用技術的使用情況，以確保此功能未用於未經授權的散發、展示、執行或著作權作品重製。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 未部署任何對等檔案共用功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 控制措施 CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>軟體使用限制 | 開放原始碼軟體

**CM-10 (1)** 組織會在開放原始碼軟體的使用上確立下列限制：[指派：組織定義的限制]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級組態管理原則可能會處理開放原始碼軟體使用上的限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 控制措施 CM-11.a

#### <a name="user-installed-software"></a>使用者安裝的軟體

**CM-11.a** 組織會建立管理使用者進行之軟體安裝的 [指派：組織定義的原則]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責建立管理使用者在客戶部署資源上進行之軟體安裝的原則。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 控制措施 CM-11.b

#### <a name="user-installed-software"></a>使用者安裝的軟體

**CM-11.b** 組織會透過 [指派：組織定義的方法] 來強制執行軟體安裝原則。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責強制執行軟體安裝原則。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 控制措施 CM-11.c

#### <a name="user-installed-software"></a>使用者安裝的軟體

**CM-11.c** 組織會依 [指派：組織定義的頻率] 來監視是否符合原則規範。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責監視客戶部署的資源是否符合 CM-11.a 中所識別的原則規範。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 控制措施 CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>使用者安裝的軟體 | 未經授權之安裝的警示

**CM-11 (1)** 資訊系統會在偵測到未經授權的軟體安裝時，對 [指派：組織定義的人員或角色] 發出警示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責在偵測到未經授權的軟體安裝時提供警示。 |
| **提供者 (Microsoft Azure)** | 不適用 |

