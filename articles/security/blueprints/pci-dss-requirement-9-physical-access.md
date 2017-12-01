---
title: "Azure 付款處理藍圖 - 實際存取需求"
description: "PCI DSS 需求 9"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的實際存取需求 
## <a name="pci-dss-requirement-9"></a>PCI DSS 需求 9

**限制持卡人資料的實際存取**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

對任何資料或存有持卡人資料的系統進行實際存取，都會讓人有機會存取裝置或資料，以及移除系統或紙本文件，因此應適當地加以限制。 就需求 9 而言，「現場人員」是指實際出現在實體場所內的全職和兼職員工、臨時工、約聘員工與顧問。 「訪客」是指廠商、任何現場人員的賓客、服務業人員，或任何需要進入設施一小段時間的人員 (通常不會超過一天)。 「媒體」是指包含持卡人資料的所有紙本和電子媒體。

## <a name="pci-dss-requirement-91"></a>PCI DSS 需求 9.1

**9.1** 在持卡人資料環境中，使用適當的設施進入控制項來限制和監視系統的實際存取。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責實作、強制執行及監視資料中心的實際存取安全性。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-911"></a>PCI DSS 需求 9.1.1

**9.1.1** 使用攝影機或存取控制機制 (或兩者一起用) 來監視敏感區域的個別實際存取。 檢閱收集的資料，並與其他項目相互關聯。 除非法律限制，否則儲存至少三個月。

> [!NOTE]
> 「敏感區域」是指裝載儲存、處理或傳輸持卡人資料系統的任何資料中心、伺服器機房或任何區域。 這不包括僅銷售點終端機存在的公開區域，例如零售商店的收銀區域。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責實作、強制執行及監視 CCTV 與採用生物辨識技術的資料中心實際存取機制。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-912"></a>PCI DSS 需求 9.1.2

**9.1.2** 實作實體和/或邏輯控制項，以對可公開存取的網路端子限制存取權。 

例如，可以停用位於公用區域和訪客可存取區的網路端子，並且只在網路存取已明確獲得授權後才能啟用。 或者，可以實作處理流程，以確保在網路端子作用中的區域內，訪客始終處於護送狀態。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 平台沒有可公開存取的網路端子。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-913"></a>PCI DSS 需求 9.1.3

**9.1.3** 限制無線存取點、閘道、手持式裝置、網路/通訊硬體和電信線路的實際存取。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 網路硬體的實際存取由已下項目嚴格控制：存取清單、多種形式的驗證，輸入項目的實體屏障，以及需要核准才能存取設備的商務需求。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



## <a name="pci-dss-requirement-92"></a>PCI DSS 需求 9.2

**9.2** 開發程序以輕鬆地分辨現場人員和訪客，包括：
- 識別現場人員及訪客 (例如，指派徽章)
- 存取需求的變更
- 撤銷或終止現場人員及過期訪客的身分識別 (例如識別碼徽章)。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責實作、強制執行及監視資料中心的實際存取安全性，以及員工或約聘人員造訪資料中心時的身分識別。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



## <a name="pci-dss-requirement-93"></a>PCI DSS 需求 9.3

**9.3** 控制現場人員對敏感區域的實際存取，如下所示：
- 必須經過授權並且根據個人職責授予存取權。
- 存取會在終止時遭到撤銷，並傳回或停用所有實際存取機制，例如金鑰和門禁卡等。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 資料中心小組根據最低權限準則核准的存取清單，會用來控制 Microsoft 資料中心的存取授權。 存取控制清單每季都會進行檢閱、驗證和更新。<br /><br />Microsoft Azure 資料中心會利用實際存取裝置，例如周邊閘道、電子存取條碼讀卡機、生物識別系統、雙門互鎖/入口網站及防折返裝置。 存取徽章裝置會受到持續監視。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



## <a name="pci-dss-requirement-94"></a>PCI DSS 需求 9.4

**9.4** 實作程序以識別及授權訪客。 程序應該包括下列項目。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責強制讓預先核准的傳遞項目在安全的接應區域被接收，此區域會實際地與資訊處理設施隔離，且會由授權的人員加以監視。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-941"></a>PCI DSS 需求 9.4.1

**9.4.1** 對於處理或保留持卡人資料的區域，訪客須經過授權才能進入，並且一律有人護送。


**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責強制讓預先核准的傳遞項目在安全的接應區域被接收，此區域會實際地與資訊處理設施隔離，且會由授權的人員加以監視。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-942"></a>PCI DSS 需求 9.4.2

**9.4.2** 對於過期訪客和為了清楚分辨訪客和現場人員，訪客會被識別並得到徽章或其他身分識別。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft 資料中心的存取必須預先核准，且已授權的訪客必須在到達時透過實體安全進行登記，然後提供有效的識別碼證明後才能進入。 徽章會清楚地指出員工。 約聘員工和訪客會收到臨時徽章，且必須在離開設施時交回徽章。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-943"></a>PCI DSS 需求 9.4.3

**9.4.3** 訪客須在離開設施前或到期日已到時須交回徽章或身分識別。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 訪客從任何 Microsoft 設施離開時須交回徽章。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-944"></a>PCI DSS 需求 9.4.4

