---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 識別與驗證"
description: "FedRAMP 的 Web 應用程式 - 識別與驗證"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5ceac04f3f5c58ac5810dda6c99b40ebc8ad736f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="identification-and-authentication-ia"></a>識別與驗證 (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 控制措施 IA 1

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

#### <a name="identification-and-authentication-policy-and-procedures"></a>識別與驗證原則和程序

**IA-1** 組織開發、記錄識別與驗證原則及程序，並散佈給 [指派：組織定義的人員或角色]，這些原則滿足用途、領域、角色、職責和管理承諾、組織實體間的協調及合規性；程序則是加速實作識別與驗證原則及關聯的識別與驗證控制措施；以及檢閱和更新目前的識別與驗證原則 [指派：組織定義的頻率] 和識別與驗證程序 [指派：組織定義的頻率]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級識別與驗證原則和程序，可能不足以因應此控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 控制措施 IA-2

#### <a name="identification-and-authentication-organizational-users"></a>識別與驗證 (組織使用者)

**IA-2** 資訊系統以唯一方式識別及驗證組織使用者 (或代替組織使用者處理)。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 建立的帳戶具有唯一識別碼。 會停用或移除具有非唯一識別碼的內建帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 控制措施 IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>識別與驗證 (組織使用者) | 授權帳戶的網路存取

**IA-2 (1)** 資訊系統針對授權帳戶的網路存取實作多重要素驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責對授權帳戶的網路存取實作多重要素驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 控制措施 IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>識別與驗證 (組織使用者) | 非授權帳戶的網路存取

**IA-2 (2)** 資訊系統針對非授權帳戶的網路存取實作多重要素驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責對非授權帳戶的網路存取實作多重要素驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 控制措施 IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>識別與驗證 (組織使用者) | 授權帳戶的本機存取

**IA-2 (3)** 資訊系統針對授權帳戶的本機存取實作多重要素驗證。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法在本機存取 Azure 資料中心的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 不允許本機存取，除非需要實際存取。 只有在成員伺服器發生網路問題且網域驗證無法運作的情況下，才能使用本機系統管理員存取權對執行個體中的問題進行疑難排解。 <br /> Azure 藉由實際存取環境所需的存取控制機制，對本機存取實作多重要素驗證。 系統界限內包含所有 Azure 基礎結構系統的 Azure 資料中心內的機房，會以各種實體安全性機制 (包括公司的智慧卡徽章存取和生物識別裝置的需求) 限制進出。 實際存取 Azure 資料中心主機託管服務的進入點時，需同時進行兩種驗證。 |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 控制措施 IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>識別與驗證 (組織使用者) | 非授權帳戶的本機存取

**IA-2 (4)** 資訊系統針對非授權帳戶的本機存取實作多重要素驗證。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法在本機存取 Azure 資料中心的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會將 Microsoft Azure Government 人員使用的所有 Microsoft Azure Government 帳戶視為已授權。 使用智慧卡和 PIN 對所有 Microsoft Azure Government 人員帳戶實作多重要素驗證，其中包括本機存取。 |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 控制措施 IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>識別與驗證 (組織使用者) | 群組驗證

**IA-2 (5)** 採用群組驗證器時，組織要求人員以個人驗證器進行驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署的資源上未啟用共用/群組帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 控制措施 IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>識別與驗證 (組織使用者) | 授權帳戶的網路存取 - 防重新執行

**IA-2 (8)** 資訊系統針對授權帳戶的網路存取實作防重新執行驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署資源的存取權受 Azure Active Directory、Active Directory 及 Windows 作業系統的內建 Kerberos 功能保護，能防範重新執行攻擊。 在 Kerberos 驗證中，用戶端傳送的驗證器包含其他資料，例如加密的 IP 清單、用戶端時間戳記以及票證存留期。 如果重新執行封包，就會檢查時間戳記。 如果時間戳記早於或等同於先前的驗證器，該封包會遭到拒絕。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 控制措施 IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>識別與驗證 (組織使用者) | 非授權帳戶的網路存取 - 防重新執行

**IA-2 (9)** 資訊系統針對非授權帳戶的網路存取實作防重新執行驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署資源的存取權受 Azure Active Directory、Active Directory 及 Windows 作業系統的內建 Kerberos 功能保護，能防範重新執行攻擊。 在 Kerberos 驗證中，用戶端傳送的驗證器包含其他資料，例如加密的 IP 清單、用戶端時間戳記以及票證存留期。 如果重新執行封包，就會檢查時間戳記。 如果時間戳記早於或等同於先前的驗證器，該封包會遭到拒絕。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 控制措施 IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>識別與驗證 (組織使用者) | 遠端存取 - 個別裝置

**IA-2 (11)** 資訊系統針對授權帳戶和非授權帳戶的網路存取實作多重要素驗證，其中一個要素需由不同於取得存取權的裝置提供，且該裝置符合 [指派：組織定義的機制需求強度]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責實作多重要素驗證，以從遠端存取客戶部署的資源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 控制措施 IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>識別與驗證 (組織使用者) | Piv 認證的認可

**IA-2 (12)** 資訊系統認可並以電子方式驗證個人識別驗證 (PIV) 認證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責認可和驗證個人識別驗證 (PIV) 認證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 控制措施 IA-3

#### <a name="device-identification-and-authentication"></a>裝置識別與驗證

**IA-3** 資訊系統先以唯一方式識別並驗證 [指派：組織定義的特定裝置和/或裝置類型]，再建立 [選取項目 (一個或多個)：本機; 遠端; 網路] 連線。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責實作裝置識別與驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 控制措施 IA-4.a

#### <a name="identifier-management"></a>識別碼管理

**IA-4.a** 組織藉由接收來自 [指派：組織定義的人員或角色] 的授權，指派個人、群組、角色或裝置識別碼，以管理資訊系統識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責管理客戶資源的識別碼 (也就是個人、群組、角色和裝置)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 控制措施 IA-4.b

#### <a name="identifier-management"></a>識別碼管理

**IA-4.b** 組織藉由選取識別個人、群組、角色或裝置的識別碼，以管理資訊系統識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 部署期間，此 Azure Blueprint (藍圖) 會針對個別帳戶提示輸入客戶指定的識別碼。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 控制項 IA-4.c

#### <a name="identifier-management"></a>識別碼管理

**IA-4.c** 組織藉由指派識別碼給預期的個人、群組、角色或裝置，以管理資訊系統識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責管理客戶資源的識別碼 (也就是個人、群組、角色和裝置)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 控制項 IA-4.d

#### <a name="identifier-management"></a>識別碼管理

**IA-4.d** 組織藉由防止在 [指派：組織定義的時間週期] 內重複使用識別碼，以管理資訊系統識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 為 Active Directory 和本機 Windows 作業系統帳戶指派唯一的安全性識別碼 (SID)。 為 Azure Active Directory 帳戶指派全域唯一的物件識別碼。 這些唯一識別碼無法重複使用。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 控制措施 IA 4.e

#### <a name="identifier-management"></a>識別碼管理

**IA-4.e** 組織藉由在 [指派：組織定義的 	非使用狀態時間週期] 後停用識別碼，以管理資訊系統識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 為 Active Directory 實作一項排定的工作，在 35 天的非使用狀態後自動停用帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 控制措施 IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>識別碼管理 |識別使用者狀態

**IA-4 (4)** 組織將每個人以唯一方式識別為 [指派：組織定義的特性，用於識別個人狀態]，以管理個人識別碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure Active Directory 和 Active Directory 支援使用套用至識別碼的命名慣例，以表示承包商、廠商及其他使用者類型。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 控制措施 IA 5.a

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.a** 組織藉由在初始驗證器發佈時驗證接收驗證器的個人、群組、角色或裝置的識別，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責管理驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 控制措施 IA 5.b

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.b**組織為組織定義的驗證器建立初始驗證器內容，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 建立之帳戶的所有初始驗證器內容，符合 IA-5 (1) 所述客戶在部署期間指定時的驗證需求。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 控制措施 IA-5.c

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.c** 組織藉由確保驗證器有足夠強度的機制滿足預期用途，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 使用的驗證器符合 FedRAMP 所要求之強度需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 控制措施 IA-5.d

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.d**組織建立並實作適合以下狀況的管理程序，以管理資訊系統驗證器：初始驗證器發佈、驗證器遺失/遭竊或損毀，以及撤銷驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責管理驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 控制措施 IA-5.e

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.e** 組織藉由在資訊系統安裝前變更預設的驗證器內容，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 之元件的所有驗證器已由預設值變更。 驗證器是部署此解決方案期間由客戶指定的。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 控制措施 IA-5.f

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.f** 組織藉由建立最小和最大存留期限制和驗證器的重複使用條件，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責管理驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 控制措施 IA-5.g

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.g** 組織藉由變更/重新整理驗證器 [指派：組織依驗證器類型定義的時間週期]，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署一部網域控制站，以加入所有已部署的虛擬機器。 建立和設定群組原則，以實作密碼存留期限制 (60 天)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 控制措施 IA-5.h

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.h** 組織藉由防範未經授權揭露和修改驗證器內容，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會實作 Key Vault，以防範未經授權揭露和修改驗證器內容。 下列驗證器會儲存在 Key Vault 中：部署帳戶的 Azure 密碼、虛擬機器系統管理員密碼、SQL Server 服務帳戶密碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 控制措施 IA-5.i

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.i** 組織藉由要求人員採取特定的安全性預防措施 (和採取裝置手段) 來保護驗證器，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會實作 Key Vault，以防範未經授權揭露和修改驗證器內容。 下列驗證器會儲存在 Key Vault 中：部署帳戶的 Azure 密碼、虛擬機器系統管理員密碼、SQL Server 服務帳戶密碼。 Key Vault 可使用受硬體安全模組 (HSM) 保護的金鑰來加密金鑰和密碼 (例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰和密碼)。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 控制措施 IA-5.j

#### <a name="authenticator-management"></a>驗證器管理

**IA-5.j** 組織藉由在群組/角色帳戶的成員資格變更時變更其驗證器，以管理資訊系統驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 所部署的資源上未啟用共用/群組帳戶。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 控制措施 IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).a** 資訊系統針對密碼型驗證強制執行 [指派：組織定義的大小寫區分、字元數、混合使用大寫字母、小寫字母、數字和特殊字元需求，包括每一類型最低需求] 的最低密碼複雜性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署一部網域控制站，以加入所有已部署的虛擬機器。 建立和設定群組原則，針對虛擬機器本機帳戶和 AD 帳戶強制執行密碼複雜性需求。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 控制措施 IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).b** 建立新密碼時，資訊系統針對密碼型驗證強制執行至少變更下列數目的字元：[指派：組織定義的數字]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內採用密碼型驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 控制措施 IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).c** 資訊系統針對密碼型驗證僅儲存和傳輸以密碼編譯方式保護的密碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 使用 Azure Directory，以確保所有密碼在儲存和傳輸時以密碼編譯方式保護。 Active Directory 在部署的 Windows 虛擬機器本機儲存的密碼，會自動雜湊為內建安全性功能的一部分。 遠端桌面驗證工作階段使用 TLS 保護，以確保驗證器傳輸時受到保護。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 控制措施 IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).d** 資訊系統針對密碼型驗證強制執行 [指派：組織定義的最少和最多存留期數字] 的密碼最少和最多存留期限制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署一部網域控制站，以加入所有已部署的虛擬機器。 建立和設定群組原則以對密碼強制執行限制，為本機帳戶和 AD 帳戶強制執行最少 (1 天) 和最多 (60 天) 的存留期限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 控制措施 IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).e** 資訊系統針對密碼型驗證禁止重複使用 [指派：組織定義的數字] 組的密碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 會部署一部網域控制站，以加入所有已部署的虛擬機器。 建立和設定群組原則，為本機帳戶和 AD 帳戶強制執行重複使用條件 (24 組密碼) 的限制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 控制措施 IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>驗證器管理 | 密碼型驗證

