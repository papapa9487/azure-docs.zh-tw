---
title: "FedRAMP Azure Blueprint 自動化 - 存取控制"
description: "FedRAMP 的 Web 應用程式- 存取控制"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>存取控制 (AC)

> [!NOTE]
> 這些控制措施是由 NIST 和美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指引，請參閱 NIST 800-53 第 4 版。

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 控制措施 AC-1

#### <a name="access-control-policy-and-procedures"></a>存取控制原則和程序

**AC-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、職責、管理承諾、組織實體間之協調合作及規範遵循的存取控制原則，以及可協助實作存取控制原則和相關存取控制措施的程序；並檢閱和更新目前的存取控制原則 [指派：組織定義的頻率] 與存取控制程序 [指派：組織定義的頻率]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則與程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 控制措施 AC-2.a

#### <a name="account-management"></a>帳戶管理

**AC-2.a** 組織會找出並選取 下列類型的資訊系統帳戶，以支援組織的任務/商務函式：[指派：組織定義的資訊系統帳戶類型]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Blueprint 會仰賴並實作下列系統帳戶類型：Azure Active Directory 使用者 (用來部署解決方案及管理 Azure 資源的存取權)、Windows OS 使用者 (由 Active Directory 管理)、SQL Server 服務帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 控制措施 AC-2.b

#### <a name="account-management"></a>帳戶管理

**AC-2.b** 組織會指派資訊系統帳戶的帳戶管理員。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責將管理員指派給 AC-02.a 中識別的帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 控制措施 AC-2.c

#### <a name="account-management"></a>帳戶管理

**AC-2.c** 組織會建立群組和角色成員資格的條件。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶會負責建立客戶控制帳戶類型 (請參閱 AC-02.a) 的角色和群組成員資格準則。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 控制措施 AC-2.d

#### <a name="account-management"></a>帳戶管理

**AC-2.d** 組織會針對每個帳戶指定資訊系統的授權使用者、群組與角色成員資格和存取授權 (也就是權限) 和其他屬性 (視需要)。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴已建立的企業層級帳戶授權程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 控制措施 AC-2.e

#### <a name="account-management"></a>帳戶管理

**AC-2.e** 組織需要 [指派：組織定義的人員或角色] 的核准，才能要求建立資訊系統帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴已建立的企業層級帳戶授權程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 控制措施 AC-2.f

#### <a name="account-management"></a>帳戶管理

**AC-2.f** 組織會按照 [指派：組織定義的程序或條件] 來建立、啟用、修改、停用並移除資訊系統帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴已建立的企業層級帳戶管理程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 控制措施 AC-2.g

#### <a name="account-management"></a>帳戶管理

**AC-2.g** 組織會監視資訊系統帳戶的使用。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作「OMS 安全性與稽核」解決方案的身分識別和存取儀表板。 此儀表板可讓帳戶管理員監視資訊系統帳戶的使用。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 控制措施 AC-2.h

#### <a name="account-management"></a>帳戶管理

**AC-2.h** 組織會在不再需要帳戶、使用者終止或傳送，以及個別資訊系統使用量或須知變更時，通知帳戶管理員。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可以建立流程，當不再需要帳戶時通知適當的帳戶管理員。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 控制措施 AC-2.i

#### <a name="account-management"></a>帳戶管理

**AC-2.i** 組織會視組織或相關聯之任務/商務函式的需要，以有效的存取授權、目標的系統使用狀況及其他屬性作為基礎，將存取權授權給資訊系統。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立存取授權流程。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 控制措施 AC-2.j

#### <a name="account-management"></a>帳戶管理

**AC-2.j** 組織會檢閱帳戶是否符合帳戶管理需求 [指派：組織定義的頻率]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶需負責以要求的頻率檢閱客戶控制的帳戶，以判斷帳戶是否符合組織需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 控制措施 AC-2.k

#### <a name="account-management"></a>帳戶管理

**AC-2.k** 當個人從群組中移除時，組織會建立程序來重新發出共用/群組帳戶認證 (如有部署)。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立管理群組帳戶認證的流程。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 控制措施 AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>帳戶管理 | 自動化系統帳戶管理

