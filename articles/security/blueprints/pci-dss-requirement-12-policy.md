---
title: "Azure 付款處理藍圖 - 原則需求"
description: "PCI DSS 需求 12"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 05e9ed7c886d37a024db1eedbc541705b7d8a9a9
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 規範之環境的原則需求  
## <a name="pci-dss-requirement-12"></a>PCI DSS 需求 12

**維護解決所有人員資訊安全性的原則**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 若要了解每個需求的測試程序和指導方針相關資訊，請參閱 PCI DSS。

增強式安全性原則為整個實體設定了安全基調，並告知人員對其有何期望。 所有人員都應該了解資料的敏感性及其保護資料的責任。 就「需求 12」而言，「人員」是指在該實體的站台上「常駐」或以其他方式有權存取持卡人資料環境的全職和兼職員工、臨時僱員，承包商和顧問。

## <a name="pci-dss-requirement-121"></a>PCI DSS 需求 12.1

**12.1** 建立、發佈、維護及散佈安全性原則。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護資訊安全性原則。|



### <a name="pci-dss-requirement-1211"></a>PCI DSS 需求 12.1.1

**12.1.1** 至少每年檢閱安全性原則，並在環境變更時更新原則。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶負責至少每年更新其資訊安全性原則，或者在持卡人資料環境 (CDE) 變更時更新。|



## <a name="pci-dss-requirement-122"></a>PCI DSS 需求 12.2

**12.2** 實作風險評定程序：
- 至少每年執行，以及在環境有重大變更 (例如，收購、合併、遷移等) 時執行。
- 識別重要資產、威脅和弱點
- 產生風險的正式記錄分析。
- > 風險評定方法的範例包括但不限於 OCTAVE、ISO 27005 以及 NIST SP 800-30。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責實作風險評定程序，以解決「需求 12.2」中列出的所有潛在威脅。|



## <a name="pci-dss-requirement-123"></a>PCI DSS 需求 12.3

**12.3** 開發適用於重要技術的使用原則，並定義這些技術的適當使用方式。

> [!NOTE]
> 重要技術的範例包括但不限於，遠端存取和無線技術、膝上型電腦、平板電腦、抽取式電子媒體、電子郵件使用方式和網際網路使用方式。
請確定這些使用原則需要下列事項。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1231"></a>PCI DSS 需求 12.3.1

**12.3.1** 已授權之合作對象的明確核准

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1232"></a>PCI DSS 需求 12.3.2

**12.3.2** 技術的使用驗證

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1233"></a>PCI DSS 需求 12.3.3

**12.3.3** 所有此類裝置以及具有存取權的人員清單

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1234"></a>PCI DSS 需求 12.3.4

**12.3.4** 精確且迅速地判斷擁有者、連絡人資訊與目的 (例如，標記、程式碼撰寫和/或裝置的清查) 的方法

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1235"></a>PCI DSS 需求 12.3.5

**12.3.5** 可接受的技術使用方式

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1236"></a>PCI DSS 需求 12.3.6

**12.3.6** 可接受的技術網路位置

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責決定雲端式 VM、儲存體和支援服務的可接受網路位置。|



### <a name="pci-dss-requirement-1237"></a>PCI DSS 需求 12.3.7

**12.3.7** 公司核准的產品清單

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責決定雲端式 VM、儲存體和支援服務的可接受網路位置。|



### <a name="pci-dss-requirement-1238"></a>PCI DSS 需求 12.3.8

**12.3.8** 在指定未使用期間之後，自動中斷遠端存取技術的工作階段連線

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 使用了 Microsoft 公司 AD 工作階段鎖定功能，該功能會在一段未使用期間之後強制執行工作階段鎖定。 未使用 30 分鐘後會終止網路連線。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1239"></a>PCI DSS 需求 12.3.9

**12.3.9** 只有在廠商和商業合作夥伴需要時，才能針對廠商和商業合作夥伴啟用遠端存取技術，並在使用後立即停用

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-12310"></a>PCI DSS 需求 12.3.10

**12.3.10** 針對透過遠端存取技術存取持卡人資料的人員，禁止將持卡人資料複製、移動及儲存至本機硬碟和抽取式電子媒體，除非針對已定義商務需求明確授權。
當有授權的商務需求時，使用原則必須要求依據所有適用的「PCI DSS 需求」來保護資料。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責確保禁止透過遠端存取技術存取持卡人資料的人員，將持卡人資料複製、移動及儲存至本機硬碟和抽取式電子媒體，除非針對已定義商務需求明確授權。|



## <a name="pci-dss-requirement-124"></a>PCI DSS 需求 12.4

