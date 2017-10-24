---
title: "Azure Stack 整合系統的規劃考量 | Microsoft Docs"
description: "了解您現在可以規劃的內容並準備多節點 Azure Stack。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: twooley
ms.openlocfilehash: 790b65720a8457b780dc727b0f67fcd12aed31bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Azure Stack 整合系統的規劃考量

*適用於：Azure Stack 整合系統*

如果您對 Azure Stack 整合系統有興趣，您會想要瞭解一些有關部署的重大規劃考量，及系統如何融入您的資料中心。 本主題提供這些考量的的高階概觀。

如果您決定購買整合系統，原始設備製造商 (OEM) 硬體廠商可引導您更深入完成大部分規劃程序。 它們也會執行實際部署。

## <a name="management-considerations"></a>管理考量

Azure Stack 是密封的系統，已從權限和網路觀點鎖定基礎結構。 會套用網路存取控制清單 (ACL) 來封鎖所有未經授權的連入流量，及基礎結構元件之間所有不必要的通訊。 這會使得未經授權的使用者難以存取系統。

對於每日的管理與作業，系統管理員存取基礎結構不受限制。 Azure Stack 操作員必須透過系統管理員入口網站或 Azure Resource Manager (透過 PowerShell 或 REST API) 管理系統。 其他管理工具 (例如 HYPER-V 管理員或容錯移轉叢集管理員) 不能存取系統。 為了協助保護系統，無法在 Azure Stack 基礎結構元件中安裝協力廠商軟體 (例如，代理程式)。 透過 PowerShell 或 REST API，可與外部的管理與安全性軟體建立互通性。

需要較高層級的存取權來疑難排解無法透過警示中繼步驟解決的問題時，您必須與支援中心合作。 透過支援中心，有一個方法可將暫時的完整系統管理員存取權提供給系統，以執行更進階的操作。 

## <a name="deploy-connected-or-disconnected"></a>已連線或已中斷連線部署
 
您可以選擇部署 Azure Stack 以連線網際網路 (和 Azure)，或者中斷連線。 若要發揮 Azure Stack 的最大功效，包括 Azure Stack 與 Azure 之間的混合式情節，您可能希望讓部署連線至 Azure。 下表可協助摘要部署模式之間的主要差異。

| 領域 | 連線模式 | 中斷連線模式 |
| -------- | ------------- | ----------|
| 識別提供者 | Azure Active Directory (Azure AD) 或 Active Directory 同盟服務 (AD FS) | 僅限 AD FS |
| Marketplace 摘要整合 | 直接從 Azure Marketplace 將項目下載至 Azure Stack 中的 Marketplace | 需要手動管理 Azure Stack 中的 Marketplace |
| 授權模型 | 使用時付費或以容量為基礎 | 僅以容量為基礎|
| 修補和更新  | 將更新套件直接下載到 Azure Stack | 需要卸除式媒體及個別的連接裝置 |
| | | |

欠缺完整的系統重新部署，您稍後將無法變更部署模式。

## <a name="identity-considerations"></a>身分識別考量

### <a name="choose-identity-provider"></a>選擇識別提供者

您必須考量要使用於 Azure Stack 部署 (可能是 Azure AD 或 AD FS) 的識別提供者。 重新部署不含完整系統的部署之後，您無法切換識別提供者。

您的識別提供者選項與租用戶虛擬機器、識別系統、其使用的帳戶、它們是否能加入 Active Directory 網域等等無關。這是分開的。

**考慮使用 Azure AD 的原因**

- 您目前已投資 Azure AD (例如 Azure 或 Office 365)。
- 您想要跨 Azure 和 Azure Stack 雲端使用相同的身分識別。
- 您想要支援多租用戶情節，可讓您在相同的 Azure Stack 執行個體上裝載不同的組織。
- 您想要透過 Azure AD Graph 使用 REST 型目錄管理，以透過 API 佈建使用者、群組等等。

