---
title: "Azure 網路安全性最佳作法 |Microsoft Docs"
description: "本文提供使用內建 Azure 功能的一些網路安全性最佳作法。"
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3dee3411dadbca5e88951dec2ed1836d440423c4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="azure-network-security-best-practices"></a>Azure 網路安全性最佳作法
Microsoft Azure 可讓您將虛擬機器和應用裝置放在 Azure 虛擬網路上，進而將它們連接到其他網路裝置。 Azure 虛擬網路是一種建構，可讓您將虛擬網路介面卡連線到虛擬網路，允許有網路功能的裝置之間進行以 TCP/IP 為基礎的通訊。 連接到 Azure 虛擬網路的 Azure 虛擬機器能夠連接到相同 Azure 虛擬網路、不同 Azure 虛擬網路、網際網路或甚至您自己的內部部署網路上的裝置。

本文將討論 Azure 網路安全性最佳作法的集合。 這些最佳作法衍生自我們的 Azure 網路經驗和客戶的經驗。

針對每個最佳作法，我們會說明︰

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這篇「Azure 網路安全性最佳作法」是以共識意見以及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 網路安全性最佳作法包括︰

* 以邏輯方式分割子網路
* 控制路由行為
* 啟用強制通道
* 使用虛擬網路應用裝置
* 部署 DMZ 進行安全性分區
* 避免暴露於具有專用 WAN 連結的網際網路
* 將執行時間和效能最佳化
* 使用全域負載平衡
* 停用 Azure 虛擬機器的 RDP 存取
* 啟用 Azure 資訊安全中心
* 將資料中心擴充至 Azure

## <a name="logically-segment-subnets"></a>以邏輯方式分割子網路
[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)類似於內部部署網路上的 LAN。 Azure 虛擬網路背後的構想是您建立單一私人 IP 位址空間型網路，以將所有 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)置於其上。 可用的私人 IP 位址空間位於類別 A (10.0.0.0/8)、類別 B (172.16.0.0/12) 和類別 C (192.168.0.0/16) 範圍中。

類似於您在內部部署執行的作業，您應該將較大的位址空間分成子網路。 您可以使用 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 型子網路原則來建立子網路。

子網路之間的路由傳送會自動發生，您不需要手動設定路由表。 不過，預設設定是您在 Azure 虛擬網路上建立的子網路之間沒有任何網路存取控制。 若要建立子網路之間的網路存取控制，您必須在子網路之間放置一些項目。

其中一項可用來完成這項工作的是[網路安全性群組](../virtual-network/virtual-networks-nsg.md) (NSG)。 NSG 會簡單具狀態封包檢查裝置，其使用 5 個 Tuple (來源 IP、來源連接埠、目的地 IP、目的地連接埠和第 4 層通訊協定) 的方法來建立網路流量的允許/拒絕規則。 您可以允許或拒絕單一 IP 位址、多個 IP 位址或甚至整個子網路的流量。

將 NSG 用於子網路之間的網路存取控制，可讓您將屬於相同安全性區域或角色的資源置於其本身的子網路中。 例如，簡單的 3 層式應用程式具有 Web 層、應用程式邏輯層和資料庫層。 您可將屬於上述各層的虛擬機器置於其自己的子網路中。 然後您可使用 NSG 來控制子網路之間的流量︰

* Web 層虛擬機器只可以起始對應用程式邏輯機器的連線，而且只可以接受來自網際網路的連線
* 應用程式邏輯虛擬機器只可以起始與資料庫層的連線，而且只可以接受來自 Web 層的連線
* 資料庫層虛擬機器只可以起始與其本身子網路外部項目的連線，而且只可以接受來自應用程式邏輯層的連線

若要深入了解網路安全性群組以及如何使用它們以邏輯方式分割您的 Azure 虛擬網路的詳細資訊，請參閱[什麼是網路安全性群組](../virtual-network/virtual-networks-nsg.md) (NSG) 一文。

