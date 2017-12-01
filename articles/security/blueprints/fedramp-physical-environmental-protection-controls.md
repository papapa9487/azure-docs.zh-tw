---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 實際與環境保護"
description: "適用於 FedRAMP 的 Web 應用程式 - 實際與環境保護"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0bf8349b-450d-413c-a535-6f7b80b82781
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9e9e473285f4e82e1da40b8c3d496d65733fbd45
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="physical-and-environmental-protection-pe"></a>實際與環境保護 (PE)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-pe-1"></a>NIST 800-53 控制措施 PE-1

#### <a name="physical-and-environmental-protection-policy-and-procedures"></a>實際與環境保護原則和程序

**PE-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、責任、管理承諾、組織實體間之協調合作及合規性的實際與環境保護原則，以及可協助實作實際與環境保護原則和相關實際與環境保護控制措施的程序；並檢閱和更新目前的實際與環境保護原則 (依 [指派：組織定義的頻率]) 和實際與環境保護程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級實際與環境保護原則和程序可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pe-2a"></a>NIST 800-53 控制措施 PE-2.a

#### <a name="physical-access-authorizations"></a>實際存取授權

**PE-2.a** 組織會開發、核准及維護一份個人清單，其中每個人對資訊系統所在設施都具有已獲授權的存取權限。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 若要實際存取 Microsoft 資料中心，必須由資料中心管理 (DCM) 小組使用資料中心存取工具來核准。 存取指派需要一個結束日期，存取會在該日期之後自動移除且必須重新核准。 此外，若已不再需要存取，資料中心的安全人員或管理就會手動要求終止存取。 |


 ## <a name="nist-800-53-control-pe-2b"></a>NIST 800-53 控制措施 PE-2.b

#### <a name="physical-access-authorizations"></a>實際存取授權

**PE-2.b** 組織會發出授權認證以進行設施存取。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 資料中心存取工具是授權來源，其中列出具備特定資料中心之已獲授權存取權限的所有人員。 此工具會連結到資料中心的實體安全性存取控制裝置，並根據 DCM 小組核准的存取層級授與存取權限。 存取層級會在工具中指派給使用者之 Microsoft 發行的識別證，或由控制室監督員 (CRS) 在資料中心指派的臨時門禁識別證。 存取層級會由 DCM 小組進行核准。 除了指派給實體識別證的認證，資料中心的某些區域還需要註冊使用者的生物特徵辨識資料 (手形或指紋)。 |


 ## <a name="nist-800-53-control-pe-2c"></a>NIST 800-53 控制措施 PE-2.c

#### <a name="physical-access-authorizations"></a>實際存取授權

**PE-2.c** 組織會依 [指派：組織定義的頻率] 來檢閱存取清單，其中會依個人詳述已獲授權的設施存取權限。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 除了 a 小節中所述的撤銷存取權限之外，Microsoft Azure 每隔 90 天還會檢閱適用於 Azure 資料中心的已獲授權存取清單，以便視需要移除/更新個別存取權限。 |


 ## <a name="nist-800-53-control-pe-2d"></a>NIST 800-53 控制措施 PE-2.d

#### <a name="physical-access-authorizations"></a>實際存取授權