**AC-2 (1)** 組織會使用自動化機制來支援管理資訊系統帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作「OMS 安全性與稽核」解決方案的身分識別和存取儀表板。 此儀表板可讓帳戶管理員監視資訊系統帳戶的使用。 OMS 可以設定為在疑似非典型活動或其他預先定義事件發生時傳送警示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 控制措施 AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>帳戶管理 | 移除暫存 / 緊急帳戶

**AC-2 (2)** 資訊系統在 [指派：組織針對每個類型帳戶定義的時間週期] 之後，會自動 [選取項目：移除、停用] 暫存和緊急帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 不會部署暫存或緊急帳戶。 如果未以手動方式停用，已部署的網域控制站就會在 35 天後自動停用所有非使用中的帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 控制措施 AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>帳戶管理 | 停用非使用中的帳戶

**AC-2 (3)** 資訊系統在 [指派：組織定義的時間週期] 之後，會自動停用非使用中的帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 所部署的網域控制站設定為在非使用狀態 35 天後停用所有使用者帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 控制措施 AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>帳戶管理 | 自動化稽核動作

**AC-2 (4)** 資訊系統會自動稽核帳戶建立、修改、啟用、停用及移除動作和通知 [指派：組織定義的人員或角色]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作下列系統帳戶類型：Azure Active Directory 使用者、Windows OS 使用者、SQL Server 服務帳戶。 Azure Active Directory 帳戶管理動作會在 Azure 活動記錄中產生事件；OS 層級帳戶管理動作會在系統記錄中產生一個事件。 這些記錄是由 Log Analytics 所收集，並且會儲存在 OMS 存放庫中。 OMS 可以設定為當預先定義的事件發生時傳送警示。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 控制措施 AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>帳戶管理 | 非使用狀態之登出

**AC-2 (5)** 當 [指派：組織定義的預期非使用狀態週期或何時登出的描述] 時，組織會要求使用者登出。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會建立一個原則，當使用者預期一段時間為非作用中 (或其他因素) 時就會登出。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 控制措施 AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>帳戶管理 | 角色型配置

**AC-2 (7).a** 組織會依據將允許的資訊系統存取權和權限組織到角色的角色型存取配置，來建立並管理具特殊權限的使用者帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作下列系統帳戶類型：Azure Active Directory 使用者、Windows OS 使用者、SQL Server 服務帳戶。 Azure Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作；Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作。 客戶可以延伸這些角色型配置來滿足任務需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 控制措施 AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>帳戶管理 | 角色型配置

**AC-2 (7).b** 組織會監視特殊權限角色指派。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作「OMS 安全性與稽核」解決方案的身分識別和存取儀表板。 此儀表板可讓帳戶管理員監視資訊系統帳戶的使用。 可以查詢這個解決方案來報告特殊權限角色指派。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 控制措施 AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>帳戶管理 | 角色型配置

**AC-2 (7).c** 當具特殊權限的角色指派不再適用時，組織會採取 [指派：組織定義的動作]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 當特殊權限角色不再適當時，客戶需負責對客戶控制的帳戶採取動作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 控制措施 AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>帳戶管理 | 使用共用 / 群組帳戶的限制

**AC-2 (9)** 組織只允許使用符合 [指派：組織定義要建立共用/群組帳戶的條件] 的共用/群組帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 所部署的資源上未啟用共用/群組帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 控制措施 AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>帳戶管理 | 共用 / 群組帳戶認證終止

**AC-2 (10)** 成員離開群組時，資訊系統會終止共用/群組帳戶認證。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 所部署的資源上未啟用共用/群組帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 控制措施 AC-2 (11)

#### <a name="account-management--usage-conditions"></a>帳戶管理 | 使用狀況

**AC-2 (11)** 資訊系統會強制 [指派：組織定義情況及/或使用條件] 用於 [指派：組織定義的資訊系統帳戶]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則可能會在 Active Directory 中建立並設定，以實作日期時間限制或其他帳戶的使用狀況。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 控制措施 AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>帳戶管理 | 帳戶監視 / 非典型的使用方式