## <a name="control-routing-behavior"></a>控制路由行為
當您將虛擬機器置於 Azure 虛擬網路時，您會注意到虛擬機器可以連接到相同 Azure 虛擬網路上的任何其他虛擬機器，即使其他虛擬機器位於不同的子網路。 這種情況的可能原因是預設會啟用一些允許這種通訊類型的系統路由。 這些預設路由可讓相同 Azure 虛擬網路上的虛擬機器彼此起始連線，以及與網際網路連線 (僅適用於網際網路的輸出通訊)。

當預設系統路由適用於許多部署案例時，您有時會想為您的部署自訂路由組態。 這些自訂項目可讓您設定下一個躍點位址以連到特定的目的地。

建議您在部署虛擬網路安全性應用裝置時設定「使用者定義的路由」，我們將在更新版的最佳作法中進行討論。

> [!NOTE]
> 不需要使用者定義的路由，而預設系統路由適用於大部分的情況。
>
>

閱讀[什麼是使用者定義的路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)一文，可讓您進一步了解使用者定義的路由及其設定方式。

## <a name="enable-forced-tunneling"></a>啟用強制通道
若要進一步了解強制通道，最好能了解「分割通道」是什麼。
最常見的分割通道範例出現於 VPN 連線。 想像一下您從旅館房間建立對公司網路的 VPN 連線。 這個連線可讓您存取公司資源，以及通過 VPN 通道的所有公司網路通訊。

當您要連接到網際網路上的資源時，會發生什麼事？ 啟用分割通道時，這些連線會直接連接到網際網路，而不會經過 VPN 通道。 某些安全性專家將此視為潛在的風險，因此建議停用分割通道，而以網際網路為目標和以公司資源為目標的所有連線都經過 VPN 通道。 這麼做的好處是對網際網路的連線會被迫通過公司網路安全性裝置，而如果 VPN 用戶端連接到 VPN 通道之外的網際網路，將不會發生這種情況。

現在讓我們回到 Azure 虛擬網路上的虛擬機器。 Azure 虛擬網路的預設路由可讓虛擬機器起始對網際網路的流量。 這也可以代表安全性風險，這些輸出連線可能會增加虛擬機器的受攻擊面並遭到攻擊者利用。
基於這個理由，當您的 Azure 虛擬網路與內部部署網路之間有跨單位連線時，建議您在虛擬機器上啟用強制通道。 我們稍後會在這份 Azure 網路最佳作法文件中討論跨單位的連線能力。

如果沒有跨單位連線，請務必利用網路安全性群組 (稍早討論) 或 Azure 虛擬網路安全性應用裝置 (接下來討論) 來防止從 Azure 虛擬機器對網際網路的輸出連線。

若要深入了解強制通道及其啟用方式，請參閱[使用 PowerShell 和 Azure Resource Manager 設定強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)一文。

## <a name="use-virtual-network-appliances"></a>使用虛擬網路應用裝置
雖然網路安全性群組和使用者定義路由可以在 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的網路和傳輸層提供特定網路安全性測量，但在某些情況下，您會想要或需要啟用高堆疊層級的安全性。 在這類情況下，建議您部署 Azure 合作夥伴所提供的虛擬網路安全性應用裝置。

Azure 網路安全性應用裝置可透過網路層級控制項所提供的內容來提供大幅增強的安全性層級。 虛擬網路安全性應用裝置所提供的網路安全性功能包括︰

* 防火牆
* 入侵偵測/入侵預防
* 弱點管理
* 應用程式控制
* 以網路為基礎的異常偵測
* Web 篩選
* 防毒
* 殭屍網路防護

如果您需要的網路安全性層級高於您可以利用網路層級存取控制項取得的層級，則建議您調查並部署 Azure 虛擬網路安全性應用裝置。

若要了解有哪些可用的 Azure 虛擬網路安全性應用裝置及其相關功能，請造訪 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 並搜尋「安全性」和「網路安全性」。

## <a name="deploy-dmzs-for-security-zoning"></a>部署 DMZ 進行安全性分區
DMZ 或「周邊網路」是實體或邏輯網路區段，主要用來在您的資產與網際網路之間提供額外的安全性層級。 DMZ 的目的是要將特殊化網路存取控制裝置放在 DMZ 網路的邊緣，只允許所需的流量通過網路安全性裝置而進入您的 Azure 虛擬網路。