**PE-2.d** 不再需要存取時，組織就會從設施存取清單中移除個人。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 會在到達存取指派結束日期時自動移除存取權限。 若已不再需要存取，資料中心的安全人員或管理將會在資料中心存取工具中手動要求終止存取權限。 此外，Microsoft Azure 將移除因 c 小節中所述之存取清單檢閱而發現到的任何不必要的存取授權。 |


 ## <a name="nist-800-53-control-pe-3a"></a>NIST 800-53 控制措施 PE-3.a

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.a** 組織會在 [指派：組織針對資訊系統所在設施定義的進入/結束點] 強制執行實際存取授權，方法是先驗證個人存取授權，然後再授與設施的存取權限；以及使用 [選取 (一或多個)：[指派：組織定義的實體存取控制系統/裝置]；警衛] 來控制設施的出入口。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 會針對所有對於 Azure 資料中心的實際存取點，透過全天候的人員配置、警報、視訊監視、多重要素驗證及門禁裝置，來強制執行實際存取授權。 |


 ## <a name="nist-800-53-control-pe-3b"></a>NIST 800-53 控制措施 PE-3.b

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.b** 組織會針對 [指派：組織定義的進入/結束點] 維護實際存取稽核記錄。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 所有對於 Azure 資料中心設施的存取均已記錄和稽核。 |


 ## <a name="nist-800-53-control-pe-3c"></a>NIST 800-53 控制措施 PE-3.c

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.c** 組織會提供 [指派：組織定義的安全性防護] 來控制存取正式指定為可公開存取之設施中的區域。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Azure 資料中心並未包含指定為可公開存取的區域。 |


 ## <a name="nist-800-53-control-pe-3d"></a>NIST 800-53 控制措施 PE-3.d

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.d** 組織會陪同訪客，並監視訪客活動 (依 [指派：組織定義之需要陪同與監視訪客的情況])。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 已核准存取資料中心的所有訪客 (請參閱 PE-2)，均已在其識別證上或透過其他視覺提示 (例如彩色識別證) 指定為 'Escort Only' (僅限陪同)，而且要求他們隨時都要和其陪同者一起。 陪同的訪客不會被授與任何存取層級，而且進出只能透過他們的陪同者來進行。 陪同者會監視其訪客在資料中心內的所有活動。 |


 ## <a name="nist-800-53-control-pe-3e"></a>NIST 800-53 控制措施 PE-3.e

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.e** 組織會保護鑰匙、密碼鎖及其他實體存取裝置。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 實際的鑰匙和臨時門禁識別證會安全地放置於安全作業中心 (SOC) 內。 安全人員是全天候配置的人員。 藉由比對特定人員的門禁識別證與實際鑰匙，讓該人員能夠領取鑰匙。 每個排班期間都會進行鑰匙清查，而且不允許將鑰匙帶至他處。 |


 ## <a name="nist-800-53-control-pe-3f"></a>NIST 800-53 控制措施 PE-3.f

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.f** 組織每隔 [指派：組織定義的頻率] 就會清查 [指派：組織定義的實體存取裝置]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Azure 資料中心內的實體存取裝置每年至少會清查一次。 鑰匙和臨時門禁識別證會在一天內的每個排班期間多次清查。 門禁識別證讀取器和類似的存取裝置都會連結到持續顯示狀態的實體安全性系統。 |


 ## <a name="nist-800-53-control-pe-3g"></a>NIST 800-53 控制措施 PE-3.g

#### <a name="physical-access-control"></a>實際存取控制措施

**PE-3.g** 組織會依 [指派：組織定義的頻率]，和 (或) 遺失鑰匙、密碼鎖遭到洩漏，或者人員已轉移或終止時，變更密碼鎖和鑰匙。  

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 資料中心具備在遺失門禁識別證或鑰匙，或者人員已終止或轉移時要實作的程序。 萬一發生終止或轉移，就會立即從系統中移除該人員的存取權限並移除他們的門禁識別證。 |


 ### <a name="nist-800-53-control-pe-3-1"></a>NIST 800-53 控制措施 PE-3 (1)

#### <a name="physical-access-control--information-system-access"></a>實體存取控制措施 | 資訊系統存取

**PE-3 (1)** 除了在 [指派：包含資訊系統一或多個元件之組織定義的實體空間] 內對設施的實體存取控制措施之外，組織還會對資訊系統強制執行實體存取授權。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會透過各種安全性機制 (例如，電子存取控制、生物特徵辨識裝置及防潛回控制)，進一步限制 Microsoft 資料中心內包含重要系統 (例如，主機託管、關鍵環境、MDF 室等) 的區域。 相較於資料中心的其他存取區域，對於 Azure 主機託管的存取權限會當作更高層級的個別 DCAT 存取權限來授與。 此外，能夠存取 Azure Government 主機託管的所有 Azure FTE 和廠商，都必須先正式進行 Microsoft 的雲端檢測和美國公民身分驗證，才能獲得授權來存取環境。 如需針對 Azure Government 主機託管進行雲端檢測的詳細資訊，請參閱 PS-03 一節。 |


 ## <a name="nist-800-53-control-pe-4"></a>NIST 800-53 控制措施 PE-4

#### <a name="access-control-for-transmission-medium"></a>適用於傳輸媒體的存取控制措施

