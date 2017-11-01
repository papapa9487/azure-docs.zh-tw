---
title: "使用 Azure Site Recovery 來設計用於災害復原的網路基礎結構 | Microsoft Docs"
description: "這篇文章討論 Azure Site Recovery 的網路設計考量"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: pratshar
ms.openlocfilehash: 5b6fb7bac852b29663866e99758241bd5a7ab59e
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>設計用於災害復原的網路

本文探討使用 [Azure Site Recovery](site-recovery-overview.md) 來進行災害復原 (從內部部署環境復原到 Azure 或次要內部部署站台) 時的網路考量。 本文著重於在容錯移轉至次要位置之後，如何定義 IP 位址範圍和子網路。

## <a name="overview"></a>概觀

Site Recovery 是一項 Microsoft Azure 服務，可協調虛擬化應用程式的保護和復原，以達到提供商務持續性和災害復原 (BCDR) 的目的。

在連線全年無休不中斷的世界裡，讓業務基礎結構和應用程式啟動並正常運作相當重要。 BCDR 的目的是要還原失敗的元件，讓組織可以快速恢復正常營運。 制定災害復原策略來因應不太可能發生的事件非常具有挑戰性。 這是因為預測未來的事件 (特別是未必會發生的事件) 原本就相當困難。 此外，也因為維護防範深遠災難事件的適當保護措施需要付出相當高的成本。

BCDR 規劃的關鍵在於，必須在您的 BCDR 計畫中定義「復原時間目標」(RTO) 和「復原點目標」(RPO)。 當資料中心發生災害時，您可以讓其位於次要資料中心或 Microsoft Azure 的複寫虛擬機器迅速 (低 RTO) 上線，而將資料的遺失降至最低 (低 RPO)。

Site Recovery 可讓容錯移轉得以實現，它會先將指定的虛擬機器從主要資料中心複製到次要資料中心或 Azure (視案例而定)，然後定期更新複本。 規劃基礎結構時，應將網路設計視為潛在瓶頸，可能會讓您無法達成公司的 RTO 和 RPO 目標。  

當系統管理員打算要部署災害復原解決方案時，腦中的其中一個重要問題是，如何在容錯移轉完成後與虛擬機器連線。 Site Recovery 可讓系統管理員選擇在容錯移轉後虛擬機器所要連線的網路。 如果主要站台是 Azure 或是由 VMM 伺服器管理的內部部署站台，則可以使用網路對應來達到此目的。 深入了解 [Azure 至 Azure DR 中的網路對應](site-recovery-network-mapping-azure-to-azure.md)和[從 VMM 的網路對應](site-recovery-network-mapping.md)


設計復原網站的網路時，系統管理員有兩種選擇：

* 在復原網站的網路使用不同的 IP 位址範圍。 在此案例中，虛擬機器在容錯移轉之後會收到新的 IP 位址，系統管理員必須進行 DNS 更新。 
* 在復原網站的網路使用相同的 IP 位址範圍。 在某些案例中，即使容錯移轉之後，系統管理員偏好保留他們的 IP 位址給主要網站。 在正常案例中，系統管理員必須更新路由以指出 IP 位址的新位置。 但是，有些案例在主要和復原網站之間有部署延伸的子網路，保留虛擬機器的 IP 位址會變成一個不錯的選擇。 無法從內部部署網路將子網路延展到 Azure 網路，也無法在兩個 Azure 網路之間延展子網路。  

當系統管理員打算要部署災害復原解決方案時，腦中的其中一個重要問題是，如何在容錯移轉完成後與應用程式連線。 現代應用程式幾乎一律在某種程度上依賴網路，因此實際將某個網站中的服務移至另一個網站，是一種網站挑戰。 在災害復原解決方案中，解決這個問題有兩種主要方法。 第一種方法是要維持固定 IP 位址。 儘管移動服務和主控伺服器開始於不同的實體位置，但是應用程式會將 IP 位址組態連同它們帶至新位置。 第二種方法則是在轉換為復原網站期間完全變更 IP 位址。 每一種方法有數個實作變化，摘要說明如下。

設計復原網站的網路時，系統管理員有兩種選擇：

