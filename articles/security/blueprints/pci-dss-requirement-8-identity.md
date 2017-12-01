---
title: "Azure 付款處理藍圖 - 身分識別需求"
description: "PCI DSS 需求 8"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的身分識別需求 
## <a name="pci-dss-requirement-8"></a>PCI DSS 需求 8

**識別及驗證系統元件的存取**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

將唯一識別碼 (ID) 指派給每個具有存取權的人員，可確保每個人對自己的動作都負有責任。 當這類責任存在時，對重要資料和系統所採取的動作就會由已知且經過授權的使用者和流程來執行，並可對其進行追蹤。
密碼的效能主要取決於設計和實作驗證系統，特別是，攻擊者可嘗試破解密碼的次數，以及在入口點、傳輸期間和儲存體內保護使用者密碼的安全性方法。

> [!NOTE]
> 這些需求適用於所有具有系統管理功能的帳戶 (包括銷售點)，而帳戶若用來檢視或存取持卡人資料或存取持卡人資料的系統，則也適用這些需求。 這包括由廠商和其他第三方 (例如提供支援或維護) 使用的帳戶。 這些需求並不適用於客戶 (例如持卡人) 所使用的帳戶。 不過，需求 8.1.1、8.2、8.5、8.2.3 到 8.2.5 及 8.1.6 到 8.1.8 的目的不是套用至銷售點付款應用程式內的使用者帳戶，該應用程式一次只可存取一張卡片，以便完成單一交易 (例如收銀員帳戶)。
 
## <a name="pci-dss-requirement-81"></a>PCI DSS 需求 8.1

**8.1** 確保針對系統元件上的所有非客戶使用者和系統管理員，都已定義和實作適當的使用者識別管理原則和程序，如下所示。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 針對範例部署提供了正確使用系統管理員的使用案例和描述。|



### <a name="pci-dss-requirement-811"></a>PCI DSS 需求 8.1.1

**8.1.1** 將唯一識別碼指派給所有使用者後，才能允許他們存取系統元件或持卡人資料。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會實作 Azure Active Directory 和 Azure Active Directory 角色型存取控制 (RBAC)，確保所有使用者都有唯一識別碼。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS 需求 8.1.2

**8.1.2** 控制使用者識別碼、認證和其他識別碼物件的新增、刪除和修改。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會實作 Azure Active Directory 和 Azure Active Directory 角色型存取控制 (RBAC)，確保所有使用者都有唯一識別碼。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS 需求 8.1.3

**8.1.3** 對於任何終止動作的使用者，立即撤銷存取權。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會利用 Azure Active Directory 進行使用者管理。 您可以在 Active Directory 中撤銷使用者。|



### <a name="pci-dss-requirement-814"></a>PCI DSS 需求 8.1.4

**8.1.4** 在90 天內移除或停用非作用中的使用者帳戶。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Active Directory 進行使用者管理。 可設定 `-enableADDomainPasswordPolicy` 選項，以確保密碼會在 90 天後到期。|



### <a name="pci-dss-requirement-815"></a>PCI DSS 需求 8.1.5

**8.1.5** 管理第三方用來存取、支援或維護系統元件的識別碼 (透過遠端存取)，如下所示：
- 只在所需期間內啟用，並在不使用時將其停用。
- 在使用中時進行監視。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已採用適用的公司和組織安全性原則，包括資訊安全性原則。 原則已核准、發佈並傳送到 Microsoft Azure。 資訊安全性原則需要授與 Microsoft Azure 資產的存取權，且須以業務正當理由為基礎，以及具有資產擁有者的授權，並根據「必須知道」和「最低權限」的原則來加以限制。 此外，原則也會解決存取管理週期的需求，包括存取佈建、驗證、存取授權、存取權移除和定期存取檢閱。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 範例已實作 Azure Active Directory 和 Azure Active Directory 角色型存取控制，可管理安裝的使用者存取權。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS 需求 8.1.6

**8.1.6** 限制重複的存取，嘗試輸入使用者識別碼最多六次後，即鎖定該使用者識別碼。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 已對範例中所有使用者實作清楚的職責分工 (SOD)。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)中的「Azure Active Directory Identity Protection」。|



