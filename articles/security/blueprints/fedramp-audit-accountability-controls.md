---
title: "FedRAMP Azure Blueprint (藍圖) 自動化 - 稽核和權責"
description: "適用於 FedRAMP 的 Web 應用程式 - 稽核和權責"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>稽核和權責 (AU)

> [!NOTE]
> 這些控制措施是由 NIST 與美國商務部 (U.S. Department of Commerce) 於 NIST 特刊 800-53 第 4 版 (NIST Special Publication 800-53 Revision 4) 中所定義。 如需了解每項控制措施的測試程序和指導方針相關資訊，請參閱《NIST 800-53 第 4 版》。

## <a name="nist-800-53-control-au-1"></a>NIST 800-53 控制措施 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>稽核和權責原則與程序

**AU-1** 組織會針對 [指派：組織定義的人員或角色]，開發、記載及散佈因應目的、範圍、角色、責任、管理承諾、組織實體間之協調合作及合規性的稽核和權責原則，以及可協助實作稽核和權責原則及相關稽核和權責控制措施的程序；並檢閱和更新目前的稽核和權責原則 (依 [指派：組織定義的頻率]) 與稽核和權責程序 (依 [指派：組織定義的頻率])。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶的企業層級稽核和權責原則與程序可能就足以因應這項控制措施。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-2a"></a>NIST 800-53 控制措施 AU-2.a

#### <a name="audit-events"></a>稽核事件

**AU-2.a** 組織會判斷資訊系統是否能夠稽核下列事件：[指派：組織定義的可稽核事件]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 適用於這個 Azure Blueprint (藍圖) 的稽核功能是由 Azure 監視器和 OMS 中的 Log Analytics 服務所提供。 Azure 監視器會提供有關與已部署資源相關聯之活動的詳細稽核記錄。 這些記錄和 OS 層級的記錄都會透過 Log Analytics 來收集，並儲存於 OMS 存放庫中。 Log Analytics 會跨由此解決方案部署的資源將稽核資料相互關聯，並且可以擴充到客戶部署的 Web 應用程式。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-2b"></a>NIST 800-53 控制措施 AU-2.b

#### <a name="audit-events"></a>稽核事件

**AU-2.b** 組織會協調安全性稽核功能與需要稽核相關資訊的其他組織實體，以增強相互支援，並協助引導選取可稽核的事件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會依賴既定的企業層級程序來判斷可稽核的事件。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-2c"></a>NIST 800-53 控制措施 AU-2.c

#### <a name="audit-events"></a>稽核事件

**AU-2.c** 組織會提供一個基本理由，來闡述為什麼認為可稽核的事件足以支援安全性事件的事後調查。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所稽核的事件包括足以判斷事件發生時機、事件來源、事件結果的相關資訊，以及支援調查安全性事件的其他詳細資訊。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-2d"></a>NIST 800-53 控制措施 AU-2.d

#### <a name="audit-events"></a>稽核事件

**AU-2.d** 組織會判斷要在資訊系統內稽核下列事件：[指派：組織定義的稽核事件 (AU-2.a 中定義的可稽核事件子集) 以及針對每個識別出的事件 (或所需的情況) 進行稽核的頻率]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所稽核的事件包括由 Azure 活動記錄，針對已部署的資源、OS 層級記錄、Active Directory 記錄及 SQL Server 記錄進行稽核的事件。 客戶可以選取要稽核以符合任務需求的其他事件。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 控制措施 AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>稽核事件 | 檢閱和更新

**AU-2 (3)** 組織會依 [指派：組織定義的頻率] 來檢閱和更新稽核的事件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 針對已定義的稽核事件組，客戶可能會依賴既定的企業層級定期檢閱和更新程序。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-3"></a>NIST 800-53 控制措施 AU-3

#### <a name="content-of-audit-records"></a>稽核記錄的內容

**AU-3** 資訊系統會產生稽核記錄，其中包含確立發生了哪種類型的事件、發生事件的時機、發生事件的位置、事件來源、事件結果，以及與事件相關聯之任何個人或主體的身分識別的資訊。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會依賴 Azure、Windows Server 及 SQL Server 的內建稽核功能。 這些稽核解決方案會擷取含有足夠詳細資料的稽核記錄，以滿足這個控制措施的需求。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 控制措施 AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>稽核記錄的內容 | 其他稽核資訊