**AC-2 (12).a** 組織會監視 [指派：組織定義的非典型使用方式] 的資訊系統帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作「OMS 安全性與稽核」解決方案的身分識別和存取儀表板。 此儀表板可讓帳戶管理員監視嘗試存取已部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 控制措施 AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>帳戶管理 | 帳戶監視 / 非典型的使用方式

**AC-2 (12).b** 組織會向 [指派：組織定義的人員或角色] 報告資訊系統帳戶的使用方式。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作「OMS 安全性與稽核」解決方案的身分識別和存取儀表板。 此儀表板可讓帳戶管理員監視嘗試存取已部署的資源。 這個解決方案可以設定為在疑似非典型活動或其他預先定義事件發生時傳送警示。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 控制措施 AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>帳戶管理 | 停用高風險個人版的帳戶

**AC-2 (13)** 組織在探索到風險的 [指派：組織定義時間週期] 內，會停用暴露於極大風險的使用者帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則和程序可能會建立一些條件，來停用對組織暴露於極大風險之使用者的帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 控制措施 AC-3

#### <a name="access-enforcement"></a>強制存取

**AC-3** 資訊系統會依據適用的存取控制原則，強制執行邏輯存取資訊和系統資源的已核准授權。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會藉由將使用者指派至角色，使用 Azure Active Directory 所強制的角色型存取控制來強制邏輯存取授權，Active Directory 是將使用者指派給安全性群組和 Windows OS 層級控制。 指派給使用者或群組的 Azure Active Directory 角色可以資源、群組或訂用帳戶層級控制 Azure 內的資源邏輯存取。 Active Directory 安全性群組可控制 OS 層級之資源和函式的邏輯存取。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 控制措施 AC-4

#### <a name="information-flow-enforcement"></a>資訊流程強制

**AC-4** 資訊系統會以 [指派：組織定義的資訊流程控制原則] 作為基礎強制執行核准的授權，來控制系統內及互連式系統之間的資訊流程。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會透過使用套用至當中部署資源的子網路、應用程式閘道和負載平衡器的網路安全性群組，來強制執行資訊流程限制。 網路安全性群組會以核准的規則作為基礎，確保資源之間的資訊流程受到控制。 應用程式閘道和負載平衡器會以核准的角色作為基礎，將流量動態路由傳送到特定的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 控制措施 AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>資訊流程強制 | 安全性原則篩選器

**AC-4 (8)** 資訊系統會使用 [指派：組織定義的安全性原則篩選條件] 作為 [指派：組織定義的資訊流程] 的流程控制決定基礎，來強制執行資訊流程控制。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內強制執行資訊流程控制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 控制措施 AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>資訊流程強制 | 資訊流程的實體 / 邏輯區分

**AC-4 (21)** 資訊系統會使用 [指派：組織定義的機制和/或技術] 來完成 [指派：組織定義依類型資訊的必要區分]，以邏輯或實體方式區分資訊流程。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內區隔資訊流程。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 控制措施 AC-5.a

#### <a name="separation-of-duties"></a>職責區分

**AC-5.a** 組織會區分 [指派：組織定義的個人職責]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶控制帳戶之間的責任區隔。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 控制措施 AC-5.a

#### <a name="separation-of-duties"></a>職責區分

**AC-5.b** 組織會記錄個人責任區分。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責記錄客戶控制帳戶之間的責任區隔。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 控制措施 AC-5.c

#### <a name="separation-of-duties"></a>職責區分

**AC-5.c** 組織會定義資訊系統存取授權來支援責任區分。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作角色型存取控制，可根據組織需求設定為個別責任。 Azure Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作；Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 控制措施 AC-6

#### <a name="least-privilege"></a>最小特殊權限