**9.4.4** 訪客記錄是用來維護儲存或傳輸持卡人資料所在設備 (電腦機房和資料中心) 的訪客活動實際稽核記錄。
記錄上會記錄訪客名稱、呈報公司和授權實際存取的現場人員。
除非法律限制，否則請保留此記錄至少三個月。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會負責維護訪客記錄，以作為儲存或傳輸持卡人資料所在設備 (電腦機房和資料中心) 的訪客活動實際稽核記錄。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



## <a name="pci-dss-requirement-95"></a>PCI DSS 需求 9.5

**9.5** 實際保護所有媒體。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-951"></a>PCI DSS 需求 9.5.1

**9.5.1** 將媒體備份儲存在安全的位置，最好是場外設施，例如替代或備份站台，或是商業儲存設施。 至少每年檢閱位置的安全性。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-96"></a>PCI DSS 需求 9.6

**9.6** 對任何類型的內部或外部媒體分佈維持嚴格控管，包括下列項目。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-961"></a>PCI DSS 需求 9.6.1

**9.6.1** 將媒體分類，如此一來便可判斷敏感資料。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-962"></a>PCI DSS 需求 9.6.2

**9.6.2** 透過安全且可正確追蹤的快遞或其他方法傳送媒體。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-963"></a>PCI DSS 需求 9.6.3

**9.6.3** 請確定管理作業會核准任何和所有媒體從受保護的區域移出 (包括將媒體發佈到個人時)。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-97"></a>PCI DSS 需求 9.7

**9.7** 維持嚴格控管儲存體和媒體協助工具。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-971"></a>PCI DSS 需求 9.7.1

**9.7.1** 適當地維護所有媒體記錄的庫存，並至少每年一次地整理媒體庫存。


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-98"></a>PCI DSS 需求 9.8

**9.8** 當媒體不再有商務或法務原因上的需求時，則銷毀該媒體，如下所示。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-981"></a>PCI DSS 需求 9.8.1

**9.8.1** 撕毀、焚燒或攪碎紙本原料，使得持卡人資料無法重建。 保護好要銷毀原料所用的儲存體容器。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有資料儲存在 Azure SQL Database。 PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-982"></a>PCI DSS 需求 9.8.2

**9.8.2** 使電子媒體上的持卡人資料無法復原，如此便無法重建持卡人資料。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 資料解構技術會因為要銷毀的資料物件類型不同，而有所差異，無論是訂用帳戶、儲存體、虛擬機器或資料庫。 在 Microsoft Azure 多租用戶環境中，請小心注意，務必確認一個客戶的資料不會「洩漏」至另一個客戶的資料，或是當客戶刪除資料時，沒有其他客戶 (包括在大部分情況下，曾擁有一次該資料的客戶) 可以存取這份已刪除的資料。<br /><br />Microsoft Azure 會遵循 Media Sanitization 上的 NIST 800 88 指引，該指引可解決確保資料不會意外釋出的主要顧慮。 這些指引包括電子和實體上的清理。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 您可以藉由刪除部署期間使用的資源群組，來完全刪除 Contoso Webstore。|



## <a name="pci-dss-requirement-99"></a>PCI DSS 需求 9.9

**9.9** 保護透過與卡片實際互動來擷取支付卡資料的裝置，避免其遭到竄改或取代。

> [!NOTE]
> 這些需求適用於在銷售點出示卡片進行交易 (也就是刷卡) 時所使用的卡片讀取裝置。 此需求不適用於手動金鑰輸入元件，例如電腦鍵盤和 POS 鍵台。 

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 OMS 來記錄系統的所有變更。<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) 提供廣泛的變更記錄。 為確保正確性，您可以檢閱及驗證變更。 如需更具體的指引，請參閱 [PCI 指引 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-991"></a>PCI DSS 需求 9.9.1

**9.9.1** 維持最新的裝置清單。 清單應該包括下列項目：
- 裝置型號
- 裝置位置 (例如，裝置所在的站台或設施)
- 裝置序號或其他作為唯一識別碼的方法

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供參考架構和其部署文件中使用的所有服務清單。|



### <a name="pci-dss-requirement-992"></a>PCI DSS 需求 9.9.2

**9.9.2** 定期檢查裝置外觀以偵測是否遭到竄改 (例如裝置被裝上其他盜卡器 (card skimmer)) 或替代 (例如，藉由檢查序號或其他裝置特性，以確認它是否已被替換為詐騙裝置)。

> [!NOTE]
> 裝置可能遭到竄改或取代的徵象範例包括非預期的附加物或插入裝置的纜線、遺失或變更的安全標籤、損毀或不同顏色的外殼、序號變更或其他外部標示。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-993"></a>PCI DSS 需求 9.9.3

**9.9.3** 提供人員訓練，讓他們注意嘗試竄改或替代裝置的行為。 訓練應該包括下列項目：
- 對任何聲稱是工人或維護人員的第三方人員進行身分驗證，然後才能授與他們權限來修改裝置或對裝置進行移難排解。
- 不要在沒有驗證的情況下，安裝、取代或帶回裝置。
- 請留意裝置附近的可疑行為 (例如，未知人員嘗試拔除或開啟裝置)。
- 向適當人員 (例如經理或保全人員) 舉報可疑行為和裝置遭到竄改或替代的徵象。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



## <a name="pci-dss-requirement-910"></a>PCI DSS 需求 9.10

**9.10** 確定限制持卡人資料實際存取的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|