**PE-4** 組織會使用 [指派：組織定義的安全性防護]，在組織設施內，控制對於 [指派：組織定義的資訊系統發佈和傳輸線路] 的實際存取。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 已透過主配線架 (MDF) 室與主機託管場所的設計和設置，實作適用於傳輸媒體的存取控制來保護資訊系統發佈與傳輸線路，以防止發生意外損壞、中斷及遭到實際竄改。 對於 MDF 室和主機託管場所的存取需要使用雙因素驗證 (門禁識別證與生物特徵辨識技術)。 這確保會限制為只有已獲得授權的人員可以存取 (請參閱 PE-2、PE-3)。 在 MDF 內，傳輸和發佈線路會受到保護，以防止發生意外損壞、中斷，以及透過使用金屬導管、鎖定的機架、機箱或纜線托盤進行實際的竄改。 |


 ## <a name="nist-800-53-control-pe-5"></a>NIST 800-53 控制措施 PE-5

#### <a name="access-control-for-output-devices"></a>適用於輸出裝置的存取控制措施

**PE-5** 組織會控制對於資訊系統輸出裝置的實際存取，以防止未經授權的人員取得輸出。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 資料中心沒有永久連接到 Azure 資產或 Azure 共用資產的輸出裝置 (監視器、印表機、音訊裝置等)。 除了沒有輸出裝置，資訊安全人員每個排班都會多次實際巡察設施，以檢查像是門已上鎖以及機架安全無虞等項目。 只有具備已核准存取授權的人員才能存取資料中心。 主機託管場所需要雙因素驗證 (門禁識別證與生物特徵辨識技術) 才能進入。 |


 ## <a name="nist-800-53-control-pe-6a"></a>NIST 800-53 控制措施 PE-6.a

#### <a name="monitoring-physical-access"></a>監視實際存取

**PE-6.a** 組織會監視對於資訊系統所在設施的實際存取，以偵測及回應實際的安全性事件。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 藉由在資料中心實作安全性裝置及流程來監視實際存取。 範例包括全天候電子監視存取控制、警報和視訊系統，以及全天候對設施及場地進行現場安全巡邏。 控制室監督員隨時都在 SOC 內，以監視資料中心內的實際存取。 |


 ## <a name="nist-800-53-control-pe-6b"></a>NIST 800-53 控制措施 PE-6.b

#### <a name="monitoring-physical-access"></a>監視實際存取

**PE-6.b** 組織會依 [指派：組織定義的頻率] 來檢閱實際存取記錄，並在發生 [指派：組織定義的事件或事件的潛在指示] 時進行檢閱。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 會持續檢閱實際存取記錄，並加以維護，以利後續調查檢閱。 |


 ## <a name="nist-800-53-control-pe-6c"></a>NIST 800-53 控制措施 PE-6.c

#### <a name="monitoring-physical-access"></a>監視實際存取

**PE-6.c** 組織會利用組織事件回應能力來協調檢閱與調查的結果。 

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 發生在資料中心的安全性事件會由安全性小組來記載。 安全性小組會建立報告，在事件發生之後擷取安全性事件的詳細資料。 <br /> 若是需要通知政府的事件，Microsoft Azure 安全性小組將協調主要應用程式提供者 (例如 O365) 來通知政府機構客戶、美國 CERT，以及美國 CERT 指引內的 FedRAMP (請參閱 IR-6)。 |


 ### <a name="nist-800-53-control-pe-6-1"></a>NIST 800-53 控制措施 PE-6 (1)

#### <a name="monitoring-physical-access--intrusion-alarms--surveillance-equipment"></a>監視實際存取 | 入侵警報 / 監視設備

**PE-6 (1)** 組織會監視實際入侵警報與監視設備。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 除了全天候維持現場安全性，Microsoft Azure 資料中心 (已租用且完全管理) 也會利用警報監視系統和 CCTV。 警報會由 SOC 中全天候配置的控制室監督員來監視與回應。 發生回應情況時，控制室監督員會利用要調查之事件區域中的攝影機，來為回應者提供即時資訊。 |


 ### <a name="nist-800-53-control-pe-6-4"></a>NIST 800-53 控制措施 PE-6 (4)

#### <a name="monitoring-physical-access--monitoring-physical-access-to-information-systems"></a>監視實際存取 | 監視對資訊系統的實際存取

**PE-6 (4)** 除了對於作為 [指派：包含資訊系統一或多個元件之組織定義的實體空間] 之設施的實際存取監視之外，組織還會監視對資訊系統的實際存取。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會監視對資料中心內之設施以及資訊系統的實際存取。 所有 Microsoft 線上服務的設備都放置於要監視實際存取之資料中心內的位置上。 所有的主機託管場所和 MDF 室都會受到存取控制、警報及視訊所保護。 |


 ## <a name="nist-800-53-control-pe-8a"></a>NIST 800-53 控制措施 PE-8.a