## <a name="option-1-retain-ip-addresses"></a>選項 1：保留 IP 位址
從災害復原程序的觀點來看，使用固定的 IP 位址似乎是最簡單的實作方法，但由於它有一些潛在的挑戰，因此反而使得它變成實際上最不常用的方法。 Azure Site Recovery 可讓您保留所有案例中的 IP 位址。 決定要保留 IP 之前，應該詳加考量它將加諸於容錯移轉功能的約束。 讓我們看一下可協助您決定是否保留的 IP 位址的因素。 可以透過兩種方式來達成，使用延伸的子網路，或執行完整的子網路容錯移轉。

### <a name="stretched-subnet"></a>延伸的子網路
若採取此做法，可在主要位置和 DR 位置同時使用子網路。 簡單地說，這表示您可以將伺服器和其 IP (第 3 層) 組態移至第二個網站，而且網路會自動將流量路由傳送至新位置。 從伺服器的觀點來看，這是微不足道的處理，但它有幾項挑戰：

* 從第 2 層 (資料連結層) 的觀點來看，它需要可以管理延伸之 VLAN 的網路設備，不過這已不成問題，因為現在已普遍使用這類設備。 第二個且更困難的問題是，透過延伸 VLAN，潛在的容錯網域會延伸到兩個網站，基本上成為單一失敗點。 雖然不太可能，過去曾經發生過廣播風暴但無法隔離。 關於這最後一個問題，我們得到各種混雜的意見和看到許多成功的實作，也有遇到「我們不會在此實作這種技術」的案例。
* 如果您使用 Microsoft Azure 做為 DR 網站，就不可能實作延伸的子網路。

### <a name="subnet-failover"></a>子網路容錯移轉
可以實作子網路容錯移轉，以獲得延伸子網路解決方案的上述優點，而無需真的跨多個網站延伸子網路。 若採取此做法，任何給定的子網路會出現在網站 1 或網站 2 中，但永遠不會同時出現在這兩個網站。 為了在發生容錯移轉時維持 IP 位址空間，可以程式設計方式排列路由器基礎結構，將子網路從某個網站移到另一個網站。 在容錯移轉案例中，子網路會和與其相關聯的受保護 VM 一起移動。 這個方法的主要缺點在於，如果發生失敗，您將必須移動整個子網路。 這可能並不困難，但卻可能影響容錯移轉的細微程度。