> [!NOTE]
> 您無法同時將 Azure Stack 部署連線到 Azure AD 執行個體與現有的 AD FS 執行個體。 如果您使用 Azure AD 進行部署，而且想要使用現有的 AD FS 執行個體，您可以在同盟您的內部部署 AD FS 執行個體與 Azure AD。

**考慮使用 AD FS 的原因**

- 沒有任何網際網路連線或只有部分網際網路連線能力。
- 存在法規/主權需求。
- 您想要針對操作員和使用者帳戶使用您自己的身分識別系統 (例如公司的 Active Directory)。 若要這樣做，您可與 Active Directory 支援的現有 AD FS 執行個體 (位於 Windows Server 2012、2012 R2 或 2016) 同盟。
- 您沒有任何現有的 Azure 投資，並且不想要使用 Azure AD。

> [!NOTE]
> Azure Stack 只能與 Active Directory 支援的另一個 AD FS 執行個體同盟。 不支援其他識別提供者。 如果您不希望其他識別提供者使用 Azure Stack，請考慮改為使用 Azure AD 型部署。

### <a name="ad-fs-and-graph-integration"></a>AD FS 和 Graph 整合

如果您選擇將 AD FS 作為識別提供者來部署 Azure Stack，您必須透過同盟信任來整合 Azure Stack 上的 AD FS 執行個體與現有的 AD FS 執行個體。 這可讓現有 Active Directory 樹系中的身分識別來驗證 Azure Stack 中的資源。

您也可以整合 Azure Stack 中的 Graph 服務與現有的 Active Directory。 這可讓您在 Azure Stack 中管理角色型存取控制 (RBAC)。 委派資源的存取權後，Graph 元件會使用 LDAP 通訊協定來查閱現有 Active Directory 樹系中的使用者帳戶。