DMZ 非常實用，因為您可以將網路存取控制管理、監視、記錄和報告的重點放在位於 Azure 虛擬網路邊緣的裝置。 在此您通常會啟用 DDoS 預防、入侵偵測/入侵預防系統 (IDS/IPS)、防火牆規則和原則、Web 篩選、網路反惡意程式碼等。 網路安全性裝置位於網際網路與您的 Azure 虛擬網路之間，具有兩個網路均適用的介面。

雖然這是 DMZ 的基本設計，但有許多不同的 DMZ 設計，例如背對背式、三閘式、多閘式等等。

建議對所有的高安全性部署，考慮部署 DMZ 以增強您的 Azure 資源的網路安全性層級。

若要深入了解 DMZ 及其在 Azure 中的部署方式，請參閱 [Microsoft Cloud 服務和網路安全性](../best-practices-network-security.md)一文。

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>避免暴露於具有專用 WAN 連結的網際網路
許多組織已選擇混合式 IT 路由。 在混合式 IT 中，有些公司的資訊資產是在 Azure 中，而有些公司則維持在內部部署上。 在許多情況下，服務的某些元件會在 Azure 中執行，而其他元件則維持在內部部署上。

在混合式 IT 案例中，通常會有某種類型的跨單位連線。 此種跨單位連線可讓公司將其內部部署網路連接到 Azure 虛擬網路。 可用的跨單位連線解決方案有兩個︰

* 站對站 VPN
* ExpressRoute

