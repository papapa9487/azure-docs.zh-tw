---
title: "Azure 負載平衡器概觀 | Microsoft Docs"
description: "Azure 負載平衡器功能、架構和實作的概觀。 了解負載平衡器的運作方式，並將其用於雲端。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 617da1cf41db08d319d6fe9fa7bc96b794a0001e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---

# <a name="azure-load-balancer-overview"></a>Azure 負載平衡器概觀

Azure 負載平衡器可為您的應用程式提供高可用性和網路效能。 這是 Layer 4 (TCP、UDP) 負載平衡器，可將連入流量分配到負載平衡集中所定義服務的狀況良好執行個體。

Azure Load Balancer 可以設定為：

* 對虛擬機器的連入網際網路流量進行負載平衡。 這個組態稱為 [網際網路面向的負載平衡](load-balancer-internet-overview.md)。
* 平衡虛擬網路中的虛擬機器之間、雲端服務中的虛擬機器之間，或內部部署電腦與跨單位部署虛擬網路中的虛擬機器之間的流量負載。 這個組態稱為 [內部負載平衡](load-balancer-internal-overview.md)。
* 將外部流量轉送到特定的虛擬機器。

雲端中的所有資源都需要可從網際網路觸及的公用 IP 位址。 Azure 中的雲端基礎結構會針對其資源使用無法路由傳送的 IP 位址。 Azure 會使用具有公用 IP 位址的網路位址轉譯 (NAT) 來與網際網路通訊。

## <a name="azure-deployment-models"></a>Azure 部署模型

請務必了解 Azure 傳統與 Resource Manager [部署模型](../azure-resource-manager/resource-manager-deployment-model.md)之間的差異。 每個模型中設定 Azure Load Balancer 的方式均不同。

### <a name="azure-classic-deployment-model"></a>Azure 傳統部署模型

部署於雲端服務界限內的虛擬機器可群組來使用負載平衡器。 在此模型中，會將公用 IP 位址和完整網域名稱 (FQDN) 指派給雲端服務。 負載平衡器利用雲端服務的公用 IP 位址來進行連接埠轉譯作業，以及進行網路流量的負載平衡。

負載平衡的流量是由端點所定義。 連接埠轉譯端點在公用 IP 位址中指派的公用通訊埠與指派給特定虛擬機器上服務的本機連接埠間具有一對一關聯。 負載平衡端點在公用 IP 位址與指派給雲端服務中虛擬機器上服務的本機連接埠間具有一對多關聯。

![傳統部署模型中的 Azure Load Balancer](./media/load-balancer-overview/asm-lb.png)

圖 1 - 傳統部署模型中的 Azure Load Balancer

在此部署模型中，負載平衡器所使用的公用 IP 位址網域標籤是 \<雲端服務名稱\>.cloudapp.net。 下圖顯示此模型中的 Azure Load Balancer。

### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager 部署模型

在 Resource Manager 部署模型中，不需要建立雲端服務。 負載平衡器是建立來在多個虛擬機器之間明確路由傳送流量。

公用 IP 位址是具有網域標籤 (DNS 名稱) 的個別資源。 公用 IP 位址會與負載平衡器資源相關聯。 負載平衡器規則和傳入 NAT 規則會使用公用 IP 位址做為接收負載平衡網路流量之資源的網際網路端點。

私人或公用 IP 位址會指派給連接至虛擬機器的網路介面資源。 一旦將網路介面加入到負載平衡器的後端 IP 位址集區之後，負載平衡器就能根據建立的負載平衡規則傳送負載平衡的網路流量。

下圖顯示此模型中的 Azure Load Balancer：

![Resource Manager 中的 Azure Load Balancer](./media/load-balancer-overview/arm-lb.png)

圖 2 - Resource Manager 中的 Azure Load Balancer

負載平衡器可以透過以 Resource Manager 為基礎的範本、API 及工具來管理。 若要深入了解 Resource Manager，請參閱 [Resource Manager 概觀](../azure-resource-manager/resource-group-overview.md)。

## <a name="load-balancer-features"></a>負載平衡器功能

* 雜湊型分散

    Azure 負載平衡器會使用雜湊型分散演算法。 預設會使用 5-tuple 的雜湊 (由來源 IP、來源連接埠、目的地 IP、目的地連接埠及通訊協定類型所組成)，以將流量對應至可用的伺服器。 它只在傳輸工作階段「內」  提供綁定。 相同 TCP 或 UDP 工作階段中的封包會被導向至負載平衡端點後面的同一個執行個體。 當用戶端關閉並重新開啟連線或從相同的來源 IP 啟動新的工作階段時，來源連接埠會變更。 這可能會造成流量移至不同資料中心的不同 IP 端點。

    如需詳細資訊，請參閱 [分負載平衡器配模式](load-balancer-distribution-mode.md)。 下圖顯示雜湊型分配：

    ![雜湊型分散](./media/load-balancer-overview/load-balancer-distribution.png)

    圖 3 - 雜湊型分配