**IA-5 (1).f** 資訊系統針對密碼型驗證允許使用暫時密碼登入系統，並立即變更永久密碼。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 使用 Azure Active Directory 來管理資訊系統的控制存取權。 每當一開始建立帳戶或產生暫時密碼時，都會使用 Azure Active Directory 要求使用者變更下次登入時要用的密碼。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 控制措施 IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>驗證器管理 | PKI 型驗證

**IA-5 (2).a** 資訊系統針對 PKI 型驗證藉由建構和驗證憑證路徑到認可的信賴起點 (包括檢查憑證狀態資訊) 以驗證憑證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內採用 PKI 型驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 控制措施 IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>驗證器管理 | PKI 型驗證

**IA-5 (2).b** 資訊系統針對 PKI 型驗證強制執行對應私密金鑰的授權存取。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內採用 PKI 型驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 控制措施 IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>驗證器管理 | PKI 型驗證

**IA-5 (2).c** 資訊系統針對 PKI 型驗證，將已驗證的識別對應到個人或群組帳戶。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內採用 PKI 型驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 控制措施 IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>驗證器管理 | PKI 型驗證

**IA-5 (2).d** 資訊系統針對 PKI 型驗證實作本機快取撤銷資料，以在無法透過網路存取撤銷資訊時支援路徑探索和驗證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責在客戶部署的資源內採用 PKI 型驗證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 控制措施 IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>驗證器管理 | 本人或信任的協力廠商登錄

