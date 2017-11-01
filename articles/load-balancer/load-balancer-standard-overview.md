---
title: "Azure Load Balancer Standard 概觀 | Microsoft Docs"
description: "Azure Load Balancer Standard 的功能概觀"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 08e4e22ae7e5d6f6efad458b4240a6d57090e865
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer Standard 概觀 (預覽)

Azure Load Balancer Standard SKU 和 Public IP Standard SKU 搭配使用，可讓您建置極為靈活可靠的架構。 使用 Load Balancer Standard 的應用程式將可利用新功能。 所有 TCP 和 UDP 應用程式的數百萬個流程都可享有低延遲、高輸送量及彈性規模的好處。

>[!NOTE]
> Load Balancer Standard SKU 目前為預覽版。 在預覽階段，功能可能沒有與正式運作版功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 針對您的生產環境服務，請使用正式運作版的 [Load Balancer Basic SKU](load-balancer-overview.md)。 與這個預覽版關聯的功能、[可用性區域](https://aka.ms/availabilityzones)及 [HA 連接埠](https://aka.ms/haports)目前需要個別註冊。 除了 Load Balancer [Standard 預覽版](#preview-sign-up)之外，請依照個別指示註冊這些功能。

## <a name="why-use-load-balancer-standard"></a>為什麼要使用 Load Balancer Standard？

您可以將 Load Balancer Standard 用於整個虛擬資料中心範圍。 從小規模部署到既大型又複雜的多區域架構，都可以使用 Load Balancer Standard 來利用下列功能：

- 使用 Load Balancer Standard 可達到[企業級規模](#enterprisescale)。 此功能可以與虛擬網路內的任何虛擬機器 (VM) 執行個體搭配使用，最多可達 1,000 個 VM 執行個體。

- [新的診斷見解](#diagnosticinsights)可協助您了解、管理虛擬資料中心的這個重要元件，以及對其進行疑難排解。 使用「Azure 監視器」(預覽版) 來顯示、篩選用於連續資料路徑健康情況測量的新多維度計量並加以分組。 監視您的資料，從前端到 VM、端點健康情況探查 (了解是否有 TCP 連線嘗試)，再到連出連線。

- [網路安全性群組](#nsg)現在對於與 Load Balancer Standard SKU 或 Public IP Standard SKU 關聯的所有 VM 執行個體來說都是必要的。 網路安全性群組 (NSG) 可為您的案例提供增強的安全性。

- [高可用性 (HA) 連接埠可提供高可靠性](#highreliability)和彈性規模 (針對網路虛擬設備和其他應用程式案例)。 「HA 連接埠」可將 Azure 內部負載平衡器 (ILB) 前端上所有連接埠的負載平衡至 VM 執行個體集區。

- [連出連線](#outboundconnections)現在使用新的「來源網路位址轉譯」(SNAT) 連接埠配置模型，可提供更大的恢復能力和規模。

- [Load Balancer Standard 搭配可用性區域](#availabilityzones)可用來建構區域備援和區域性架構。 這兩個架構都可包含跨區域負載平衡。 您不需倚賴 DNS 記錄即可達到區域備援。 單一 IP 位址預設就有區域備援。  單一 IP 位址可以連線至跨所有「可用性區域」的某個區域內某個區域網路中的任何 VM。


您可以在公開或內部組態中使用 Load Balancer Standard，以支援下列基本案例：

- 將輸入流量負載平衡至狀況良好的後端執行個體。
- 透過連接埠將輸入流量轉送至單一後端執行個體。
- 將輸出流量從虛擬網路內的私人 IP 位址轉譯成「公用 IP」位址。

### <a name = "enterprisescale"></a>企業級規模

 使用 Load Balancer Standard 來設計您的高效能虛擬資料中心，以及支援任何 TCP 或 UDP 應用程式。 在後端集區中使用獨立 VM 執行個體，或最多可達 1,000 個執行個體的虛擬機器擴展集。 在完全受管理的 Azure 服務上，繼續使用低轉送延遲、高輸送量效能及彈性規模，來處理數百萬個流程。
 
Load Balancer Standard 可以將流量轉送到某個區域內某個虛擬網路中的任何 VM 執行個體。 後端集區大小最大可達 1,000 個執行個體，其中可包含下列 VM 案例的任意組合：

- 沒有可用性設定組的獨立 VM
- 具有可用性設定組的獨立 VM
- 虛擬機器擴展集 (最多可達 1,000 個執行個體)
- 多個虛擬機器擴展集
- VM 與虛擬機器擴展集混合

可用性設定組已不再是必要條件。 您可以選擇使用可用性設定組來獲得其提供的其他好處。

### <a name = "diagnosticinsights"></a>診斷見解

Load Balancer Standard 提供公用和內部 Load Balancer 設定的新多維診斷功能。 這些新計量是透過「Azure 監視器」(預覽版) 提供的，並且會利用所有相關的功能，包括與下游取用者整合的功能。

| 計量 | 說明 |
| --- | --- |
| VIP 可用性 | Load Balancer Standard 會持續運用從區域內到 Load Balancer 前端再一直到支援您 VM 之 SDN 堆疊的資料路徑。 只要狀況良好的執行個體持續存在，測量就會依循與您應用程式的負載平衡流量相同的路徑。 此外，也會驗證您客戶所使用的資料路徑。 此測量對您的應用程式來說是看不見的，也不會干擾到其他作業。|
| DIP 可用性 | Load Balancer Standard 使用分散式健康情況探查服務，可根據您的組態設定監視應用程式端點的健康情況。 這個計量會提供 Load Balancer 集區中每個個別執行個體端點的彙總檢視，或各端點篩選過的檢視。  您可以看到 Load Balancer 藉由健康情況探查設定如何檢視應用程式的健康情況。
| SYN 封包 | Load Balancer Standard 不會終止 TCP 連線，或是與 TCP 或 UDP 封包流程互動。 流程及其交握一律是在來源與 VM 執行個體之間進行。 若要針對您的 TCP 通訊協定案例進行進一步疑難排解，您可使用 SYN 封包來了解有多少個已進行的 TCP 連線嘗試。 此計量會回報已收到的 TCP SYN 封包數。 此計量可能也會反映出嘗試連線到您服務的用戶端。|
| SNAT 連線 | Load Balancer Standard 會回報連至「公用 IP」位址前端的偽裝連出連線數目。 SNAT 連接埠是可耗盡的資源。 此計量可以指出應用程式有多依賴 SNAT 來處理連出的起始連線。|
| 位元組計數器 | Load Balancer Standard 會回報每個前端處理的資料。|
| 封包計數器 | Load Balancer Standard 會回報每個前端處理的封包。|

### <a name = "highreliability"></a>高可靠性

設定負載平衡規則，讓應用程式延展和變得高度可靠。 您可以設定個別連接埠的規則，或使用「HA 連接埠」來平衡所有流量，不論 TCP 或 UDP 連接埠號碼為何。  

您可以使用新的「HA 連接埠」功能來開啟各種案例，包括內部 NVA 的高可用性和彈性規模。 對於那些指定個別連接埠並不實際或不符合需求的其他案例來說，此功能相當實用。 「HA 連接埠」可讓您依所需的數目指定多個執行個體，來為您提供備援和彈性規模。 您的組態不再侷限於主動/被動案例。 健康情況探查設定將流量只轉接到狀況良好的執行個體，以保護您的服務。

NVA 廠商可以為其客戶提供完全受廠商支援、有彈性的案例。 這移除了單一失敗點的缺點，而可支援多個作用中執行個體來提供彈性規模。 您可以視設備的功能而定，將規模調整成兩個或更多個執行個體。 請連絡您的 NVA 廠商以取得這些案例的額外指引。

### <a name = "availabilityzones"></a>可用性區域

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

在支援的區域中使用可用性區域來提升應用程式的恢復功能。 「可用性區域」目前在特定區域中為預覽版，需要額外選擇加入。

### <a name="automatic-zone-redundancy"></a>自動區域備援

您可以選擇 Load Balancer 應該針對每個應用程式提供區域備援前端還是區域性前端。 使用 Load Balancer Standard 來建立區域備援相當簡單。 單一前端 IP 位址會自動具備區域備援。 區域中的所有可用性區域會同時為區域備援前端提供服務。 系統會針對連入和連出連線建立區域備援資料路徑。 區域備援性在 Azure 中不需要多個 IP 位址和 DNS 記錄。 

區域備援適用於公用或內部前端。 您內部 Load Balancer 的「公用 IP」位址和前端私人 IP 都可以具備區域備援。

請使用下列指令碼，為您的內部 Load Balancer 建立區域備援「公用 IP」位址。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

請使用下列指令碼，為您的內部 Load Balancer 建立區域備援前端 IP。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

如果您的「公用 IP」前端是區域備援前端，則從 VM 執行個體進行的連出連線都會自動變成區域備援連線。 此前端會受到保護而不受區域失敗影響。 您的 SNAT 連接埠配置也會不受區域失敗影響。

#### <a name="cross-zone-load-balancing"></a>跨區域負載平衡

跨區域負載平衡可供在後端集區的區域內使用，能夠為您的 VM 執行個體提供最大的彈性。 前端會將流程傳遞給虛擬網路中的任何 VM，不論該 VM 執行個體的「可用性區域」為何。

此外，您也可以為前端和後端執行個體指定特定區域，以讓您的資料路徑和資源與特定區域搭配運作。

虛擬網路和子網路永遠不會受到區域限制。 只要定義一個含有您所需 VM 執行個體的後端集區，您的組態便設定完成。

#### <a name="zonal-deployments"></a>區域性部署

您也可以選擇定義一個區域性前端，以讓負載平衡器前端與特定區域搭配運作。 區域性前端僅由指定的單一「可用性區域」為其提供服務。 當前端與區域性 VM 執行個體結合時，您可以讓資源與特定區域搭配運作。

在特定區域中建立的「公用 IP」位址一律只存在於該區域中。 「公用 IP」位址的區域無法變更。 若要讓「公用 IP」位址能夠連結到多個區域中的資源，請改為建立區域備援「公用 IP」。

請使用下列指令碼，在「可用性區域 1」中建立「公用 IP」位址。 如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

請使用下列指令碼，定義進入「可用性區域 1」的內部 Load Balancer 前端。

如果您在組態中使用現有的 Resource Manager 範本，請將 **sku** 區段新增到這些範本。 此外，也請定義子資源前端 IP 組態中的 **zones** 屬性。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

請將虛擬網路中的 VM 執行個體放到您的後端集區中，來為該集區新增跨區域負載平衡功能。

Load Balancer Standard 資源一律是區域性資源，並且在支援「可用性區域」的情況下也是區域備援資源。 您可以在任何區域中部署「公用 IP」位址，或部署沒有已指派區域的內部 Load Balancer 前端。 對「可用性區域」的支援並不影響部署功能。 如果區域稍後取得「可用性區域」，先前部署的「公用 IP」或內部 Load Balancer 前端將會自動具備區域備援。 區域備援資料路徑並不代表 0% 的封包遺失率。

### <a name = "nsg"></a>網路安全性群組

Load Balancer Standard 和 Public IP Standard 可在虛擬網路中完整上線運作，其中需要用到「網路安全性群組」(NSG)。 NSG 使您能夠將流量加入白名單中。 您可以使用 NSG 來完全控制傳送到您部署的流量。 不再需要等候其他流量完成。

將 NSG 與後端集區中 VM 執行個體的子網路或網路介面 (NIC) 建立關聯。 請將此組態與 Load Balancer Standard 搭配使用，當作為執行個體層級的「公用 IP」時，則與 Public IP Standard 搭配使用。 NSG 必須將您想要允許的流量明確加入白名單中，才能允許該流量通過。

若要深入了解 NSG 及如何將其套用至您的案例，請參閱[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

### <a name ="outboundconnections"></a>輸出連線

當負載平衡器使用連接埠偽裝 SNAT 時，Load Balancer Standard 會為虛擬網路內的 VM 提供連出連線。 連接埠偽裝 SNAT 演算法可提供更佳的健全性和彈性規模。

當公用 Load Balancer 資源與 VM 執行個體建立關聯時，系統會改寫每個連出連線來源。 來源會從虛擬網路私人 IP 位址空間改寫成負載平衡器的前端「公用 IP」位址。

當連出連線與區域備援前端搭配使用時，這些連線也會具備區域備援，而使得 SNAT 連接埠配置不受區域失敗影響。

Load Balancer Standard 中的新演算法會為每個 VM 的 NIC 預先配置 SNAT 連接埠。 將 NIC 新增到集區時，系統會依據集區大小配置 SNAT 連接埠。 下表說明 6 層後端集區大小的連接埠預先配置：

| 集區大小 (VM 執行個體) | 預先配置的 SNAT 連接埠 |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT 連接埠不會直接轉譯為連出連線數目。 SNAT 連接埠可以重複使用於多個唯一目的地。 如需詳細資料，請檢閱[輸出連線](load-balancer-outbound-connections.md)一文。

如果後端集區大小增加而轉換成較高的層級，系統就會回收半數已配置的連接埠。 與已回收連接埠關聯的連線將會逾時，而必須重新建立連線。 新的連線嘗試則會立即成功。 如果後端集區大小縮減而轉換成較低的層級，可用的 SNAT 連接埠數目就會增加。 在此情況下，現有的連線將不受影響。

Load Balancer Standard 有可依個別規則使用的額外組態選項。 當有多個前端可供使用時，您可以控制要將哪個前端用於連接埠偽裝 SNAT。

當只有 Load Balancer Standard 為 VM 執行個體提供服務時，無法使用連出 SNAT 連線。 您可以一併將 VM 執行個體指派給公用負載平衡器，來明確還原這項功能。 您也可以為每個 VM 執行個體直接指派「公用 IP」作為執行個體層級「公用 IP」。 有些作業系統和應用程式案例可能會需要這個組態選項。 

### <a name="port-forwarding"></a>連接埠轉送

Basic 和 Standard Load Balancer 可讓您設定輸入 NAT 規則，以將前端連接埠對應至個別的後端執行個體。 藉由設定這些規則，您將可以公開「遠端桌面通訊協定」端點和 SSH 端點，或是執行其他應用程式案例。

Load Balancer Standard 會透過輸入 NAT 規則持續提供連接埠轉送功能。 當與區域備援前端搭配使用時，輸入 NAT 規則會具備區域備援，而不受區域失敗影響。

### <a name="multiple-front-ends"></a>多個前端

當應用程式需要公開多個個別的 IP 位址 (例如 TLS 網站或「SQL AlwaysOn 可用性群組」端點) 時，您可以設定多個前端來提供設計彈性。 

Load Balancer Standard 可在您需要在唯一 IP 位址上公開特定應用程式端點的情況下，持續提供多個前端。

如需有關設定多個前端 IP 的詳細資訊，請參閱[多 IP 組態](load-balancer-multivip-overview.md)。

## <a name = "sku"></a>關於 SKU

只有在 Azure Resource Manager 部署模型中，才會提供 SKU。 此預覽版針對 Load Balancer 和「公用 IP」資源導入兩個 SKU：Basic 和 Standard。 這些 SKU 的差異在於功能、效能特性、限制及一些內在行為。 虛擬機器可以與任一 SKU 搭配使用。 對 Load Balancer 與「公用 IP」資源兩者而言，SKU 仍然是選用屬性。 當案例定義中省略 SKU 時，組態預設會使用 Basic SKU。

>[!IMPORTANT]
>資源的 SKU 是不可變動的。 您無法變更現有資源的 SKU。  

### <a name="load-balancer"></a>負載平衡器

[現有的 Load Balancer 資源](load-balancer-overview.md)會變成 Basic SKU，並維持正式運作版狀態而不會改變。

Load Balancer Standard SKU 是新的 SKU且目前為預覽版。 Microsoft.Network/loadBalancers 的 2017 年 8 月 1 日 API 版本在資源定義中新增了 **sku** 屬性：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer Standard 在提供「可用性區域」的區域中會自動具備區域復原能力。 如果已經將 Load Balancer 宣告為區域性，它就不會自動具備區域復原能力。

### <a name="public-ip"></a>公用 IP

[現有的公用 IP 資源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)會變成 Basic SKU，並維持正式運作版狀態且具備其所有功能、效能特性及限制。

Public IP Standard SKU 是新的 SKU且目前為預覽版。 Microsoft.Network/publicIPAddresses 的 2017 年 8 月 1 日 API 版本在資源定義中新增了 **sku** 屬性：

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

不同於 Public IP Basic 提供多個配置方法，Public IP Standard 一律使用靜態配置。

Public IP Standard 在提供「可用性區域」的區域中會自動具備區域復原能力。 如果已經將「公用 IP」宣告為區域性，它就不會自動具備區域復原能力。 區域性「公用 IP」無法從一個區域變更到另一個區域。

## <a name="migration-between-skus"></a>在 SKU 之間移轉

SKU 是不可變動的。 請依照本節中的步驟從一個資源 SKU 移到另一個 SKU。

### <a name="migrate-from-basic-to-standard-sku"></a>從 Basic 移轉到 Standard SKU

1. 視需要針對 Load Balancer 和「公用 IP」建立一個新的 Standard 資源。 重新建立您的規則和探查定義。

2. 從所有 VM 執行個體中移除 Basic SKU 資源 (看 Load Balancer 和「公用 IP」哪一個適用)。 請務必也移除可用性設定組的所有 VM 執行個體。

3. 將所有 VM 執行個體附加至新的 Standard SKU 資源。

### <a name="migrate-from-standard-to-basic-sku"></a>從 Standard 移轉到 Basic SKU

1. 視需要針對 Load Balancer 和「公用 IP」建立一個新的 Basic 資源。 重新建立您的規則和探查定義。 

2. 從所有 VM 執行個體中移除 Standard SKU 資源 (看 Load Balancer 和「公用 IP」哪一個適用)。 請務必也移除可用性設定組的所有 VM 執行個體。

3. 將所有 VM 執行個體附加至新的 Basic SKU 資源。

>[!IMPORTANT]
>
>在 Basic 和 Standard SKU 的使用上有一些限制。
>
>只有在 Standard SKU 中才有提供「HA 連接埠」和 Standard SKU 的「診斷」。 您無法既從 Standard SKU 移轉到 Basic SKU 又同時保留這些功能。
>
>Load Balancer 和 Public IP 資源必須使用相符的 SKU。 您無法將 Basic SKU 資源與 Standard SKU 資源混用。 您無法將 VM、「可用性設定組」中的 VM 或虛擬機器擴展集同時連結到這兩個 SKU。
>

## <a name="region-availability"></a>區域可用性

Load Balancer Standard 目前已在以下地區推出：
- 美國東部 2
- 美國中部
- 北歐
- 美國中西部
- 西歐
- 東南亞

## <a name="sku-service-limits-and-abilities"></a>SKU 服務限制與功能

Azure [網路服務的服務限制](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits)適用於每個區域和每個訂閱。 

下表比較 Load Balancer Basic 與 Standard SKU 的限制和功能：

| 負載平衡器 | 基本 | 標準 |
| --- | --- | --- |
| 後端集區大小 | 最多 100 個 | 最多 1,000 個 |
| 後端集區界限 | 可用性設定組 | 虛擬網路、區域 |
| 後端集區設計 | 「可用性設定組」中的 VM 、「可用性設定組」中的虛擬機器擴展集 | 虛擬網路中的任何 VM 執行個體 |
| HA 連接埠 | 不支援 | 可用 |
| 診斷 | 有限，僅限公用 | 可用 |
| VIP 可用性  | 不支援 | 可用 |
| 快速 IP 行動性 | 不支援 | 可用 |
|可用性區域案例 | 僅限區域性 | 區域性、區域備援、跨區域負載平衡 |
| 輸出 SNAT 演算法 | 隨選 | 預先配置 |
| 輸出 SNAT 前端選取項目 | 無法設定、多個候選項目 | 選用設定以減少候選項目 |
| 網路安全性群組 | 選用 NIC/子網路 | 必要 |

下表比較 Public IP Basic 與 Standard SKU 的限制和功能：

| 公用 IP | 基本 | 標準 |
| --- | --- | --- |
| 可用性區域案例 | 僅限區域性 | 區域備援 (預設)，區域性 (選用) | 
| 快速 IP 行動性 | 不支援 | 可用 |
| VIP 可用性 | 不支援 | 可用 |
| Counters | 不支援 | 可用 |
| 網路安全性群組 | 選用 NIC | 必要 |


## <a name="preview-sign-up"></a>註冊預覽

若要參與 Load Balancer Standard SKU 和所隨附 Public IP Standard SKU 的預覽，請註冊您的訂用帳戶。  註冊訂用帳戶將可讓您從 PowerShell 或 Azure CLI 2.0 存取。 若要註冊，請執行下列步驟：

>[!NOTE]
>註冊 Load Balancer Standard 功能最多可能需要一小時的時間才會全面生效。 如果您想要將 Load Balancer Standard 與[可用性區域](https://aka.ms/availabilityzones)及 [HA 連接埠](https://aka.ms/haports)搭配使用，就必須針對這些預覽版功能個別註冊。 請依照這些功能的個別註冊指示來進行操作。

### <a name="sign-up-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 來進行註冊

1. 向提供者註冊功能：

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. 此作業最多可能需要 10 分鐘的時間才能完成。 您可以使用下列命令來檢查作業狀態：

    ```cli
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```
    
    當功能註冊狀態傳回 'Registered' 時，請繼續進行下一步：
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. 向資源提供者重新註冊您的訂用帳戶，以完成預覽版註冊：

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-by-using-powershell"></a>使用 PowerShell 來進行註冊

1. 向提供者註冊功能：

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. 此作業最多可能需要 10 分鐘的時間才能完成。 您可以使用下列命令來檢查作業狀態：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    當功能註冊狀態傳回 'Registered' 時，請繼續進行下一步：
   
    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. 向資源提供者重新註冊您的訂用帳戶，以完成預覽版註冊：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>價格

Load Balancer Standard SKU 是根據所設定的規則和處理的資料收費。 在預覽版期間不會產生任何費用。 如需詳細資訊，請檢閱 [Load Balancer](https://aka.ms/lbpreviewpricing) 和[公用 IP](https://aka.ms/lbpreviewpippricing)定價頁面。

客戶可繼續享有免費的 Load Balancer Basic SKU。

## <a name="limitations"></a>限制

下列限制適用於預覽版，並且可能變更：

- Load Balancer 後端執行個體目前不能位於對等互連的虛擬網路中。 所有後端執行個體都必須在相同的區域中。
- SKU 是不可變動的。 您無法變更現有資源的 SKU。
- 這兩個 SKU 都可以與獨立 VM、「可用性設定組」中的 VM 執行個體或虛擬機器擴展集搭配使用。 VM 組合無法與這兩個 SKU 同時搭配使用。 不允許使用混用 SKU 的組態。
- 使用內部 Load Balancer Standard 搭配 VM 執行個體 (或「可用性設定組」的任何部分) 會停用[預設 SNAT 連出連線](load-balancer-outbound-connections.md)。 您可以將此功能還原至獨立 VM、「可用性設定組」中的 VM 執行個體，或是虛擬機器擴展集。 您也可以還原此功能以進行連出連線。 若要還原這些功能，請為同一個 VM 執行個體同時指派公用 Load Balancer Standard 或 Public IP Standard 作為執行個體層級的「公用 IP」。 指派完成之後，就會再次提供「公用 IP」位址的連接埠偽裝 SNAT。
- 您可能必須將 VM 執行個體組成可用性設定組，才能達到完整的後端集區規模。 在單一後端集區中，最多可以放置 150 個可用性設定組和獨立 VM。
- 不支援 IPv6。
- 在「可用性區域」的內容中，前端不可從區域性變更為區域備援，反之亦然。 將前端建立為區域備援前端之後，它就會保持為區域備援前端。 將前端建立為區域性前端之後，它就會保持為區域性前端。
- 在「可用性區域」的內容中，區域性「公用 IP」位址無法從一個區域移到另一個區域。


## <a name="next-steps"></a>後續步驟

- 深入了解 [Load Balancer Basic](load-balancer-overview.md)。
- 深入了解[可用性區域](../availability-zones/az-overview.md)。
- 了解 Azure 中的一些其他重要[網路功能](../networking/networking-overview.md)。