### <a name="pci-dss-requirement-817"></a>PCI DSS 需求 8.1.7

**8.1.7** 將鎖定期間設定為最少 30 分鐘，或直到系統管理員啟用該使用者識別碼。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責建立、強制執行及監視符合 PCI DSS 需求的密碼原則。|



### <a name="pci-dss-requirement-818"></a>PCI DSS 需求 8.1.8

**8.1.8** 如果工作階段已閒置超過 15 分鐘，則使用者需要重新進行驗證以重新啟動終端機或工作階段。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責建立、強制執行及監視符合 PCI DSS 需求的密碼原則。|



## <a name="pci-dss-requirement-82"></a>PCI DSS 需求 8.2

**8.2** 除了指派唯一識別碼，針對所有系統元件上的非客戶使用者和系統管理員，請確保至少有採用下列其中一種方法來驗證所有使用者的適當使用者驗證管理：
- 您知道的密碼或複雜密碼等
- 您擁有的權杖裝置或智慧卡 (smart card) 等
- 您的特徵，例如生物辨識技術

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 已停用多重要素驗證的 Contoso Webstore 實作，讓範例更容易使用。 可以使用 [Azure Multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) 實作多重要素驗證。|



### <a name="pci-dss-requirement-821"></a>PCI DSS 需求 8.2.1

**8.2.1** 在所有系統元件上進行傳輸和儲存時，使用強式密碼編譯，將所有驗證認證 (例如密碼/複雜密碼) 處理為無法讀取。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已建立金鑰管理程序來管理整個週期的密碼編譯金鑰 (例如，產生、發佈和撤銷)。 Microsoft Azure 會使用 Microsoft 的企業 PKI 基礎結構。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會強制執行指南中所述的強式密碼。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS 需求 8.2.2

**8.2.2** 修改任何驗證認證 (例如執行密碼重設、佈建新權杖或產生新金鑰) 之前，先驗證使用者的身分識別。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 已建立金鑰管理程序來管理整個週期的密碼編譯金鑰 (例如，產生、發佈和撤銷)。 Microsoft Azure 會使用 Microsoft 的企業 PKI 基礎結構。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會強制執行指南中所述的強式密碼。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。|



### <a name="pci-dss-requirement-823"></a>PCI DSS 需求 8.2.3

**8.2.3** 密碼/複雜密碼必須符合下列規範：
- 需要至少七個字元長。
- 包含數字及英文字母字元。
或者，密碼/複雜密碼的複雜性及強度至少要相當於上方指定的參數。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會強制執行指南中所述的強式密碼。|



### <a name="pci-dss-requirement-824"></a>PCI DSS 需求 8.2.4

**8.2.4** 至少每隔 90 天變更使用者密碼/複雜密碼。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Active Directory 進行使用者管理。 可設定 `-enableADDomainPasswordPolicy` 選項，以確保密碼至少每 90 天會到期。|



### <a name="pci-dss-requirement-825"></a>PCI DSS 需求 8.2.5

**8.2.5** 個人提交的新密碼/複雜密碼不可與他們前四次使用的任一密碼/複雜密碼一樣。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會強制執行指南中所述的強式密碼。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS 需求 8.2.6

**8.2.6** 為使用者設定唯一值作為密碼/複雜密碼以供第一次或重設時使用，並在第一次使用之後立即變更。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會強制執行指南中所述的強式密碼。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS 需求 8.3

**8.3** 保護所有非主控台的個別系統管理存取權，以及保護持卡人資料環境 (CDE) 中所有使用多重要素驗證的遠端存取。

> [!NOTE]
> 多重要素驗證至少需要使用三種驗證方法中的兩個 (如需驗證方法的說明，請參閱需求 8.2)，才可進行驗證。 若使用其中一個要素兩次 (例如，使用兩個重複的密碼) ，則不被視為多重要素驗證。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 系統管理員在對 Azure 系統和伺服器執行維護或管理時，會需要使用多重要素驗證來進行存取。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 範例中不會實作多重要素驗證。|



