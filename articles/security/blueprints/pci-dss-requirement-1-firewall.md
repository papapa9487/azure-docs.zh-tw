---
title: "Azure 付款處理藍圖 - 防火牆需求"
description: "PCI DSS 需求 1"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容環境的防火牆需求 
## <a name="pci-dss-requirement-1"></a>PCI DSS 需求 1

**安裝及維護防火牆設定，以保護持卡人資料**

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 如需每個需求之測試程序和指引的相關資訊，請參閱 PCI DSS。

防火牆是種裝置，可控制實體網路 (內部) 和未受信任網路 (外部) 之間允許的電腦流量，以及實體內部受信任網路內流進和流出較敏感區域的流量。 持卡人資料環境就是個例子，它是實體受信任網路內的較敏感區域。
防火牆會檢查所有網路流量，並封鎖不符合指定安全準則的傳輸。
所有系統皆必須防範來自未受信任網路的未經授權存取，像是透過網際網路作為為電子商務進入系統、員工透過桌面瀏覽器的網際網路存取、員工電子郵件存取、企業對企業連線專用的連線，或是透過無線網路或其他來源的存取。 通常，來自 (或目標是) 未受信任網路的不起眼路徑，可能會提供未受保護的路徑來通往主要系統。 防火牆是任何電腦網路的關鍵保護機制。
其他系統元件也可能會提供防火牆功能，只要這些元件符合需求 1 中所定義的防火牆最低需求。 當持卡人資料環境中的其他系統元件提供防火牆功能時，這些裝置就必須包含在需求 1 的評估範圍內。

## <a name="pci-dss-requirement-11"></a>PCI DSS 需求 1.1

