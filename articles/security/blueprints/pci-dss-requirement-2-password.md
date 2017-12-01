---
title: "Azure 付款處理藍圖 - 密碼需求"
description: "PCI DSS 需求 2"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 4ae9fc7d5b53d33f9feb98c450970e0560afa2af
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 規範之環境的密碼需求 
## <a name="pci-dss-requirement-2"></a>PCI DSS 需求 2

**請勿使用廠商提供的預設值作為系統密碼和其他安全性參數**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 若要了解每個需求的測試程序和指導方針相關資訊，請參閱 PCI DSS。

(位於實體外部和內部的) 惡意個人通常會使用廠商預設密碼和其他廠商預設設定來危害系統。 這些密碼和設定皆為駭客社群熟知的資訊，且能夠輕易地從公開資訊中取得。

## <a name="pci-dss-requirement-21"></a>PCI DSS 需求 2.1
 
**2.1** 一律在網路上安裝系統**之前**，先變更廠商提供的預設值，並移除或停用非必要的預設帳戶。
此需求適用於「所有」預設密碼，包括但不限於由作業系統、提供安全性服務的軟體、應用程式和系統帳戶、銷售點 (POS) 終端機、簡易網路管理通訊協定 (SNMP) 社群字串等所使用的密碼。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure Active Directory 密碼原則需求會針對客戶在 AADUX 入口網站內所提供的新密碼強制執行。 系統會針對客戶起始的自助式密碼變更要求驗證先前的密碼。 系統會在後續登入時要求變更系統管理員重設密碼。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會針對所有使用者要求使用者設定強式密碼。 示範中沒有啟用範例或來賓帳戶。<br /><br />解決方案中沒有實作無線和 SNMP。|



### <a name="pci-dss-requirement-211"></a>PCI DSS 需求 2.1.1

**2.1.1** 針對連線到持卡人資料環境或是傳輸持卡人資料的無線環境，應於安裝時變更「所有」無線廠商預設值，包括但不限於預設的無線加密金鑰、密碼和 SNMP 社群字串。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 解決方案中沒有實作無線和 SNMP。|



## <a name="pci-dss-requirement-22"></a>PCI DSS 需求 2.2

