---
title: "Azure 付款處理藍圖 - CHD 需求"
description: "PCI DSS 需求 3"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的 CHD 需求
## <a name="pci-dss-requirement-3"></a>PCI DSS 需求 3

**保護儲存的持卡人資料**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

加密、截斷 (truncation)、遮罩和雜湊等保護方法是保護持卡人資料的重要元素。 如果入侵者避開其他安全性控制項，並取得加密資料的存取權，但若沒有正確的密碼編譯金鑰，該使用者無法讀取也無法使用此資料。 其他保護儲存資料的有效方法，也應視為降低潛在風險的機會。 例如，可將風險降到最低的方法包括不儲存持卡人的資料 (除非絕對必要)、截斷持卡人資料 (若不需要完整的 PAN)，以及不要透過使用者傳訊技術 (例如電子郵件和即時訊息) 傳送未受保護的 PAN。
請參閱 PCI DSS 和 PA DSS 詞彙表、縮寫及縮略字來了解「強式密碼編譯」和其他 PCI DSS 詞彙的定義。

## <a name="pci-dss-requirement-31"></a>PCI DSS 需求 3.1

**3.1** 透過實作資料保留期和處置原則、程序和流程的方法，將持卡人資料儲存量維持在最低，且方法中至少為所有持卡人資料 (CHD) 儲存加入了下列項目：
- 針對法律、法規、和商業需求所需的資料，限制資料儲存量和保留時間
- 持卡人資料的特定保留需求
- 資料不再需要時的安全刪除流程
- 每一季對超出所定義保留期的持卡人資料，進行識別並安全地加以刪除。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 會負責確認指定要刪除的客戶資料，都已使用其安全處置 (Secure Disposa) 原則中指定的 NIST 800-88 相容通訊協定來安全地清除。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 不會刪除或損毀任何儲存的 CHD。 且無論如何，所有資料都會加密，並且不會儲存任何主要帳戶號碼 (PAN) 資料。<br /><br />|



## <a name="pci-dss-requirement-32"></a>PCI DSS 需求 3.2

**3.2** 授權之後，請勿儲存敏感的驗證資料 (即使有加密)。 如果收到敏感的驗證資料，請在授權流程完成時使所有資料變得無法收回。 
- 這是業務上的正當理由，而且 
- 資料會被安全地儲存。
敏感驗證資料所包含的資料，會在下方的需求 3.2.1 至 3.2.3 中說明：


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 不會刪除或損毀任何儲存的 CHD；儲存的範例資料僅供示範用。 且無論如何，所有資料都會加密，並且不會儲存任何主要帳戶號碼 (PAN) 資料。<br /><br />|



### <a name="pci-dss-requirement-321"></a>PCI DSS 需求 3.2.1

**3.2.1** 請勿在授權後儲存任何追蹤的完整內容 (卡片後面的磁條資料或晶片上的同等資料等)。 此資料或許稱為完整追蹤、追蹤、追蹤 1、追蹤 2 和磁條資料。 

> [!NOTE]
> 在一般的企業課程中，下列來自磁條中的資料元素可能需要保留： 
> - 持卡人的名稱 
> - 主要帳戶號碼 (PAN) 
> - 到期日期 
> - 服務程式碼 
>
> 若要將風險降至最低，請只在業務上有需要時儲存這些資料元素。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 不會儲存任何 CHD 的完整內容。|



### <a name="pci-dss-requirement-322"></a>PCI DSS 需求 3.2.2

**3.2.2** 請勿在授權後儲存卡片驗證碼或值 (列印在支付卡正面或背面的三位數或四位數數字，用來驗證不用出示卡片的交易)。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會加密所有資料，包括 CVV 範例。|



### <a name="pci-dss-requirement-323"></a>PCI DSS 需求 3.2.3

**3.2.3** 請勿在授權後儲存個人識別碼 (PIN) 或加密的 PIN 區塊。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 不會儲存 PIN 的資訊。|



## <a name="pci-dss-requirement-33"></a>PCI DSS 需求 3.3

**3.3** 顯示 PAN 時使用遮罩 (前六個和後四個數字是要顯示的數字數目上限)，只讓有合法商務需求的人員可以看到完整 PAN。 

> [!NOTE]
> 這項需求不會取代現有的嚴格持卡人資料顯示需求 — 例如，銷售點 (POS) 收據上要顯示法定或支付卡廠牌的需求。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用透明資料加密、永遠加密的資料行和動態資料遮罩，來遮罩主要帳戶號碼 (PAN)。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-34"></a>PCI DSS 需求 3.4

**3.4** 使用以下任一方法，將儲存在任何位置 (包括可攜式數位媒體、備份媒體和記錄) 的 PAN 處理為無法讀取：
- 以強式密碼編譯為基礎的單向雜湊 (必須是整個 PAN 的雜湊)
- 截斷 (雜湊不可用來取代 PAN 的截斷區段)
- 索引權杖 (Index token) 和 Pad (Pad 必須安全地儲存)
- 具有相關聯金鑰管理流程和程序的強式密碼編譯。 

