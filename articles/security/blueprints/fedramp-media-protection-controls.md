---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 媒體保護"
description: "FedRAMP 的 Web 應用程式- 媒體保護"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>媒體保護 (MP)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-mp-1"></a>NIST 800-53 控制措施 MP-1

#### <a name="media-protection-policy-and-procedures"></a>媒體保護原則和程序

**MP-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、職責、管理承諾、組織實體間之協調合作及合規性的媒體保護原則，以及可協助實作媒體保護原則和相關媒體保護控制措施的程序；並檢閱和更新目前的媒體保護原則 (依 [指派：組織定義的頻率]) 與媒體保護程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級媒體保護原則和程序可能即已足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-mp-2"></a>NIST 800-53 控制措施 MP-2

#### <a name="media-access"></a>媒體存取

**MP-2** 組織會限制只有 [指派：組織定義的人員或角色] 才能存取 [指派：組織定義的數位和/或非數位媒體類型]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 已透過實作「Microsoft 安全性原則」來實作媒體存取權。 對數位媒體的邏輯存取權會透過「Active Directory 群組原則物件」(AD GPO) 和安全性群組來控制。 對所有媒體的實體存取權會受到資料中心存取程序限制。 僅限具有正當業務目的來存取資料的個人，才享有存取權。 如需有關現有資料中心存取控制的更多詳細資料，請參閱＜PE-3 實體存取控制＞。 「資產保護標準」定義了保護 Microsoft Azure 資料中心內資訊資產之機密性、完整性及可用性所需的保護。 |


 ## <a name="nist-800-53-control-mp-3a"></a>NIST 800-53 控制措施 MP-3.a

#### <a name="media-marking"></a>媒體標記

**MP-3.a** 組織會標記指出資訊之散發限制、處理須知及所適用安全性標記 (如果有的話) 的資訊系統媒體。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會將 Microsoft 資料中心內的資產標示為具有 HBI、MBI 或 LBI (高、中或低業務影響) 指定，分別需要不同層級的安全性和處理預防措施。 資產擁有者必須將其儲存在 Microsoft 資料中心內的資產分類。 如需詳細資訊，請參閱＜資產分類標準＞和＜資產保護標準＞。 |


 ## <a name="nist-800-53-control-mp-3b"></a>NIST 800-53 控制措施 MP-3.b

#### <a name="media-marking"></a>媒體標記

**MP-3.b** 只要 [指派：組織定義的資訊系統媒體類型] 保留在 [指派：組織定義的管制區] 內，組織就會將其從標記作業中排除。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會要求資產擁有者為其資產指派資產分類，沒有任何資產可以從這項要求豁免。 在 Microsoft 資料中心環境中，資產係指伺服器、網路裝置及磁帶。 未使用 USB 快閃磁碟機/隨身碟、外部/卸除式硬碟或 CD/DVD 等其他數位媒體。 資料中心內未使用非數位媒體。 |


 ## <a name="nist-800-53-control-mp-4a"></a>NIST 800-53 控制措施 MP-4.a

#### <a name="media-storage"></a>媒體儲存

**MP-4.a** 組織會在 [指派：組織定義的管制區] 內，實際控制並安全地存放 [指派：組織定義的數位和/或非數位媒體類型]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 數位媒體資產會實際並安全地存放在資料中心共置室內。 Microsoft 資料中心現有多層實體存取控制 (存取徽章、生物識別技術；如需有關實體存取控制的進一步詳細資料，請參閱 PE-3) 和視訊監視來提供安全的儲存。 數位媒體包括伺服器、網路裝置及用於備份的磁帶。 資料中心環境中未使用非數位媒體。 |


 ## <a name="nist-800-53-control-mp-4b"></a>NIST 800-53 控制措施 MP-4.b

#### <a name="media-storage"></a>媒體儲存

**MP-4.b** 組織會保護資料系統媒體，直到已使用核准的設備、技術及程序來銷毀或清理媒體為止。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | 在 Microsoft 資料中心共置環境中，會在 Microsoft Azure 數位媒體資產的存留期內，透過實體存取控制 (PE-3) 和邏輯存取控制 (IA-2) 為這些資產提供保護。 在處置 Microsoft Azure 資產之前，會先使用符合 NIST SP 800-88 的方法來予以清理、清除或銷毀。 針對資產銷毀，Microsoft Azure 會使用現場的資產銷毀服務。 |


 ## <a name="nist-800-53-control-mp-5a"></a>NIST 800-53 控制措施 MP-5.a