**AU-3 (1)** 資訊系統會產生稽核記錄，其中包含下列其他資訊：[指派：組織定義的其他更詳細資訊]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure 活動記錄事件會使用詳細的結構描述，其中包含適用於超過 20 種類型之稽核資訊的欄位。 除了活動記錄，這個 Azure Blueprint (藍圖) 也會在 OMS 中部署 Log Analytics 解決方案，以支援不同的資料來源組合，包括 Windows 記錄、Linux 記錄、Azure 診斷記錄及客戶記錄。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 控制措施 AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>稽核記錄的內容 | 集中管理已計劃的稽核記錄內容

**AU-3 (2)** 資訊系統會針對在 [指派：組織定義的資訊系統元件] 所產生之稽核記錄中擷取的內容，提供集中管理和設定。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的所有虛擬機器會都加入已部署的 Active Directory 網域。 所有已加入網域的虛擬機器都會實作群組原則，可設定此原則來集中管理 OS 層級的稽核系統設定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-4"></a>NIST 800-53 控制措施 AU-4

#### <a name="audit-storage-capacity"></a>稽核儲存體容量

**AU-4** 組織會根據 [指派：組織定義的稽核記錄儲存體需求] 來配置稽核記錄儲存體容量。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會配置足夠的儲存體容量來保留稽核記錄一年的時間。 所有稽核記錄都是透過 Log Analytics 收集的，其會設定為保留一年。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-5a"></a>NIST 800-53 控制措施 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>稽核處理失敗時的回應

**AU-5.a** 資訊系統會在發生稽核處理失敗時，向 [指派：組織定義的人員或角色] 發出警示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure 監視器和 Log Analytics 的服務狀態可在 Azure 狀態網站上，以及 Azure 入口網站的 [服務健康情況] 刀鋒視窗中取得。 警示可以透過 Log Analytics 設定，以提供其他類型之稽核處理失敗的通知。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-5b"></a>NIST 800-53 控制措施 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>稽核處理失敗時的回應

**AU-5.b** 資訊系統會採取下列其他動作：[指派：要採取之組織定義的動作 (例如，關閉資訊系統、覆寫最舊的稽核記錄、停止產生稽核記錄)]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署之資源產生的所有稽核記錄都會透過 Log Analytics 收集，並保留一年的時間。 適用於這個稽核記錄儲存體的儲存體配置會以動態方式配置，以確保有足夠的容量可供使用。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 控制措施 AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>稽核處理失敗時的回應 | 稽核儲存體容量

**AU-5 (1)** 資訊系統會在配置的稽核記錄儲存體容量到達存放庫稽核記錄儲存體容量上限的 [指派：組織定義的百分比] 時，在 [指派：組織定義的時間週期] 內，為 [指派：組織定義的人員、角色和 (或) 位置] 提供警告。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署之資源產生的所有稽核記錄都會透過 Log Analytics 收集，並保留一年的時間。 適用於這個稽核記錄儲存體的儲存體配置會以動態方式配置，以確保有足夠的容量可供使用。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 控制措施 AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>稽核處理失敗時的回應 | 即時警示

**AU-5 (2)** 資訊系統會在發生下列稽核失敗事件時，於 [指派：組織定義的即時期間]，為 [指派：組織定義的人員、角色和 (或) 位置] 提供警示：[指派：組織定義的稽核失敗事件需要即時警示]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | Azure 的服務狀態可在 Azure 入口網站的 [服務健康情況] 刀鋒視窗上取得。 警示可以透過 Log Analytics 設定，以提供其他類型之稽核處理失敗的通知。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-6a"></a>NIST 800-53 控制措施 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>稽核檢閱、分析和報告

**AU-6.a** 組織會依 [指派：組織定義的頻率] 來檢閱和分析資訊系統稽核記錄，以取得 [指派：組織定義的不當或不尋常活動] 的指示。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責檢閱和分析客戶部署資源 (包含應用程式、作業系統、資料庫及軟體) 的稽核記錄。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 控制措施 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>稽核檢閱、分析和報告

**AU-6.b** 組織會向 [指派：組織定義的人員或角色] 報告結果。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶須負責報告客戶部署資源上不當或不尋常活動 (定義於 AU-06.a) 的結果。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 控制措施 AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>稽核檢閱、分析和報告 | 程序整合