讓我們看看虛構企業 Contoso 如何能夠將其 VM 複寫到復原位置，同時又能夠容錯移轉整個子網路。 首先，讓我們看看 Contoso 如何能夠既管理其子網路，同時又在兩個內部部署位置之間複寫 VM，然後，我們將探討當 [Azure 作為災害復原站台](#failover-to-azure)時，子網路容錯移轉如何運作。

#### <a name="fail-over-from-on-premises-to-azure"></a>從內部部署環境容錯移轉至 Azure 
Azure Site Recovery 可讓 Azure 作為您虛擬機器的災害復原站台。  

讓我們看看一個案例，在這個案例中，虛構公司 Woodgrove Bank 讓內部部署基礎結構裝載其企業營運系統應用程式，其行動應用程式則裝載在 Azure 上。 Azure 中的 Woodgrove Bank VM 以及內部部署伺服器之間的連線能力，由站對站 (S2S) 虛擬私人網路 (VPN) 或 ExpressRoute 提供。 站對站 VPN 使得 Woodgrove Bank 在 Azure 中的虛擬網路可被視為 Woodgrove Bank 內部部署網路的延伸。 這個通訊可藉由 Woodgrove Bank 邊緣與 Azure 虛擬網路之間的站對站 VPN 實現。 現在，Woodgrove 想要使用 Site Recovery 將執行主要 Azure 區域的工作負載複寫到另一個 Azure 區域。 此做法符合 Woodgrove 的需求，既擁有經濟實惠的 DR 選項，又能夠將資料儲存在公用雲端環境。 Woodgrove 必須處理相依於硬式編碼 IP 位址的應用程式和組態，因此在容錯移轉至 Azure 中的另一個區域之後，需要保留其應用程式的 IP 位址。

Woodgrove 決定將來自 IP 位址範圍 (172.16.1.0/24, 172.16.2.0/24) 的 IP 位址指派給其在 Azure 中執行的資源。

為了讓 Woodgrove 能夠將其虛擬機器複寫至 Azure 並同時保留 IP 位址，需要建立 Azure 虛擬網路。 此網路應該是內部部署網路的延伸，這樣應用程式才可以順暢地從內部部署站台容錯移轉至 Azure。 Azure 可讓您將站對站以及點對站 VPN 連線能力加入在 Azure 中建立的虛擬網路。 當設定站對站連線時，只在 IP 位址範圍不同於內部部署 IP 位址範圍時，Azure 網路才可讓您將流量路由傳送到內部部署位置 (Azure 將它稱為本機網路)，因為 Azure 不支援延伸子網路。  這表示，如果您有子網路 192.168.1.0/24 內部部署，則您無法在 Azure 網路中加入本機網路 192.168.1.0/24。 這是預期行為，因為 Azure 不知道子網路中沒有任何作用中的 VM，也不知道子網路是針對 DR 用途而建立的。 若要能夠從 Azure 網路正確地路由傳送網路流量，網路和本機網路中的子網路不得發生衝突。

![子網路容錯移轉之前](./media/site-recovery-network-design/network-design7.png)

容錯移轉之前

為了協助 Woodgrove 滿足他們的業務需求，我們必須實作下列工作流程：

* 建立額外的網路，我們叫它「復原網路」，容錯移轉的虛擬機器將在這裡建立。
* 為了確保 VM 的 IP 會在容錯移轉之後保留，請移至於 VM 屬性下方的 [設定] 索引標籤，指定 VM 在內部部署使用的相同 IP，然後按一下 [儲存]。 當 VM 容錯移轉時，Azure Site Recovery 將指派前述 IP 給虛擬機器。

![網路屬性](./media/site-recovery-network-design/network-design8.png)

一旦觸發容錯移轉，並在復原網路中以想要的 IP 位址建立虛擬機器後，可以使用 [Vnet 對 Vnet 連接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)建立與此網路的連接。 如果需要，此動作可以編寫指令碼。  如同我們在關於子網路容錯移轉的前一節所討論，如果容錯移轉至 Azure，路由也須適當地修改，以反映該 192.168.1.0/24 現在已移至 Azure。

![子網路容錯移轉之後](./media/site-recovery-network-design/network-design9.png)

容錯移轉之後

如果您沒有如上圖所示的「Azure 網路」。 您可以在容錯移轉之後，在「主要站台」與「復原網路」之間建立站對站 VPN 連線。  


#### <a name="fail-over-to-a-secondary-on-premises-site"></a>容錯移轉至次要內部部署站台
讓我們看看一個案例，在此案例中，我們想要保留每個 VM 的 IP，並將整個子網路一起容錯移轉。 主要站台有在子網路 192.168.1.0/24 中執行的應用程式。 當容錯移轉發生時，屬於此子網路中的所有虛擬機器將會容錯移轉至復原網站，並保留其 IP 位址 必須適當地修改路由，以反映所有屬於子網路 192.168.1.0/24 的虛擬機器現在都已移至復原網站的事實。

下圖中，主要網站與復原網站、第三個網站與主要網站、以及第三個網站與復原網站之間的路由都必須適當地修改。

下圖顯示容錯移轉之前的子網路。 子網路 192.168.0.1/24 在容錯移轉之前於主要網站上為作用中，在容錯移轉之後於復原網站變成作用中

![容錯移轉之前](./media/site-recovery-network-design/network-design2.png)

容錯移轉之前

下圖顯示容錯移轉之後的網路和子網路。

![容錯移轉之後](./media/site-recovery-network-design/network-design3.png)

容錯移轉之後

如果您的次要站台是內部部署站台，而且您使用 VMM 伺服器來管理它，則為特定虛擬機器啟用保護時，Site Recovery 會根據以下工作流程配置網路資源：

* Site Recovery 會從相關網路上針對每個 System Center VMM 執行個體定義的靜態 IP 位址集區，為虛擬機器上每個網路介面配置 IP 位址。
* 如果系統管理員為復原站台上網路定義的 IP 位址集區，與主要站台上網路的 IP 位址集區相同，則在為複本虛擬機器配置 IP 位址時，Site Recovery 會配置與主要虛擬機器相同的 IP 位址。  此 IP 位址會保留在 VMM 中，但不會設定為 Hyper-V 主機上的容錯移轉 IP 位址。 Hyper-V 主機上的容錯移轉 IP 位址會在容錯移轉之前設定。


如果沒有相同的 IP 可供使用，Site Recovery 就會從已定義的 IP 位址集區中配置一些其他可用的 IP 位址。

在啟用 VM 保護之後，您可以使用下列範例指令碼，來確認已配置給虛擬機器的 IP。 相同的 IP 會設定為容錯移轉 IP，並在容錯移轉時指派給 VM：

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> 在虛擬機器使用 DHCP 的案例中，IP 位址的管理完全在 Site Recovery 的控制範圍外。 系統管理員必須確保負責`提供復原網站上 IP 位址的 DHCP 伺服器，可以從與主要網站相同的範圍中提供位址。
>
>



## <a name="option-2-changing-ip-addresses"></a>選項 2：變更 IP 位址
根據我們所見，這種方法似乎最普遍。 採取的做法是變更容錯移轉中每個 VM 的 IP 位址。 此方法的缺點是連入網路需要「學習」原本在 IPx 的應用程式現在在 IPy。 即使 IPx 和 IPy 是邏輯名稱，通常也必須針對整個網路中的 DNS 項目進行變更或排清，以及針對網路資料表中的已快取項目進行更新或排清，因此，視 DNS 基礎結構的設定方式而定，將會有停機時間。 這些問題有相應的緩和之道，亦即針對內部網路應用程式的情況，可以使用低 TTL 值，針對網際網路架構應用程式，則可使用 [Azure 流量管理員搭配 Site Recovery](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) \(英文\)

### <a name="changing-the-ip-addresses---illustration"></a>變更 IP 位址 - 圖
讓我們看一下在主要和復原網站使用不同 IP 的案例。 在以下範例中，我們有第三個網站，可以從中存取主要或復原網站上裝載的應用程式。

![不同的 IP - 容錯移轉之前](./media/site-recovery-network-design/network-design10.png)


在上圖中，有些應用程式裝載在主要網站上的子網路 192.168.1.0/24 中，而且它們已設定為在容錯移轉後出現在子網路 172.16.1.0/24 的復原網站上。 已適當地設定 VPN 連線/網路路由，讓所有的三個站台可以互相存取。

如下圖所示，在容錯移轉一或多個應用程式之後，它們會在復原子網路中還原。 在此情況下，我們不會受限於只能同時容錯移轉整個子網路。 不需要進行任何變更來重新設定 VPN 或網路路由。 容錯移轉和某些 DNS 更新會確定應用程式可供存取。 如果 DNS 設定為允許動態更新，則虛擬機器一旦在容錯移轉之後啟動，就會使用新的 IP 自行註冊。

![不同的 IP - 容錯移轉之後](./media/site-recovery-network-design/network-design11.png)


在容錯移轉之後，複本虛擬機器具有的 IP 位址可能與主要虛擬機器的 IP 位址不同。 虛擬機器在啟動後將更新他們正在使用的 DNS 伺服器。 在整個網路中，DNS 項目通常需要變更或排清，而且網路資料表中的快取項目必須更新或排清，因此這些狀態變更發生時面臨停機時間，不是罕見的情況。 這個問題的緩和方式包括：

* 針對內部網路應用程式使用低 TTL 值。
* 針對網際網路架構應用程式使用「Azure 流量管理員」搭配 Site Recovery。
* 在您的復原計劃內使用下列指令碼來更新 DNS 伺服器，以確保及時更新 (如果已設定動態 DNS 登錄，不需要指令碼)

        param(
        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--disaster-recovery-to-azure"></a>變更 IP 位址 - 發生災害時復原至 Azure
[作為災害復原站台之 Microsoft Azure 的網路基礎結構設定](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) \(英文\) 部落格文章說明在不一定要保留 IP 位址時，如何設定所需的 Azure 網路基礎結構。 此文章是從應用程式開始說明，接著探討如何在內部部署環境及 Azure 上設定網路，最後再以如何執行測試容錯移轉和計劃性容錯移轉做總結。

## <a name="next-steps"></a>後續步驟
深入了解[網路對應](site-recovery-network-mapping.md)。
