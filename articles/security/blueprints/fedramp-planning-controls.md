---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 規劃"
description: "FedRAMP 的 Web 應用程式- 規劃"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 55032e87-763a-452d-bb22-9c28936d5bb4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 8e4d4f228940d08bc522aabcd6f4ab573ff231d4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="planning-pl"></a>規劃 (PL)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-pl-1"></a>NIST 800-53 控制措施 PL-1

#### <a name="security-planning-policy-and-procedures"></a>安全性規劃原則和程序

**PL-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、職責、管理承諾、組織實體間之協調合作及合規性的安全性規劃原則，以及可協助實作安全性規劃原則和相關安全性規劃控制措施的程序；並檢閱和更新目前的安全性規劃原則 (依 [指派：組織定義的頻率]) 與安全性規劃程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級安全性規劃原則和程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-2a"></a>NIST 800-53 控制措施 PL-2.a

#### <a name="system-security-plan"></a>系統安全性計畫

**PL-2.a** 組織會為資訊系統開發符合組織企業架構的安全性計畫；為系統明確定義授權界限；說明任務和商務程序方面的資訊系統操作內容；提供資訊系統的安全性分類 (包括所依據的基本原理)；說明資訊系統的操作環境和與其他資訊系統的關聯性或連線；提供系統的安全性需求概觀；識別任何相關的重疊 (如果適用)；說明現有或已規劃來滿足這些需求的安全性控制措施 (包括適用於量身打造決策的基本原理)；並且會先由授權官方人員或指定的代表檢閱並核准，然後才實作計畫。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責開發符合目標授權 (例如 FedRAMP) 所定義準則的系統安全性計畫 (SSP)。 客戶可以參考《NIST 特刊 800-18 R1 聯邦資訊系統安全性計畫開發指南》(NIST Special Publication 800-18 R1, Guide for Developing Security Plans for Federal Information Systems)。 客戶 SSP 應該處理從 Microsoft Azure 繼承的控制措施，並參考 Microsoft Azure SSP 來取得實作詳細資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-2b"></a>NIST 800-53 控制措施 PL-2.b

#### <a name="system-security-plan"></a>系統安全性計畫

**PL-2.b** 組織會將安全性計畫的複本散發給 [指派：組織定義的人員或角色]，並將後續的計畫變更傳達給他們。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責散發系統安全性計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-2c"></a>NIST 800-53 控制措施 PL-2.c

#### <a name="system-security-plan"></a>系統安全性計畫

**PL-2.c** 組織會依 [指派：組織定義的頻率] 來檢閱資訊系統的安全性計畫。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱系統安全性計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-2d"></a>NIST 800-53 控制措施 PL-2.d

#### <a name="system-security-plan"></a>系統安全性計畫

**PL-2.d** 組織會更新計畫以因應資訊系統/作業環境變更，或是進行計畫實作或安全性控制評估期間所識別出的問題。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責更新系統安全性計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-2e"></a>NIST 800-53 控制措施 PL-2.e

#### <a name="system-security-plan"></a>系統安全性計畫

**PL-2.e** 組織會防止安全性計畫遭受未經授權的公開和修改。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責保護系統安全性計畫。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-pl-2-3"></a>NIST 800-53 控制措施 PL-2 (3)

#### <a name="system-security-plan--plan--coordinate-with-other-organizational-entities"></a>系統安全性計畫 | 與其他組織實體一起規劃/協調合作

**PL-2 (3)** 組織會先與 [指派：組織定義的個人或群組] 規劃並協調影響資訊系統的安全性相關活動，然後才進行這類活動，以降低對其他組織實體的影響。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責規劃和協調安全性相關活動，以降低對其他組織實體的影響。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-4a"></a>NIST 800-53 控制措施 PL-4.a

#### <a name="rules-of-behavior"></a>行為規則

**PL-4.a** 組織會為需要存取資訊系統的個人，針對資訊和資訊系統使用方面，建立並隨時提供說明其職責和應有行為的規則。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級行為規則可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-4b"></a>NIST 800-53 控制措施 PL-4.b

#### <a name="rules-of-behavior"></a>行為規則

**PL-4.b** 組織會先從這類個人收到指出他們已閱讀、了解並同意遵守行為規則的已簽署通知，然後才授權他們存取資訊和資訊系統。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級行為規則可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-4c"></a>NIST 800-53 控制措施 PL-4.c

#### <a name="rules-of-behavior"></a>行為規則

**PL-4.c** 組織會依 [指派：組織定義的頻率] 來檢閱和更新行為規則。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級行為規則可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-4d"></a>NIST 800-53 控制措施 PL-4.d

#### <a name="rules-of-behavior"></a>行為規則

**PL-4.d** 組織會在已修訂或更新行為規則時，要求已簽署舊版行為規則的個人閱讀並重新簽署。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級行為規則可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-pl-4-1"></a>NIST 800-53 控制措施 PL-4 (1)

#### <a name="rules-of-behavior--social-media-and-networking-restrictions"></a>行為規則 | 社交媒體和網路限制

**PL-4 (1)** 組織會在行為規則中，包含有關使用社交媒體/網站及在公用網站上張貼組織資訊的明確限制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級行為規則可以包含對社交媒體/網站的限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-8a"></a>NIST 800-53 控制措施 PL-8.a

#### <a name="information-security-architecture"></a>資訊安全性架構

**PL-8.a** 組織會為資訊系統開發資訊安全架構，以說明與保護組織資訊之機密性、完整性及可用性相關的整體原理、需求和要採取的方法；說明如何將資訊安全性架構整合至企業架構並支援企業架構；以及說明與外部服務相關和相依的任何資訊安全性假設。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責為客戶部署的資源開發資訊安全性架構。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-8b"></a>NIST 800-53 控制措施 PL-8.b

#### <a name="information-security-architecture"></a>資訊安全性架構

**PL-8.b** 組織會依 [指派：組織定義的頻率] 來檢閱和更新資訊安全性架構，以反映企業架構中的更新。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱和更新資訊安全性架構。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pl-8c"></a>NIST 800-53 控制措施 PL-8.c

#### <a name="information-security-architecture"></a>資訊安全性架構

**PL-8.c** 組織會確保安全性計畫、安全性操作概念文件 (CONOPS) 及組織的採購/收購中反映計畫性資訊安全性架構變更。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責說明資訊安全性架構的計畫性變更。 |
| **提供者 (Microsoft Azure)** | 不適用 |
