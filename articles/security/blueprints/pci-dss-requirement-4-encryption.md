---
title: "Azure 付款處理藍圖 - 加密需求"
description: "PCI DSS 需求 4"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的加密需求 
## <a name="pci-dss-requirement-4"></a>PCI DSS 需求 4

**在開放的公用網路上傳輸持卡人資料時進行加密**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

對於可能輕易遭到惡意人士存取的敏感資訊，必須在網路傳輸期間進行加密。 設定錯誤的無線網路和傳統加密與驗證通訊協定的弱點，可能會繼續成為惡意人士的目標，他們會利用這些弱點來取得特殊權限，以存取持卡人資料環境。

## <a name="pci-dss-requirement-41"></a>PCI DSS 需求 4.1

**4.1** 使用強式密碼編譯和安全性通訊協定 (例如 TLS、IPSEC 和 SSH 等)，使敏感持卡人資料在開放的公用網路中進行傳輸期間受到保護，包括下列項目：
- 只接受信任的金鑰和憑證。
- 使用的通訊協定僅支援安全的版本或設定。
- 加密強度適合使用中的加密方法。 

> [!NOTE]
> 在使用 SSL/早期 TLS 的情況下，必須滿足＜附件 A2＞中的需求。
>
> 開放的公用網路範例包括 (但不限於)：
> - 網際網路
> - 無線技術，包括 802.11 和藍牙
> - 行動電話通訊技術，例如全球行動通訊系統 (Global System for Mobile communications，GSM)，分碼多重存取 (Code division multiple access，CDMA)
> - 整合封包無線電服務 (GPRS)
> - 衛星通訊


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 是 PaaS 解決方案，會為部署實作強式密碼編譯，如下所示：<br /><br />為符合加密的待用資料需求，所有 [Azure 儲存體](https://azure.microsoft.com/services/storage/)都會使用下列項目：<br /><br /><ul><li>[待用資料的 Azure 儲存體服務加密 (SSE)](/azure/storage/storage-service-encryption)</li><li>SQL Database：PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。</li><li>[Azure 磁碟加密 (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>使用 Azure Key Vault 遵循 Azure Government、PCI DSS 和 HIPAA 的需求。|



### <a name="pci-dss-requirement-411"></a>PCI DSS 需求 4.1.1

**4.1.1** 確保傳輸持卡人資料或與持卡人資料環境連線的無線網路，是使用業界最佳作法 (例如，IEEE 802.11i) 來實作驗證和傳輸的強式加密。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 解決方案中不會實作無線及 SNMP。|



## <a name="pci-dss-requirement-42"></a>PCI DSS 需求 4.2

**4.2** 永遠不會透過使用者傳訊技術 (例如電子郵件、即時訊息、SMS 和 聊天軟體等) 傳送未受保護的 PAN。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 若有傳訊解決方案可能會傳送未受保護的主要帳戶號碼 (PAN) 資料，Contoso Webstore 不會實作任何這類解決方案。|



## <a name="pci-dss-requirement-43"></a>PCI DSS 需求 4.3

**4.3** 確定持卡人資料傳輸加密的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責記錄並加密包含持卡人資料的傳輸。|