[站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 代表您的內部部署網路和 Azure 虛擬網路之間的虛擬私人連線。 此連線透過網際網路進行，可讓您在您的網路與 Azure 之間的加密連結內的「輸送」資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 使用 [IPsec 通道模式](https://technet.microsoft.com/library/cc786385.aspx)可執行通道加密。

雖然站對站 VPN 是受信任、可靠且已確立的技術，通道中的流量會周遊網際網路。 此外，最大頻寬相對受限於大約 200Mbps。

如果您需要跨單位連線的例外安全性或效能層級，建議您對跨單位連線使用 Azure ExpressRoute。 ExpressRoute 是內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 因為這是電信公司連線，所以您的資料不會透過網際網路傳輸，因此不會暴露於網際網路通訊固有的潛在風險。

若要深入了解 Azure ExpressRoute 的運作方式和部署方式，請閱讀 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)一文。

## <a name="optimize-uptime-and-performance"></a>將執行時間和效能最佳化
機密性、完整性和可用性 (CIA) 三者構成現今最具影響力的安全性模型。 機密性有關於加密隱私權，完整性有關於確保資料不會遭到未經授權的人員變更，而可用性有關於確保經過授權的個人可以存取他們有權存取的資訊。 上述任何一個區域失敗都代表可能破壞安全性。

可用性可被視為與執行時間和效能有關。 如果服務已關閉，便無法存取資訊。 如果效能不佳，以致資料無法使用，我們可將此資料視為無法存取。 因此，從安全性角度來看，我們需要儘可能確保我們的服務有最佳的執行時間和效能。
用來增強可用性和效能的常用且有效的方法是使用負載平衡。 負載平衡是將網路流量分散於服務中各伺服器的方法。 比方說，如果您的服務中有前端 Web 伺服器，您可以使用負載平衡將流量分散於多部前端 Web 伺服器。

此流量分散會提高可用性，因為如果其中一部 Web 伺服器無法使用，負載平衡器將會停止將流量傳送到該伺服器，並將流量重新導向至仍在運作的伺服器。 負載平衡也有助於效能，因為服務要求的處理器、網路和記憶體額外負荷會分散於所有負載平衡的伺服器。

建議您儘可能為您的服務採用適當的負載平衡。 我們將在下列各節中說明適當性：在 Azure 虛擬網路層級中，Azure 提供三個主要的負載平衡選項：

* 以 HTTP 為基礎的負載平衡
* 外部負載平衡
* 內部負載平衡

## <a name="http-based-load-balancing"></a>以 HTTP 為基礎的負載平衡
以 HTTP 為基礎的負載平衡會利用 HTTP 通訊協定的特性決定哪一部伺服器傳送連線。 Azure 有以應用程式閘道名稱為名的 HTTP 負載平衡器。

建議您在下列情況使用 Azure 應用程式閘道︰

* 需要要求來自相同使用者/用戶端工作階段，才能到達相同後端虛擬機器的應用程式。 此情況的範例為：購物車應用程式和網頁郵件伺服器。
* 想要利用應用程式閘道的 [SSL 卸載](https://f5.com/glossary/ssl-offloading)功能，從 SSL 終止負荷釋出 Web 伺服器陣列的應用程式。
* 內容傳遞網路之類的應用程式，需要將在相同的長時間執行 TCP 連接上的多個 HTTP 要求路由/負載平衡到不同的後端伺服器。

若要深入了解 Azure 應用程式閘道的運作方式及其在您的部署中的使用方式，請閱讀[應用程式閘道概觀](../application-gateway/application-gateway-introduction.md)一文。

## <a name="external-load-balancing"></a>外部負載平衡
來自網際網路的連入連線在位於 Azure 虛擬網路中的伺服器之間達到平衡負載時，就會進行外部負載平衡。 Azure 外部負載平衡器可以提供這項功能，建議您在不需要黏性工作階段或 SSL 卸載時使用。

相對於以 HTTP 為基礎的負載平衡，外部負載平衡器會使用 OSI 網路模型的網路和傳輸層資訊來決定哪一部伺服器可平衡連線負載。

只要有[無狀態應用程式](http://whatis.techtarget.com/definition/stateless-app)接受來自網際網路的連入要求時，建議您使用外部負載平衡。

若要深入了解 Azure 外部負載平衡器的運作方式和部署方式，請閱讀[開始使用 PowerShell 在 Resource Manager 中建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="internal-load-balancing"></a>內部負載平衡
內部負載平衡類似於外部負載平衡，使用相同的機制對其背後伺服器的連線進行負載平衡。 唯一的差別在於，在此情況下的負載平衡器會接受來自不在網際網路上的虛擬機器的連線。 在大部分情況下，Azure 虛擬網路上的裝置會起始負載平衡可接受的連線。

建議您將內部負載平衡用於將受益於此功能的案例，例如當您需要對 SQL 伺服器或內部 Web 伺服器的連線進行負載平衡時。

若要深入了解 Azure 內部負載平衡的運作方式和部署方式，請閱讀[開始使用 PowerShell 在 Resource Manager 中建立網際網路面向的負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer)。

## <a name="use-global-load-balancing"></a>使用全域負載平衡
公用雲端運算可部署遍布全球的應用程式，而其元件位於世界各地的資料中心。 因為 Azure 有全域資料中心，在 Microsoft Azure 上才可行。 相對於先前所提的負載平衡技術，全域負載平衡可讓服務即使在整個資料中心可能無法使用時也能使用。

您可以利用 [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)在 Azure 中取得這種類型的全域負載平衡。 流量管理員可以根據使用者的位置，對您服務的連線進行負載平衡。

比方說，如果使用者從 EU 對您的服務提出要求，此連線則會被導向到您位於 EU 資料中心的服務。 這部分的流量管理員全域負載平衡有助於改善效能，因為連接到最近的資料中心比連接到遠處的資料中心還要快。

在可用性方面，全域負載平衡可確保即使整個資料中心變得無法使用，您的服務仍可使用。

比方說，如果 Azure 資料中心因為環境原因或服務中斷 (例如區域網路失敗) 而無法使用，對您的服務的連線會被重新路由傳送至最近的線上資料中心。 運用您可以在流量管理員中建立的 DNS 原則來完成此全域負載平衡。

如果您所開發的雲端解決方案廣泛分散於多個區域且需要最高層級的執行時間，則建議使用流量管理員。

若要了解 Azure 流量管理員及其部署方式的詳細資訊，請參閱[什麼是流量管理員](../traffic-manager/traffic-manager-overview.md)一文。

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>停用 Azure 虛擬機器的 RDP/SSH 存取
使用[遠端桌面通訊協定](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) 和[安全殼層](https://en.wikipedia.org/wiki/Secure_Shell) (SSH) 通訊協定可以連到 Azure 虛擬機器。 這些通訊協定可供從遠端位置管理虛擬機器，而且是資料中心運算的標準。

在網際網路上使用這些通訊協定的潛在安全性問題是，攻擊者可以使用各種[暴力密碼破解](https://en.wikipedia.org/wiki/Brute-force_attack)技術來存取 Azure 虛擬機器。 攻擊者一旦取得存取權，就可以使用您的虛擬機器做為破壞您的 Azure 虛擬網路上其他電腦的啟動點，或甚至攻擊 Azure 之外的網路裝置。

因為這個緣故，建議停用從網際網路對您的 Azure 虛擬機器的直接 RDP 和 SSH 存取。 停用從網際網路的直接 RDP 和 SSH 存取之後，您有其他選項可用來存取這些虛擬機器以便遠端管理︰

* 點對站 VPN
* 站對站 VPN
* ExpressRoute

[點對站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) 是遠端存取 VPN 用戶端/伺服器連線的另一種說法。 點對站 VPN 可讓單一使用者透過網際網路連接到 Azure 虛擬網路。 點對站連線之後，使用者就能夠使用 RDP 或 SSH 連接到位於使用者透過點對站 VPN 連接之 Azure 虛擬網路上的所有虛擬機器。 這是假設使用者已獲得授權存取這些虛擬機器。

點對站 VPN 比直接 RDP 或 SSH 連線更安全，因為使用者必須先經過兩次驗證，才會連接到虛擬機器。 第一次，使用者必須經過驗證 (並獲得授權) 以建立點對站 VPN 連線；第二次，使用者必須經過驗證 (並獲得授權) 以建立 RDP 或 SSH 工作階段。

[站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 透過網際網路將整個網路連接到另一個網路。 您可以使用站台對站 VPN 將內部部署網路連接到 Azure 虛擬網路。 如果您部署站對站 VPN，您的內部部署網路上的使用者就能夠透過站對站 VPN 連線、使用 RDP 或 SSH 通訊協定來連接到您的 Azure 虛擬網路上的虛擬機器，而您不需要允許透過網際網路的直接 RDP 或 SSH 存取。

您也可以使用專用的 WAN 連結，提供類似站對站 VPN 的功能。 主要的差異有 1. 專用的 WAN 連結不會周遊網際網路，2. 專用的 WAN 連結通常比較穩定且效能較好。 Azure 會提供給您 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) 形式的專用 WAN 連結解決方案。

## <a name="enable-azure-security-center"></a>啟用 Azure 資訊安全中心
Azure 資訊安全中心可協助您預防、偵測和回應威脅，並加強提供對 Azure 資源安全性的能見度及控制權。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

Azure 資訊安全中心藉由下列方式來協助您最佳化和監視網路安全性︰

* 提供網路安全性建議
* 監視您的網路安全性組態的狀態
* 對端點和網路層級的網路型威脅發出警示。

強烈建議您在所有的 Azure 部署上啟用 Azure 資訊安全中心。

若要深入了解 Azure 資訊安全中心以及如何為您的部署進行啟用，請閱讀 [Azure 資訊安全中心簡介](../security-center/security-center-intro.md)一文。

## <a name="securely-extend-your-datacenter-into-azure"></a>安全地將資料中心擴充至 Azure
許多企業 IT 組織都希望擴充到雲端，而不是增加其內部部署資料中心。 此擴充代表將現有的 IT 基礎結構延伸至公用雲端。 利用跨單位連線選項，可將您的 Azure 虛擬網路視為內部部署網路基礎結構上的另一個子網路。

不過，必須先解決許多規劃和設計問題。 這點在網路安全性方面格外重要。 查看範例是了解如何著手處理此種設計的最佳方式之一。

Microsoft 已建立[資料中心延伸參考架構圖表](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content)和支援相關資料，協助您了解這類資料中心延伸模組的外觀。 其中的提供範例參考實作，可用來規劃和設計安全的企業資料中心延伸至雲端。 我們建議您檢閱這份文件，以了解安全解決方案的重要元件。

若要深入了解如何安全地將資料中心擴充至 Azure，請觀賞 [Extending Your Datacenter to Microsoft Azure (將資料中心擴充至 Microsoft Azure)](https://www.youtube.com/watch?v=Th1oQQCb2KA) 視訊。