### <a name="pci-dss-requirement-831"></a>PCI DSS 需求 8.3.1

**8.3.1** 針對具有系統管理員存取權的人員，將適用於非主控台存取的多重要素驗證併入 CDE。

> [!NOTE]
> 這項需求在 2018 年 1 月 31 日前是屬於最佳作法，該日期之後會變成需求。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 系統管理員在對 Azure 系統和伺服器執行維護或管理時，會需要使用多重要素驗證來進行存取。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 範例中不會實作多重要素驗證。|



### <a name="pci-dss-requirement-832"></a>PCI DSS 需求 8.3.2

**8.3.2** 針對所有來自實體網路外部的遠端網路存取 (包含使用者和系統管理員，以及用於支援或維護的第三方存取)，併入多重要素驗證。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 系統管理員在對 Azure 系統和伺服器執行維護或管理時，會需要使用多重要素驗證來進行存取。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 範例中不會實作多重要素驗證。|



## <a name="pci-dss-requirement-84"></a>PCI DSS 需求 8.4

**8.4** 記錄驗證原則和程序並傳遞給所有使用者，包括：
- 選取強式驗證認證的指引
- 使用者應如何保護其驗證憑證的指引
- 指示不重複使用先前用過的密碼
- 變更密碼的簡介 (如果懷疑密碼有洩露的可能)

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶會負責遵循指引，以及記錄驗證程序與原則，並傳遞給所有使用者。|



## <a name="pci-dss-requirement-85"></a>PCI DSS 需求 8.5

**8.5** 請勿使用群組、共用或一般的識別碼、密碼或其他驗證方法，如下所示：
- 一般使用者識別碼會遭到停用或移除。
- 共用的使用者識別碼不適用於系統管理與其他重要功能。
- 共用和一般的使用者識別碼不會用來管理任何系統元件。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 範例中不會實作多重要素驗證。|



### <a name="pci-dss-requirement-851"></a>PCI DSS 需求 8.5.1

**8.5.1** **僅適用於服務提供者的其他需求：**服務提供者若從遠端存取客戶所在場所 (例如，支援 POS 系統或伺服器所需)，則必須針對每個客戶使用唯一的驗證認證 (例如密碼/複雜密碼)。 

> [!NOTE]
> 這項需求不適用於讓共用裝載的提供者存取自有裝載環境，因為其中裝載多個客戶環境。

**職責：&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用於 Microsoft Azure 客戶。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用於 Microsoft Azure 客戶。|



## <a name="pci-dss-requirement-86"></a>PCI DSS 需求 8.6

**8.6** 若使用其他驗證機制 (例如，實體或邏輯安全性權杖、智慧卡和憑證等)，則這些機制的使用必須如下列所示進行指派：
- 驗證機制必須指派給個別帳戶，並不在多個帳戶之間共用。
- 必須有實體和 (或) 邏輯控制項，以確保只有預定帳戶可以使用該機制來取得存取權。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署期間建立三個帳戶：admin、sqladmin 和 edna (在範例執行期間登入 Web 應用程式的預設使用者)。 範例中不會實作多重要素驗證。 所有存取權都會透過 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理，其可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 |



## <a name="pci-dss-requirement-87"></a>PCI DSS 需求 8.7

**8.7** 所有包含持卡人資料的資料庫存取 (包括來自應用程式、系統管理員和所有其他使用者的存取)，都會受到如下所示的限制：
- 所有資料庫上的使用者存取、使用者查詢和使用者動作都是透過程式設計的方法進行。
- 只有資料庫管理員能夠直接存取或查詢資料庫。
- 資料庫應用程式的應用程式識別碼僅能由應用程式使用 (而不是由個別使用者或其他非應用程式流程)。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 Azure Key Vault 保護所有持卡人資料，而部署文件中會概述記錄的加密方式。 如需詳細資訊，請參閱 [PCI 指引 - 加密](payment-processing-blueprint.md#encryption-and-secrets-management)。<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS 需求8.8

**8.8** 確定識別碼和驗證的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 客戶須負責確定識別碼和驗證的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。|




