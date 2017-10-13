---
title: "Azure 中的網路安全性概念和需求 | Microsoft Docs"
description: " 本文可讓您更容易了解 Microsoft Azure 必須在網路安全性區域中提供的資訊。 我們提供核心網路安全性概念和需求的基本說明，以及 Azure 必須在所有這些區域中提供之資訊的相關資訊。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: terrylan
ms.openlocfilehash: 27243856d0c6b70c7515b6bde66b99ef6160eb36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security-overview"></a>Azure 網路安全性概觀
Microsoft Azure 包括強大網路基礎結構以支援您的應用程式和服務連線需求。 在 Azure 的資源之間、內部部署與 Azure 託管資源之間，以及到網際網路和 Azure 與來自網際網路和 Azure，可能會有網路連線。

本文的目的是讓您更容易了解 Microsoft Azure 必須在網路安全性區域中提供的資訊。 這裡我們提供核心網路安全性概念和需求的基本說明。 我們也會提供一些資訊，讓您了解 Azure 在這些領域中必須提供的內容，還有多個連結，幫助您深入了解感興趣的領域。

本＜Azure 網路安全性概觀＞一文著重於下列領域︰

* Azure 網路
* 網路存取控制
* 安全遠端存取和跨單位連線
* Availability
* 名稱解析
* DMZ 架構
* 監視與威脅偵測


## <a name="azure-networking"></a>Azure 網路
虛擬機器需要遠端連線。 為了支援該需求，Azure 需要虛擬機器連接到 Azure 虛擬網路。 Azure 虛擬網路是以實體 Azure 網路網狀架構為基礎所建置的邏輯建構。 每個邏輯 Azure 虛擬網路都會與其他所有 Azure 虛擬網路隔離。 這可協助確保其他 Microsoft Azure 客戶無法存取您部署中的網路流量。

深入了解：