**AU-6 (1)** 組織會採用自動化機制，來整合稽核檢閱、分析和報告程序，以支援用以調查與回應可疑活動的組織程序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 客戶可能會仰賴企業層級的集中式稽核檢閱、分析和報告功能。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 控制措施 AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>稽核檢閱、分析和報告 | 將稽核存放庫相互關聯

**AU-6 (3)** 組織會跨不同的存放庫分析稽核記錄並使其相互關聯，以取得整個組織的局勢意識。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會在 OMS 中實作 Log Analytics 解決方案，跨已部署的來源將稽核資料集中，以支援整個組織的局勢意識。 客戶可能會選擇進一步將 Log Analytics 與其他系統整合。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 控制措施 AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>稽核檢閱、分析和報告 | 集中檢閱與分析

**AU-6 (4)** 資訊系統提供此功能，以集中檢閱與分析來自系統內多個元件的稽核記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會在 OMS 中實作 Log Analytics 解決方案，跨已部署的來源將稽核資料集中，以支援集中檢閱、分析和報告。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 控制措施 AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>稽核檢閱、分析和報告 | 整合 / 掃描和監視功能

**AU-6 (5)** 組織會將稽核記錄的分析與 [選取 (一或多個)：弱點掃描資訊；效能資料；資訊系統監視資訊；[指派：組織定義且收集自其他系統的資料/資訊]] 的分析整合，以進一步增強識別不當或不尋常活動的能力。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會部署「OMS 安全性與稽核」解決方案。 這個解決方案可提供安全性狀態的全面性檢視。 [安全性與稽核] 儀表板使用可跨已部署 OMS 解決方案取得的資料，來提供對於已部署資源 之安全性狀態的深入了解，以整合來自基準與修補評估的記錄資料與弱點資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 控制措施 AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>稽核檢閱、分析和報告 | 與實體監視相互關聯

**AU-6 (6)** 組織會將來自稽核記錄的資訊與從監視實際存取中取得的資訊相互關聯，以進一步增強識別可疑、不當、異常或惡意活動的能力。

**責任：**`Azure Only`

|||
|---|---|
| **客戶** | 客戶無法實際存取 Azure 資料中心內的任何系統資源。 |
| **提供者 (Microsoft Azure)** | Microsoft Azure 的 SIM 小組會使用相關的實體監視資料，並將它與稽核記錄相互關聯，以判斷在偵測到實際事件時是否發生了任何相關聯的邏輯缺口或可疑行為。 |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 控制措施 AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>稽核檢閱、分析和報告 | 允許的動作

**AU-6 (7)** 組織會針對與稽核資訊之檢閱、分析和報告相關聯的每個 [選取 (一或多個)：資訊系統程序；角色；使用者] 指定允許的動作。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的 Windows 虛擬機器會實作 OS 層級的權限，以限制使用者可以採取關於稽核資訊的動作。 在 Azure 中，可以將使用者或使用者群組指派給角色 (例如，擁有者、參與者、讀者或自訂角色)，以限制關於任何資源或已部署解決方案 (包括 Log Analytics) 的可用動作。  |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 控制措施 AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>稽核檢閱、分析和報告 | 調整稽核層級

**AU-6 (10)** 根據執法資訊、智慧資訊或其他可靠來源的資訊進行有風險的變更時，組織會在資訊系統中調整稽核檢閱、分析和報告的層級。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 根據執法、智慧或其他可靠來源所提供的資訊進行有風險的變更時，客戶須負責針對客戶部署的資源 (包含應用程式、作業系統、資料庫和軟體) 調整稽核檢閱、分析和報告的層級。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-7a"></a>NIST 800-53 控制措施 AU-7.a

#### <a name="audit-reduction-and-report-generation"></a>縮減稽核與產生報告

**AU-7.a** 資訊系統會提供縮減稽核與產生報告功能，以支援依需求稽核檢閱、分析和報告需求，以及對安全性事件進行事後調查。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 解決方案。 Log Analytics 藉由將受管理資源中的資料收集到中央存放庫，來提供 OMS 的監視服務。 所收集的資料即可用於警示、分析和匯出。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-7b"></a>NIST 800-53 控制措施 AU-7.b

#### <a name="audit-reduction-and-report-generation"></a>縮減稽核與產生報告

**AU-7.b** 資訊系統所提供的縮減稽核與產生報告功能，不會改變稽核記錄的原始內容或時間順序。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 解決方案。 Log Analytics 藉由將受管理資源中的資料收集到中央存放庫，來提供 OMS 的監視服務。 稽核記錄的內容和時間順序不會在 Log Analytics 收集時改變。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 控制措施 AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>縮減稽核與產生報告 | 自動化程序

