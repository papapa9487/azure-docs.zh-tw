---
title: "如何將 Azure API 管理與虛擬網路搭配使用"
description: "了解如何在「Azure API 管理」中設定虛擬網路連線，然後透過它存取 Web 服務。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 69fd656ee9ae440d5769aca82ff8d49fb59d7780
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何將 Azure API 管理與虛擬網路搭配使用
「Azure 虛擬網路」(VNET) 可讓您將任何 Azure 資源，放在您控制存取權的非網際網路可路由網路中。 然後，可以使用各種 VPN 技術，將這些網路連線到您的內部部署網路。 若要深入了解「Azure 虛擬網路」，請從以下資訊著手：[Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

Azure API 管理可以部署在虛擬網路 (VNET) 內，因此它可以存取網路內的後端服務。 開發人員入口網站與 API 閘道，可設定為從網際網路存取或只從虛擬網路內存取。

> [!NOTE]
> Azure API 管理支援傳統和 Azure Resource Manager Vnet。
>
>

## <a name="enable-vpn"> </a>啟用 VNET 連線
> [!NOTE]
> VNET 連線適用於「進階」和「開發人員」層。 若要在不同的層之間切換，請在「Azure 入口網站」中開啟您的「API 管理」服務，然後開啟 [級別與價格] 索引標籤。 在 [定價層] 區段下選取 [進階] 或 [開發人員] 層，然後按一下 [儲存]。
>

若要啟用 VNET 連線，請在「Azure 入口網站」中開啟您的「API 管理」服務，然後開啟 [虛擬網路] 頁面。

![API 管理的虛擬網路功能表][api-management-using-vnet-menu]

選取所需的存取類型：

* **外部**：可透過外部負載平衡器，從公用網際網路存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

![公用對等互連][api-management-vnet-public]

* **內部**：只能透過內部負載平衡器，從虛擬網路內存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

![私人對等互連][api-management-vnet-private]

您現在會看見佈建 API 管理服務所在的所有區域的清單。 為每個區域選取 VNET 和子網路。 此清單中會同時填入傳統和 Resource Manager 虛擬網路，這些您要設定之區域中所設定 Azure 訂用帳戶可用的虛擬網路。

> [!NOTE]
> 上圖中的「服務端點」包括「閘道/Proxy」、「發行者入口網站」、「開發人員入口網站」、GIT 及「直接管理端點」。
> 上圖中的「管理端點」是裝載在服務上以透過 Azure 入口網站和 Powershell 來管理組態的端點。
> 此外，請注意，即使此圖顯示其各種端點的「IP 位址」，「API 管理」服務仍然「只」會在其已設定的「主機名稱」上回應。

> [!IMPORTANT]
> 將 Azure API 管理執行個體部署至 Resource Manager VNET 時，服務必須在除了 Azure API 管理執行個體之外不包含其他資源的專用子網路中。 如果嘗試將 Azure API 管理執行個體部署到含有其他資源的 Resource Manager VNET 子網路，則部署將會失敗。
>
>

![選取 VPN][api-management-setup-vpn-select]

按一下畫面頂端的 [儲存]。

> [!NOTE]
> 「API 管理」執行個體的 VIP 位址在每次啟用或停用 VNET 時都會變更。  
> 將 API 管理中**外部**移動至**內部**或反之時，也會變更的 VIP 位址
>


> [!IMPORTANT]
> 如果您將「API 管理」從 VNET 中移除或變更其部署所在的 VNET，則先前使用的 VNET 將保持鎖定狀態最長達 4 小時。 在這段期間，將無法刪除該 VNET 或在其中部署新的資源。

## <a name="enable-vnet-powershell"> </a>使用 PowerShell cmdlet 來啟用 VNET 連線
您也可以使用 PowerShell cmdlet 啟用 VNET 連線能力

* **在 VNET 內建立 API 管理服務**：使用 cmdlet [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) 在 VNET 內建立 Azure API 管理服務。

* **在 VNET 內部署現有 API 管理服務**：使用 cmdlet [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) 移動虛擬網路內的現有 Azure API 管理服務。

## <a name="connect-vnet"> </a>連接到裝載於虛擬網路內的 Web 服務
在您的「API 管理」服務連接到 VNET 之後，存取 VNET 內的後端服務與存取公用服務便沒有差別。 只要在建立新 API 或編輯現有 API 時，於 [Web 服務 URL] 欄位中輸入您 Web 服務的本機 IP 位址或主機名稱 (如果為 VNET 設定了 DNS 伺服器) 即可。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>常見的網路組態問題
以下是將 API 管理服務部署到虛擬網路時可能發生的常見錯誤設定問題清單。

* **自訂 DNS 伺服器安裝**：API 管理服務相依於數個 Azure 服務。 當「API 管理」是裝載於具有自訂 DNS 伺服器的 VNET 中時，它必須要解析這些 Azure 服務的主機名稱。 請遵循 [這份](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 有關自訂 DNS 設定的指引。 請參閱下方的連接埠資料表和參考的其他網路需求。

> [!IMPORTANT]
> 如果您針對 VNET 使用「自訂 DNS 伺服器」，建議您在將 API 管理服務部署到該伺服器**之前**，先將該伺服器設定妥當。 否則，每次變更 DNS 伺服器時，您都需要執行[套用網路設定作業](https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementservice#ApiManagementService_ApplyNetworkConfigurationUpdates)來更新 API 管理服務

* **API 管理所需的連接埠**︰使用[網路安全性群組][Network Security Group]可以控制到 API 管理部署於其中的子網路之輸入和輸出流量。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。 搭配 VNET 使用 API 管理時，封鎖這其中一或多個連接埠是另一個常見的錯誤組態問題。

當 API 管理服務執行個體裝載於 VNET 時，會使用下表中的連接埠。

| 來源 / 目的地連接埠 | 方向 | 傳輸通訊協定 | 目的 | 來源 / 目的地 | 存取類型 |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |輸入 |TCP |與 API 管理的用戶端通訊 |INTERNET / VIRTUAL_NETWORK |外部 |
| * / 3443 |輸入 |TCP |Azure 入口網站和 PowerShell 的管理端點 |INTERNET / VIRTUAL_NETWORK |外部和內部 |
| * / 80, 443 |輸出 |TCP |與「Azure 儲存體」和「Azure 服務匯流排」的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| * / 1433 |輸出 |TCP |與 Azure SQL 的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| * / 11000 - 11999 |輸出 |TCP |與 Azure SQL V12 的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| * / 14000 - 14999 |輸出 |TCP |與 Azure SQL V12 的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| * / 5671 |輸出 |AMQP |「記錄到事件中樞」原則和監視代理程式的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| 6381 - 6383 / 6381 - 6383 |輸入和輸出 |UDP |與「Redis 快取」的相依性 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |外部和內部 |-
| * / 445 |輸出 |TCP |與「適用於 GIT 的 Azure 檔案共用」的相依性 |VIRTUAL_NETWORK / INTERNET |外部和內部 |
| * / * | 輸入 |TCP |Azure 基礎結構負載平衡器 | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK |外部和內部 |

* **SSL 功能**︰若要啟用 SSL 憑證鏈結建立和驗證，API 管理服務需要 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 的輸出網路連線。 如果您上傳至 API 管理的任何憑證包含 CA 根的完整鏈結，則不需要此相依性。

* **DNS 存取**：需要有連接埠 53 的輸出存取，才能與 DNS 伺服器通訊。 如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從裝載 API 管理的子網路連接該 DNS 伺服器。

* **計量和健康情況監視**︰對於解析為屬於下列網域之 Azure 監視端點的輸出網路連線能力︰global.metrics.nsatc.net、shoebox2.metrics.nsatc.net、prod3.metrics.nsatc.net。

* **快速路由安裝**：常見的客戶組態是定義其專屬預設路由 (0.0.0.0/0)，以強制輸出網際網路流量來替代透過內部部署方式流動。 此流量流程一定會中斷與 Azure API 管理的連線，因為已在內部部署封鎖輸出流量，或者 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。 解決方法是在子網路上定義包含 Azure API 管理的一 (或多個) 使用者定義路由 ([UDR][UDRs])。 UDR 會定義將使用的子網路特有路由，而非預設路由。
  如果可能，建議使用下列設定：
 * ExpressRoute 組態會通告 0.0.0.0/0 而且預設會使用強制通道將所有輸出流量傳送至內部部署。
 * 套用至包含 Azure API 管理的子網路之 UDR 會使用網際網路的下一個躍點類型定義 0.0.0.0/0。
 這些步驟的合併效果是子網路層級 UDR 會優先於 ExpressRoute 強制通道，因而確保來自 Azure API 管理的輸出網際網路存取。

>[!WARNING]  
>**未正確交叉通告從公用對等互連路徑至私人對等互連路徑之路由**的 ExpressRoute 組態不支援 Azure API 管理。 已設定公用對等互連的 ExpressRoute 組態，會收到來自 Microsoft 的一大組 Microsoft Azure IP 位址範圍的路由通告。 如果這些位址範圍在私人對等互連路徑上不正確地交叉通告，最後的結果會是來自 Azure API 管理執行個體子網路的所有輸出網路封包，都會不正確地使用強制通道傳送至客戶的內部部署網路基礎結構。 這個網路流量會中斷 Azure API 管理。 此問題的解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。


## <a name="troubleshooting"></a>疑難排解
對您的網路進行變更時，請參閱 [NetworkStatus API](https://docs.microsoft.com/en-us/rest/api/apimanagement/networkstatus)，以驗證 API 管理服務是否未遺失相依之任何關鍵資源的存取權。 連線狀態應該每隔 15 分鐘更新一次。

## <a name="limitations"> </a>限制
* 包含「API 管理」執行個體的子網路無法包含任何其他 Azure 資源類型。
* 子網路和「API 管理」服務必須位於同一個訂用帳戶中。
* 包含「API 管理」執行個體的子網路無法跨訂用帳戶移動。
* 使用內部虛擬網路時，只有 [RFC 1918](https://tools.ietf.org/html/rfc1918) 中所述範圍的內部 IP 位址可用，無法提供公用 IP 位址。
* 針對已設定內部虛擬網路的多區域「API 管理」部署，使用者需負責管理自己的負載平衡，因為他們是 DNS 的擁有者。


## <a name="related-content"> </a>相關內容
* [使用 VPN 閘道將虛擬網路連線到後端](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [從不同的部署模型連接虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md