**AC-6** 組織會使用最小權限的準則，依據組織的任務和商務函式，僅允許完成指派之工作的必要使用者 (或代表使用者處理的處理序) 取得授權的存取。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作角色型存取控制，限制使用者只能使用明確指派的權限。 Azure Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作；Active Directory 帳戶權限是透過將使用者指派至角色，使用角色型存取控制來實作。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 控制措施 AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>最小權限 | 授權存取安全性函式

**AC-6 (1)** 組織會明確授與 [指派：組織定義的安全性函式 (部署在硬體、軟體和韌體中) 和安全性相關資訊] 的存取權。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立包含安全性函式存取權的存取授權流程。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 控制措施 AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>最小權限 | 非安全性函式的非特殊權限存取

**AC-6 (2)** 組織要求具有 [指派：組織定義的安全性函式或安全性相關資訊] 存取權的資訊系統帳戶或角色之使用者在存取非安全性函式時，使用非特殊權限的帳戶或角色。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則在存取非安全性函式時，可能需要使用者使用非特殊權限的帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 控制措施 AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>最小權限 | 網路存取具有特殊權限的命令

**AC-6 (3)** 組織僅授權 [指派：組織定義的強制作業需求] 的 [指派：組織定義的特殊權限命令] 網路存取權，並在資訊系統的安全性計畫中記錄這類存取的基本原理。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會定義可透過網路存取的特殊權限命令。 注意：客戶無法實體存取 Azure 基礎結構。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 控制措施 AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>最小權限 | 具有特殊權限的帳戶

**AC-6 (5)** 組織會限制資訊系統上 [指派：組織定義的人員或角色] 的特殊權限帳戶。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會定義使用特殊權限帳戶的限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 控制措施 AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>最小權限 | 檢閱使用者權限

**AC-6 (7).a** 組織會 [指派：組織定義的頻率] 檢閱指派給 [指派：組織定義的角色或使用者的類別] 的權限，來驗證這類權限的需求。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責檢閱客戶控制帳戶的使用者權限。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 控制措施 AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>最小權限 | 檢閱使用者權限

**AC-6 (7).b** 組織會重新指派或移除權限 (如果有必要)，請以正確反映組織的任務/商務需求。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶要負責在適當時重新指派或移除客戶控制帳戶的權限。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 控制措施 AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>最小權限 | 程式碼執行的權限層級

**AC-6 (8)** 資訊系統可防止 [指派：組織定義的軟體] 比執行軟體之使用者執行更高的權限等級。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作角色型存取控制，限制使用者只能使用明確指派的權限。 虛擬機器 OS 層級保護不允許軟體執行比使用者執行軟體更高之權限層級。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 控制措施 AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>最小權限 | 稽核具有特殊權限的函式使用

**AC-6 (9)** 資訊系統會稽核具特殊權限函式的執行。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作 OMS 中的 Log Analytics 服務。 部署 VM 和 Azure 診斷儲存體帳戶是連線到 Log Analytics 的來源，可確保具有特殊權限的函式執行時受到稽核。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 控制措施 AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>最小權限 | 禁止非特殊權限使用者執行具有特殊權限的函式

**AC-6 (10)** 資訊系統可防止非特殊權限使用者執行具特殊權限的函式，以包括停用、規避，或改變實作的安全性保護措施/因應對策。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會實作角色型存取控制，限制使用者只能使用明確指派的權限。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 控制措施 AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>不成功的登入嘗試

**AC-7.a** 資訊系統在 [指派：組織定義的時間週期] 期間，會強制執行使用者連續無效登入 [指派：組織定義的數字] 次的限制。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | Azure 入口網站會限制使用者連續無效的登入嘗試。 作業系統層級的群組原則適用於此 Azure Blueprint 所部署的所有虛擬機器。 原則會限制使用者連續無效的登入嘗試在 15 分鐘期間內不超過三次。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 控制措施 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>不成功的登入嘗試