**AU-7 (1)** 資訊系統提供此功能，以根據 [指派：稽核記錄內組織定義的稽核欄位] 處理感興趣事件的稽核記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 解決方案。 Log Analytics 藉由將受管理資源中的資料收集到中央存放庫，來提供 OMS 的監視服務。 所收集的資料即可用於警示、分析和匯出。 Log Analytics 包含強大的查詢語言，可擷取儲存機制中儲存的資料。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-8a"></a>NIST 800-53 控制措施 AU-8.a

#### <a name="time-stamps"></a>時間戳記

**AU-8.a** 資訊系統會使用內部系統時鐘來產生稽核記錄的時間戳記。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會使用內部系統時鐘來產生稽核記錄的時間戳記。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-8b"></a>NIST 800-53 控制措施 AU-8.b

#### <a name="time-stamps"></a>時間戳記

**AU-8.b** 資訊系統會記錄稽核記錄的時間戳記，其可對應至國際標準時間 (UTC) 或格林威治標準時間 (GMT)，並符合 [指派：組織定義的時間度量規模]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會使用內部系統時鐘來產生稽核記錄的時間戳記。 時間戳記是以 UTC 格式記錄的。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-8-1a"></a>NIST 800-53 控制措施 AU-8 (1).a

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>時間戳記 | 與授權時間來源同步處理

**AU-8 (1).a** 資訊系統會依 [指派：組織定義的頻率] 來比較內部資訊系統時鐘與 [指派：組織定義的授權時間來源]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會使用內部系統時鐘來產生稽核記錄的時間戳記。 內部系統時鐘均設定為與授權時間來源同步處理。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-8-1b"></a>NIST 800-53 控制措施 AU-8 (1).b

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>時間戳記 | 與授權時間來源同步處理

**AU-8 (1).b** 資訊系統會在時間差異超過 [指派：組織定義的時間週期] 時，將內部系統時鐘同步處理至授權時間來源。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所部署的資源會使用內部系統時鐘來產生稽核記錄的時間戳記。 內部系統時鐘均設定為與授權時間來源同步處理。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-9"></a>NIST 800-53 控制措施 AU-9

#### <a name="protection-of-audit-information"></a>保護稽核資訊

**AU-9** 資訊系統會保護稽核資訊和稽核工具，以防止未經授權的存取、修改和刪除。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 在這個 Azure Blueprint (藍圖) 內使用邏輯存取控制項來保護稽核資訊和工具，以防止遭到未經授權的存取、修改和刪除。 Azure Active Directory 會使用角色型群組成員資格，強制執行已核准的邏輯存取。 只限需要那些權限的使用者，才能檢視稽核資訊和使用稽核工具。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 控制措施 AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>保護稽核資訊 | 在不同的實體系統 / 元件上稽核備份

**AU-9 (2)** 資訊系統會依 [指派：組織定義的頻率]，將稽核記錄備份到實際上與所稽核之系統或元件不同的系統或系統元件中。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 服務。 已部署的 VM 和 Azure 診斷儲存體帳戶均為連線到 Log Analytics 的來源，並會與其來源分開保留。 資料是由 OMS 以接近即時的方式收集的。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 控制措施 AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>保護稽核資訊 | 密碼編譯保護

**AU-9 (3)** 資訊系統會實作密碼編譯機制，以保護稽核資訊和稽核工具的完整性。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 服務。 Log Analytics 透過 Azure 驗證來驗證憑證和資料完整性，以確保傳入的資料來自信任的來源。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 控制措施 AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>保護稽核資訊 | 透過具特殊權限的使用者子集存取

**AU-9 (4)** 組織只會將稽核功能管理的存取權限授與 [指派：組織定義之具特殊權限的使用者子集]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 在這個 Azure Blueprint (藍圖) 內使用邏輯存取控制項來保護稽核資訊和工具，以防止遭到未經授權的存取、修改和刪除。 Azure Active Directory 會使用角色型群組成員資格，強制執行已核准的邏輯存取。 只限需要那些權限的使用者，才能檢視稽核資訊和使用稽核工具。
 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-10"></a>NIST 800-53 控制措施 AU-10

#### <a name="non-repudiation"></a>不可否認性