**1.1** 建立和實作防火牆與路由器設定標準，包括下列項目 (請參閱 1.1.1 至 1.1.7)。


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 PaaS 隔離提供 CDE 的防火牆功能，以及使用 App Service Environment 實作確保 CDE 輸入和輸出的資料會受到保護。<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 是基於相容性因素而使用的進階服務方案。 如需有關 ASE 控制項和設定的詳細資訊，請參閱 [PCI 指引 - App Service Environment](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-111"></a>PCI DSS 需求 1.1.1

**1.1.1** 核准和測試所有網路連線及防火牆和路由器設定變更的正式流程


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 執行個體會建立 CI/CD DevOps 模型，確保所有變更受到正確地管理。 [Operations Management Suite (OMS)](/azure/operations-management-suite/) 提供廣泛的變更記錄。 為確保正確性，您可以檢閱及驗證變更。 如需更具體的指引，請參閱 [PCI 指引 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。<br /><br />[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)可提供所有 Azure 資源安全性狀態的集中檢閱。 只需看一眼，您就可以確認安全性控制項是否已就緒並正確設定，以及快速找出任何需要注意的資源。|



### <a name="pci-dss-requirement-112"></a>PCI DSS 需求 1.1.2

**1.1.2** 目前網路圖表會識別持卡人資料環境和其他網路間的連線，包括任何無線網路

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 請參閱 Contoso Webstore 參考架構和設計文件 (解決方案安裝模式的一部分)。|



### <a name="pci-dss-requirement-113"></a>PCI DSS 需求 1.1.3

**1.1.3** 目前圖表會顯示系統和網路上所有持卡人資料流程

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 請參閱 Contoso Webstore DFD 和威脅模型。|



### <a name="pci-dss-requirement-114"></a>PCI DSS 需求 1.1.4

**1.1.4** 每個網際網路連線及任何非軍事區域 (DMZ) 與內部網路區域之間的防火牆需求

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 採用界限保護裝置，例如閘道和網路 ACL，以及使用應用程式防火牆來控制平台層級上外部和內部界限的通訊。 接著，客戶會依據他們的規格與需求來設定這些項目。 通訊進入平台時，Microsoft Azure 會加以篩選。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 PaaS 隔離提供 DMZ，以及使用 App Service Environment 實作確保 CDE 輸入和輸出的資料會受到保護。<br /><br />[App Service Environment (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 是基於相容性因素而使用的進階服務方案。 如需有關 ASE 控制項和設定的詳細資訊，請參閱 [PCI 指引 - App Service Environment](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-115"></a>PCI DSS 需求 1.1.5

**1.1.5** 管理網路元件的群組、角色和責任說明

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會使用 [Azure 角色型存取控制 (RBAC)](/azure/active-directory/role-based-access-control-configure) 來分隔使用者角色。 RBAC 可以對 Azure 進行精確且專注的存取權管理。 訂用帳戶存取及 Azure Key Vault 存取會使用特定設定。|



### <a name="pci-dss-requirement-116"></a>PCI DSS 需求 1.1.6

**1.1.6** 使用所有可用服務、通訊協定和連接埠的業務正當理由和核准文件，包括針對被視為不安全通訊協定所實作的安全性功能文件。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 在整個 RA 設計中，Contoso Webstore 只會開啟必要的連接埠和通訊協定。 在 DFD 和威脅模型中可以看到資料流的詳細資料。|



### <a name="pci-dss-requirement-117"></a>PCI DSS 需求 1.1.7

**1.1.7** 必須至少每六個月檢閱防火牆和路由規則集

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 在 Contoso Webstore 中，防火牆規則集會受到檢閱，以確保沒有包含不必要或不使用的規則。 根據設計，範例會以最低權限和最小路徑耗用量進行部署。|



## <a name="pci-dss-requirement-12"></a>PCI DSS 需求 1.2

**1.2** 建置防火牆和路由器設定，以限制未受信任網路與持卡人資料環境中任何系統元件之間的連線。 

> [!NOTE]
> 「未受信任網路」是指接受檢閱之實體網路以外的任何網路，和/或超出實體控制或管理能力的任何網路。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 的 CDE 定義於 RA 和部署文件。 根據設計，不受信任的網路會遭到拒絕。|



### <a name="pci-dss-requirement-121"></a>PCI DSS 需求 1.2.1

**1.2.1** 僅限持卡人資料環境中需要的輸入和輸出流量，並明確拒絕所有其他流量。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 的 CDE 定義於 RA 和部署文件。 根據設計，不受信任的網路會遭到拒絕。 Contoso Webstore 會示範設定 Microsoft Azure 應用程式防火牆，只允許指定範圍的 IP 位址來存取 Microsoft Azure 服務。 Contoso Webstore 在所有 CDE 界限上提供「全部拒絕」防火牆。 部署的初始設定期間就會進行所有設定。

> [!NOTE]
> App Service Environment (ASE) 用來在解決方案中隔離 CDE，不過，當您的合格安全評估機構 (QSA) 評估此解決方案時，ASE 就十分重要，因為 ASE 會實作可讓 ASE 產生輸出連線的 DMZ 隔離。 PCI-DSS 要求必須封鎖不必要的所有輸入和輸出連線。 為了讓 ASE 正常運作，ASE 將會在需要時建立輸出連線，如同＜[App Service Environment 的網路功能考量](/azure/app-service/app-service-environment/network-info)＞中所述。 客戶應先評估過 QSA 的輸出連線，再將解決方案部署到生產環境，以確保解決方案符合需求。 |



### <a name="pci-dss-requirement-122"></a>PCI DSS 需求 1.2.2

**1.2.2** 保護並同步路由器設定檔。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會提供與 Microsoft Azure 原生網路控制項同步的設定。|



### <a name="pci-dss-requirement-123"></a>PCI DSS 需求 1.2.3

**1.2.3** 在持卡人資料環境與所有無線網路之間安裝周邊防火牆，並設定防火牆來拒絕或允許 (如果流量是業務上所需的) 無線環境和持卡人資料環境之間已經授權的流量。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 並未啟用任何無線解決方案或功能。|



## <a name="pci-dss-requirement-13"></a>PCI DSS 需求 1.3

**1.3** 禁止網際網路和持卡人資料環境中任何系統元件之間的直接公開存取。

**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會採用網路型和主機型界限保護裝置，例如防火牆、負載平衡器和 ACL。 這些裝置會使用 VLAN 隔離、NAT 和封包篩選等機制，來分隔網際網路的客戶流量和管理流量。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署階段提供 Azure 應用程式防火牆的設定，以允許特定範圍的 IP 位址存取網站，包括 CDE 中的防禦 Azure 虛擬機器。|



### <a name="pci-dss-requirement-131"></a>PCI DSS 需求 1.3.1

**1.3.1** 實作 DMZ，以限制輸入流量只能流入提供已授權公開存取服務、通訊協定和連接埠的系統元件。


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 其 DMZ 的 Contoso Webstore 實作可確保只有獲得授權的服務可以與 CDE 連線。|



### <a name="pci-dss-requirement-132"></a>PCI DSS 需求 1.3.2

**1.3.2** 限制輸入網際網路流量流至 DMZ 內的 IP 位址。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 其 DMZ 的 Contoso Webstore 實作可確保只有獲得授權的服務可以與 CDE 連線。|



### <a name="pci-dss-requirement-133"></a>PCI DSS 需求 1.3.3

**1.3.3** 實作反詐騙措施來偵測並封鎖偽造的來源 IP 位址，防止其進入網路。 (例如，封鎖產生至內部來源位址的網際網路流量)。

**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會實作網路篩選以防止假冒流量，並將傳入和傳出的流量限定於信任的平台元件。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-134"></a>PCI DSS 需求 1.3.4

**1.3.4**不允許未經授權的輸出流量從持卡人資料環境傳送到網際網路。


**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 架構可防止未經授權的輸出流量從範圍內的環境傳送到網際網路。 這會透過在 Microsoft Azure 中設定已核准連接埠和通訊協定的輸出流量 ACL 來完成。 這些控制項包括 SQL Server 資料庫中的 CDE 存取權。 <br /><br />PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-135"></a>PCI DSS 需求 1.3.5

**1.3.5** 網路內允許「已建立」的連線。


**職責：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會實作網路篩選以防止假冒流量，並將傳入和傳出的流量限定於信任的平台元件。 Microsoft Azure 網路是以分開客戶流量和管理流量來加以隔離。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | 不適用。|



### <a name="pci-dss-requirement-136"></a>PCI DSS 需求 1.3.6

**1.3.6** 將儲存持卡人資料的系統元件 (例如資料庫) 放置在內部網路區域中，與 DMZ 和其他未受信任的網路分開。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會使用網路隔離和 NAT 來分開客戶流量和管理流量。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 架構可防止未經授權的輸出流量從範圍內的環境傳送到網際網路。 這會透過在 Microsoft Azure 中設定已核准連接埠和通訊協定的輸出流量 ACL 來完成。 這些控制項包括 SQL Server 資料庫中的 CDE 存取權。 <br /><br />PaaS SQL Database 執行個體用來示範資料庫安全性措施。 如需詳細資訊，請參閱 [PCI 指引 - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-137"></a>PCI DSS 需求 1.3.7

**1.3.7** 請勿將私人 IP 位址和路由資訊透露給未經授權的合作對象。 

> [!NOTE]
> 遮蔽 IP 定址的方法包括 (但不限於)：
> - 網路位址轉譯 (NAT)。
> - 將包含持卡人資料的伺服器放在 Proxy 伺服器/防火牆後方。
> - 移除或篩選採用註冊定址的私人網路路由通告。
> - 在內部使用 RFC1918 位址空間，而不是已註冊的位址。


**職責：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | Microsoft Azure 會使用網路位址轉譯 (NAT) 和網路隔離和來分開客戶流量和管理流量。 Azure 裝置可透過其 UUID 來進行唯一識別，並使用 Kerberos 驗證。 Azure 受管理的網路裝置會藉由 RFC 1918 IP 位址加以識別。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會將所有持卡人資料放在 Proxy 伺服器/防火牆後面，並會在內部使用 RFC1918 位址空間。|



## <a name="pci-dss-requirement-14"></a>PCI DSS 需求 1.4

**1.4** 在可攜式計算裝置 (包括公司和/或員工所擁有) 上安裝個人防火牆軟體或同等功能，因為這些裝置會從網路外部連線到網際網路 (例如，員工使用的筆記型電腦)，而且也會用來存取 CDE。 防火牆 (或同等功能) 的設定包括： -定義特定的組態設定。
- 個人防火牆 (或同等功能) 正在執行。
- 個人防火牆 (或同等功能) 不可由可攜式電腦裝置的使用者變更。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 不提供使用者裝置的保護。 [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) 可以管理您工作人員用來存取公司資料的行動裝置。|



## <a name="pci-dss-requirement-15"></a>PCI DSS 需求 1.5

**1.5** 確定管理防火牆的安全性原則和作業程序已記錄、已使用且已讓相關單位了解。

**職責：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供者<br />(Microsoft&nbsp;Azure)** | 不適用。 |
| **客戶<br />(PCI&#8209;DSS&nbsp;藍圖)** | Contoso Webstore 會在部署階段提供 Azure 應用程式防火牆的設定，以允許特定範圍的 IP 位址存取網站，包括 CDE 中的防禦 Azure 虛擬機器。|