**IA-5 (3)** 組織要求登錄程序接收 [指派：組織定義的驗證器類型和/或特定驗證器] 的施行方式是先由 [選取項目：本人; 信任的協力廠商] 處理，之後才是具有授權的 [指派：組織定義的登錄授權單位] 的 [指派：組織定義的人員或角色]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責登錄驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 控制措施 IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>驗證器管理 | 密碼強度判斷的自動化支援

**IA-5 (4)** 組織使用自動化工具，判斷密碼驗證器強度是否足以滿足 [指派：組織定義的需求]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 與此 Azure Blueprint (藍圖) 一起部署的使用者帳戶包含 AD 和本機使用者帳戶。 這兩種提供的機制都會強制符合已建立密碼需求的規範，以便在密碼變更期間建立初始密碼。 採用 Azure Active Directory 這個自動化工具，判斷密碼驗證器強度是否足以滿足 IA-5 (1) 中所建立的密碼長度、複雜度、輪替及存留期限制。 Azure Active Directory 可確保建立的密碼驗證器強度符合這些標準。 檢查用來部署此解決方案的客戶指定密碼是否符合密碼強度需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 控制措施 IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>驗證器管理 | 驗證器保護

**IA-5 (6)** 組織保護驗證器，使其對應於使用驗證器允許存取的資訊安全性類別。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責保護驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 控制措施 IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>驗證器管理 | 無內嵌的未加密靜態驗證器