#### <a name="visitor-access-records"></a>訪客存取記錄

**PE-8.a** 組織會針對 [指派：組織定義的時間週期]，保留資訊系統所在設施的訪客存取記錄。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 資料中心存取記錄會以已核准的要求形式保留於資料中心存取工具中。 如 PE-3 中所述，訪客隨時都需有人陪同。 資料中心內陪同者的存取會加以記錄，而且可以視需要來與訪客相互關聯，以利未來檢閱。 |


 ## <a name="nist-800-53-control-pe-8b"></a>NIST 800-53 控制措施 PE-8.b

#### <a name="visitor-access-records"></a>訪客存取記錄

**PE-8.b** 組織會依 [指派：組織定義的頻率] 來檢閱訪客存取記錄。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 具備已核准存取要求的訪客，將會在根據政府發出的 ID 或 Microsoft 發出的識別證形式來驗證其識別時，檢閱他們的存取記錄。 如 PE-3 中所述，當訪客在資料中心時，隨時都需有人陪同。 |


 ### <a name="nist-800-53-control-pe-8-1"></a>NIST 800-53 控制措施 PE-8 (1)

#### <a name="visitor-access-records--automated-records-maintenance--review"></a>訪客存取記錄 | 自動化的記錄維護 / 檢閱

**PE-8 (1)** 組織採用自動化機制，以協助維護與檢閱訪客存取記錄。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會在 DCAT 中，以已核准的 DCAT 要求形式保留資料中心存取記錄。 DCAT 要求只能由 DCM 小組核准。 資料中心內的存取層級會在 DCAT 內指派與管理。 每季都會檢閱資料中心存取。 對於 Azure 資料中心的所有存取都會記錄在 DCAT，以供未來可能的調查使用。 訪客隨時都需有人陪同。 資料中心內陪同者的存取會記錄於警報監視系統內，而且可以視需要來與訪客相互關聯，以利未來檢閱。 訪客存取會由指派的陪同者，以及由控制室監督員透過 CCTV 和警報監視系統來持續檢閱。 不會為訪客提供存取權限，而且隨時都必須由他們的陪同者伴隨。 |


 ## <a name="nist-800-53-control-pe-9"></a>NIST 800-53 控制措施 PE-9

#### <a name="power-equipment-and-cabling"></a>電源設備及連接纜線

**PE-9** 組織會保護資訊系統的電源設備與電力纜線連接，以防止發生損毀和破壞。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 會為纜線提供保護空間並適當地設定標籤。 Microsoft Azure 基礎結構設備 (例如，纜線、電線及備用發電機) 必須放置於已建造來受到保護以免於承擔環境風險的環境中，例如，遭竊、火災、炸藥、煙霧、水、灰塵、震動、地震、有害的化學藥品、電子干擾、電力中斷、電氣干擾 (尖峰)。 所有的可攜式線上服務資產 (例如，機架、伺服器、網路裝置) 都必須就定位鎖住或固定，以便提供保護，來防止遭竊或移動損毀。 任何 Microsoft Azure 環境中的電源線和資訊系統纜線都會適當地加上標籤，並提供保護以防止遭到攔截或損毀。 電源線和資訊系統纜線在環境內的所有點都是彼此分隔的，以避免發生干擾。 |


 ## <a name="nist-800-53-control-pe-10a"></a>NIST 800-53 控制措施 PE-10.a

#### <a name="emergency-shutoff"></a>緊急關閉

**PE-10.a** 組織提供可在緊急情況下關閉資訊系統或個別系統元件電源的功能。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 已依當地消防法規，在資訊中心內的數個位置安裝了緊急電源關閉 (EPO) 按鈕。 在某些 Microsoft Azure 管理的資料中心，資料中心設計不再需要 EPO 按鈕。 |


 ## <a name="nist-800-53-control-pe-10b"></a>NIST 800-53 控制措施 PE-10.b

#### <a name="emergency-shutoff"></a>緊急關閉

**PE-10.b** 組織將緊急關閉開關或裝置設置於 [指派：組織依資訊系統或系統元件定義的位置]，以方便人員透過安全又簡單的方式來存取。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 策略性地設置 EPO 按鈕，以允許在發生緊急情況時啟動。 EPO 按鈕可設置於主機託管場所、已配置人員的設施作業中心 (FOC)，或根據當地消防法規來設置。 |


 ## <a name="nist-800-53-control-pe-10c"></a>NIST 800-53 控制措施 PE-10.c

