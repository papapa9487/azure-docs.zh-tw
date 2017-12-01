---
title: "Azure 付款處理藍圖 - 監視需求"
description: "PCI DSS 需求 10"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 規範之環境的監視需求 
## <a name="pci-dss-requirement-10"></a>PCI DSS 需求 10

**追蹤並監視針對網路資源與持卡人資料的所有存取**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 若要了解每個需求的測試程序和指導方針相關資訊，請參閱 PCI DSS。

記錄機制和追蹤使用者活動的能力，對於防止、偵測或降低資料洩漏的影響而言皆非常重要。 在所有環境中皆保存記錄的情況下，便可在發生問題時進行徹底的追蹤、警示和分析。 在沒有系統活動記錄的情況下，判斷洩漏的原因雖然不是不可能，但將會非常困難。

## <a name="pci-dss-requirement-101"></a>PCI DSS 需求 10.1

**10.1** 實作稽核線索以將針對系統元件的所有存取連結至每個個別的使用者。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會限制系統管理與診斷工具，僅允許經授權且負責相關作業的人員存取。 Microsoft Azure 會根據最低權限準則，限制用於生產環境之工具的特殊權限存取。 Microsoft Azure 會記錄並維護針對平台環境中 Microsoft Azure 系統元件的所有個別使用者存取記錄。<br /><br />Microsoft Azure 平台元件 (包括作業系統、CloudNet、網狀架構等等) 會設定以記錄並收集安全性事件。 系統會記錄 Microsoft Azure 平台中的系統管理員活動。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 具有針對所有系統和使用者活動的詳盡記錄 (包括 CHD 記錄)。 如需詳細資訊，請參閱 [PCI 指引 - 記錄](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-102"></a>PCI DSS 需求 10.2

**10.2** 針對所有系統元件實作自動化的稽核線索，以重新建構下列事件：
- **10.2.1** 所有個別使用者針對持卡人資料的存取
- **10.2.2** 由具備根權限或系統管理權限的人員所採取的所有動作
- **10.2.3** 對所有稽核線索的存取
- **10.2.4** 無效的邏輯存取嘗試
- **10.2.5** 識別資料與驗證機制的用法及變更 (包括但不限於建立新帳號和提升權限)，以及針對具備根權限或系統管理權限的帳戶所進行的所有變更、新增或刪除
- **10.2.6** 稽核記錄的初始化、停止或暫停
- **10.2.7** 系統層級物件的建立和刪除

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會限制系統管理與診斷工具，僅允許經授權且負責相關作業的人員存取。 Microsoft Azure 會根據最低權限準則，限制用於生產環境之工具的特殊權限存取。 Microsoft Azure 會記錄並維護針對平台環境中 Microsoft Azure 系統元件的所有個別使用者存取記錄。<br /><br />Microsoft Azure 平台元件 (包括作業系統、CloudNet、網狀架構等等) 會設定以記錄並收集安全性事件。 系統會記錄 Microsoft Azure 平台中的系統管理員活動。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 具有針對所有系統和使用者活動的詳盡記錄 (包括 CHD 記錄)。 如需詳細資訊，請參閱 [PCI 指引 - 記錄](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-103"></a>PCI DSS 需求 10.3

**10.3** 針對每個事件的所有系統元件，至少會記錄下列稽核線索項目：
- **10.3.1** 使用者識別資料
- **10.3.2** 事件類型
- **10.3.3** 日期和時間
- **10.3.4** 指出成功或失敗
- **10.3.5** 事件的起源
- **10.3.6** 受影響資料、系統元件或資源的身分識別或名稱

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已建立程序，以透過和全球定位系統 (GPS) 衛星同步的 NTP Stratum 1 時間伺服器，來同步 Microsoft Azure 環境中的伺服器和網路裝置。 同步處理每隔五分鐘便會自動執行。 Microsoft Azure 須負責確保服務主機會正確地同步時間。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會依 10.3 控制措施的要求，記錄使用者識別資料、事件類型、日期時間戳記、成功/失敗事件、事件來源，以及資源名稱。|



## <a name="pci-dss-requirement-104"></a>PCI DSS 需求 10.4

**10.4** 使用時間同步處理技術，同步所有重要的系統時鐘和時間，並確保實作下列項目以取得、發佈和儲存時間。 
> [!NOTE]
> 時間同步處理技術的其中一個範例為網路時間通訊協定 (NTP)。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已建立程序，以透過和全球定位系統 (GPS) 衛星同步的 NTP Stratum 1 時間伺服器，來同步 Microsoft Azure 環境中的伺服器和網路裝置。 同步處理每隔五分鐘便會自動執行。 Microsoft Azure 須負責確保服務主機會正確地同步時間。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | PaaS 服務的時間同步處理是由 Azure 執行。|



### <a name="pci-dss-requirement-1041"></a>PCI DSS 需求 10.4.1

**10.4.1** 重要系統具有正確且一致的時間。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.4](#pci-dss-requirement-10-4) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | PaaS 服務的時間同步處理是由 Azure 執行。|



### <a name="pci-dss-requirement-1042"></a>PCI DSS 需求 10.4.2

**10.4.2** 時間資料受到保護。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.4](#pci-dss-requirement-10-4) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | PaaS 服務的時間同步處理是由 Azure 執行。|



### <a name="pci-dss-requirement-1043"></a>PCI DSS 需求 10.4.3

**10.4.3** 從業界接受的時間來源接收時間設定。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.4](#pci-dss-requirement-10-4) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | PaaS 服務的時間同步處理是由 Azure 執行。|



## <a name="pci-dss-requirement-105"></a>PCI DSS 需求 10.5

**10.5** 保護稽核線索以使它們無法被變更。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | FIM 和 IDS 工具會實作於 Microsoft Azure 環境內。 Microsoft Azure 使用 EWS 以支援對其操作環境內的事件進行即時分析。 MA 和 AIMS 針對可能會危害系統的事件，產生近乎即時的相關警示。 <br /><br />已啟用針對服務、使用者和安全性事件 (Web 伺服器記錄、FTP 伺服器記錄等等) 的記錄，並集中保留。 Azure 會限制稽核記錄，僅允許依作業職責獲得授權的人員存取。 事件記錄檔會封存在 Azure 安全封存基礎結構上，並保留 180 天。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



### <a name="pci-dss-requirement-1051"></a>PCI DSS 需求 10.5.1

**10.5.1** 限制稽核線索的檢視，僅提供給有作業相關需求的人員。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.5](#pci-dss-requirement-10-5) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



### <a name="pci-dss-requirement-1052"></a>PCI DSS 需求 10.5.2

**10.5.2** 保護稽核線索檔案以防止未經授權的修改。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.5](#pci-dss-requirement-10-5) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



### <a name="pci-dss-requirement-1053"></a>PCI DSS 需求 10.5.3

**10.5.3** 立即將稽核線索檔案備份到集中式記錄伺服器，或是難以變更的媒體。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.5](#pci-dss-requirement-10-5) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



### <a name="pci-dss-requirement-1054"></a>PCI DSS 需求 10.5.4

**10.5.4** 將對外技術的記錄寫入到安全、集中式的內部記錄伺服器或媒體裝置。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.5](#pci-dss-requirement-10-5) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



### <a name="pci-dss-requirement-1055"></a>PCI DSS 需求 10.5.5

**10.5.5** 在記錄上使用檔案完整性監視或變更偵測軟體，以確保現有的記錄資料無法在不產生警示的情況下變更 (不過加入新資料應不會造成警示)。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.5](#pci-dss-requirement-10-5) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會對所有針對 OMS 的項目提供稽核。 備份到外部來源可由 [Azure 備份](https://azure.microsoft.com/services/backup/)執行。|



## <a name="pci-dss-requirement-106"></a>PCI DSS 需求 10.6

**10.6** 檢閱所有系統元件的記錄和安全性事件，以識別異常或可疑的活動。
 
> [!NOTE]
> 為了符合此需求，可以使用記錄檔收集、剖析和警示工具。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | FIM 和 IDS 工具會實作於 Microsoft Azure 環境內。 Microsoft Azure 使用 EWS 以支援對其操作環境內的事件進行即時分析。 MA 和 AIMS 針對可能會危害系統的事件，產生近乎即時的相關警示。 <br /><br />已啟用針對服務、使用者和安全性事件 (Web 伺服器記錄、FTP 伺服器記錄等等) 的記錄，並集中保留。 Azure 會限制稽核記錄，僅允許依作業職責獲得授權的人員存取。 事件記錄檔會封存在 Azure 安全封存基礎結構上，並保留 180 天。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)以監視、報告和防止異常狀況。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 可針對所有的 Azure 資源提供一致的合併建議檢視。|



### <a name="pci-dss-requirement-1061"></a>PCI DSS 需求 10.6.1

**10.6.1** 至少每日檢閱下列項目：
- 所有安全性事件
- 儲存、處理或傳輸 CHD 和/或 SAD 之所有系統元件的記錄
- 所有重要系統元件的記錄
- 執行安全性功能 (例如防火牆、入侵偵測系統/入侵防護系統 (IDS/IPS)、驗證伺服器、電子商務重新導向伺服器等等) 之所有伺服器和系統元件的記錄。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.6](#pci-dss-requirement-10-6) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)以監視、報告和防止異常狀況。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 可針對所有的 Azure 資源提供一致的合併建議檢視。|



### <a name="pci-dss-requirement-1062"></a>PCI DSS 需求 10.6.2

**10.6.2** 根據由組織年度風險評定所決定的組織原則和風險管理策略，定期檢閱所有其他系統元件的記錄。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.6](#pci-dss-requirement-10-6) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)以監視、報告和防止異常狀況。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 可針對所有的 Azure 資源提供一致的合併建議檢視。|



### <a name="pci-dss-requirement-1063"></a>PCI DSS 需求 10.6.3

**10.6.3** 追蹤在檢閱程序期間所識別的例外狀況與異常狀況。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 請參閱[需求 10.6](#pci-dss-requirement-10-6) 的＜Microsoft Azure＞小節。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)以監視、報告和防止異常狀況。 [Azure Advisor](/azure/advisor/advisor-security-recommendations) 可針對所有的 Azure 資源提供一致的合併建議檢視。|



## <a name="pci-dss-requirement-107"></a>PCI DSS 需求 10.7

**10.7** 至少將稽核線索歷程記錄保留一年，且可立即提供至少三個月內的記錄以進行分析 (例如線上、封存，或可從備份還原)。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會將稽核記錄保留一年，且最近 3 個月內的記錄可透過其內部入口網站立即存取。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 具有針對所有系統和使用者活動的詳盡記錄 (包括 CHD 記錄)。 如需詳細資訊，請參閱 [PCI 指引 - 記錄](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-108"></a>PCI DSS 需求 10.8

**10.8** **僅適用於服務提供者的額外需求：**實作能及時偵測並報告重大安全性控制系統失敗的程序，失敗項目包括但不限於：
- 防火牆
- IDS/IPS
- FIM
- 防毒
- 實體存取控制
- 邏輯存取控制
- 稽核記錄機制
- 分割控制 (若有使用) 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。



**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 使用 EWS 以支援對其操作環境內的事件進行即時分析。 MA 和 AIMS 針對可能會危害系統的事件，產生近乎即時的相關警示。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 具有針對所有系統和使用者活動的詳盡記錄 (包括 CHD 記錄)。 如需詳細資訊，請參閱 [PCI 指引 - 記錄](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-1081"></a>PCI DSS 需求 10.8.1

**10.8.1** **適用於服務提供者的額外需求：**及時回應任何重大安全性控制的失敗。 回應安全性控制失敗的程序必須包括：
- 還原安全性功能
- 識別並記錄安全性失敗的持續時間 (開始至結束的日期和時間)
- 識別並記錄失敗的原因 (包括根本原因)，並記錄處理根本原因所需的補救步驟
- 識別並解決在失敗期間引發的安全性問題
- 執行風險評定以判斷是否因安全性失敗而需要進行進一步的動作
- 實作控制措施以防止失敗的原因再次發生。繼續監視安全性控制 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前為最佳做法，並會於該日期後變成需求。


**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 使用 EWS 以支援對其操作環境內的事件進行即時分析。 MA 和 AIMS 針對可能會危害系統的事件，產生近乎即時的相關警示。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 具有針對所有系統和使用者活動的詳盡記錄 (包括 CHD 記錄)。 如需詳細資訊，請參閱 [PCI 指引 - 記錄](payment-processing-blueprint.md#logging-and-auditing)。|



## <a name="pci-dss-requirement-109"></a>PCI DSS 需求 10.9

**10.9** 確定用於監視針對網路資源與持卡人資料之所有存取的安全性原則和作業程序都已經記錄、正在使用，且所有相關合作對象皆已經了解。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供使用案例，以及管理及保護 CHD 之方式的說明。|




