---
title: "Azure 付款處理藍圖 - 存取需求"
description: "PCI DSS 需求 7"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: ac3afee9-0471-465d-a115-67488a1635a6
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5a3c9eac552fb96309cfa791a2e72a7102662e60
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的存取需求 
## <a name="pci-dss-requirement-7"></a>PCI DSS 需求 7

**限制讓業務上必須知道的人存取持卡人資料**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

為確保重要資料只能由取得授權的人員存取，必須以「必須知道」的人為基礎和根據工作職責，來制定限制存取的系統和流程。

因為「必須知道」而取得的存取權，只有執行作業所需的最少資料和最低權限。

## <a name="pci-dss-requirement-71"></a>PCI DSS 需求 7.1

**7.1** 限制只有作業上需要這類存取的個人，可存取系統元件及持卡人資料。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 會因為下列情形強制執行現有 ISMS 原則：Azure 人員存取 Azure 系統元件、驗證存取控制的效力、提供 Just-In-Time 的系統管理存取權、不再需要存取權時將其撤銷，以及確保員工是因為業務需求而存取 Azure 平台環境。 Azure 在存取客戶環境時會有很高的限制，並且只有客戶核准後才允許存取。<br /><br />已建立程序來限制只有獲得授權的員工、廠商、約聘人員和訪客可實際存取資料中心。 暫時存取內部資料中心設施的人員，需經過安全性驗證和登記。 Azure 小組會每季檢閱實際存取記錄。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責限制只有作業上需要這類存取的個人，可存取系統元件及持卡人資料。 這包括限制 Azure 管理入口網站的存取權，以及指定有權建立、修改或刪除 PaaS 服務的帳戶或角色。|



### <a name="pci-dss-requirement-711"></a>PCI DSS 需求 7.1.1

**7.1.1** 定義每個角色的存取需求，包括：
- 每個角色需要為其作業職責存取的系統元件和資料資源
- 存取資源所需的權限層級 (例如使用者或系統管理員等)

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責定義和記錄使用者識別碼核准流程、定義最低權限、限制持卡人資料的存取、使用唯一識別碼、提供責任區隔，以及當使用者不再需要存取權時，撤銷存取權。|



### <a name="pci-dss-requirement-712"></a>PCI DSS 需求 7.1.2

**7.1.2** 對於有權限的使用者識別碼，將其存取權限制為執行工作職責所需的最低權限。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已採用適用的公司和組織安全性原則，包括資訊安全性原則。 原則已核准、發佈並傳送到 Windows Azure。 Microsoft Azure 資訊安全性原則需要授與 Microsoft Azure 資產的存取權，且須以業務正當理由為基礎，以及具有資產擁有者的授權，並以「必須知道」和「最低權限」的原則為基礎。 原則也會解決存取管理週期的需求，包括存取佈建、存取授權、存取權的驗證移除和定期存取檢閱。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 使用者角色受限於示範案例中描述的責任。|



### <a name="pci-dss-requirement-713"></a>PCI DSS 需求 7.1.3

**7.1.3** 根據個別人員的作業分類與職責來指派存取權。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 使用者角色受限於示範案例中描述的責任。|



### <a name="pci-dss-requirement-714"></a>PCI DSS 需求 7.1.4

**7.1.4** 需有已獲授權合作對象的記錄核准，才能指定所需權限。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責限制只有作業上需要這類存取的個人，可存取系統元件及持卡人資料。 這包括限制 Azure 管理入口網站的存取權，以及指定有權建立、修改或刪除 PaaS 服務的帳戶或角色。|



## <a name="pci-dss-requirement-72"></a>PCI DSS 需求 7.2

**7.2** 為系統元件建立存取控制系統，以限制只有「必須知道」的使用者才可進行存取，否則，除非特別允許，系統將設定為「全部拒絕」。
此存取控制系統必須包含下列項目：
- 7.2.1 涵蓋所有系統元件。
- 7.2.2 根據作業分類和職責指派個人權限。
- 7.2.3 預設「全部拒絕」。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Active Directory 限制只有指定的使用者可進行存取。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。|



## <a name="pci-dss-requirement-73"></a>PCI DSS 需求 7.3

**7.3** 確定限制持卡人資料存取的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 文件會提供使用案例，並說明誰會使用 CHD 及如何使用 CHD。|




