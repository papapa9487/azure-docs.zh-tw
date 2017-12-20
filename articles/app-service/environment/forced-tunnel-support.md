---
title: "設定 Azure App Service Environment 使用強制通道"
description: "連出流量使用強制通道處理時讓 App Service Environment 可以運作"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>設定 App Service Environment 搭配強制通道

App Service Environment 是 Azure App Service 在客戶之 Azure 虛擬網路執行個體中的部署。 許多客戶將其虛擬網路設定為內部部署網路 (具備 VPN 或 Azure ExpressRoute 連線) 的延伸。 由於公司的策略或其他安全性限制，他們設定路由讓所有輸出流量先在內部部署網路中傳送，之後再傳送至網際網路。 變更虛擬網路的路由，使來自虛擬網路的輸出流量流經 VPN 或 ExpressRoute 連線傳送至內部部署網路，這稱為強制通道處理。 

強制通道處理可能會造成 App Service Environment 的問題。 App Service Environment 有一些外部相依性，列舉於 [App Service Environment 網路架構][network]文件中。 根據預設，App Service Environment 要求所有連出通訊流經使用 App Service Environment 佈建的 VIP。

路由是強制通道處理以及其處理方式的重要關鍵。 在 Azure 虛擬網路中，路由是根據 最長首碼比對 (LPM) 而進行。 如果有多個符合相同 LPM 的路由，則會根據其來源，以下列順序選取路由：

* 使用者定義的路由 (UDR)
* BGP 路由 (使用 ExpressRoute 時)
* 系統路由

若要深入了解虛擬網路中的路由，請閱讀[使用者定義的路由和 IP 轉送][routes]。 

如果您希望 App Service Environment 在強制通道虛擬網路中運作，您有兩個選擇：

* 讓 App Service Environment 具備網際網路直接存取權。
* 變更 App Service Environment 的輸出端點。

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>讓 App Service Environment 具備網際網路直接存取權

若要讓您的 App Service Environment 在虛擬網路是使用 ExpressRoute 連線所設定的情況下運作，您可以：

* 將 ExpressRoute 設定為公告 0.0.0.0/0。 根據預設，它會使用強制通道將所有輸出流量傳送至內部部署網路。
* 建立 UDR。 以位址首碼 0.0.0.0/0 及網際網路的下一個躍點類型，將它套用至包含 App Service Environment 的子網路。

如果您做了這兩個變更，則源自 App Service Environment 子網路且目的地為網際網路的流量，將不會被強制經由 ExpressRoute 連線傳輸，而且 App Service Environment 能夠運作。

> [!IMPORTANT]
> UDR 中定義的路由必須足夠明確，以優先於 ExpressRoute 組態所通告的任何路由。 前面的範例使用廣泛的 0.0.0.0/0 位址範圍。 因此有可能會不小心由使用更明確位址範圍的路由通告所覆寫。
>
> 針對從公用對等互連路徑至私人對等互連路徑的路由進行交叉通告的 ExpressRoute 設定，不支援 App Service Environment。 已設定公用對等互連的 ExpressRoute 設定，會收到來自 Microsoft 的路由通告。 通告中會包含一大組 Microsoft Azure IP 位址範圍。 如果位址範圍在私人對等互連路徑上交叉通告，來自 App Service Environment 子網路的所有連出網路封包都會使用強制通道傳送至客戶的內部部署網路基礎結構。 App Service Environment 目前預設不支援這種網路流量。 此問題的一個解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。 另一個解決方式是讓 App Service Environment 可以在強制通道設定中運作。

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>變更 App Service Environment 的輸出端點 ##

本節描述如何透過變更 App Service Environment 所使用的輸出端點，讓 App Service Environment 在強制通道設定下運作。 如果 App Service Environment 的連出流量經過強制通道送至內部部署網路，則您必須允許該流量來自 App Service EnvironmentE VIP 位址以外的 IP 位址。

App Service Environment 不僅具備外部相依性，還必須接聽連入流量並回應此類流量。 系統無法從另一個位址傳回回覆，因為這樣會中斷 TCP。 變更 App Service Environment 的輸出端點需要有三個步驟：

1. 設定路由表，確保連入管理流量可以從相同的 IP 位址傳回。

2. 新增 IP 位址，此 IP 位址將用於 App Service Environment 防火牆的輸出。

3. 將 App Service Environment 連出流量的路由設定為通過通道。

   ![強制通道網路流程][1]

在 App Service Environment 已經啟動且可運作之後，您可以使用不同的輸出位址設定 App Service Environment，或者您可以在 App Service Environment 部署期間進行設定。

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>在 App Service Environment 可運作之後，變更輸出位址 ###
1. 取得用來當作 App Service Environment 輸出 IP 使用的 IP 位址。 如果您使用強制通道處理，則這些位址來自您的 NAT 或閘道 IP。 如果您想要透過 NVA 路由傳送 App Service Environment 連出流量，則輸出位址為 NVA 的公用 IP。

2. 設定 App Service Environment 設定資訊中的輸出位址。 移至 resource.azure.com 並前往 Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>。 接著，您就可以看到描述您 App Service Environment 的 JSON。 確定最上方寫的是「讀取/寫入」。 選取 [編輯]。 向下捲動至底部，並將 **userWhitelistedIpRanges** 值從 **null** 變更為類似以下這樣。 使用您要設為輸出位址範圍的位址。 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   選取頂端的 [PUT]。 此選項會觸發 App Service Environment 上的規模調整作業，並調整防火牆。
 
3. 建立或編輯路由表並填入規則，允許與對應至 App Service Environment 位置的管理位址相互存取。 若要尋找管理位址，請參閱 [App Service Environment 管理位址][management]。

4. 利用路由表或 BGP 路由，調整套用至 App Service Environment 子網路的路由。 

如果在入口網站中 App Service Environment 沒有回應，就表示您的變更有問題。 此問題可能是輸出位址清單不完整、流量已遺失，或該流量被封鎖。 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>使用不同的輸出位址建立新的 App Service Environment ###

如果您的虛擬網路已設定為所有流量均需通過強制通道，您必須採取一些額外的步驟來建立 App Service Environment，使其可以順利運作。 您必須在 App Service Environment 建立期間啟用使用另一個輸出端點。 若要這樣做，您必須使用可指定允許之輸出位址的範本來建立 App Service Environment。

1. 取得要當作 App Service Environment 輸出位址使用的 IP 位址。

2. 預先建立將由 App Service Environment 使用的子網路。 您需要此資訊才能設定路由，而且範本也需要它。

3. 使用對應到 App Service Environment 位置的管理 IP 來建立路由表。 將它指派給 App Service Environment。

4. 依照[使用範本建立 App Service Environment][template] 中的指示進行。 提取適當的範本。

5. 編輯 azuredeploy.json 檔案中的 "resources" 區段。 加入一行 **userWhitelistedIpRanges** 並加上您的值，如下所示：

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

如果此區段的設定正確，App Service Environment 啟動時應該沒有問題。 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