#### <a name="emergency-shutoff"></a>緊急關閉

**PE-10.c** 組織會保護緊急電源關閉功能，以防止未經授權的啟動。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 為了防止意外啟動，EPO 按鈕可能具有防護的機箱、需要雙重啟動，或利用可發出聲音的警報作為啟動之前的警告。 此外，EPO 按鈕會處於視訊監視狀態下。 |


 ## <a name="nist-800-53-control-pe-11"></a>NIST 800-53 控制措施 PE-11

#### <a name="emergency-power"></a>緊急電源

**PE-11** 組織提供短期不斷電供應，有助於在發生主要電力來源遺失的情況下，[選取 (一或多個)：循序關閉資訊系統；將資訊系統轉換到長期備用電源]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 已利用不斷電供應 (UPS) 系統來保護資料中心設備和電路，藉以供應緊急電源，而 UPS 系統可提供短期電源供應來提供電力，直到發電機能夠上線為止。 |


 ### <a name="nist-800-53-control-pe-11-1"></a>NIST 800-53 控制措施 PE-11 (1)

#### <a name="emergency-power--long-term-alternate-power-supply---minimal-operational-capability"></a>緊急電源 | 長期備用電源供應 - 基本運作能力

**PE-11 (1)** 組織會針對資訊系統提供長期備用電源供應，使其能夠在延長發生主要電力來源遺失時維持所需的最低限度運作能力。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 已針對資訊系統提供長期備用電源供應，使其能夠在延長發生主要電力來源遺失時維持所需的最低限度運作能力。 當電源故障或降至不可接受的電壓等級時，不斷電供應 (UPS) 系統就會立即開始運作並接管 電力負載。 這提供足夠的電力來執行伺服器，直到發電機可以接管為止。 緊急發電機可提供備用電源來延長中斷時間以及進行有計劃的維護，而且可以在發生天然災害時，使用現場燃料儲量來讓資料中心運作。 Azure 在我們的許多資料中心都備有柴油發電機。 必要時可以使用備用發電機，協助維持電網的穩定性或進行非常規性維修，以及需要我們從電網中卸除資料中心的維護情況。 |


 ## <a name="nist-800-53-control-pe-12"></a>NIST 800-53 控制措施 PE-12

#### <a name="emergency-lighting"></a>緊急照明

**PE-12** 組織會針對資訊系統採用和維護自動緊急照明，其會在發生電力中斷或干擾時啟動，並涵蓋設施內的緊急出口和撤離路線。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 資料中心 (已租用且完全管理) 會在透過 UPS 和發電機系統進行備用 (請參閱 PE-11) 的專用線路上，以高架的緊急照明形式來提供緊急照明。 根據美國消防協會 (NFPA) 的生命安全法規，需沿著所有撤離線路、緊急出口及主機託管場所內部實施自動緊急照明。 如果公用電源斷電，緊急照明將自動切換到由 UPS 和發電機系統所提供的電源。 Microsoft Azure 資料中心內的緊急照明系統均會進行例行性維護工作，以確保它們處於正常運作狀態。 |


 ## <a name="nist-800-53-control-pe-13"></a>NIST 800-53 控制措施 PE-13

#### <a name="fire-protection"></a>消防措施

**PE-13** 組織會針對資訊系統，採用並維護由獨立能源所支援的滅火和火警探測裝置/系統。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 已藉由在 Microsoft Azure 資料中心安裝火警探測和滅火系統來實作防火措施。 <br /> Microsoft Azure 資料中心會實作健全的火警探測機制。 Microsoft Azure 的消防方法包含使用安裝於地板下方和天花板上的光電煙霧警報器，整合之後形成消防撒水系統。 此外，在每個主機託管場所都有 Xtralis VESDA (極早期偵煙系統) 系統來監測空氣。 VESDA 單位是在多個高價值空間內四處安裝的高靈敏度空氣取樣系統。 VESDA 單位允許在發生實際火災探測警報之前進行調查性回應。 <br /> 整個資料中心四處均會安裝「報警按鈕」火災警報箱，以便手動進行火警通知。 資料中心內到處都會放置滅火器，而且每年均會進行適當的檢查、維修及標記。 每個排班的安全人員均會多次巡邏所有建築物區域。 資料中心的人員會進行每日現場巡察，以確保符合所有消防巡察的要求。 <br /> 包含敏感性電子設備 (主機託管、MDF 等) 的區域，均會受到雙重聯鎖預動式 (乾管式) 消防撒水系統所保護。 乾管式撒水系統是兩段式預動系統，需要啟動撒水噴頭 (基於熱度)，以及進行煙霧偵測來噴水。 啟動撒水噴頭會釋放管子中的空氣壓力，以便讓水充滿整個管子。 當煙霧或熱度探測器也被啟動時，就會噴出水。 <br /> 火災檢測/滅火以及緊急照明系統都已透過線路連接到資料中心 UPS 和警報器系統，這些系統都會提供來供備用電力來源使用。 |


 ### <a name="nist-800-53-control-pe-13-1"></a>NIST 800-53 控制措施 PE-13 (1)

