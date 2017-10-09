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
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 2728e8b1e190b4ecd0635925b96e97775564a2ee
ms.contentlocale: zh-tw
ms.lasthandoff: 09/29/2017

---

# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer Standard 概觀 (預覽)

Azure Load Balancer Standard SKU 和 Public IP Standard SKU 搭配使用，可讓您建置極為靈活可靠的架構。  使用 Load Balancer Standard 的應用程式不但可以利用新功能，還具有低延遲、高輸送量，和為所有 TCP 和 UDP 應用程式的數百萬個流程調整規模的能力。

>[!NOTE]
> Load Balancer Standard SKU 目前為預覽狀態。 在預覽階段，功能可能沒有與正式發行版本功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 為您的生產服務使用正式推出的 [Load Balancer Basic SKU](load-balancer-overview.md)。

## <a name="why-use-load-balancer-standard"></a>為什麼要使用 Load Balancer Standard

使用 Load Balancer Standard，從小規模的部署到大型且複雜的多重區域架構，讓各種大小的虛擬資料中心都能利用其下列功能：

- 使用 Load Balancer Standard 可達到[企業級規模](#enterprisescale)。  它可以搭配在虛擬網路中的任何虛擬機器執行個體，以及多達 1000 個 VM 執行個體。

- [新的診斷見解](#diagnosticinsights)可讓您了解、管理並針對虛擬資料中心的此重要元件進行疑難排解。 使用 Azure 監視器 (預覽) 以顯示、篩選和分組連續資料路徑健全狀況測量的新多維度計量，從前端到 VM、每個端點健全狀態探查、TCP 連線嘗試、輸出連線。

- [網路安全性群組](#nsg)現在對於任何與 Load Balancer 或公開 IP 的 Standard SKU 相關聯的 VM 執行個體來說是不可或缺的，並提供增強式的安全性。

- [連接埠可以提供高可靠性](#highreliability)，並針對網路虛擬設備和其他應用程式案例進行調整。 HA 連接埠可將內部 Load Balancer 前端的所有連接埠負載平衡到 VM 執行個體的集區。

- [輸出連線](#outboundconnections)現在使用新的 SNAT 連接埠配置模型，提供更高的恢復功能和規模。

- [Load Balancer Standard 搭配可用性區域](#availabilityzones)可以用來建構區域備援和區域性架構，同時跨區域負載平衡。 您可以取得區域備援而無須 DNS 記錄相依性：單一 IP 位址就是預設的區域備援，且可觸達所有可用性區域各區域之 VNet 中的任何 VM。


您可以在公開或內部設定中使用 Load Balancer Standard，只要該設定持續支援下列基本案例：

- 負載平衡輸入流量至狀況良好的後端執行個體。
- 連接埠轉接輸入流量至單一後端執行個體。
- 在 VNet 中將輸出流量從私人 IP 位址轉譯至公用 IP 位址。

### <a name = "enterprisescale"></a>企業級規模

 使用 Load Balancer Standard 設計高效能的虛擬資料中心。 您可以使用獨立 VM 執行個體或後端集區中多達 1000 個執行個體虛擬機器擴展集，並支援任何 TCP 或 UDP 應用程式。 透過 Load Balancer Standard，您的應用程式仍可以利用完全受管理的 Azure 服務上的低轉送延遲、高輸送量效能及數百萬個流程調整規模。
 
Load Balancer Standard 可以將流量轉接到區域中 VNet 的任何 VM 執行個體。 後端集區大小可多達 1000 個執行個體，以下為其組合：

- 沒有可用性設定組的獨立 VM
- 具有可用性設定組的獨立 VM
- 具有多達 1000 個執行個體的虛擬機器擴展集
- 多個虛擬機器擴展集
- 混合 VM 和虛擬機器擴展集。

可用性設定組不再是必須，但您可以選擇使用可用性設定組以獲得它們所提供的其他好處。

### <a name = "diagnosticinsights"></a>診斷見解

Load Balancer Standard 提供公用和內部 Load Balancer 設定的新多維診斷功能。 這些新的度量會透過 Azure 監視器 (預覽) 提供，並利用所有相關的功能，包括與各種下游取用者整合的功能。

| 計量 | 說明 |
| --- | --- |
| VIP 可用性 | Load Balancer Standard 持續執行區域內的 Load Balancer 前端資料路徑，最後到 SDN 堆疊以支援您的 VM。 只要執行個體保持良好的狀況，測量就會遵循與您的應用程式負載平衡流量的相同路徑，並驗證您的客戶可能正在使用的資料路徑。 測量對您的應用程式不可見，而且不會影響。|
| DIP 可用性 | Load Balancer Standard 使用分散式的健康情況探查服務，根據您的設定監視應用程式端點的健康情況。  這項計量提供 Load Balancer 集區中每個個別執行個體端點的彙總值，或各端點篩選過的檢視。  您可以看到 Load Balancer 藉由健康情況探查設定如何檢視應用程式的健康情況。
| SYN 封包 | Load Balancer Standard 不會終止 TCP 連線或與 TCP 或 UDP 封包流程互動；流程和其交握總是在來源和 VM 執行個體之間。 若要針對所有的 TCP 通訊協定案例進行進一步疑難排解，您可使用這個計量了解 TCP 連線有多少次嘗試。 此計量會報告 TCP SYN 封包數目，這些封包已被接收，而且可能會反映用戶端嘗試與您的服務建立連線。|
| SNAT 連線 | Load Balancer Standard 報告偽裝的公用 IP 位址前端傳出連線數目。  SNAT 連接埠是可耗盡的資源，而此度量可以指出應用程式有多依賴 SNAT 傳出起始連線。|
| 位元組計數器 | Load Balancer Standard 報告每個前端處理的資料。|
| 封包計數器 | Load Balancer Standard 報告每個前端處理的封包。|

### <a name = "highreliability"></a>高可靠性

設定負載平衡規則，讓應用程式延展和變得高度可靠。  您可以設定個別連接埠的規則，或使用新 HA 連接埠來平衡 TCP 或 UDP 連接埠號碼的所有流量。  

您可以使用新的 HA 連接埠功能解除鎖定各種案例，包括內部網路虛擬設備及其他案例的高可用性和延展性，這對於指定個別的連接埠而言不實際或不必要。 HA 連接埠允許您所需的盡可能多個執行個體，而非限制主動/被動案例，藉此提供備援和規模。 健康情況探查設定將流量只轉接到狀況良好的執行個體，以保護您的服務。

網路虛擬設備廠商可以提供完整廠商支援且有彈性的案例，方法是為其客戶移除單一失敗點，並允許可調整的多個使用中執行個體。 如果您的應用裝置允許這類設定，您可以調整至兩個或多個執行個體。 您的網路虛擬設備廠商必須針對這些案例提供進一步的指引。

### <a name = "availabilityzones"></a>可用性區域

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

在支援的區域中使用可用性區域來提升應用程式的恢復功能。 在特定區域中，可用性區域目前處於預覽狀態，而且需要其他選擇加入。

### <a name="automatic-zone-redundancy"></a>自動區域備援

您可以選擇 Load Balancer 應該針對每個應用程式提供區域備援或區域性前端。  使用 Load Balancer Standard 輕鬆建立區域備援。  單一前端 IP 位址會自動成為區域備援。  區域中的所有可用性區域會同時提供區域備援前端。 這會為輸入及輸出連線建立區域備援資料路徑。 區域備援性在 Azure 中不需要多個 IP 位址和 DNS 記錄。 

此區域備援適用於公用或內部前端。 您的 Public IP 位址以及內部 Load Balancer 前端的私人 IP 都可以是區域備援。

使用下列建立區域備援 Public IP 位址 (將「sku」新增到任何現有的 Resource Manager 範本)：

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

使用下列建立內部 Load Balancer 區域備援前端 IP 設定 (將「sku」新增到任何現有的 Resource Manager 範本)：

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

如果您的 Public IP 前端是區域備援，從 VM 執行個體所做的任何輸出連線都會自動變成區域備援，並防止區域失敗。  您的 SNAT 連接埠配置度過了區域失敗。

#### <a name="cross-zone-load-balancing"></a>跨區域負載平衡

跨區域負載平衡可在後端集區的區域內取得，為您的虛擬機器執行個體提供最大彈性。  前端可以將流程傳遞至 VNet 中的任何 VM，不論 VM 執行個體的可用性區域。

此外，您也可以選擇為前端和後端執行個體指定特定區域，將資料路徑和資源與特定區域對齊。

由於 Vnet 和子網路絕不會限制區域，您只需要使用所需的 VM 執行個體定義後端集區，如此設定就大功告成。

#### <a name="zonal-deployments"></a>區域性部署

或者，您也可以藉由定義區域性前端，將前端對齊到特定區域。  區域性前端僅由指定的單一可用性區域提供，而當與區域性 VM 執行個體合併時，您可以將資源對齊至指定的區域。

使用下列在可用性區域 1 中建立區域性 Public IP 位址 (將「zones」和「sku」新增到任何現有的 Resource Manager 範本)：

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

使用下列將內部 Load Balancer 前端建立至可用性區域 1 (將「sku」新增到任何現有的 Resource Manager 範本，並將「zones」放置前端 IP 設定子資源)：

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

此外，您可以為您的後端使用跨區域負載平衡，方法是將 VNet 中的 VM 執行個體放入後端集區。

在可用性區域支援的位置，Load Balancer Standard 資源本身一直都是區域性和區域備援。 未指派區域的 Public IP 位址或內部 Load Balancer 前端，可以部署在任何區域中，無論可用性區域是否支援。 如果區域稍後取得可用性區域，已部署的 Public IP 或內部 Load Balancer 前端會自動變成區域備援。 區域備援資料路徑不代表 0% 的封包遺失。

### <a name = "nsg"></a>網路安全性群組

Load Balancer Standard 和 Public IP Standard 完全加入 VNet 和網路安全性群組 (NSG) 現在是必要的。 NSG 可讓流量加入白名單，並設定讓客戶完全控制允許其部署的流量透過 NSG 的時間，而不是其他設定完成的時間。

將 NSG 與子網路或後端集區中的 VM 執行個體 NIC相關聯。  當做為執行個體層級 Public IP 使用時，這適用於 Load Balancer Standard 和 Public IP Standard。 您必須明確地將您想要允許透過 NSG 的流量加入白名單，才能讓它進行流動。

若要深入了解網路安全性群組，以及如何將其套用至您的案例，請參閱[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。

### <a name ="outboundconnections"></a>輸出連線

 當使用連接埠偽裝來源網路位址轉譯 (SNAT) 與 Load Balancer 相關聯時，Load Balancer Standard 會為 VM 在 VNet 內中提供輸出連線。

公用 Load Balancer 資源與 VM 執行個體相關聯時，每個輸出連線的來源會從 VNet 的私人 IP 位址空間重寫為 Load Balancer 前端的 Public IP 位址。  Load Balancer Standard 會針對增加的穩定性和規模，使用新的連接埠偽裝來源網路位址轉譯 (SNAT) 演算法。  

此外，當搭配使用區域備援前端時，輸出連線也是區域備援，且 SNAT 連接埠配置會度過區域失敗。

當 SNAT 連接埠根據下列階層新增至集區時，Load Balancer Standard 的新演算法會將其預先配置給每個 VM 的網路介面：

| 後端集區大小 | 預先配置的 SNAT 連接埠 |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

SNAT 連接埠不會直接轉譯為連線數目。 SNAT 連接埠可以重複使用於多個唯一目的地。  如需更多詳細資訊，請檢閱[輸出連線](load-balancer-outbound-connections.md)一文。

如果您的後端集區增加，並從一個大小階層轉換成下一個較大的大小，一半已配置的連接埠會被回收。 與已回收連接埠相關聯的任何連線將會逾時，且需要重新建立連線。 任何新的連線嘗試會立即成功。 如果您的後端集區從一個大小階層減少為下一個較小的大小，SNAT 連接埠會成長，且不會影響現有的連線。

Load Balancer Standard 也有以每個規則為基礎的其他設定選項，可讓客戶在有多個可用的前端時，控制用於連接埠偽裝 SNAT 的前端。

最後，當只有 Load Balancer Standard 提供 VM 執行個體時，輸出 SNAT 連線無法使用。 您可以明確地還原這項功能，方法是也將 VM 執行個體指派到公用 Load Balancer，或是也將 Public IP 為執行個體層級 Public IP 直接指派給每個 VM 執行個體。 這可能需要一些作業系統和應用程式案例。 

### <a name="port-forwarding"></a>連接埠轉送

 Basic 和 Standard Load Balancer 提供了可設定輸入 NAT 規則以對應前端連接埠至個別後端執行個體的功能。  此功能有許多用途，包括公開遠端桌面通訊協定端點、SSH 端點或各種其他應用程式案例。

Load Balancer Standard 會透過輸入 NAT 規則繼續提供連接埠轉接功能。  搭配區域備援前端使用時，輸入 NAT 規則會變成區域備援，而且將能度過區域失敗。

### <a name="multiple-frontends"></a>多個前端

設定多個前端以實現設計彈性，其中應用程序需要公開多個單獨 IP 位址 (例如 TLS 網站或 SQL AlwaysOn 可用性群組端點)。  在 [這裡](load-balancer-multivip-overview.md)可以找到更多詳細資訊。

Load Balancer Standard 會繼續提供多個前端，其中在唯一 IP 位址上需要這端點來公開特定應用程式端點。

 在 [這裡](load-balancer-multivip-overview.md)可以找到更多詳細資訊。

## <a name = "sku"></a>關於 SKU

僅可透過 Azure Resource Manager 部署模型取得 SKU。  此預覽引進了 Load Balancer 和 Public IP 資源的兩個 SKU (Basic 和 Standard)。  SKU 的差異在於能力、效能特性、限制和一些內建函式的行為。 虛擬機器可搭配任一個 SKU 使用。 對於 Load Balancer 與 Public IP 資源，SKU 為選用的屬性，並會在被省略時預設為 Basic。

>[!IMPORTANT]
>資源的 SKU 是不可變動的。  您無法變更現有資源的 SKU。  

### <a name="load-balancer"></a>負載平衡器

[現有的 Load Balancer 資源](load-balancer-overview.md)會變成 Basic SKU，並保持正式推出且不會改變。

Load Balancer Standard SKU 是新的產品，且目前為預覽狀態。 2017-08-01 API 版本的 Microsoft.Network/loadBalancers 會將 SKU 引進資源。

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
在也提供可用性區域的區域中使用時，Load Balancer Standard 會自動區域復原，除非它被宣告為區域性。

### <a name="public-ip"></a>公用 IP

[現有 Public IP 資源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)會變成 Basic SKU，並且保持正式推出，具備所有能力、效能特性和限制。

Public IP Standard SKU 是新的產品，且目前為預覽狀態。 2017-08-01 API 版本的 Microsoft.Network/publicIPAddresses 資源會引進 SKU。

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

不同於 Public IP Basic 提供多個配置方法，Public IP Standard 一定是靜態配置。

在也提供可用性區域的區域中使用時，Public IP Standard 會自動區域復原，除非它被宣告為區域性。

## <a name="migration-between-skus"></a>在 SKU 之間移轉

如果您想要從一個資源 SKU 移到另一個，請遵循下列步驟：

### <a name="migrating-from-basic-to-standard-sku"></a>從 Basic 移轉到 Standard SKU

1. 建立新的 Standard 資源 (Load Balancer 和 Public IP，視需要)，並重新建立規則和探查定義。
2. 從所有 VM 執行個體 (包括可用性設定組的所有執行個體) 移除 Basic SKU 資源 (Public IP 和 LB)。
3. 將所有 VM 執行個體附加至新的 Standard SKU 資源。

### <a name="migrating-from-standard-to-basic-sku"></a>從 Standard 移轉到 Basic SKU：

1. 建立新的 Basic 資源 (Load Balancer 和 Public IP，視需要)，並重新建立規則和探查定義。 
2. 從所有 VM 執行個體 (包括可用性設定組的所有執行個體) 移除 Standard SKU 資源 (Public IP 和 LB)。
3. 將所有 VM 執行個體附加至新的 Basic SKU 資源。

  >[!NOTE]
  >HA 連接埠和 Standard SKU 的診斷僅可在 Standard SKU 中取得。 您無法從 Standard 移轉為 Basic，並保留這項功能。

Load Balancer 和 Public IP 資源必須使用相符的 SKU。  您無法將 Basic 和 Standard SKU 的資源混在一起，或在可用性設定組中附加 VM，或同時使用虛擬機器擴展集。

## <a name="region-availability"></a>區域可用性

Load Balancer Standard 目前已在以下地區推出：
- 美國東部 2
- 美國中部
- 北歐
- 美國中西部
- 西歐
- 東南亞

## <a name="service-limits--abilities-comparison"></a>服務限制與功能比較

Azure [網路服務的服務限制](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits)適用於每個區域和每個訂閱。 

下表提供 Load Balancer Basic 和 Standard SKU 之間的限制與功能比較：

| 負載平衡器 | 基本 | 標準 |
| --- | --- | --- |
| 後端集區大小 | 最多 100 個 | 最多 1000 個 |
| 後端集區界限 | 可用性設定組 | VNet，區域 |
| 後端集區設計 | 可用性設定組的 VM 或可用性設定組的虛擬機器擴展集 | 在 VNet 中的任何 VM 執行個體 |
| HA 連接埠 | 不支援 | 可用 |
| 診斷 | 有限，僅限公用 | 可用 |
| VIP 可用性  | 不支援 | 可用 |
| 快速 IP 行動性 | 不支援 | 可用 |
|可用性區域案例 | 僅限區域性 | 區域性，區域備援，跨區域負載平衡 |
| 輸出 SNAT 演算法 | 隨選 | 預先配置 |
| 輸出 SNAT 前端選取項目 | 無法設定，多個候選項目 | 選用設定以減少候選項目 |
| 網路安全性群組 | 選用 NIC/子網路 | 必要 |

下表提供 Public IP Basic 和 Standard SKU 之間的限制與功能比較：

| 公用 IP | 基本 | 標準 |
| --- | --- | --- |
| 可用性區域案例 | 僅限區域性 | 區域備援 (預設)，區域性 (選用) | 
| 快速 IP 行動性 | 不支援 | 可用 |
| VIP 可用性 | 不支援 | 可用 |
| Counters | 不支援 | 可用 |
| 網路安全性群組 | 選用 NIC | 必要 |


## <a name="preview-sign-up"></a>註冊預覽

若要參加 Load Balancer Standard SKU 和其附屬 Public IP Standard SKU 的預覽，請使用 PowerShell 或 Azure CLI 2.0 來註冊您的訂用帳戶以獲得存取。

- 使用 PowerShell 註冊

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- 使用 Azure CLI 2.0 註冊

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>註冊 Load Balancer Standard 功能最多可能需要一小時的時間。

>[!NOTE]
>如果您想要搭配 Load Balancer 和 Public IP 使用可用性區域，您也必須註冊可用性區域預覽的訂用帳戶。

## <a name="pricing"></a>價格

Load Balancer Standard SKU 是根據設定的規則和處理的資料收費。  預覽期間不會產生任何費用。  如需詳細資訊，請參閱 [Load Balancer](https://aka.ms/lbpreviewpricing) 和 [Public IP](https://aka.ms/lbpreviewpippricing) 定價頁面。

客戶可繼續享有免費的 Load Balancer Basic SKU。

## <a name="limitations"></a>限制

下列限制適用於預覽，且可能會有所變更：

- Load Balancer 後端執行個體目前不能位於對等互連 VNet。 所有後端執行個體必須在相同的區域中。
- SKU 是不可變動的。 您無法變更現有資源的 SKU。
- 您可以透過獨立 VM、可用性設定組或虛擬機器擴展集中的所有 VM 執行個體來使用 Basic SKU 或 Standard SKU。 獨立 VM、可用性設定組中的所有 VM 執行個體或虛擬機器擴展集無法同時使用這兩者。 不允許混用 SKU。
- 使用內部 Load Balancer Standard 搭配 VM 執行個體 (或可用性設定組的任何部分) 會停用[預設 SNAT 輸出連線](load-balancer-outbound-connections.md)。  您可以將這項功能還原至獨立 VM、VM 執行個體可用性設定組或虛擬機器擴展集並進行輸出連線，方法是同時指派 Load Balancer Standard 或 Public IP Standard 為執行個體層級 Public IP 至相同的 VM 執行個體。 一旦完成，連接埠偽裝 SNAT 至 Public IP 位址會再次提供。
- VM 執行個體可能需要分組為可用性設定組來獲得完整的後端集區調整。 可以將多達 150 個可用性設定組和獨立 VM 放到單一的後端集區。
- 不支援 IPv6。
- 在可用性區域的內容中，前端不可從區域性變動為區域備援，反之亦然。 一旦建立為區域備援，則永遠都是區域備援。 一旦建立為區域性，則永遠都是區域性。
- 在可用性區域的內容中，區域性的 Public IP 位址可能不會從一個區域移動到另一個區域。


## <a name="next-steps"></a>後續步驟

- 深入了解 [Basic Load Balancer](load-balancer-overview.md)
- 深入了解[可用性區域](../availability-zones/az-overview.md)
- 深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)