**AC-7.b** 資訊系統會在超過失敗嘗試次數上限時，自動 [選取項目：鎖定 [指派：組織定義的時間週期] 的帳戶/節點；鎖定帳戶/節點直到系統管理員釋放為止；根據 [指派：組織定義的延遲演算法] 延遲下一次登入提示]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 使用者連續無效的登入嘗試之後，Azure 入口網站就會鎖定帳戶。 作業系統層級的群組原則適用於此 Azure Blueprint 所部署的所有虛擬機器。 使用者連續三次無效的登入嘗試之後，原則會鎖定帳戶三個小時。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 控制措施 AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>不成功的登入嘗試 | 清除/抹除行動裝置

**AC-7 (2)** 資訊系統在連續 [指派：組織定義的數字] 次登入裝置失敗之後，會以 [指派：組織定義的清除/抹除需求/技術] 作為基礎，從 [指派：組織定義的行動裝置] 清除/抹除資訊。

**職責：** `Not Applicable`

|||
|---|---|
| **客戶** | 行動裝置不在 Azure 上部署的系統範圍內。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Azure 界線內的行動裝置。 因此，這項控制措施不適用於 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 控制措施 AC-8.a

#### <a name="system-use-notification"></a>系統使用通知

**AC-8.a** 資訊系統會先行向使用者顯示 [指派：組織定義的系統使用通知訊息或橫幅]，而後向提供符合適用之聯邦法律、行政命令、指令、原則、規定、標準和指導方針的隱私權和安全性聲明之系統授與存取權，以及使用者存取美國政府資訊系統之陳述；資訊系統使用方式可能會受到監視、錄音且受限於稽核；禁止在未經授權下使用資訊系統，且須遵守民事和刑事處分；使用資訊系統即表示同意監視及錄音。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則會實作系統使用通知，使用者在登入前就會看到。 注意：Azure Blueprint 會實作範例系統使用通知。 客戶必須編輯此文字以符合組織及/或法規的主體需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 控制措施 AC-8.b

#### <a name="system-use-notification"></a>系統使用通知

**AC-8.b** 資訊系統會保留通知訊息或螢幕上的橫幅，直到使用者確認使用狀況，並採取明確動作登入或進一步存取資訊系統。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則會實作系統使用通知，使用者在登入前就會看到。 使用者必須確認通知才能登入。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 控制措施 AC-8.c

#### <a name="system-use-notification"></a>系統使用通知

**AC-8.c** 可公開存取系統之資訊系統在授與進一步存取之前，會顯示系統使用資訊 [指派：組織定義的條件]；顯示符合通常會禁止這些活動這類系統的隱私權需求之參考 (若有的話) 監視、錄音或稽核；且包含系統之授權用途的描述。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責顯示所有可公開存取之客戶部署資源上的系統使用通知。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 控制措施 AC-10

#### <a name="concurrent-session-control"></a>並行工作階段控制項

**AC-10** 資訊系統會將每個 [指派：組織定義的帳戶和/或帳戶類型] 的並行工作階段數目限制為 [指派：組織定義的數字]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 作業系統原則是由這個 Azure Blueprint 部署之虛擬機器所實作。 原則會實作並行工作階段限制 (兩個工作階段)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 控制措施 AC-11.a

#### <a name="session-lock"></a>工作階段鎖定

**AC-11.a** 資訊系統在非使用狀態 [指派：組織定義的時間週期] 之後，或接收到來自使用者之要求時，會起始工作階段鎖定，從而防止進一步存取系統。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則會實作 RDP 工作階段的非使用狀態鎖定。 使用者可以手動起始鎖定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 控制措施 AC-11.b

#### <a name="session-lock"></a>工作階段鎖定

**AC-11.b** 資訊系統會保留工作階段鎖定，直到使用者使用識別與驗證程序重新建立存取為止。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則會實作 RDP 工作階段的非使用狀態鎖定。 使用者必須重新驗證才能解除鎖定工作階段。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 控制措施 AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>工作階段鎖定 | 模式隱藏顯示

**AC-11 (1)** 資訊系統會透過工作階段鎖定，將先前在可公開檢視影像上顯示的資訊隱藏。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint 會部署一部網域控制站，以加入所有已部署的虛擬機器。 群組原則會實作 RDP 工作階段的非使用狀態鎖定。 工作階段鎖定會隱藏先前顯示的資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 控制措施 AC-12