下圖顯示整合的 AD FS 和 Graph 流量。
![顯示 AD FS 與 Graph 流量的圖表](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>授權模型

您必須決定要使用哪個授權模型。 針對已連線的部署，您可以選擇使用時付費或以容量為基礎的授權。 使用時付費需要連線至 Azure 來報告使用量，然後透過 Azure 商務計費。 如果您的部署中斷與網際網路的連線，只能使用以容量為基礎的授權。 如需授權模式的詳細資訊，請參閱 [Microsoft Azure Stack 封裝和定價](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。

## <a name="naming-decisions"></a>命名決策

您將需要思考如何規劃 Azure Stack 命名空間，尤其是區域名稱和外部網域名稱。 公開端點之 Azure Stack 部署的完整網域名稱 (FQDN) 是這兩個名稱的組合 ( &lt;*region*&gt;&lt;*external_FQDN*&gt;)，例如 *east.cloud.fabrikam.com*。在此範例中，Azure Stack 入口網站將位於下列 URL：

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

下表摘要這些網域命名決策。

| 名稱 | 說明 | 
| -------- | ------------- | 
|區域名稱 | 您的第一個 Azure Stack 區域名稱。 這個名稱會用於 Azure Stack 管理的公用虛擬 IP 位址 (VIP) 之 FQDN 的一部分。 一般而言，區域名稱會是資料中心位置之類的實體位置識別碼。 | 
| 外部網域名稱 | 含外部 VIP 之端點的網域名稱系統 (DNS) 區域名稱。 針對這些公用 VIP 在 FQDN 中使用。 | 
| 私人 (內部) 網域名稱 | 在 Azure Stack 上為基礎結構管理建立的網域 (和內部 DNS 區域) 的名稱。 
| | |

## <a name="certificate-requirements"></a>憑證需求

針對部署，您將需要為公開端點提供安全通訊端層 (SSL) 憑證。 概括而言，憑證具有下列需求：

> [!IMPORTANT]
> 此文章中的憑證資訊僅提供做為一般指引。 取得 Azure Stack 的任何憑證之前，請與您的 OEM 硬體合作夥伴接洽。 它們將提供更詳細的憑證指引和需求。

- 您可以使用單一萬用字元憑證或以使用一組專用的憑證，並只針對端點 (例如儲存體和金鑰保存庫) 使用萬用字元。
- 憑證必須由大眾信任的憑證授權單位 (CA) 或企業 CA 發行。
- 中繼 CA 無法發行憑證。
 
下表顯示需要服務，和需要初始 Azure Stack 部署所需憑證的公開端點數目。 

| 用於 | 端點 
| -------- | ------------- | 
| Azure Resource Manager (系統管理員) | adminmanagment.[region].[external_domain] |
| Azure Resource Manager (使用者) | management.[region].[external_domain] |
| 入口網站 (系統管理員) | adminportal.[region].[external_domain] |
| 入口網站 (使用者) | portal. [region].[external_domain] |
| 金鑰保存庫 (使用者) | &#42;.vault.[region].[external_domain] |
| 金鑰保存庫 (系統管理員) | &#42;.adminvault.[region].[external_domain] |
| 儲存體 | &#42;.blob.[region].[external_domain]<br>&#42;.table.[region].[external_domain]<br>&#42;.queue.[region].[external_domain]  |
| Graph** | graph.[region].[external_domain] |
| AD FS** | adfs.[region].[external_domain] |
| | |

** 只有 AD FS 部署需要 Graph 和 AD FS 端點的憑證。

如果您想要使用單一萬用字元憑證，您總計需要初始 Azure Stack 部署的六個主體別名 (SAN)。 這些 SAN 是： 

- &#42;.[region].[external_domain]
- &#42;.vault.[region].[external_domain]
- &#42;.adminvault.[region].[external_domain]
- &#42;.blob.[region].[external_domain]
- &#42;.table.[region].[external_domain] 
- &#42;.queue.[region].[external_domain]

## <a name="time-synchronization"></a>時間同步處理

您必須使用可透過 IP 位址解析的外部時間伺服器來同步 Azure Stack 時間伺服器。 中斷連線的部署需要公司網路上的時間伺服器。

## <a name="network-connectivity"></a>網路連線

### <a name="dns-integration"></a>DNS 整合

若要解析 Azure Stack 的外部 DNS 名稱 (例如 www.bing.com)，您必須提供 Azure Stack 可用來轉送 Azure Stack 對其而言不具權威性之 DNS 要求的 DNS 伺服器。 作為部署輸入，您至少必須提供兩部伺服器以作為容錯的 DNS 轉寄站。

若要從 Azure Stack 外部解析 Azure Stack 端點的 DNS 名稱 (例如，從公司樹系)，您必須將裝載 Azure Stack 外部 DNS 區域的 DNS 伺服器，與裝載要使用之上層區域的 DNS 伺服器整合。 這需要在 Azure Stack 與資料中心內現有 DNS 區域之間進行 DNS 名稱解析。 若要這麼做，您將使用條件式轉接或區域委派之類的方法。 如果您可以直接控制裝載 Azure Stack 外部 DNS 命名空間之上層區域的 DNS 伺服器，建議您使用條件式轉接。 如果外部的 Azure Stack DNS 區域會顯示成您公司網域名稱的子網域 (例如，azurestack.contoso.com 和 contoso.com)，必須改用區域委派。

### <a name="network-infrastructure"></a>網路基礎結構

Azure Stack 的網路基礎結構包含交換器上設定的數個邏輯網路。 下圖顯示這些邏輯網路，及其如何整合機架頂端 (TOR)、基礎板管理控制器 (BMC) 與界限 (客戶網路) 交換器。

![邏輯網路圖表和交換器連線](media/azure-stack-planning-considerations/NetworkDiagram.png)

下表顯示邏輯網路及您必須規劃的相關 IPv4 子網路範圍。

| 邏輯網路 | 說明 | 大小 | 
| -------- | ------------- | ------------ | 
| 公用 VIP | 小 Azure Stack 服務集的公用 IP 位址，含租用戶虛擬機器所使用的其餘位址。 Azure Stack 基礎結構會從這個網路使用 32 個位址。 如果您打算使用 App Service 和 SQL 資源提供者，則會再使用 7 個位址。 | /26 (62 部主機) - /22 (1022 部主機)<br><br>建議 = / 24 (254 部主機) | 
| 交換器基礎結構 | 路由用途的點對點 IP 位址、專屬的交換器管理介面，及指派給參數的回送位址。 | /26 | 
| 基礎結構 | 用於通訊的 Azure Stack 內部元件。 | /24 |
| 私人 | 用於存放網路和私用 VIP。 | /24 | 
| BMC | 用來與實體主機上的 BMC 通訊。 | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>上行連結至界限交換器

您將需要設定為資料中心內框線交換器的上行連結。 您可以使用下列方法之一，從 Azure Stack 整合系統的機架頂端 (TOR) 交換器路由此第 3 層流量：

- 邊界閘道協定 (BGP) 
- 靜態路由

我們建議 BGP，因為它可自動更新軟體負載平衡多工器 (SLB MUX) 針對外部網路所發佈的路由。 如果您在部署之後新增公用 IP 位址範圍，則這很重要。 如果您從 TOR 交換器至其連接的彙總交換器執行 BGP 對等互連，則會將您新增後續部署的公用 IP 位址範圍，通知不需要手動介入的彙總交換器。 如果您使用靜態路由，每次您新增公用 IP 位址區塊時，您都必須手動更新新範圍的路由。

### <a name="proxy-server"></a>Proxy 伺服器

Azure Stack 僅支援 Transparent Proxy 伺服器。 透明 Proxy 會攔截網路層上的要求，而不需要任何特殊的用戶端組態。

### <a name="publish-azure-stack-services"></a>發佈 Azure Stack 服務

您必須將 Azure Stack 服務開放給 Azure Stack 的外部使用者使用。 Azure Stack 會設定其基礎結構角色的各種端點。 這些端點是來自公用 IP 位址集區的指派 VIP。 會針對外部 DNS 區域中的每個端點建立一個 DNS 項目，這是在部署時間所指定。 例如，會將 "portal.<*region*>.<*external_FQDN*>" 的 DNS 主機項目指派給使用者入口網站。 

#### <a name="ports-and-urls"></a>連接埠和 URL

若要讓外部網路使用 Azure Stack 服務 (例如入口網站、Azure Resource Manager、DNS 等)，您必須允許針對特定 URL、連接埠和通訊協定允許至這些端點的輸入流量。
 
在上行連結至傳統 Proxy 伺服器的部署中，您必須允許特定連接埠和 URL 的連出通訊。 其中包括用於身分識別、Marketplace 摘要整合、修補和更新、註冊和使用資料的連接埠和 URL。

如需詳細資訊，請參閱：
- [輸入連接埠和通訊協定](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [輸出連接埠和 URL](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>將 Azure Stack 連線至 Azure

對於混合式雲端情節，您必須規劃如何將 Azure Stack 連線至 Azure。 支援兩種方法將 Azure Stack 中的虛擬網路連線至 Azure 中的虛擬網路： 
- **網站間**。 透過 IPsec (IKE v1 和 IKE v2) 的虛擬私人網路 (VPN) 連線。 此類型的連線需要 VPN 裝置或路由及遠端存取服務 (RRAS)。 如需 Azure 中 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 會透過這個通道的通訊加密，並且是安全的。 不過，頻寬會受限於通道的最大輸送量 (100-200 Mbps)。
- **輸出 NAT**。 根據預設，Azure Stack 中的所有虛擬機器皆可透過輸出 NAT 連線到外部網路。 Azure Stack 中建立的每個虛擬網路會被指派一個公用 IP 位址。 無論虛擬機器被直接指派公用 IP 位址，或是位於含公用 IP 位址的負載平衡器後方，它皆可使用虛擬網路的 VIP，透過輸出 NAT 進行外部存取。 這只適用於虛擬機器所起始，並針對外部網路 (網際網路或內部網路) 的通訊。 它不能與來自外部的虛擬機器進行通訊。

#### <a name="hybrid-connectivity-options"></a>混合式連線能力選項

對於混合式連線能力，務必考慮您想要提供何種部署及其部署位置。 您必須考量是否需要隔離每個租用戶的網路流量，以及是否也有內部網路或網際網路部署。

- **單一租用戶 Azure Stack**。 Azure Stack 部署看起來 (至少從網路觀點) 就像是一個租用戶。 可能有許多租用戶訂用帳戶，但就像任何內部網路服務，所有流量都會透過相同的網路傳輸。 一個訂用帳戶會與另一個訂用帳戶透過相同的網路連線傳輸網路流量，並且不需要透過加密通道隔離。

- **多租用戶 Azure Stack**。 一個 Azure Stack 部署，其中每個租用戶訂用帳戶流量與 Azure Stack 外部的網路繫結，必須將其與其他租用戶的網路流量隔離。
 
- **內部網路部署**。 位於公司內部網路上 Azure Stack 部署，一般位於私人 IP 位址空間與一或多個防火牆之後。 公用 IP 位址並非真正公用，因為它們無法透過公用網際網路直接路由。

- **網際網路部署**。 一個 Azure Stack 部署，其連線到公用網際網路，並針對公用 IP 位址使用網際網路的可路由公用 IP 位址。 部署仍可放在防火牆後面，但公用 VIP 範圍可從公用網際網路和 Azure 直接連線。
 
下表摘要說明混合式連線情節，包含優點、缺點和使用案例。

| 案例 | 連線方法 | 優點 | 缺點 | 適用對象 |
| -- | -- | --| -- | --|
| 單一租用戶 Azure Stack，內部網路部署 | 輸出 NAT | 用於更快傳輸的更佳頻寬。 簡單實作；沒有必要的閘道。 | 不會加密流量；TOR 外部無隔離或加密。 | 同樣信任所有租用戶的企業部署。<br><br>具有至 Azure 的 Azure ExpressRoute 電路的企業。 |
| 多租用戶 Azure Stack，內部網路部署 | 站對站 VPN | 從租用戶 VNet 到目的地的流量是安全的。 | 頻寬會受限於站對站 VPN 通道。<br><br>需要虛擬網路上的閘道和目的地網路上的 VPN 裝置。 | 必須避免其他租用戶接觸其部分租用戶流量的企業部署。 |
| 單一租用戶 Azure Stack，網際網路部署 | 輸出 NAT | 用於更快傳輸的更佳頻寬。 | 不會加密流量；TOR 外部無隔離或加密。 | 裝載情節，其中租用戶取得其自己的 Azure Stack 部署和 Azure Stack 環境的專屬電路。 例如，ExpressRoute 和多重通訊協定標籤交換 (MPLS)。
| 多租用戶 Azure Stack，網際網路部署 | 站對站 VPN | 從租用戶 VNet 到目的地的流量是安全的。 | 頻寬會受限於站對站 VPN 通道。<br><br>需要虛擬網路上的閘道和目的地網路上的 VPN 裝置。 | 裝載情節，其中的提供者要提供多租用戶雲端，讓租用戶彼此不信任且必須加密流量。
|  |  |  |  |  |

#### <a name="using-expressroute"></a>使用 ExpressRoute

您可以針對單一租用戶內部網路和多租用戶情節，透過 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 將 Azure Stack 連線至 Azure。 您將需要透過[連線提供者](https://docs.microsoft.com/azure/expressroute/expressroute-locations)佈建的 ExpressRoute 電路。

下圖顯示單一租用戶情節的 ExpressRoute (其中「客戶的連線」是 ExpressRoute 電路)。

![顯示單一租用戶 ExpressRoute 情節的圖表](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

下圖顯示多租用戶情節的 ExpressRoute。

![顯示多租用戶 ExpressRoute 情節的圖表](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部監視
若要從您的 Azure Stack 部署和裝置取得所有警示的單一檢視，並針對票證將警示整合至現有的 IT 服務管理工作流程，您可以整合 Azure Stack 與外部的資料中心監視解決方案。

Azure Stack 解決方案隨附的硬體生命週期主機是 Azure Stack 外部的電腦，其執行 OEM 硬體廠商為硬體提供的管理工具。 您可以使用這些工具，或與資料中心內的現有監視解決方案直接整合的其他解決方案。

下表摘要列出目前的可用選項清單。

| 領域 | 外部監視解決方案 |
| -- | -- |
| Azure Stack 軟體 | - [Operations Manager 的 Azure Stack 管理組件](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios 外掛程式](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- REST 型 API 呼叫 | 
| 實體伺服器 (透過 IPMI 的 BMC) | - Operations Manager 廠商管理組件<br>- OEM 硬體廠商提供的解決方案<br>- 硬體廠商 Nagios 外掛程式 | OEM 合作夥伴支援的監視解決方案 (包含) | 
| 網路裝置 (SNMP) | - Operations Manager 網路裝置探索<br>- OEM 硬體廠商提供的解決方案<br>- Nagios 交換器外掛程式 |
| 租用戶訂用帳戶健康情況監視 | - [System Center Management Pack for Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

請注意下列需求：
- 您使用的解決方案必須是無代理程式。 您無法在 Azure Stack 元件內部安裝協力廠商代理程式。 
- 如果您想要使用 System Center Operations Manager，這需要 Operations Manager 2012 R2 或 Operations Manager 2016。

## <a name="backup-and-disaster-recovery"></a>備份和災害復原

規劃備份及災害復原牽涉到規劃這兩個基礎 Azure Stack 基礎結構 (用來裝載 IaaS 虛擬機器和 PaaS 服務)，以及租用戶應用程式和資料。 您必須分開規劃它們。

### <a name="protect-infrastructure-components"></a>保護基礎結構元件

Azure Stack 會將基礎結構元件備份到您指定的共用。

- 您在現有的 Windows 型檔案伺服器或協力廠商裝置上需要有外部 SMB 檔案共用。
- 您應該將這個相同的共用使用於網路交換器與硬體生命週期主機的備份。 OEM 硬體廠商將協助提供這些元件的備份和還原指引，因為這些是 Azure Stack 外部的作業。 您負責根據 OEM 廠商的建議來執行備份工作流程。

如果發生重大資料遺失，您可以使用基礎結構來重新植入部署資料，例如部署輸入和識別碼、服務帳戶、CA 根憑證、同盟的資源 (在中斷連線的部署中)、計劃、優惠、訂用帳戶、配額、RBAC 原則與角色指派，和金鑰保存庫密碼。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>保護 IaaS 虛擬機器上的租用戶應用程式

Azure Stack 不會備份租用戶應用程式和資料。 您必須針對 Azure Stack 的外部目標規劃備份及災害復原保護。 租用戶保護是租用戶驅動的活動。 對於 IaaS 虛擬機器，可在來賓技術中使用租用戶來保護檔案資料夾、應用程式資料和系統狀態。 不過，作為企業或服務提供者，您可能想要在相同資料中心或雲端外部提供備份和復原解決方案。

若要備份 Linux 或 Windows IaaS 虛擬機器，您必須使用含來賓作業系統存取權的備份產品來保護檔案、資料夾、作業系統狀態和應用程式資料。 您可以使用 Azure 備份，System Center Data Center Protection Manager 或支援的協力廠商產品。

若要將資料複寫到次要位置並協調應用程式在發生災難時容錯移轉，您可以使用 Azure Site Recovery 或支援的協力廠商產品。 (在初始版本的整合系統中，Azure Site Recovery 不會支援容錯回復。 不過，您可以透過手動程序完成容錯回復。)此外，支援原生複寫 (如 Microsoft SQL Server) 的應用程式可將資料複寫到正在執行應用程式的另一個位置。

> [!IMPORTANT]
> 在初始版本的整合系統中，我們將支援在 IaaS 虛擬機器的來賓層級上工作的保護技術。 您無法在基礎的基礎結構伺服器上安裝代理程式。

## <a name="next-steps"></a>後續步驟

- 如需使用案例、購買、合作夥伴和 OEM 硬體廠商的詳細資訊，請參閱 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 產品頁面。
- 如需 Azure Stack 整合系統的藍圖和地理可用性的詳細資訊，請參閱白皮書：[Azure Stack：Azure 的延伸模組](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