#### <a name="fire-protection--detection-devices--systems"></a>消防措施 | 探測裝置 / 系統

**PE-13 (1)** 組織會針對資訊系統採用火災探測裝置/系統，其會在發生火災時自動啟動，並通知 [指派：組織定義的人員或角色] 和 [指派：組織定義的緊急應變人員]。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會針對資訊系統採用火災探測裝置/系統，其會在發生火災時自動啟動，並通知資訊中心的人員以及緊急應變人員。 如果任何一個主機託管場所中啟動了某一個火警探測機制，即會透過火災警報系統自動通知當地的消防部門。 此外，會將消防措施和火災探測系統繫結到安全系統，以通知當地的設施和安全人員。 |


 ### <a name="nist-800-53-control-pe-13-2"></a>NIST 800-53 控制措施 PE-13 (2)

#### <a name="fire-protection--suppression-devices--systems"></a>消防措施 | 滅火裝置 / 系統

**PE-13 (2)** 組織會針對資訊系統採用滅火裝置/系統，為 [指派：組織定義的人員或角色] 和 [指派：組織定義的緊急應變人員] 提供任何啟動的自動通知。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 如果資料中心內啟動了某一個滅火系統，即會透過火災警報系統自動通知當地的消防部門。 此外，會將消防措施和火災探測系統繫結到安全系統，以通知當地的設施和安全人員。 |


 ### <a name="nist-800-53-control-pe-13-3"></a>NIST 800-53 控制措施 PE-13 (3)

#### <a name="fire-protection--automatic-fire-suppression"></a>消防措施 | 自動滅火

**PE-13 (3)** 組織會在設施並未持續配置人員時，針對資訊系統採用自動滅火功能。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 資料中心會全天候配置人員。 在偵測到火災警報狀況時，不需人工介入，滅火系統就會自動啟動。 |


 ## <a name="nist-800-53-control-pe-14a"></a>NIST 800-53 控制措施 PE-14.a

#### <a name="temperature-and-humidity-controls"></a>溫度和溼度的控制措施

**PE-14.a** 組織會在 [指派：組織定義的可接受層級]，於資訊系統所在設施內維持溫度和濕度。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 會依據美國空調冷凍工程師學會 (ASHRAE) 的指引來維持溫度和溼度。 溫度和溼度會由資料中心的建築物管理系統 (BMS) 持續監控。 |


 ## <a name="nist-800-53-control-pe-14b"></a>NIST 800-53 控制措施 PE-14.b

#### <a name="temperature-and-humidity-controls"></a>溫度和溼度的控制措施

**PE-14.b** 組織會依 [指派：組織定義的頻率] 來監控溫度和溼度。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 在 Microsoft Azure 資料中心，溫度和溼度會由建築物管理系統 (BMS) 持續監控。 資料中心的人員會從設施作業中心 (FOC) 監控 BMS，因此，他們可以在超過任何一個警報點之前，管理資料中心內的溫度和濕度。 |


 ### <a name="nist-800-53-control-pe-14-2"></a>NIST 800-53 控制措施 PE-14 (2)

#### <a name="temperature-and-humidity-controls--monitoring-with-alarms--notifications"></a>溫度和溼度的控制措施 | 使用警報 / 通知進行監控