**2.2** 針對所有系統元件開發設定標準。 確保這些標準能處理所有已知的安全性弱點，且與業界接受的系統強化標準一致。
業界接受的系統強化標準來源可能包括但不限於：
- 網際網路安全中心 (CIS)
- 國際標準化組織 (ISO)
- 系統管理稽核網路安全 (SANS) 研究機構
- 美國國家標準與技術研究院 (NIST)

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 針對 Microsoft Azure，OSSC 技術安全性服務小組會針對 Microsoft Azure 環境中的系統，開發與業界接受的強化標準一致的安全性設定標準。 這些設定會記錄於系統基準中，且會將相關的設定變更通知受影響的小組 (例如 IPAK 小組)。 會實作程序以監視針對安全性設定標準的合規性。 針對 Microsoft Azure 環境中系統的安全性設定標準會與業界接受的強化標準一致，且至少會每年進行檢閱。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會針對持卡人資料環境 (CDE) 範圍中的所有服務提供強化。 <br /><br />Contoso Webstore 也會部署 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)，其可提供所有 Azure 資源安全性狀態的集中式檢閱。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，且可以快速找出任何需要注意的資源。<br /><br />Contoso Webstore 會使用 Operations Management Suite 來記錄系統的所有變更。 [Operations Management Suite (OMS)](/azure/operations-management-suite/) 能提供廣泛的變更記錄。 可對變更進行檢閱及驗證以確保正確性。 如需更具體的指導方針，請參閱 [PCI 指導方針 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-221"></a>PCI DSS 需求 2.2.1

**2.2.1** 在每部伺服器上只實作一個主要功能，以避免要求不同安全性層級的功能會共同存在於同一部伺服器上。 (例如，Web 伺服器、資料庫伺服器及 DNS 皆應實作於個別的伺服器上)。 

> [!NOTE]
> 在使用虛擬化技術的情況下，在每個虛擬系統元件上應僅實作一個主要功能。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 服務會部署為 PaaS 服務。 使用網路分割來隔離及分割所有服務。<br /><br />Contoso Webstore 也會使用 [App Service 環境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 來強制執行關鍵作法。 如需詳細資訊，請參閱 [PCI 指引 - App Service 環境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-222"></a>PCI DSS 需求 2.2.2

**2.2.2** 僅啟用系統運作所需的必要服務、通訊協定、精靈等。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 軟體與硬體設定至少會每季進行檢閱，以識別並排除所有非必要的功能、通訊埠、通訊協定和服務。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 服務會部署為 PaaS 服務。 使用網路分割來隔離及分割所有服務。<br /><br />Contoso Webstore 也會使用 [App Service 環境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 來強制執行關鍵作法。 如需詳細資訊，請參閱 [PCI 指引 - App Service 環境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-223"></a>PCI DSS 需求 2.2.3

**2.2.3** 針對被視為不安全的所有必要服務、通訊協定或精靈，實作額外的安全性功能。 

> [!NOTE]
> 在使用 SSL/早期 TLS 的情況下，必須滿足＜附件 A2＞中的需求。


**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 服務會部署為 PaaS 服務。 使用網路分割來隔離及分割所有服務。 該部署也會針對 CDE 範圍中的所有服務提供強化。 <br /><br />Contoso Webstore 也會部署 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)，其可提供所有 Azure 資源安全性狀態的集中式檢閱。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，且可以快速找出任何需要注意的資源。<br /><br />Contoso Webstore 也會使用 [App Service 環境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 來強制執行關鍵作法。 如需詳細資訊，請參閱 [PCI 指引 - App Service 環境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-224"></a>PCI DSS 需求 2.2.4

**2.2.4** 設定系統安全性參數以防止誤用。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Azure 會使用多重要素存取控制及已記錄的業務需求，來確保僅有經授權的人員能夠設定 Azure 平台的安全性控制。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會運用 AAD 和 AD RBAC 來確保安全性參數已正確部署。 如需詳細資訊，請參閱 [PCI 指引 - 身分識別管理](payment-processing-blueprint.md#identity-management)。|



### <a name="pci-dss-requirement-225"></a>PCI DSS 需求 2.2.5

**2.2.5** 移除所有非必要的功能，例如指令碼、驅動程式、功能、子系統、檔案系統和非必要的 Web 伺服器。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore c會提供說明界限建立方式的文件。 Contoso 的威脅模型與資料流圖表會針對所有使用的服務及啟用的控制措施提供說明。|



## <a name="pci-dss-requirement-23"></a>PCI DSS 需求 2.3

**2.3** 使用強式加密對所有非主控台系統管理存取進行加密。 

> [!NOTE]
> 在使用 SSL/早期 TLS 的情況下，必須滿足＜附件 A2＞中的需求。

**責任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會確保在存取 Hypervisor 基礎結構時強制使用強式加密。 Microsoft Azure 也會確保使用 Microsoft Azure 管理入口網站的客戶能透過強式加密存取其服務/IaaS 主控台。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會示範如何在解決方案中實作強式加密。此外，將能夠執行所有測試，以驗證加密已實作於整個解決方案。<br /><br />Contoso Webstore 也會使用 [App Service 環境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 來強制執行關鍵作法。 如需詳細資訊，請參閱 [PCI 指引 - App Service 環境](payment-processing-blueprint.md#app-service-environment)。|



## <a name="pci-dss-requirement-24"></a>PCI DSS 需求 2.4

**2.4** 維護 PCI DSS 範圍中之系統元件的清查。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 的示範 PaaS 解決方案清查可於所提供的文件中檢閱。 如需詳細資訊，請參閱 [PCI 指引 - 預先安裝的 OMS 解決方案](payment-processing-blueprint.md#oms-solutions)。|



## <a name="pci-dss-requirement-25"></a>PCI DSS 需求 2.5

**2.5** 確定用於管理廠商預設值和其他安全性參數的安全性原則和作業程序都已經記錄、正在使用，且所有相關合作對象皆已經了解。

**責任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供能提供安全性參數深入解析並記錄服務項目的文件。 |



## <a name="pci-dss-requirement-26"></a>PCI DSS 需求 2.6

**2.6** 共用主機提供者必須保護每個實體的主控環境和持卡人資料。 這些提供者必須符合在＜附錄 A：適用於共用主控提供者的其他 PCI DSS 需求＞中所詳述的特定需求。

**責任：&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 Microsoft Azure 並非共用主控提供者。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。 Microsoft Azure 並非共用主控提供者。|




