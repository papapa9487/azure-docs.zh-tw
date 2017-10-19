---
title: "Azure 網路安全性概觀 | Microsoft Docs"
description: "深入了解用於控制 Azure 資源之間網路流量流程的安全性選項。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 98559cbb0acab91c4b2c30c6d0129e955eef85f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="network-security"></a>網路安全性

您可以使用網路安全性群組，將網路流量限制為虛擬網路中的資源。 網路安全性群組包含一些安全性規則，可根據來源或目的地 IP 位址、連接埠和通訊協定允許或拒絕輸入或輸出網路流量。 

## <a name="network-security-groups"></a>網路安全性群組

每個網路介面都有零個或一個相關聯的網路安全性群組。 每個網路介面都存在於[虛擬網路](virtual-networks-overview.md)子網路中。 子網路也可以有零個或一個相關聯的網路安全性群組。 

若套用至子網路，則安全性規則會套用至子網路中的所有資源。 除了網路介面，您可能具有其他 Azure 服務的執行個體，例如 HDInsight、虛擬機器擴展集，以及在子網路中部署的應用程式服務環境。

當網路安全性群組與網路介面和網路介面所在子網路相關聯時，網路安全性群組的套用方式如下所示：

- **輸入流量**：系統會先評估與網路介面所在子網路相關聯的網路安全性群組。 再由與網路介面相關聯的網路安全性群組，評估經由與子網路相關聯的網路安全性群組允許的任何流量。 例如，您可能需要從網際網路透過連接埠 80 對虛擬機器進行輸入存取。 如果您將網路安全性群組與網路介面和網路介面所在子網路產生關聯，則與子網路和網路介面相關聯的網路安全性群組必須允許連接埠 80。 如果您經由與子網路或子網路所在網路介面相關聯的網路安全性群組只允許連接埠 80，則通訊會因為預設安全性規則而失敗。 如需詳細資訊，請參閱[預設安全性規則](#default-security-rules)。 如果您只將網路安全性群組套用至子網路或網路介面，而且此網路安全性群組含有允許輸入連接埠 80 流量 (舉例來說) 的規則，則通訊會成功。 
- **輸出流量**：系統會先評估與網路介面相關聯的網路安全性群組。 再由與子網路相關聯的網路安全性群組，評估經由與網路介面相關聯的網路安全性群組允許的任何流量。

您可能永遠不會注意網路安全性群組何時同時套用至網路介面和子網路。 藉由檢視網路介面的[有效安全性規則](virtual-network-manage-nsg-arm-portal.md)，可以輕鬆地檢視套用至網路介面的彙總規則。 您也可以使用 Azure 網路監看員中的 [IP 流量確認](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能來判斷是否允許網路介面的雙向通訊。 此工具會告訴您是否允許通訊，以及哪個網路安全性規則允許或拒絕流量。
 
> [!NOTE]
> 網路安全性群組會與子網路或與部署傳統部署模型的虛擬機器和雲端服務相關聯，而不是與 Resource Manager 部署模型中的網路介面相關聯。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

相同的網路安全性群組可以套用您所選的多個個別網路介面和子網路。

## <a name="security-rules"></a>安全性規則

視 Azure 訂用帳戶[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)而定，網路安全性群組可包含零個或多個規則。 每個規則都會指定下列屬性：

|屬性  |說明  |
|---------|---------|
|名稱|網路安全性群組中的唯一名稱。|
|優先順序 | 100 和 4096 之間的數字。 系統會依照優先權順序處理規則，較低的數字會在較高的數字之前處理，因為較低的數字具有較高的優先順序。 一旦流量符合規則，處理就會停止。 因此，如果存在較低優先順序 (較高數字) 的規則具有與較高優先順序之規則相同的屬性，則不會進行處理。|
|來源或目的地| 任何或個別的 IP 位址、CIDR 區塊 (例如 10.0.0.0/24)、服務標籤或應用程式安全性群組。 深入了解[服務標籤](#service-tags)和[應用程式安全性群組](#application-security-groups)。 指定範圍、服務標籤或應用程式安全性群組，可讓您建立較少的安全性規則。 在規則中指定多個個別 IP 位址和範圍 (您無法指定多個服務標籤或應用程式群組) 的功能在預覽版本中，也稱為增強型安全性規則。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立的網路安全性群組中指定多個 IP 位址與 IP 位址範圍。|
|通訊協定     | TCP、UDP 或 [任何]，其中包括 TCP、 UDP 和 ICMP。 您無法單獨指定 ICMP，如果您需要 ICMP，您必須使用 [任何]。 |
|方向| 規則是否套用至輸入或輸出流量。|
|連接埠範圍     |您可以指定個別連接埠或連接埠範圍。 例如，您可以指定 80 或 10000-10005。 指定範圍可讓您建立較少的安全性規則。 在規則中指定多個個別連接埠和連接埠範圍的功能在預覽版本中，也稱為增強型安全性規則。 使用增強型安全性規則之前，請先閱讀[預覽功能](#preview-features)以取得重要資訊。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立之網路安全性群組的相同安全性規則中指定多個連接埠與連接埠範圍。   |
|動作     | 允許或拒絕        |

**考量**

- **主機節點的虛擬 IP：** 基本的基礎結構服務，例如 DHCP、DNS 和健康情況監控是透過虛擬化主機 IP 位址 168.63.129.16 和 169.254.169.254 所提供。 這些公用 IP 位址屬於 Microsoft，而且是針對此目的唯一用於所有地區的虛擬 IP。 IP 位址對應至伺服器電腦的實體 IP 位址 (主機節點)，該伺服器用來主控虛擬機器。 主機節點的作用如同 DHCP 轉送、DNS 遞迴解析程式，以及負載平衡器健康狀態探查和電腦健康狀態探查的探查來源。 這些 IP 位址的通訊並不是攻擊。 如果您封鎖這些 IP 位址的流量，虛擬機器可能無法正常運作。
- **授權 (金鑰管理服務)**：必須授權在虛擬機器中執行的 Windows 映像。 若要確保授權，授權要求會傳送至處理此類查詢的金鑰管理服務主機伺服器。 此要求是透過連接埠 1688 輸出。
- **負載平衡集區中的虛擬機器**：套用的來源連接埠和位址範圍是來自原始電腦，而不是負載平衡器。 目的地連接埠和位址範圍屬於目的地電腦，而不是負載平衡器。
- **Azure 服務執行個體**：虛擬網路子網路中會部署數個 Azure 服務的執行個體，例如 HDInsight、應用程式服務環境及虛擬機器擴展集。 將網路安全性群組套用至部署資源的子網路之前，請先確定您熟悉每個服務的連接埠需求。 如果您拒絕服務所需要的連接埠，服務就無法正常運作。 

安全性規則具有狀態。 如果您將輸出安全性規則指定至任何透過連接埠 80 (舉例來說) 的位址，則不必為了回應輸出流量來指定輸入安全性規則。 如果在外部起始通訊，您只需要指定輸入安全性規則。 反之亦然。 如果允許透過連接埠傳送輸入流量，則不必指定輸出安全性規則來透過連接埠回應流量。 若要了解建立安全性規則時的相關限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="augmented-security-rules"></a>增強型安全性規則

增強型規則可簡化虛擬網路的安全性定義，讓您定義更大且複雜的網路安全性原則 (但規則比較少)。 您可以將多個連接埠、多個明確 IP 位址、服務標籤和應用程式安全性群組，合併成易於了解的單一安全性規則。 在規則的來源、目的地和連接埠欄位中使用增強型規則。 建立規則時，您可以指定多個明確的 IP 位址、CIDR 範圍和連接埠。 若要簡化安全性規則定義的維護，請合併增強型安全性規則與服務標籤或應用程式安全性群組。 

增強型安全性規則已在預覽版本中提供。 若要了解建立增強型安全性規則時的相關限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 預覽狀態的功能並沒有與一般版本中的功能相同層級的可用性和可靠性。 這些功能只在下列地區提供：美國東部、美國西部、美國西部 2、美國西部、澳大利亞東部、澳大利亞東南部和英國南部。

## <a name="default-security-rules"></a>預設安全性規則

如果網路安全性群組並未與子網路或網路介面相關聯，則允許所有流量輸入到或輸出自子網路或網路介面。 一旦建立網路安全性群組，Azure 就會在網路安全性群組中建立下列預設規則：

### <a name="inbound"></a>輸入

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|優先順序|來源|來源連接埠|目的地|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|全部|允許|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|優先順序|來源|來源連接埠|目的地|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|全部|允許|

#### <a name="denyallinbound"></a>DenyAllInbound

|優先順序|來源|來源連接埠|目的地|目的地連接埠|通訊協定|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|全部|拒絕|

### <a name="outbound"></a>輸出

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|優先順序|來源|來源連接埠| 目的地 | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 全部 | 允許 |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|優先順序|來源|來源連接埠| 目的地 | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 全部 | 允許 |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|優先順序|來源|來源連接埠| 目的地 | 目的地連接埠 | 通訊協定 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 全部 | 拒絕 |

在 [來源] 和 [目的地] 欄中，*VirtualNetwork*、*AzureLoadBalancer*和 *Internet* 都是[服務標籤](#tags)，而不是 IP 位址。 在 [通訊協定] 欄中，[全部] 包含 TCP、 UDP 和 ICMP。 建立規則時，您可以指定 TCP、UDP 或 [全部]，但不能單獨指定 ICMP。 因此，如果您的規則需要 ICMP，您必須針對通訊協定選取 [所有]。 [來源] 和 [目的地] 欄中的 *0.0.0.0/0* 代表所有位址。
 
您無法移除預設規則，但可以建立較高優先順序的規則來覆寫預設規則。

## <a name="service-tags"></a>服務標籤

 服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，或指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 下列服務標籤可使用於安全性規則定義中。 其在 [Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)之間的名稱稍有不同。

* **VirtualNetwork** (Resource Manager) (適用於傳統部署的 VIRTUAL_NETWORK*)：這個標籤包含虛擬網路位址空間 (針對虛擬網路定義的所有 CIDR 範圍)、所有已連線的內部部署位址空間，以及[對等互連](virtual-network-peering-overview.md)的虛擬網路或已連線至[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的虛擬網路。
* **AzureLoadBalancer** (Resource Manager) (適用於傳統部署的 **AZURE_LOADBALANCER**)：這個標籤代表 Azure 基礎結構的負載平衡器。 此標籤會轉譯成作為 Azure 健康情況探查來源的 [Azure 資料中心 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)。 如果您未使用 Azure 負載平衡器，則可以覆寫此規則。
* **Internet** (Resource Manager) (適用於傳統部署的 **INTERNET**)：這個標籤代表虛擬網路以外且可以透過公用網際網路進行存取的 IP 位址空間。 此位址範圍也包括 [Azure 擁有的公用 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。
* **AzureTrafficManager** (僅限 Resource Manager)：這個標籤代表 Azure 流量管理員服務的 IP 位址空間。
* **Storage** (僅限 Resource Manager)：這個標籤代表 Azure 儲存體服務的 IP 位址空間。 如果您指定 *Storage* 值，則會允許或拒絕儲存體的流量。 如果您只想要允許存取特定[地區](https://azure.microsoft.com/regions)中的儲存體，您可以指定地區。 例如，如果您只想要允許存取美國東部地區的 Azure 儲存體，您可以指定 *Storage.EastUS* 作為服務標籤。 其他可用的地區性服務標籤：Storage.AustraliaEast、Storage.AustraliaSoutheast、Storage.EastUS、Storage.UKSouth、Storage.WestCentralUS、Storage.WestUS 和 Storage.WestUS2。 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。
* **Sql** (僅限 Resource Manager)：這個標籤代表 Azure SQL Database 和 Azure SQL 資料倉儲服務的位址前置詞。 您只可以為此服務標籤指定特定地區。 例如，如果您只想要允許存取美國東部地區的 Azure SQL Database，您可以指定 *Sql.EastUS* 作為服務標籤。 您無法針對所有 Azure 地區指定 Sql，您必須個別指定地區。 有其他區域服務標籤可用：Sql.AustraliaEast、Sql.AustraliaSoutheast、Sql.EastUS、Sql.UKSouth、Sql.WestCentralUS、Sql.WestUS 和 Sql.WestUS2。 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 Azure SQL Database 服務，但不代表特定的 Azure SQL Database。

> [!WARNING]
> AzureTrafficManager、Storage 和 Sql 服務標籤已在預覽版本中提供。 預覽狀態的功能並沒有與一般版本中的功能相同層級的可用性和可靠性。 這些服務標籤只在下列地區提供：美國東部、美國西部、美國西部 2、美國西部、澳大利亞東部、澳大利亞東南部和英國南部。

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作虛擬網路服務端點，Azure 會將路由新增至服務的虛擬網路子網路。 路由的位址前置詞為相同的位址前置詞，或 CIDR 範圍，以及對應的服務標籤。

## <a name="application-security-groups"></a>應用程式安全性群組

應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 這項功能可讓您大規模重複使用您的安全性原則，而不需進行明確 IP 位址的手動維護。 此平台可處理明確 IP 位址和多個規則集的複雜性，讓您專注於商務邏輯。

您可以指定應用程式安全性群組作為安全性規則中的來源和目的地。 定義安全性原則後，您可以建立虛擬機器，並將虛擬機器中的網路介面指派給應用程式安全性群組。 此原則會根據虛擬機器內每個網路介面的應用程式安全性群組成員資格來套用。 下列範例說明如何對您訂用帳戶中的所有 Web 伺服器使用應用程式安全性群組：

1. 建立名為 *WebServers* 的應用程式的安全性群組。
2. 建立名為 *MyNSG* 的網路安全性群組。
3. 在網路安全性群組中建立輸入安全性規則，指定來源位址的 *Internet* 服務標籤並指定 *WebServers* 應用程式安全性群組作為目的地位址，以及允許連接埠 80 和 443。
4. 部署執行 Web 伺服器應用程式的虛擬機器。 指定虛擬機器中的網路介面是 *WebServers* 應用程式安全性群組的成員。 然後允許連接埠 80 和 443 連至虛擬機器。 此外，也允許這些連接埠連至您建立並成為 *WebServers* 應用程式安全性群組成員的任何後續 Web 伺服器。 

如果您建立其他規則，並指定其他應用程式安全性群組作為目的地，則這些規則並不會套用到 Web 伺服器 (在前一個範例中)。 用於指定應用程式安全性群組的規則，只會套用至屬於此應用程式安全性群組成員的網路介面。 結合增強型安全性規則和服務標籤的應用程式安全性群組，有可能建立最少的網路安全性群組來管理您訂用帳戶的網路安全性。
 
若要了解建立應用程式安全性群組以及在安全性規則中指定它們時的相關限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

應用程式安全性群組已在預覽版本中提供。 使用應用程式安全性群組之前，您必須完成[建立具有應用程式安全性群組的網路安全性群組](create-network-security-group-preview.md#powershell)中的步驟 1-5 才註冊使用它們，並閱讀[預覽功能](#preview-features)中的重要資訊。 在預覽期間，應用程式安全性群組受限於虛擬網路的範圍。 與網路安全性群組中應用程式安全性群組之交叉參考對等互連的虛擬網路不適用。 

預覽狀態的功能並沒有與一般版本中的功能相同層級的可用性和可靠性。 使用應用程式安全性群組之前，您必須先註冊要使用它們。 這些功能只在下列地區中提供：美國中西部。

## <a name="next-steps"></a>後續步驟

* 完成[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)教學課程
* 完成[建立具有應用程式安全性群組的網路安全性群組](create-network-security-group-preview.md)教學課程