**IA-5 (7)** 組織確保未加密的靜態驗證器未內嵌在應用程式或存取指令碼中，或儲存在功能鍵。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的應用程式、存取指令碼或功能鍵中沒有內嵌未加密的靜態驗證器。 使用驗證器的任何指令碼或應用程式，會在每次使用之前呼叫 Azure Key Vault 容器。 稽核 Azure Key Vault 容器的存取情況，如果使用服務帳戶存取系統，卻沒有對應的 Azure Key Vault 容器呼叫，就能偵測出這項限制違規。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 控制措施 IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>驗證器管理 | 多個資訊系統帳戶

**IA-5 (8)** 組織實作 [指派：組織定義的安全性預防措施] 以管理因擁有多個資訊系統帳戶的人員而洩漏資訊的風險。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能需要企業層級的安全性預防措施，以管理和擁有多個系統帳戶的人員相關聯的風險。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 控制措施 IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>驗證器管理 | 硬體權杖型驗證

**IA-5 (11)** 資訊系統針對硬體權杖型驗證採用滿足 [指派：組織定義的權杖品質需求] 的機制。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責採用滿足硬體權杖型驗證品質需求的機制。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 控制措施 IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>驗證器管理 | 快取驗證器的到期日

**IA-5 (13)** 資訊系統在 [指派：組織定義的時間週期] 後禁止使用快取驗證器。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 部署的資源不會設為允許使用快取驗證器。 已部署虛擬機器的驗證需在驗證時輸入驗證器。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 控制措施 IA-6

#### <a name="authenticator-feedback"></a>驗證器意見回應

**IA-6** 資訊系統會遮蔽驗證過程中驗證資訊的意見回應，以避免可能遭到未經授權者利用。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 存取此 Azure Blueprint (藍圖) 部署的資源是經由遠端桌面，並需要 Windows 驗證。 Windows 驗證工作階段的預設行為會遮罩驗證工作階段期間輸入的密碼。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 控制措施 IA-7

#### <a name="cryptographic-module-authentication"></a>密碼編譯模組驗證

**IA-7** 資訊系統會實作密碼編譯模組驗證機制，其符合這類驗證適用之聯邦法律、行政命令、指令、原則、規定、標準和指導方針需求。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 此 Azure Blueprint (藍圖) 採用 Windows 驗證、遠端桌面和 BitLocker。 這些元件可以設定為依賴 FIPS 140 驗證的密碼編譯模組。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 控制措施 IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>識別與驗證 (非組織使用者)

**IA-8** 資訊系統以唯一方式識別及驗證非組織使用者 (或代替非組織使用者處理)。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責識別和驗證存取客戶部署資源的非組織使用者。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 控制措施 IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>識別與驗證 (非組織使用者) | 來自其他機構的 Piv 認證認可

**IA-8 (1)** 資訊系統認可並以電子方式驗證來自其他聯邦機構的個人識別驗證 (PIV) 認證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責認可和驗證由其他聯邦機構核發的個人識別驗證 (PIV) 認證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 控制措施 IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>識別與驗證 (非組織使用者) | 協力廠商認證的認可

**IA-8 (2)** 資訊系統只認可 FICAM 核准的協力廠商認證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責只認可經聯邦識別、認證和存取管理 (FICAM) 信任架構解決方案計劃核准的協力廠商認證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 控制措施 IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>識別與驗證 (非組織使用者) | 使用 Ficam 核准的產品

**IA-8 (3)** 組織只會採用 FICAM 核准的資訊系統元件 [指派：組織定義的資訊系統] 來認可協力廠商認證。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責只採用經聯邦識別、認證和存取管理 (FICAM) 信任架構解決方案計劃核准的資源來認可協力廠商認證。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 控制措施 IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>識別與驗證 (非組織使用者) | 使用 Ficam 發行的設定檔

**IA-8 (4)** 資訊系統符合 FICAM 發行的設定檔。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶負責符合聯邦識別、認證和存取管理 (FICAM) 信任架構解決方案計劃發行的設定檔。 |
| **提供者 (Microsoft Azure)** | 不適用 |