#### <a name="session-termination"></a>工作階段終止

**AC-12** 資訊系統在 [指派：組織定義的需要工作階段中斷連線之條件或觸發事件] 之後，會自動終止使用者工作階段。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 您可以設定由這個 Azure Blueprint 所部署之 Windows 虛擬機器的遠端桌面工作階段主機組態，來符合組織工作階段終止要求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 控制措施 AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>工作階段終止 | 使用者起始登出 / 訊息顯示

**AC-12 (1).a** 每當使用驗證來取得 [指派：組織定義的資訊資源] 之存取權時，資訊系統會針對使用者起始的通訊工作階段提供登出功能。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 所部署的 Azure 入口網站和虛擬機器作業系統可讓使用者起始登出。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 控制措施 AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>工作階段終止 | 使用者起始登出 / 訊息顯示

**AC-12 (1).b** 資訊系統會顯示明確登出訊息給使用者，指出可靠終止已驗證的通訊工作階段。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 所部署的 Azure 入口網站和虛擬機器作業系統可讓使用者起始登出。 登出程序會提供指示給使用者，告知工作階段已終止。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 控制措施 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>已允許動作，而不需要識別或驗證

**AC-14.a** 組織會識別無需符合組織任務/商務函式之識別或驗證，即可在資訊系統上執行的 [指派：組織定義的使用者動作]。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶要負責找出可在客戶部署資源上執行，而不需要識別或驗證 (例如，檢視可公開存取的網頁) 的動作。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 控制措施 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>已允許動作，而不需要識別或驗證

**AC-14.b** 組織會在資訊系統的安全性計劃中記錄並提供支援的基本原理，不需要識別或驗證使用者動作。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責提供不需要在客戶部署資源上識別或驗證的使用者動作文件。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 控制措施 AC-17.a

#### <a name="remote-access"></a>遠端存取

**AC-17.a** 組織會建立和記錄每一種允許的遠端存取之使用方式限制、組態/連線需求和實作指引。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會定義遠端存取使用限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 控制措施 AC-17.b

#### <a name="remote-access"></a>遠端存取

**AC-17.b** 組織在允許這類連線之前，會授權資訊系統的遠端存取權。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立遠端存取授權流程。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 控制措施 AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>遠端存取 | 自動化監視 / 控制

**AC-17 (1)** 資訊系統會監視並控制遠端存取方法。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會透過 Azure 入口網站、經由 jumpbox 透過遠端桌面連線，以及透過客戶實作的 web 應用程式，提供資訊系統的遠端存取權。 透過 Azure 入口網站和遠端桌面工作階段進行存取會受到稽核，並可透過 OMS 加以監視。 在必要時，客戶必須實作對 web 應用程式的遠端存取控制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 控制措施 AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>遠端存取 | 使用加密保護機密性 / 完整性

**AC-17 (2)** 資訊系統會實作密碼編譯機制，以保護遠端存取工作階段的機密性與完整性。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 使用 TLS 來保護遠端存取此 Azure Blueprint 所部署的資源 (包括 Azure 入口網站、遠端桌面連線和 web 應用程式閘道)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 控制措施 AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>遠端存取 | 受管理的存取控制點

**AC-17 (3)** 資訊系統會透過 [指派：組織定義的數字] 受管理網路存取控制點路由傳送所有的遠端存取。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 遠端存取此 Azure Blueprint 所部署的假想 web 應用程式是透過應用程式閘道進行。 會透過 jumpbox 對所有其他資源進行遠端存取。 沒有任何其他可公開存取的端點。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 控制措施 AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>遠端存取 | 具有特殊權限的命令 / 存取

**AC-17 (4).a** 組織僅針對 [指派：組織定義的需求]，透過遠端存取授權執行具特殊權限的命令和與安全性相關資訊的存取權。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會定義可遠端存取且包含基本原理的特殊權限命令。 注意：客戶無法直接存取網路 Azure 基礎結構。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 控制措施 AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>遠端存取 | 具有特殊權限的命令 / 存取