**12.4** 確保安全性原則和程序清楚定義所有人員的資訊安全性責任。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1241"></a>PCI DSS 需求 12.4.1

**12.4.1** 僅限服務提供者的其他需求：高階管理應建立持卡人資料保護責任和 PCI DSS 合規性計畫，包括：
- 維護 PCI DSS 合規性的整體權責性
- 為 PCI DSS 合規性計畫制訂章程，以及與高階管理溝通 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 身為服務提供者的客戶，必須負責記錄其 PCI 合規性計畫。|



## <a name="pci-dss-requirement-125"></a>PCI DSS 需求 12.5

**12.5** 為個人或小組指派下列資訊安全性管理責任。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責定義及指派資訊安全性責任給其員工。|



### <a name="pci-dss-requirement-1251"></a>PCI DSS 需求 12.5.1

**12.5.1** 建立、記錄及散佈安全性原則和程序。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責定義及指派資訊安全性責任給其員工。|



### <a name="pci-dss-requirement-1252"></a>PCI DSS 需求 12.5.2

**12.5.2** 監視及分析安全性警示和資訊，並散發給適當的人員。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責定義及指派資訊安全性責任給其員工。|



### <a name="pci-dss-requirement-1253"></a>PCI DSS 需求 12.5.3

**12.5.3** 建立、記錄及散佈安全性事件回應與向上提報程序，確保及時且有效地處理所有情況。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1254"></a>PCI DSS 需求 12.5.4

**12.5.4** 管理使用者帳戶，包括新增、刪除及修改。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



### <a name="pci-dss-requirement-1255"></a>PCI DSS 需求 12.5.5

**12.5.5** 監視及控制對所有資料存取。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立和維護原則，以規定適用於其 CDE 內之重要技術的適當使用方式、實作及驗證。|



## <a name="pci-dss-requirement-126"></a>PCI DSS 需求 12.6

**12.6** 實作正式安全性認知規範，讓所有人員知道持卡人資料安全性原則和程序的重要性。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責建立及維護與具備 CDE 存取權之員工有關的安全性認知原則。|



### <a name="pci-dss-requirement-1261"></a>PCI DSS 需求 12.6.1

**12.6.1** 雇用人員時進行教育，且至少每年進行教育。 

> [!NOTE]
> 方法可能會因人員的角色和其持卡人資料存取等級而有所不同。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責確保員工至少每年接收並確認資訊安全性，並舉辦 PCI-DSS 認知訓練。|



### <a name="pci-dss-requirement-1262"></a>PCI DSS 需求 12.6.2

**12.6.2** 要求人員至少每年確認已閱讀並了解安全性原則和程序。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責確保員工至少每年接收並確認資訊安全性，並舉辦 PCI-DSS 認知訓練。|



## <a name="pci-dss-requirement-127"></a>PCI DSS 需求 12.7

**12.7** 雇用前先調查潛在人員，將來自內部來源的攻擊風險降至最低 (背景調查的範例包括先前的工作經歷、刑事記錄、信用記錄和徵信查核)。 

> [!NOTE]
> 對於針對特定職位雇用的潛在人員 (例如進行交易時，一次只能存取一個卡號的收銀員)，這個需求只是建議事項。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責確保對具備 CDE 存取權的員工進行徹底的背景調查。|



## <a name="pci-dss-requirement-128"></a>PCI DSS 需求 12.8

**12.8** 維護及實作原則和程序，以管理與其共用持卡人資料 (或者可能影響持卡人資料安全性) 的服務提供者，如下所示。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責監視與其共用持卡人資料 (或者可能影響 CDE 安全性) 之服務提供者的 PCI 合規性。 客戶必須維護一份其 CDE 內所使用之所有服務提供者的清單。|



### <a name="pci-dss-requirement-1281"></a>PCI DSS 需求 12.8.1

**12.8.1** 維護一份服務提供者清單，其中包括所提供之服務的描述。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責監視與其共用持卡人資料 (或者可能影響 CDE 安全性) 之服務提供者的 PCI 合規性。 客戶必須維護一份其 CDE 內所使用之所有服務提供者的清單。|



### <a name="pci-dss-requirement-1282"></a>PCI DSS 需求 12.8.2

**12.8.2** 維護書面合約，其中包括服務提供者確認負責其擁有或以其他方式代表客戶儲存、處理或傳輸之持卡人資料的安全性，甚或其可能影響客戶之持卡人資料環境的安全性。 

> [!NOTE]
> 確認的確切措辭取決於雙方之間的合約、所提供之服務的細節，以及指派給各方的責任。 確認中不一定要包含本需求中提供的確切用詞。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責維護其中包含服務提供者確認維護持卡人資料安全性之責任的書面合約。|