**AU-10** 資訊系統會提供保護，以防止某人 (或代表個人執行的程序) 不實地否認已執行 [指派：不可否認性所涵蓋之組織定義的動作]。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 適用於這個 Azure Blueprint (藍圖) 的稽核功能是由 Azure 監視器和 OMS 中的 Log Analytics 服務所提供。 Azure 監視器會提供有關與已部署資源相關聯之活動的詳細稽核記錄。 這些記錄和 OS 層級的記錄都會透過 Log Analytics 來收集，並儲存於 OMS 存放庫中。 這些記錄包含了資訊系統事件的詳細記錄，有助於防範不可否認性。 此外，會使用角色型存取控制來限制對於記錄資料的存取，以防止對記錄資料進行未經授權的修改或刪除。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-11"></a>NIST 800-53 控制措施 AU-11

#### <a name="audit-record-retention"></a>保留稽核記錄

**AU-11** 組織會保留稽核記錄 [指派：與記錄保留原則一致之組織定義的時間週期]，以提供對於安全性事件事後調查的支援，並符合法規和組織資訊保留需求。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 服務。 Log Analytics 藉由將受管理資源中的資料收集到中央存放庫，來提供 OMS 的監視服務。 一旦收集完成之後，每個 Log Analytics 設定的資料都會保留一年。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-12a"></a>NIST 800-53 控制措施 AU-12.a

#### <a name="audit-generation"></a>稽核產生

**AU-12.a** 資訊系統會位於 [指派：組織定義的資訊系統元件] 上，針對 AU-2.a 中定義的可稽核事件， 提供稽核記錄產生功能。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所稽核的事件包括由 Azure 活動記錄，針對已部署的資源、OS 層級記錄、Active Directory 記錄及 SQL Server 記錄進行稽核的事件。 客戶可以選取要稽核以符合任務需求的其他事件。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-12b"></a>NIST 800-53 控制措施 AU-12.b

#### <a name="audit-generation"></a>稽核產生

**AU-12.b** 資訊系統可讓 [指派：組織定義的人員或角色] 選取要透過資訊系統的特定元件來稽核哪些可稽核的事件。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 在 Azure 中及虛擬機器 OS 層級上，使用角色型存取控制來限制對稽核功能的存取。 選取來要由這個 Azure Blueprint (藍圖) 所部署之資源稽核的事件設定，可以由具備適當角色型授權的使用者來設定。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ## <a name="nist-800-53-control-au-12c"></a>NIST 800-53 控制措施 AU-12.c

#### <a name="audit-generation"></a>稽核產生

**AU-12.c** 資訊系統會針對 AU-2.d 中定義的事件， 使用 AU-3 中定義的內容來產生稽核記錄。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 所稽核的事件包括由 Azure 活動記錄，針對已部署的資源、OS 層級記錄、Active Directory 記錄及 SQL Server 記錄進行稽核的事件。 客戶可以選取要稽核以符合任務需求的其他事件。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 控制措施 AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>稽核產生 | 全系統 / 時間相互相關的稽核線索

**AU-12 (1)** 資訊系統會將來自 [指派：組織定義的資訊系統元件] 的稽核記錄編譯為全系統 (邏輯或實體) 的稽核線索，其在 [指派：組織定義的容錯層級，適用於稽核線索中個別記錄之時間戳記間的關聯性] 內是時間相互關聯的。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 這個 Azure Blueprint (藍圖) 會實作 OMS 中的 Log Analytics 服務。 Log Analytics 藉由將受管理資源中的資料收集到中央存放庫，來提供 OMS 的監視服務。 稽核記錄的時間戳記不會改變，因此，稽核線索是時間相互關聯的。 |
| **提供者 (Microsoft Azure)** | 不適用 |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 控制措施 AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>稽核產生 | 已獲授權的人員所做的變更

**AU-12 (3)** 資訊系統會針對 [指派：組織定義的個人或角色] 提供此功能，以將稽核變更為要在 [指派：組織定義的時間閾值] 內，根據 [指派：組織定義的可選取事件準則]，於 [指派：組織定義的資訊系統元件] 上執行。

**責任：**`Customer Only`

|||
|---|---|
| **客戶** | 在 Azure 中及虛擬機器 OS 層級上，使用角色型存取控制來限制對稽核功能的存取。 選取來要由這個 Azure Blueprint (藍圖) 所部署之資源稽核的事件設定，可以由具備適當角色型授權的使用者來設定。 |
| **提供者 (Microsoft Azure)** | 不適用 |
