---
title: "Azure Stack 資料中心整合 - 發佈端點"
description: "了解如何在您的資料中心內發佈 Azure Stack 端點"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/18/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 0d15252079b62f6a74a1279309fb9b1b3ed5711e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack 資料中心整合 - 發佈端點

適用於：Azure Stack 整合系統

Azure Stack 會為其基礎結構角色設定各種端點 (VIP - 虛擬 IP 位址)。 這些 VIP 是從公用 IP 位址集區配置的。 針對每個 VIP，都會藉由軟體定義網路層中的存取控制清單 (ACL) 來提供保護。 ACL 也用於各個實體交換器 (TOR 和 BMC) 來進一步強化解決方案。 系統會針對在部署階段所指定外部 DNS 區域中的每個端點，都建立一個 DNS 項目。


以下架構圖顯示各種不同的網路層和 ACL：

![架構圖](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>連接埠和通訊協定 (輸入)

下表列出將 Azure Stack 端點發佈到外部網路時所需的基礎結構 VIP。 此清單顯示每個端點、所需的連接埠，以及通訊協定。 其他資源提供者 (例如 SQL 資源提供者等) 所需的端點則涵蓋在特定資源提供者部署文件中。

此處並未列出內部基礎結構 VIP，因為它們並非發佈 Azure Stack 所需的 VIP。

> [!NOTE]
> 使用者 VIP 是動態的，由使用者自己定義，並不受 Azure Stack 操作員控制。


|端點 (VIP)|DNS 主機 A 記錄|通訊協定|連接埠|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|入口網站 (系統管理員)|`Adminportal.[Region].[External FQDN]`|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (系統管理員)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|入口網站 (使用者)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (使用者)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|圖形|`Graph.[Region].[External FQDN]`|HTTPS|443|
|憑證撤銷清單|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP 和 UDP|53|
|Key Vault (使用者)|`*.vault.[Region].[External FQDN]`|TCP|443|
|Key Vault (系統管理員)|`*.adminvault.[Region].[External FQDN]`|TCP|443|
|儲存體佇列|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|儲存體資料表|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|儲存體 Blob|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>連接埠和 URL (輸出)

Azure Stack 僅支援 Transparent Proxy 伺服器。 在 Transparent Proxy 上行連結至傳統 Proxy 伺服器的部署中，您必須允許下列連接埠和 URL 才能進行連出通訊：


|目的|URL|通訊協定|連接埠|
|---------|---------|---------|---------|
|身分識別|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|Marketplace 摘要整合|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|修補程式和更新|`https://*.azureedge.net`|HTTPS|443|
|註冊|`https://management.azure.com`|HTTPS|443|
|使用量|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>防火牆發佈

上一節中所列的連接埠適用於透過現有防火牆發佈 Azure Stack 服務時的連入通訊。

建議您使用防火牆裝置來協助保護 Azure Stack 安全。 不過，並不嚴格要求這樣做。 雖然防火牆有助於防堵分散式阻斷服務 (DDOS) 攻擊和進行內容檢查等，但也會成為 Azure 儲存體服務 (例如 Blob、資料表及佇列) 的輸送量瓶頸。

根據身分識別模型 (Azure AD 或 AD FS) 而定，可能需要也可能不需要發佈 AD FS 端點。 如果使用中斷連線的部署模式，您就必須發佈 AD FS 端點。 (如需詳細資訊，請參閱「資料中心整合身分識別」主題)。

Azure Resource Manager (系統管理員)、系統管理員入口網站及 Key Vault (系統管理員) 端點並不一定需要外部發佈。 需視案例而定。 例如，作為服務提供者，您可能會想要限制攻擊面，而只從您的網路內 (不從網際網路) 管理 Azure Stack。

就企業組織而言，外部網路可能是現有的公司網路。 在這類案例中，您必須發佈這些端點，以從公司網路操作 Azure Stack。

## <a name="edge-firewall-scenario"></a>邊緣防火牆案例

在邊緣部署中，Azure Stack 會直接部署在邊緣路由器 (由 ISP 提供) 後面，其前面會有或沒有防火牆。

![Azure Stack 邊緣部署架構圖](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

一般而言，在邊緣部署的開發階段，會為公用 VIP 集區指定公用的可路由傳送 IP 位址。 此案例可讓使用者體驗類似在公用雲端 (例如 Azure) 中的完整自我控制雲端體驗。

### <a name="using-nat"></a>使用 NAT

您可以使用「網路位址轉譯」(NAT) 來發佈端點，但因為會產生額外的負荷，所以並不建議這麼做。 針對由使用者完全控制的端點發佈，這會要求每一使用者 VIP 都有一個 NAT 規則，當中包含使用者可能使用的所有連接埠。

另一個考量是 Azure 並不支援在與 Azure 搭配的混合式雲端案例中，使用 NAT 來設定端點 VPN 通道。

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>企業/內部網路/周邊網路防火牆案例

在企業/內部網路/周邊部署中，Azure Stack 會部署在第二個防火牆後面，此防火牆通常是周邊網路 (也稱為 DMZ) 的一部分。

![Azure Stack 防火牆案例](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

如果已經為 Azure Stack 的公用 VIP 集區指定公用的可路由傳送 IP 位址，這些位址在邏輯上會屬於周邊網路，而在主要防火牆則需要有發佈規則。

### <a name="using-nat"></a>使用 NAT

如果針對 Azure Stack 的公用 VIP 集區使用非公用的可路由傳送 IP 位址，則會在次要防火牆使用 NAT 來發佈 Azure Stack端點。 在此案例中，您必須在邊緣外的主要防火牆上以及在次要防火牆上設定發佈規則。 如果您想要使用 NAT，請考量下列幾點：

- NAT 會增加管理防火牆規則時額外負荷，因為使用者會在軟體定義網路 (SDN) 堆疊中控制自己的端點和自己的發佈規則。 使用者必須連絡 Azure Stack 操作員，才能發佈其 VIP 及更新連接埠清單。
- 雖然使用 NAT 會限制使用者體驗，但它可讓操作員完全控制發佈要求。
- 針對與 Azure 搭配的混合式雲端案例，請考量 Azure 不支援使用 NAT 來設定端點 VPN 通道。


## <a name="next-steps"></a>後續步驟

[Azure Stack 資料中心整合 - 安全性](azure-stack-integrate-security.md)