**AC-17 (4).b** 組織會記錄資訊系統之安全性計畫中這類存取的基本原理。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會定義可遠端存取且包含基本原理的特殊權限命令。 注意：客戶無法直接存取網路 Azure 基礎結構。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 控制措施 AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>遠端存取 | 中斷連線/停用存取

**AC-17 (9)** 組織在 [指派：組織定義的時間週期] 內，會提供迅速地中斷連線或停用遠端存取資訊系統的功能。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint 會透過 Azure 入口網站、經由 jumpbox 透過遠端桌面連線以及透過 web 應用程式，提供資訊系統的遠端存取權。 如果 Azure Active Directory 帳戶已停用或移除，就會立即中斷連線 Azure 入口網站存取。 同樣地，如果虛擬機器 OS 層級帳戶已停用或移除，就會立即中斷透過 jumpbox 的遠端桌面存取。 客戶必須實作 web 應用程式的遠端存取中斷連線。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 控制措施 AC-18.a

#### <a name="wireless-access"></a>無線存取

**AC-18.a** 組織會建立無線存取之使用方式限制、組態/連線需求和實作指引。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會針對透過明確禁止在 Microsoft Azure 環境中使用無線之「網路安全性標準」的無線存取，建立使用限制、組態/連線需求和實作指引。 |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 控制措施 AC-18.b

#### <a name="wireless-access"></a>無線存取

**AC-18.b** 組織在允許這類連線之前，會授權資訊系統的無線存取權。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許 Microsoft Azure 資料中心內的無線存取。 |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 控制措施 AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>無線存取 | 驗證與加密

**AC-18 (1)** 資訊系統會使用 [選取項目 (一個或多個)：使用者；裝置] 的驗證和加密，保護對系統的無線存取。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Microsoft Azure 環境內的無線存取。 |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 控制措施 AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>無線存取 | 停用無線網路

**AC-18 (3)** 組織在不適合使用時，會在發行和部署之前，內部停用內嵌在資訊系統元件內的無線網路功能。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Microsoft Azure 環境內的無線存取。 |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 控制措施 AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>無線存取 | 限制使用者設定

**AC-18 (4)** 組織會識別並明確授權允許獨立設定無線網路功能的使用者。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Microsoft Azure 環境內的無線存取。 |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 控制措施 AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>無線存取 | 天線 / 傳輸功率層級

**AC-18 (5)** 組織會選取收音機天線並校準傳輸功率等級，來減少在組織控制界限以外接收可用訊號的機率。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何無線存取。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Microsoft Azure 環境內的無線存取。 |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 控制措施l AC-19.a

#### <a name="access-control-for-mobile-devices"></a>適用於行動裝置的存取控制措施

**AC-19.a** 組織會建立組織控制行動裝置之使用方式限制、組態需求、連線需求和實作指引。

**職責：** `Not Applicable`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的行動裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Azure 界線內的行動裝置。 因此，這項控制措施不適用於 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 控制措施 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>適用於行動裝置的存取控制措施

**AC-19.b** 組織會授權行動裝置連線至組織的資訊系統。

**職責：** `Not Applicable`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的行動裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Azure 界線內的行動裝置。 因此，這項控制措施不適用於 Microsoft Azure。 |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 控制措施 AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>行動裝置的存取控制 | 完整的裝置 / 容器型加密

**AC-19 (5)** 組織會使用 [選取項目：完整裝置加密；容器加密] 來保護 [指派：組織定義的行動裝置] 上的資訊機密性與完整性。

**職責：** `Not Applicable`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的行動裝置。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許在 Azure 界線內的行動裝置。 因此，這項控制措施不適用於 Microsoft Azure。 |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 控制措施 AC-20.a

#### <a name="use-of-external-information-systems"></a>使用外部資訊系統