### <a name="pci-dss-requirement-1283"></a>PCI DSS 需求 12.8.3

**12.8.3** 確保在聘用服務提供者方面有一個既定流程，包括聘用前的適當盡職調查。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責確保在聘用服務提供者方面有一個既定流程，包括聘用前的適當盡職調查。|



### <a name="pci-dss-requirement-1284"></a>PCI DSS 需求 12.8.4

**12.8.4** 維護計畫，以至少每年監視服務提供者的 PCI DSS 合規性狀態。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責至少每年維護計畫以監視服務提供者的 PCI DSS 合規性狀態。|



### <a name="pci-dss-requirement-1285"></a>PCI DSS 需求 12.8.5

**12.8.5** 維護哪些 PCI DSS 需求由各服務提供者所管理，以及哪些由實體管理的相關資訊。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須保留一份[責任摘要矩陣](https://aka.ms/pciblueprintcrm32)，其中會概述屬於客戶責任的 PCI DSS 需求與屬於 Microsoft Azure 責任的 PCI DSS 需求。|



## <a name="pci-dss-requirement-129"></a>PCI DSS 需求 12.9

**12.9** 僅限服務提供者的其他需求：服務提供者以書面方式向客戶確認其負責服務提供者擁有或以其他方式代表客戶儲存、處理或傳輸之持卡人資料的安全性，甚或其可能影響客戶之持卡人資料環境的安全性。 

> [!NOTE]
> 確認的確切措辭取決於雙方之間的合約、所提供之服務的細節，以及指派給各方的責任。 確認中不一定要包含本需求中提供的確切用詞。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 本身為服務提供者的客戶必須負責確認其維護 PCI 合規性的責任。 |



## <a name="pci-dss-requirement-1210"></a>PCI DSS 需求 12.10

**12.10** 實作事件回應計劃。 隨時準備立即回應系統漏洞。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12101"></a>PCI DSS 需求 12.10.1

**12.10.1** 建立當發生系統漏洞時要實作的事件回應計劃。 確保計劃至少解決下列項目：
- 妥協事件中的角色、責任、通訊與連絡人策略，至少包含支付品牌的通知
- 特定事件回應程序
- 業務復原和持續性程序
- 資料備份程序
- 適用於報告妥協之法律要求的分析
- 涵蓋範圍和所有重要系統元件的回應
- 來自支付品牌之事件回應程序的參考或包含

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12102"></a>PCI DSS 需求 12.10.2

**12.10.2** 至少每年檢閱和測試計劃，包括「需求 12.10.1」中列出的所有項目。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12103"></a>PCI DSS 需求 12.10.3

**12.10.3** 指定特定人員全天候待命，以回應警示。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12104"></a>PCI DSS 需求 12.10.4

**12.10.4** 為具備安全性缺口回應責任之員工提供適當的訓練。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12105"></a>PCI DSS 需求 12.10.5

**12.10.5** 包含安全性監視系統 (包括但不限於入侵偵測、入侵防護、防火牆，以及檔案完整性監視系統) 的警示。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



### <a name="pci-dss-requirement-12106"></a>PCI DSS 需求 12.10.6

**12.10.6** 開發程序以修改並根據經驗傳承發展事件回應計劃，並整合產業發展。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶必須負責開發 IR 計劃和測試考量與共用接觸點有關之任何客戶控制項，以及運用 Azure 基礎結構之任何客戶應用程式的 IR 計畫。 在必須將可能影響 Azure 應用程式或資料之事件回報給 Azure 的情況中，客戶有責任向 Azure 提供正確的連絡資訊。|



## <a name="pci-dss-requirement-1211"></a>PCI DSS 需求 12.11

**12.11** **僅限服務提供者的其他需求：** 至少每季度執行一次檢閱，以確認人員是否遵守安全性原則和操作程序。
檢閱必須涵蓋下列程序：
- 每日記錄檢閱
- 防火牆規則集檢閱
- 將設定標準套用至新系統
- 回應安全性警示
- 變更管理程序 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 本身為服務提供者的客戶必須負責記錄其確認 PCI 合規性控制效能之程序的檢閱。|



### <a name="pci-dss-requirement-12111"></a>PCI DSS 需求 12.11.1

**12.11.1** 僅限服務提供者的其他需求：維護季度檢閱程序的文件，其中包括：
- 記錄檢閱結果
- 負責 PCI DSS 合規性計畫的人員對結果進行檢閱和簽署 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 本身為服務提供者的客戶必須負責記錄其確認 PCI 合規性控制效能之程序的檢閱。|