* 連接埠轉送

    Azure 負載平衡器可供您控制如何管理輸入通訊。 此通訊包括從網際網路主機、其他雲端服務中的虛擬機器，或虛擬網路起始的流量。 此控制項是以端點 (也稱為輸入端點) 表示。

    輸入端點會在公用連接埠上進行接聽，並將流量轉送至內部連接埠。 您可以對應的內部或外部端點的相同連接埠，或對它們使用不同的連接埠。 例如，當公用端點對應為連接埠 80 時，您可以將 Web 伺服器設定為接聽連接埠 81。 建立公用端點便會觸發建立負載平衡器執行個體。

    使用 Azure 入口網站建立時，入口網站會自動建立虛擬機器的端點，以供遠端桌面通訊協定 (RDP) 和遠端 Windows PowerShell 工作階段流量使用。 您可以使用這些端點透過網際網路從遠端管理虛擬機器。

* 自動重新設定

    當您相應增加或減少執行個體時，Azure 負載平衡器本身會立即重新設定。 例如，當您增加雲端服務中 Web/背景工作角色的執行個體計數，或將額外虛擬機器加入至相同的負載平衡集時，就會進行重新設定。

* 服務監視

    Azure Load Balancer 可探查各種伺服器執行個體的健全狀況。 當探查無法回應時，負載平衡器會停止傳送新的連線至狀況不良的執行個體。 現有的連線不受影響。

    支援的探查類型有三種：

    + **客體代理程式探查 (僅限平台即服務虛擬機器)：**負載平衡器會利用虛擬機器內的客體代理程式。 只有在執行個體處於就緒狀態 (即執行個體不是處於忙碌、回收中、停止中等狀態) 時，客體代理程式才會進行接聽並以 HTTP 200 OK 回應作為回應。 如果代理程式無法以 HTTP 200 OK 回應，負載平衡器就會將執行個體標示為沒有回應，並停止傳送流量到該執行個體。 負載平衡器會繼續 ping 執行個體。 如果客體代理程式以 HTTP 200 回應，則負載平衡器會再次傳送流量到該執行個體。 使用 Web 角色時，您的網站程式碼通常會在不受 Azure 網狀架構或客體代理程式監視的 w3wp.exe 中執行。 這表示不會向客體代理程式回報 w3wp.exe 中的失敗 (如 HTTP 500 回應)，而且負載平衡器不知道要將該執行個體退出循環。
    + **HTTP 自訂探查：** 此探查會覆寫預設 (客體代理程式) 探查。 您可以用來建立自己的自訂邏輯，以判斷角色執行個體的健全狀況。 負載平衡器會定期探查您的端點 (預設為每隔 15 秒)。 如果在逾時期限 (預設值為 31 秒) 內以 TCP ACK 或 HTTP 200 回應，執行個體就會被視為循環中。 這適合用於實作自己的邏輯，從負載平衡器循環中移除執行個體。 例如，您可以將執行個體設定為超過 90% 的 CPU 時傳回非 200 狀態。 對於使用 w3wp.exe 的 Web 角色，您也可自動監視您的網站，因為網站程式碼中的錯誤會將非 200 狀態傳回給探查。
    + **TCP 自訂探查：** 此探查依賴於將 TCP 工作階段成功建立至定義的探查連接埠。

    如需詳細資訊，請參閱 [LoadBalancerProbe 結構描述](https://msdn.microsoft.com/library/azure/jj151530.aspx)。

* 來源 NAT

    從您的服務送至網際網路的所有輸出流量，會使用與連入流量相同的 VIP 位址進行來源 NAT (SNAT)。 SNAT 提供一些重要優勢：

    + 它能夠輕鬆進行服務的升級及災害復原，因為 VIP 可以動態對應到服務的另一個執行個體。
    + 它讓存取控制清單 (ACL) 管理變得更容易。 當服務相應增加、相應減少或重新部署時，根據 VIP 表示的 ACL 不會變更。

    負載平衡器組態支援 UDP 的完全錐形 NAT。 在完全錐形 NAT 中，連接埠允許來自任何外部主機的輸入連線 (以回應輸出要求)。

    對於虛擬機器起始的每個新輸出連線，負載平衡器也會配置輸出連接埠。 外部主機會看到流量具有以虛擬 IP (VIP) 配置的連接埠。 針對需要大量輸出連線的案例，建議使用 [執行個體層級的公用 IP](../virtual-network/virtual-networks-instance-level-public-ip.md) 位址，讓 VM 有專用的輸出 IP 位址可進行 SNAT。 這會降低連接埠耗盡的風險。

    如需更多有關本主題的詳細資訊，請參閱[輸出連線](load-balancer-outbound-connections.md)一文。

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>支援虛擬機器有多個負載平衡的 IP 位址
您可以將多個負載平衡的公用 IP 位址指派給一組虛擬機器。 運用這項功能，您可以在同一組虛擬機器上裝載多個 SSL 網站和/或多個 SQL Server AlwaysOn 可用性群組接聽程式。 如需詳細資訊，請參閱[每一雲端服務有多重 VIP](load-balancer-multivip.md)。

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>限制

負載平衡器後端集區可以包含任何 VM SKU (基本層除外)。

## <a name="next-steps"></a>後續步驟

- 深入了解[網際網路對向負載平衡器](load-balancer-internet-overview.md)

- 深入了解[內部負載平衡器概觀](load-balancer-internal-overview.md)

- 建立[網際網路對向負載平衡器](load-balancer-get-started-internet-portal.md)

- 深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)