**AC-20.a** 組織會建立條款與條件，符合與其他組織擁有、作業、和/或維護外部資訊系統所建立之任何信任關係，可讓授權之個人存取外部資訊系統的資訊系統。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會包含有關使用 FedRAMP 底下的雲端服務供應項目之佈建。 FedRAMP 聯合授權委員會 (JAB) 已啟用由政府機關擷取使用之 Azure 雲端服務，向 Azure 授與佈建授權運作 (P-ATO)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 控制措施 AC-20.b

#### <a name="use-of-external-information-systems"></a>使用外部資訊系統

**AC-20.b** 組織會建立條款與條件，符合與其他組織擁有、作業、和/或維護外部資訊系統所建立之任何信任關係，可讓授權的個人使用外部資訊系統處理、儲存或傳輸組織控制的資訊。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會包含有關使用 FedRAMP 底下的雲端服務供應項目之佈建。 FedRAMP 聯合授權委員會 (JAB) 已啟用由政府機關擷取使用之 Azure 雲端服務，向 Azure 授與佈建授權運作 (P-ATO)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 控制措施 AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>使用外部資訊系統 | 授權使用的限制

**AC-20 (1)** 僅當組織如組織的資訊安全性原則和安全性計劃所指定，驗證外部系統上所需安全性控制項之實作；或保留已核准的資訊系統連線或處理協議利用裝載外部資訊系統的組織實體時，才組織允許已授權之個人使用外部資訊系統來存取資訊系統，或是處理、儲存或傳輸組織控制資訊。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級資訊技術群組可能會確認雲端服務提供者符合組織資訊安全性需求，並授與整個企業之核准，才可使用相關聯的雲端服務供應項目。 FedRAMP 聯合授權委員會 (JAB) 已向 Azure 授與佈建授權運作 (P-ATO)。 Azure 是由 FedRAMP 核准之第三方評定組織 (3PAO) 進行評估，驗證符合 FedRAMP 安全性控制項和其他需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 控制措施 AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>使用外部資訊系統 | 可攜式儲存裝置

**AC-20 (2)** 組織 [選取項目：限制；禁止] 使用組織控制的可攜式存放裝置，方法為授權外部資訊系統上的個人。

**職責：** `Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft 不允許在 Microsoft Azure 環境內的客戶控制可攜式存放裝置。 |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 控制措施 AC-21.a

#### <a name="information-sharing"></a>資訊共用

**AC-21.a** 組織會讓授權的使用者判斷指派給共用夥伴的存取授權是否符合 [指派：組織定義的資訊在需要使用者自行決定之位置的情況下共用] 的資訊上之存取限制，從而加速資訊共用。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制原則可能會包含有關資訊共用的佈建。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 控制措施 AC-21.b

#### <a name="information-sharing"></a>資訊共用

**AC-21.b** 組織會使用 [指派：組織定義的自動化機制或手動程序] 來協助使用者進行資訊共用/共同作業決策。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴企業層級資訊共用決策支援功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 控制措施 AC-22.a

#### <a name="publicly-accessible-content"></a>可公開存取的內容

**AC-22.a** 組織會指定個人授權張貼資訊到可公開存取的資訊系統。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會指定個人授權張貼可公開存取的資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 控制措施 AC-22.b

#### <a name="publicly-accessible-content"></a>可公開存取的內容

**AC-22.b** 組織會定型已授權的個人，確保可公開存取的資訊不包含非公用的資訊。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴個人授權張貼可公開存取資訊的企業層級定型。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 控制措施 AC-22.c

#### <a name="publicly-accessible-content"></a>可公開存取的內容

**AC-22.c** 組織在貼至可公開存取的資訊系統之前，會檢閱資訊的建議內容，以確保不包含非公用的資訊。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立內容檢閱程序，建議要張貼可公開存取的系統。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 控制措施 AC-22.d

#### <a name="publicly-accessible-content"></a>可公開存取的內容

**AC-22.d** 組織 [指派：組織定義的頻率] 會檢閱可公開存取資訊系統上的內容以取得非公用的資訊，並在探索到時移除這類資訊。

**職責：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級存取控制程序可能會建立內容定期檢閱程序，張貼可公開存取的系統。 |
| **提供者 (Microsoft Azure)** | 不適用 |