#### <a name="media-transport"></a>媒體傳輸

**MP-5.a** 使用 [指派：組織定義的安全性保護] 在管制區外傳輸 [指派：組織定義的資訊系統媒體類型] 時，組織為其提供保護和控制。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | 位於 Microsoft 資料中心的 Microsoft Azure 數位媒體包括伺服器、網路裝置，以及磁性備份磁帶與光碟 (如果適用)。 Microsoft 資料中心未使用非數位媒體。 Microsoft 使用三種方法來保護在資料中心外傳輸的媒體：1) 安全傳輸、2) 加密、3) 清理、清除或銷毀。 |


 ## <a name="nist-800-53-control-mp-5b"></a>NIST 800-53 控制措施 MP-5.b

#### <a name="media-transport"></a>媒體傳輸

**MP-5.b** 組織會在資訊系統媒體於管制區外傳輸時持續為其負責。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | 針對透過使用下列 NIST SP 800-88 指導方針來離開資料中心的資產，Microsoft Azure 會持續為其負責：一致的清理/清除、資產銷毀、加密、正確的清查，以及傳輸期間的保管鏈結追蹤和保護。 |


 ## <a name="nist-800-53-control-mp-5c"></a>NIST 800-53 控制措施 MP-5.c

#### <a name="media-transport"></a>媒體傳輸

**MP-5.c** 組織會記載與資訊系統媒體傳輸相關的活動。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會記錄傳輸前的詳細目錄、傳輸期間的保管鏈結追蹤和保護、資產清理/清除、資產銷毀、資產接收，以及傳輸後的詳細目錄驗證。 |


 ## <a name="nist-800-53-control-mp-5d"></a>NIST 800-53 控制措施 MP-5.d

#### <a name="media-transport"></a>媒體傳輸

**MP-5.d** 組織會限制只有已獲授權的人員，才能進行與資訊系統媒體傳輸相關的活動。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會透過保護保管鏈結，限制只有已獲授權的人員才能進行資產傳輸活動。 使用鎖定、防竄改密封及要求驗證資產詳細目錄，可確保只有已獲授權的人員才能參與資產傳輸。 |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800-53 控制措施 MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>媒體傳輸 | 密碼編譯保護

**MP-5 (4)** 資訊系統會實作密碼編譯機制，以在於管制區外進行傳輸時，保護數位媒體上所儲存資料的機密性與完整性。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會採用「資料保護服務」(DPS)，使用經 FIPS 140-2 層級 3 驗證的加密模組 (憑證編號 1694) 和 HSM (憑證編號 1178) 來保護磁帶上的 AES 256 位元加密資料，以管理密碼編譯金鑰。 |


 ## <a name="nist-800-53-control-mp-6a"></a>NIST 800-53 控制措施 MP-6.a

#### <a name="media-sanitization"></a>媒體清理

**MP-6.a** 組織會根據適用的聯邦與組織標準和原則，先使用 [指派：組織定義的清理技術和程序] 來清理 [指派：組織定義的資訊系統媒體]，然後才進行處置、從組織控制中釋出，或釋出以供重複使用。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會要求在重複使用或處置 Microsoft Azure 資料中心環境中的數位媒體之前，必須先使用 Microsoft Azure 核准的工具並以符合 NIST SP 800-88 (媒體清理指導方針) 的方式來清理/清除這些數位媒體。 Microsoft Azure 在資料中心環境中未使用非數位媒體。 |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800-53 控制措施 MP-6.b

#### <a name="media-sanitization"></a>媒體清理

**MP-6.b** 組織會採用強度和完整性與資訊的安全性類別或分類相當的清理機制。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會使用與 Microsoft Azure 的資產分類相當的資料清除單位和程序，以符合 NIST SP 800-88 的方式來清理/清除資料。 針對需要銷毀的資產，Microsoft Azure 會使用現場的資產銷毀服務。 |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800-53 控制措施 MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>媒體清理 |檢閱/核准/追蹤/記載/驗證