**PE-14 (2)** 組織會採用溫度和溼度監控，以便提供可能會對人員或設備有害之變更的警報或通知。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 在 Microsoft Azure 資料中心，溫度和溼度會由建築物管理系統 (BMS) 持續監控。 資料中心的人員會從設施作業中心 (FOC) 監控 BMS，因此，他們可以在超過任何一個警報點之前，管理資料中心內的溫度和濕度。 |


 ## <a name="nist-800-53-control-pe-15"></a>NIST 800-53 控制措施 PE-15

#### <a name="water-damage-protection"></a>因水損毀的防護

**PE-15** 組織會藉由提供可存取、正常運作且讓關鍵人員知道的主要關閉或隔離閥來保護資訊系統，以免遭受因漏水所造成的損害。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 會在有漏水風險的區域 (例如空氣調節器) 中提供水/洩漏檢測。 滅火系統也有要監控的洩漏檢測警報。 水/洩漏檢測系統會與設施警報和通知系統整合。 資料中心內的消防撒水系統已分區。 資料中心的人員皆熟悉需要使用停水閥及其位置的緊急程序。 撒水器豎管能夠個別關閉，或透過閘閥分組關閉。 重要空間內的所有撒水器都是雙向聯鎖預動式類型的撒水器，需要兩種形式的啟動，才能開始流動。 撒水系統的壓力會根據漏水狀況進行監控並發出警報。 |


 ### <a name="nist-800-53-control-pe-15-1"></a>NIST 800-53 控制措施 PE-15 (1)

#### <a name="water-damage-protection--automation-support"></a>因水損毀的防護 | 自動化支援

**PE-15 (1)** 組織會採用自動化機制來偵測資訊系統鄰近區域是否有水存在，並向 [指派：組織定義的人員或角色] 發出警示。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會採用自動化機制來檢測資料中心內是否有水存在，並向資訊中心的人員發出警示。 Azure 會在有漏水風險的區域 (例如空氣調節器) 中提供水/洩漏檢測。 滅火系統也有要監控的洩漏檢測警報。 水/洩漏檢測系統會與設施警報和通知系統整合。 撒水系統的壓力會根據漏水狀況進行監控並發出警報。 |


 ## <a name="nist-800-53-control-pe-16"></a>NIST 800-53 控制措施 PE-16

#### <a name="delivery-and-removal"></a>傳遞和移除

**PE-16** 組織會授權、監視和控制 [指派：組織定義的資訊系統元件類型] 進出設施，並維護這些項目的記錄。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 會代表客戶實作此需求。 Microsoft Azure 嚴格強制執行允許進出資料中心的一切。 所有系統元件/資產都會在資產管理工具資料庫中進行追蹤。 |


 ## <a name="nist-800-53-control-pe-17a"></a>NIST 800-53 控制措施 PE-17.a

#### <a name="alternate-work-site"></a>備用工作場所

**PE-17.a** 組織會在備用工作場所採用 [指派：組織定義的安全性控制措施]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級備用工作場所佈建可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pe-17b"></a>NIST 800-53 控制措施 PE-17.b

#### <a name="alternate-work-site"></a>備用工作場所

**PE-17.b** 組織會評估為可行，位於備用工作場所之安全性控制措施的有效性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級備用工作場所佈建可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pe-17c"></a>NIST 800-53 控制措施 PE-17.c

#### <a name="alternate-work-site"></a>備用工作場所

**PE-17.c** 組織提供方法，讓員工能夠在發生安全性事件或問題時與資訊安全人員溝通。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級備用工作場所佈建可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-pe-18"></a>NIST 800-53 控制措施 PE-18

#### <a name="location-of-information-system-components"></a>資訊系統元件的位置

**PE-18** 組織會將資訊系統元件安置於設施內，以減少可能來自 [指派：組織定義的實際和環境危害物] 的損害，並將未經授權的存取機會降至最低。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Azure 會實作策略性資料中心設計方法，以符合資訊系統元件控制措施的位置。 所有 Microsoft 線上服務的基礎結構設備都必須放置於已建造來受到保護以免於承擔環境風險的環境中，例如，遭竊、火災、炸藥、煙霧、水、灰塵、震動、地震、有害的化學藥品、電子干擾、電力中斷、電氣干擾 (尖峰) 及輻射。 設施和基礎結構均已實施耐震補強，以避免經歷環境危害。 所有的主機託管場所和 MDF 室都會受到存取控制、警報及視訊所保護。 設施也會由安全人員全天候巡邏。 所有的可攜式 Azure 資產均已定位鎖住或固定，以便提供保護，來防止遭竊或移動損毀。 |