> [!NOTE]
> 如果有惡意的個體可存取已截斷和已雜湊處理的 PAN 版本，並重建原始 PAN 資料，則這些方法將毫無用武之地。 由於相同的已雜湊和已截斷 PAN 版本會存在實體環境中，因此必須具有額外的控制項，來確保已雜湊和已截斷的版本不會被用來重建原始 PAN。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有信用卡資料加密，並使用 Azure Key Vault 管理金鑰，防止 CHD 遭到擷取。<br /><br />如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-341"></a>PCI DSS 需求 3.4.1

**3.4.1** 如果已使用磁碟加密 (而不是檔案或資料行層級的資料庫加密)，則邏輯存取須與原生作業系統驗證和存取控制機制分開且各自管理 (例如，不使用本機使用者帳戶資料庫或一般網路登入認證)。 解密金鑰不能與使用者帳戶相關聯。 

> [!NOTE]
> 此需求適用於所有其他 PCI DSS 加密和金鑰管理需求。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會加密所有儲存的資料，並且隔離流量，以防止提高 DevOps 函式的特殊權限。<br /><br />當 App Service 環境受到保護且鎖定時，會需要一個機制來允許任何可能必要的 DevOps 發行或變更，例如使用 Kudu 監視 Web 應用程式的功能。<br /><br />虛擬機器已透過下列設定建立為 jumpbox (防禦主機)：<br /><br /><ul><li>[反惡意程式碼軟體擴充功能](/azure/security/azure-security-antimalware)</li><li>[OMS 監視擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[虛擬機器診斷擴充功能](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Bitlocker 已加密磁碟](/azure/security/azure-security-disk-encryption)</li></ul>使用 Azure Key Vault 遵循 Azure Government、PCI DSS 和 HIPAA 的需求。|



## <a name="pci-dss-requirement-35"></a>PCI DSS 需求 3.5

**3.5** 記錄和實作保護金鑰的程序，該金鑰用來防止儲存的持卡人資料洩漏或誤用。 

> [!NOTE]
> 這項需求適用於用來加密持卡人資料的金鑰，也適用於用來保護資料加密金鑰的金鑰加密金鑰，這類金鑰加密金鑰必須至與資料加密金鑰有一樣的強度。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Microsoft Azure 會確保客戶的金鑰保存庫會以邏輯方式彼此隔離，並與 Key Vault 服務的管理平面以邏輯方式隔離。 Key Vault 須經由設計，因此 Microsoft 並沒有常態存取任何客戶的金鑰保存庫。 <br /><br />Microsoft Azure 會使用業界標準演算法、金鑰長度和硬體安全性模組 (HSM) 保護金鑰。<br /><br />儲存在 Microsoft Azure Key Vault 中的金鑰可用來保護其他金鑰。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供文件來說明和協助部署受保護的金鑰解決方案，進而保護示範 CHD。|



### <a name="pci-dss-requirement-351"></a>PCI DSS 需求 3.5.1

**3.5.1**僅適用於服務提供者的其他需求：維護已記錄的密碼編譯架構描述，包括：
- 所有演算法、通訊協定和保護持卡人資料所用金鑰的詳細資料，包括金鑰強度和到期日期
- 每個金鑰的金鑰使用方式描述
- 清查用來管理金鑰的任何 HSM 及其他 SCD 

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前是屬於最佳作法，該日期之後會變成需求。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Microsoft Azure 會確保客戶的金鑰保存庫會以邏輯方式彼此隔離，並與 Key Vault 服務的管理平面以邏輯方式隔離。 Key Vault 須經由設計，因此 Microsoft 並沒有常態存取任何客戶的金鑰保存庫。 <br /><br />Microsoft Azure 會使用業界標準演算法、金鑰長度和硬體安全性模組 (HSM) 保護金鑰。<br /><br />儲存在 Microsoft Azure Key Vault 中的金鑰可用來保護其他金鑰。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供文件來說明和協助部署受保護的金鑰解決方案，進而保護示範 CHD。|



### <a name="pci-dss-requirement-352"></a>PCI DSS 需求 3.5.2

**3.5.2** 僅限最少數的監管人在必要時可存取密碼編譯金鑰。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Key Vault 支援細微的存取原則，允許 Key Vault 擁有者將存取權授與特定功能，以執行特定實體的特定作業。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 金鑰管理已隔離至一個使用者帳戶 (admin # #@contosowebstore.com)。|



### <a name="pci-dss-requirement-353"></a>PCI DSS 需求 3.5.3

**3.5.3** 隨時使用下列其中一個 (或多個) 形式，來儲存用於加密/解密持卡人資料的密碼和私密金鑰：
- 使用強度至少與資料加密金鑰相同的金鑰加密金鑰進行加密，並且與資料加密金鑰分開儲存
- 在安全的密碼編譯裝置內 (例如硬體 (主機) 安全模組 (HSM) 或 PTS 認可的互動點裝置)
- 根據產業可接受的方法，作為至少兩個長度完整的金鑰元件或金鑰部分 

> [!NOTE]
> 公開金鑰並不需要以這些形式的任何一個加以儲存。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />金鑰會儲存在客戶所識別的特定金鑰保存庫。<br /><br />多個應用程式可以同時且不限區域地存取 Key Vault ，這樣可減少複製金鑰並儲存在多個位置的需求。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-354"></a>PCI DSS 需求 3.5.4

**3.5.4** 盡可能在最少位置中儲存密碼編譯金鑰。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />金鑰會儲存在客戶所識別的特定金鑰保存庫。 <br /><br />多個應用程式可以同時且不限區域地存取 Key Vault ，這樣可減少複製金鑰並儲存在多個位置的需求。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



## <a name="pci-dss-requirement-36"></a>PCI DSS 需求 3.6

**3.6** 針對加密持卡人資料所使用的密碼編譯金鑰，完整地記錄和實作所有金鑰管理流程和程序，包括下列程序。 

> [!NOTE]
> 金鑰管理的許多業界標準都可從各種資源取得，包括 NIST，網址為 http://csrc.nist.gov。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-361"></a>PCI DSS 需求 3.6.1

**3.6.1** 強式密碼編譯金鑰的產生

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：** <br /><br />在 Key Vault 中產生金鑰時，Azure 會根據每個客戶的特定需求產生金鑰。 金鑰是使用 HSM 產生。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-362"></a>PCI DSS 需求 3.6.2

**3.6.2** 安全的密碼編譯金鑰配送

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />攜帶您自己的金鑰 (BYOK) 工具會封裝客戶金鑰，並把繫結至特定 Azure 訂用帳戶的特定安全性保存庫視為目標。 金鑰只可以匯入至定義的訂用帳戶金鑰保存庫 (在指定區域中)。 此流程會使用硬體製造商所提供的加密程序。 客戶會收到傳送成功的通知。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-363"></a>PCI DSS 需求 3.6.3

**3.6.3** 安全的密碼編譯金鑰儲存體

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />金鑰會儲存在 HSM 中，並使用硬體製造商的密碼編譯安全性來保護金鑰。 金鑰的中繼資料會以加密的狀態儲存在 Azure 儲存體，並且對每個金鑰保存庫而言都是唯一的。 <br /><br /> |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-364"></a>PCI DSS 需求 3.6.4

**3.6.4** 金鑰的密碼編譯金鑰會因為達到加密期限而變更 (例如，經過定義的一段時間後和/或指定金鑰產生的加密文字數量達到特定量)，此變更會由相關聯應用程式廠商或金鑰擁有者所定義，並以業界最佳作法和指導方針為基礎 (例如，NIST 特殊出版品 800-57)。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Key Vault 支援更新或轉換金鑰的功能 (由客戶定義)。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-365"></a>PCI DSS 需求 3.6.5

**3.6.5** 當金鑰完整性已被削弱 (例如知道純文字金鑰元件的員工已離職)，或金鑰可能遭到洩漏時，應在必要時淘汰或取代 (例如，封存、解構及/或撤銷) 金鑰。 

> [!NOTE]
> 如果需要保留已淘汰或已取代的密碼編譯金鑰，這些金鑰必須安全地加以封存 (例如，藉由使用金鑰加密金鑰)。 封存的密碼編譯金鑰應只用於解密/驗證。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Key Vault 支援淘汰或取代的功能 (由客戶定義)。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-366"></a>PCI DSS 需求 3.6.6

**3.6.6** 如果使用手動純文字密碼編譯金鑰管理作業，則必須使用分割知識和雙重控制來管理這些作業。 

> [!NOTE]
> 手動金鑰管理作業的範例包括 (但不限於)：金鑰產生、傳輸、載入、儲存和解構。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-367"></a>PCI DSS 需求 3.6.7

**3.6.7** 防止未經授權的密碼編譯金鑰替換。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | **針對使用 Key Vault 的客戶：**<br /><br />Key Vault 會以邏輯方式分隔，而且不支援跨目錄授權。 如此一來，可防止未經授權的替換作業。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-368"></a>PCI DSS 需求 3.6.8

**3.6.8** 要求密碼編譯金鑰監管人正式聲明他們了解並接受他們身為金鑰監管人的責任。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 金鑰管理已隔離至一個使用者帳戶 (admin # #@contosowebstore.com)。|



## <a name="pci-dss-requirement-37"></a>PCI DSS 需求 3.7

**3.7** 確定保護持卡人資料的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 管理所有金鑰。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|