**MP-6 (1)** 組織會檢閱、核准、追蹤、記載及驗證媒體清理與處置動作。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 已根據 NIST SP 800-88 中「資產分類標準」與「資產保護標準」的指導方針，實作媒體清理程序。 所有磁性和電子媒體皆已根據其 Azure 資產分類，依下列 NIST SP 800-88 規格進行清理/清除。 Azure 會使用來自「極端通訊協定解決方案」(Extreme Protocol Solutions, EPS) 的資料清除單位。 EPS 軟體支援 NIST SP 800-88 對清理和清除/安全清除的需求。 |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800-53 控制措施 MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>媒體清理 | 設備測試

**MP-6 (2)** 組織會依 [指派：組織定義的頻率] 來測試清理設備和程序，以確認達成所需的清理。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會使用資料清除單位和程序，以符合 NIST SP 800-88 的方式來清理/清除資料。 每隔 180 天，DCS 作業都會測試 Microsoft Azure 資料清除單位和程序來進行清除。 在此測試中，DCS 作業會透過鑑識分析已測試的硬碟來驗證是否達成所需的清理，以確認資料清除單位是否以清理資料 |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800-53 控制措施 MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>媒體清理 | 非破壞性技術

**MP-6 (3)** 組織會在下列情況下，先在可攜式儲存裝置套用非破壞性清理技術，然後才將這類裝置連線到資訊系統：[指派：組織所定義需要清理可攜式儲存裝置的情況]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會確保 Azure 資料中心遵守「資料中心服務 Run Book」中的「工具與卸除式媒體安全性程序」，以防止政府環境受到可攜式儲存裝置上的惡意程式碼感染。 該程序指定必須先對 USB 磁碟機採取下列動作，才能在政府環境中使用： <br /> (1) 一開始從製造商或廠商購買 USB 磁碟機時，在初次使用之前或要重複用於不同工具時，先將磁碟機格式化。 <br /> (2) 將任何要在政府所指定區域中使用的 USB 磁碟機攜入該區域時，先掃描是否有惡意程式碼。 <br /> (3) 在 Government 所指定區域內使用磁碟機之後，先將磁碟機格式化後再離開。 <br /> 「工具與卸除式媒體安全性程序」也會要求一律不要將所有已遺失、丟棄、遭竊或錯置的隨身碟重新引進 Azure 資料中心，而是要登記並銷毀。 |


 ## <a name="nist-800-53-control-mp-7"></a>NIST 800-53 控制措施 MP-7

#### <a name="media-use"></a>媒體使用

**MP-7** 組織會使用 [指派：組織定義的安全性保護] 來 [選取項目：限制；禁止] 在 [指派：組織定義的資訊系統或系統元件] 上的 [指派：組織定義的資訊系統媒體類型] 使用。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會要求資產擁有者為其資產指派資產分類，沒有任何資產可以從這項要求豁免。 在 Microsoft 資料中心環境中，資產係指伺服器和網路裝置。 USB 快閃磁碟機/隨身碟等其他數位媒體，則會由控制這些裝置之管理方式的特定原則和程序管理。 未使用 CD/DVD。 資料中心內未使用非數位媒體。 Microsoft Azure 資料中心環境中的數位媒體使用情況會透過 CCTV 涵蓋範圍以全年無休的方式來進行監視。 如需更多詳細資料，請參閱 PE-06。 |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800-53 控制措施 MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>媒體使用 | 禁止在沒有擁有者的情況下使用

**MP-7 (1)** 在可攜式儲存裝置沒有可識別之擁有者的情況下，組織會禁止在組織資訊系統中使用這類裝置。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 在部署於 Azure 上的系統範圍內沒有任何由客戶控制的媒體。 |
| **提供者 (Microsoft Azure)** | Microsoft 會將可寫入之卸除式媒體的使用僅限於資料中心管理部門透過「DCS 工具」和「卸除式媒體程序」明確核准的媒體。 如《Microsoft 資料中心工作規則與規定》文件所述，在所有生產環境區域都禁止使用個人擁有或沒有可識別之擁有者的媒體。 |