* [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>網路存取控制
網路存取控制是指限制與 Azure 虛擬網路內特定裝置或子網路間之連線的動作。 網路存取控制的目標是限制只有已核准的使用者和裝置，才能存取您的虛擬機器和服務。 存取控制是根據您虛擬機器或服務間之連線的允許或拒絕決策。

Azure 支援數種類型的網路存取控制，例如︰

* 網路層控制
* 路由控制和強制通道
* 虛擬網路安全性應用裝置

### <a name="network-layer-control"></a>網路層控制
任何安全部署都需要某種程度的網路存取控制。 網路存取控制的目標是限制虛擬機器只能與必要的系統通訊，並封鎖其他的通訊嘗試。

如果您需要基本網路層級存取控制 (根據 IP 位址和 TCP 或 UDP 通訊協定)，則可以使用網路安全性群組。 網路安全性群組 (NSG) 是基本可設定狀態封包篩選防火牆，並可讓您根據 [5-tuple](https://www.techopedia.com/definition/28190/5-tuple)來控制存取權。 NSG 未提供應用程式層級檢查或已驗證的存取控制。

深入了解：

* [網路安全性群組](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>路由控制和強制通道
控制您 Azure 虛擬網路上路由行為的能力是重大網路安全性和存取控制功能。 如果路由的設定不正確，虛擬機器上所裝載的應用程式和服務可能會連線到未經授權的裝置，包括潛在攻擊者所擁有或操作的系統。

Azure 網路支援自訂您 Azure 虛擬網路上網路流量之路由行為的能力。 這可讓您改變 Azure 虛擬網路中的預設路由表項目。 路由行為的控制可協助您確保來自特定裝置或一組裝置的所有流量，都透過特定位置進入或離開您的虛擬網路。

例如，您在 Azure 虛擬網路上可能會有虛擬網路安全性應用裝置。 您想要確定與 Azure 虛擬網路之間的所有流量都會經過該虛擬安全性應用裝置。 做法是在 Azure 中設定 [使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md) 。

[強制通道](https://www.petri.com/azure-forced-tunneling) 是一種機制，可用來確保不允許您的服務起始與網際網路上裝置的連線。 請注意，這與接受連入連線，然後回應它們不同。 前端 Web 伺服器需要回應來自網際網路主機的要求，因此允許來自網際網路的流量傳入到這些 Web 伺服器，並允許 Web 伺服器回應。

您不想要允許的是前端 Web 伺服器起始傳出要求。 這類要求可能代表安全性風險，因為這些連線可能用來下載惡意程式碼。 即使您希望這些前端伺服器起始到網際網路的傳出要求，您還是可能想要強制它們經過您的內部部署 Web Proxy，讓您可以利用 URL 篩選和記錄。

因此，您可以使用強制通道來避免這個問題。 啟用強制通道時，會強制所有網際網路連線都要通過內部部署閘道。 您可以利用「使用者定義的路由」來設定強制通道。

深入了解：

* [什麼是使用者定義路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>虛擬網路安全性應用裝置
雖然網路安全性群組、使用者定義路由和強制通道提供 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)之網路和傳輸層的安全性層級，但是您有時可能會想要啟用高於網路之層級的安全性。

例如，您的安全性需求可能包括︰

* 允許存取應用程式之前的驗證和授權
* 入侵偵測和入侵回應
* 高層級通訊協定的應用程式層檢查
* URL 篩選
* 網路層級防毒和反惡意程式碼 
* 反 Bot 保護
* 應用程式存取控制
* 額外 DDoS 保護 (高於提供 Azure 網狀架構本身的 DDoS 保護)

您可以使用 Azure 合作夥伴方案，來存取這些增強的網路安全性功能。 您可以造訪 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 並搜尋「安全性」和「網路安全性」，尋找最新的 Azure 合作夥伴網路安全性方案。

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>安全遠端存取和跨單位連線
需要從遠端完成 Azure 資源的安裝、設定和管理。 此外，建議您部署內部部署和 Azure 公用雲端中含有元件的 [混合式 IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) 方案。 這些案例都需要安全遠端存取。

Azure 網路支援下列安全遠端存取案例︰

* 將個別工作站連接到 Azure 虛擬網路
* 使用 VPN 將內部部署網路連接到 Azure 虛擬網路
* 使用專用 WAN 連結將內部部署網路連接到 Azure 虛擬網路
* 彼此連接 Azure 虛擬網路

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>將個別工作站連接到 Azure 虛擬網路
您有時可能會想要讓個別開發人員或操作人員在 Azure 中管理虛擬機器和服務。 例如，您需要存取 Azure 虛擬網路上的虛擬機器，而且您的安全性原則不允許 RDP 或 SSH 遠端存取個別虛擬機器。 在此情況下，您可以使用點對站 VPN 連接。

點對站 VPN 連接使用 [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) 通訊協定，讓您可以設定使用者與 Azure 虛擬網路之間的私用和安全連接。 建立 VPN 連接之後，使用者就可以透過 VPN 連結 RDP 或 SSH 到 Azure 虛擬網路上的任何虛擬機器 (假設可以驗證和授權使用者)。

深入了解：

* [使用 PowerShell 設定虛擬網路的點對站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>使用 VPN 將內部部署網路連接到 Azure 虛擬網路
您可能想要將整個公司網路或其各部分連接到 Azure 虛擬網路。 這常見於公司 [將其內部部署資料中心擴充到 Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)的混合式 IT 案例。 在許多情況下，公司會在 Azure 和內部部署中裝載服務的各個部分 (例如，方案在 Azure 中包括前端 Web 伺服器而在內部部署中包括後端資料庫時)。 這些類型的「跨單位」連線也可更安全地管理 Azure 所在資源，並支援將 Active Directory 網域控制站擴充到 Azure 等案例。

若要這麼做，其中一種方式是使用 [站對站 VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)。 站對站 VPN 與點對站 VPN 之間的差異在於點對站 VPN 將單一裝置連接到 Azure 虛擬網路，而站對站 VPN 將整個公司 (例如內部部署網路) 連接到 Azure 虛擬網路。 Azure 虛擬網路的站對站 VPN 使用高度安全 IPsec 通道模式 VPN 通訊協定。

深入了解：

* [使用 Azure 入口網站建立具有站對站 VPN 連線的 Resource Manager VNet](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [規劃與設計 VPN 閘道](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>使用專用 WAN 連結將內部部署網路連接到 Azure 虛擬網路
點對站和站對站 VPN 連接適用於啟用跨單位連接。 不過，有些組織認為它們有下列缺點︰

* VPN 連接會透過網際網路移動資料；這會公開與透過公用網路移動資料相關之潛在安全性問題的這些連接。 此外，無法保證網際網路連接的可靠性和可用性。
* 針對一些應用程式和用途，Azure 虛擬網路的 VPN 連線的頻寬可能會受限，因為最大頻寬約為 200 Mbps。

需要最高安全性和可用性層級進行其跨單位連接的組織，一般會使用專用 WAN 連結連接到遠端網站。 Azure 可讓您使用可用來將內部部署網路連接到 Azure 虛擬網路的專用 WAN 連結。 這是透過 Azure ExpressRoute 來啟用。

深入了解：

* [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>彼此連接 Azure 虛擬網路
您可以使用多個 Azure 虛擬網路進行部署。 有許多原因可能會這麼做。 其中一個原因可能是要簡化管理；另一個可能是安全性考量。 不論將資源放在不同 Azure 虛擬網路的動機或基本理由為何，您有時可能會想要每個網路上的資源彼此連接。

其中一個選項是透過網際網路「返回」，以將某個 Azure 虛擬網路上的服務連接到另一個 Azure 虛擬網路上的服務。 連接是從某個 Azure 虛擬網路開始，並經過網際網路，然後回到目的地 Azure 虛擬網路。 這個選項會公開任何網際網路通訊固有之安全性問題的連接。

比較好的選擇可能是建立 Azure 虛擬網路到 Azure 虛擬網路站對站 VPN。 這個 Azure 虛擬網路到 Azure 虛擬網路站對站 VPN 使用與上面提到之跨單位站對站 VPN 連接相同的 [IPsec 通道模式](https://technet.microsoft.com/library/cc786385.aspx) 通訊協定。

使用 Azure 虛擬網路到 Azure 虛擬網路的站對站 VPN 時，優點在於透過 Azure 網路網狀架構建立 VPN 連線，而不是透過網際網路連線。 與透過網際網路連接的站對站 VPN 相較之下，這提供額外一層的安全性。

深入了解：

* [使用 Azure Resource Manager 和 PowerShell 來設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Availability
可用性是任何安全性程式的重要元件。 如果您的使用者和系統無法存取它們需要透過網路存取的項目，則可以將服務視為入侵。 Azure 的網路技術支援下列高可用性機制：

* 以 HTTP 為基礎的負載平衡
* 網路層級負載平衡
* 全域負載平衡

負載平衡是一種機制，設計目的是將連接平均分散到多個裝置。 負載平衡的目的如下︰

* 提高可用性 – 將連接負載平衡到多個裝置時，一或多個裝置可能會變成無法使用，而且在其餘線上裝置上執行的服務可以持續提供該服務的內容。
* 提高效能 – 將連接負載平衡到多個裝置時，單一裝置不需要採用處理器叫用。 而是將提供內容的處理和記憶體需求分散到多個裝置。

### <a name="http-based-load-balancing"></a>以 HTTP 為基礎的負載平衡
經常執行 Web 服務的組織想要在這些 Web 服務之前使用 HTTP 負載平衡器，協助確保具有適當的效能和高可用性層級。 相較於傳統網路負載平衡器，HTTP 負載平衡器所進行的負載平衡決策是根據 HTTP 通訊協定的特性，而不是根據網路和傳輸層通訊協定。

為了提供 Web 服務的 HTTP 負載平衡，Azure 提供 Azure 應用程式閘道。 Azure 應用程式閘道支援：

* HTTP 負載平衡 – 負載平衡決策是根據 HTTP 通訊協定的特有特性所進行
* Cookie 工作階段同質性 – 這個功能確定建立到受該負載平衡器保護之其中一部伺服器的連接，在用戶端與伺服器之間會保持不變。 這樣可確保交易的穩定性。
* SSL 卸載 – 使用負載平衡器建立用戶端連接時，會使用 HTTPS (SSL/) 通訊協定來加密用戶端與負載平衡器之間的工作階段。 不過，為了提高效能，您可以選擇讓負載平衡器與受負載平衡器保護之 Web 伺服器間的連接使用 HTTP (未加密) 通訊協定。 這稱為「SSL 卸載」，因為受負載平衡器保護的 Web 伺服器不會有加密的相關處理器負擔，因此應該可以更快速地服務要求。
* URL 內容路由 – 這個功能讓負載平衡器可以根據目標 URL 來決定在何處轉送連線。 這所提供的彈性大於根據 IP 位址進行負載平衡決策的方案。

深入了解：

* [應用程式閘道概觀](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>網路層級負載平衡
相較於 HTTP 負載平衡，網路層級負載平衡可根據 IP 位址和連接埠 (TCP 或 UDP) 號碼進行負載平衡決策。
您可以使用 Azure Load Balancer，獲取 Azure 中網路層級負載平衡的優點。 Azure Load Balancer 的一些重要特性包括︰

* 根據 IP 位址和連接埠號碼的網路層級負載平衡
* 支援任何應用程式層通訊協定
* 負載平衡到 Azure 虛擬機器和雲端服務角色執行個體
* 可以用於網際網路面向 (外部負載平衡) 與非網際網路面向 (內部負載平衡) 應用程式和虛擬機器
* 端點監視，用來判斷是否有任何受負載平衡器保護的服務變成無法使用

深入了解：

* [多個虛擬機器或服務之間的網際網路面向負載平衡器](../load-balancer/load-balancer-internet-overview.md)
* [內部負載平衡器概觀](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>全域負載平衡
有些組織想要盡可能具有最高層級的可用性。 達到這個目的的其中一種方法是將應用程式裝載到全球分散的資料中心。 應用程式裝載在世界各地的資料中心時，整個地緣政治區域可能會變成無法使用，但仍然啟動並執行應用程式。

除了將應用程式裝載在全球分散資料中心的可用性優點之外，您還可以擁有效能優勢。 使用將服務要求導向到最接近提出要求之裝置的資料中心的機制，可以取得這些效能優勢。

全域負載平衡可以提供這兩項優點。 在 Azure 中，使用 Azure 流量管理員，可以獲取全域負載平衡的優點。

深入了解：

* [什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>名稱解析
名稱解析是您在 Azure 中裝載之所有服務的重大功能。 從安全性角度來看，入侵名稱解析功能可能會導致攻擊者將您網站的要求重新導向到攻擊者的網站。 安全名稱解析是所有雲端裝載服務的需求。

您需要解決兩種類型的名稱解析︰

* 內部名稱解析 – Azure 虛擬網路和 (或) 內部部署網路上的服務會使用內部名稱解析。 透過網際網路無法存取用於內部名稱解析的名稱。 為了取得最佳安全性，重要的是外部使用者無法存取您的內部名稱解析結構描述。
* 外部名稱解析 – 內部部署和 Azure 虛擬網路外部的人員和裝置會使用外部名稱解析。 這些是顯示在網際網路並且用來將連接導向到雲端服務的名稱。

對於內部名稱解析，您有兩個選項︰

* Azure 虛擬網路 DNS 伺服器 – 建立新的 Azure 虛擬網路時，會建立 DNS 伺服器。 這個 DNS 伺服器可以解決該 Azure 虛擬網路上的電腦名稱。 這個 DNS 伺服器無法進行設定，並由 Azure 網狀架構管理員管理，因此成為安全的名稱解析方案。
* 攜帶您自己的 DNS 伺服器 – 您可以選擇將您自己選擇的 DNS 伺服器放在 Azure 虛擬網路上。 這個 DNS 伺服器可以是 Active Directory 整合 DNS 伺服器，或 Azure 合作夥伴所提供的專用 DNS 伺服器方案 (可取自 Azure Marketplace)。

深入了解：

* [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
* [管理虛擬網路 (VNet) 所使用的 DNS 伺服器](../virtual-network/virtual-network-manage-network.md#dns-servers)

對於外部 DNS 解析，您有兩個選項︰

* 在內部部署裝載專屬外部 DNS 伺服器
* 使用服務提供者裝載專屬外部 DNS 伺服器

許多大型組織都會在內部部署裝載其專屬 DNS 伺服器。 它們可以這麼做的原因是它們具有這麼做的網路專業知識和全域空間。

在大部分情況下，最好使用服務提供者來裝載您的 DNS 名稱解析服務。 這些服務提供者具有網路專業知識和全域空間，確保名稱解析服務的極高可用性。 可用性是 DNS 服務的不可或缺項目，因為如果您的名稱解析服務失敗，就沒有人可以連接到網際網路面向服務。

Azure 以 Azure DNS 形式提供高可用和高效能外部 DNS 方案。 這個外部名稱解析方案利用全球 Azure DNS 基礎結構。 它可讓您使用其他 Azure 服務所使用的同一組認證、API、工具和帳單，將網域裝載於 Azure。 在 Azure 時，它也會繼承平台內建的增強式安全性控制。

深入了解：

* [Azure DNS 概觀](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ 架構
許多企業組織都使用 DMZ 來區隔其網路，以建立網際網路與其服務之間的緩衝區。 網路的 DMZ 部分視為低安全性區域，而且不會將高價值資產放在該網路區段中。 您通常會看到的網路安全性裝置在 DMZ 區段上具有一個網路介面，並有另一個網路介面連接到具有接受網際網路輸入連線之虛擬機器和服務的網路。

DMZ 設計和 DMZ 部署決策有許多變化，而在決定要使用 DMZ 時，要用哪一個 DMZ 類型乃依據網路安全性需求而定。

深入了解：

* [Microsoft 雲端服務和網路安全性](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>監視與威脅偵測

在這個重要領域中，Azure 的功能可協助您及早偵測、監視，並收集和檢視網路流量。

### <a name="azure-network-watcher"></a>Azure 網路監看員
Azure 網路監看員包含大量功能，可協助您進行疑難排解，還提供一套全新的工具來協助識別安全性問題。

[安全性群組檢視](/network-watcher/network-watcher-security-group-view-overview.md)可為虛擬機器的稽核與安全性合規性提供協助，並可以用來以程式設計方式執行稽核，將由貴組織所定義的基準原則，與您每個 VM 的有效規則進行比較。 這可以協助您識別所有設定漂移。

[封包擷取](/network-watcher/network-watcher-packet-capture-overview.md)可讓您擷取傳送至虛擬機器，或是自虛擬機器傳送的網路流量。 除了讓您收集網路統計資料，以及對應用程式問題進行疑難排解之外，封包擷取在調查網路入侵上，也能提供非常寶貴的協助。 您可以搭配 Azure Functions 使用此功能，來開始針對特定 Azure 警示進行網路擷取。

如需 Azure 網路監看員的詳細資訊，以及如何開始在實驗室中測試部分功能，請參閱 [Azure 網路監看員監視概觀](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure 網路監看員目前仍為公開預覽，因此可能沒有與正式發行版本服務相同層級的可用性和可靠性。 可能不支援特定功能、可能有限制的功能，且可能無法在所有 Azure 位置提供使用。 如需此服務可用性和狀態的最新通知，請查看 [Azure 更新頁面](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Azure 資訊安全中心
資訊安全中心可協助您預防、偵測和回應威脅，並加強對 Azure 資源安全性的能見度及控制權。 它在您的 Azure 訂用帳戶之間提供整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於大量的安全性解決方案。

Azure 資訊安全中心藉由下列方式來協助您最佳化和監視網路安全性︰

* 提供網路安全性建議
* 監視您的網路安全性組態的狀態
* 對端點和網路層級的網路型威脅發出警示。

深入了解：

* [Azure 資訊安全中心簡介](../security-center/security-center-intro.md)


### <a name="logging"></a>記錄
網路層級的記錄是任何網路安全性案例的重要功能。 在 Azure 中，您可以記錄針對網路安全性群組所取得的資訊，來取得網路層級記錄資訊。 使用 NSG 記錄，您可以從下列項目取得資訊︰

* [活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – 這些記錄用來檢視提交至 Azure 訂用帳戶的所有作業。 預設會啟用這些記錄檔，並且可以在 Azure 入口網站內使用。 它們以前稱為「稽核記錄」或「作業記錄」。
* 事件記錄檔 – 這些記錄檔提供套用哪些 NSG 規則的相關資訊。
* 計數器記錄檔 – 這些記錄檔可讓您知道套用每個 NSG 規則以拒絕或允許流量的次數。

您也可以使用 [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)(功能強大的資料視覺化工具) 來檢視和分析這些記錄檔。

深入了解：

* [網路安全性群組 (NSG) 的 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md)
