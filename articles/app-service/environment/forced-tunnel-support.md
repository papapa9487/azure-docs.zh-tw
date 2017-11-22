---
title: "設定 Azure App Service Environment 使用強制通道"
description: "輸出流量使用強制通道時讓 ASE 可以運作"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>設定 App Service Environment 搭配強制通道

App Service Environment (ASE) 是在客戶的 Azure 虛擬網路 (VNet) 之中的 Azure App Service 部署。 許多客戶將其 VNet 設定為內部部署網路 (具備 VPN 或 ExpressRoute 連線) 的擴充功能。 由於公司的策略或其他安全性限制，他們設定路由讓所有輸出流量先在內部部署網路中傳送，之後再傳送至網際網路。 變更 VNet 的路由，使來自 VNet 的輸出流量流經 VPN 或 ExpressRoute 連線傳送至內部部署網路，這稱為強制通道。  

強制通道可能會造成 ASE 的問題。 ASE 有一些外部相依性，列述於此 [ASE 網路架構][network]文件。 根據預設，ASE 需要所有的輸出通訊經過 ASE 佈建的 VIP。

路由是強制通道以及其處理方式的重要關鍵。 在 Azure 虛擬網路中，路由是根據 最長首碼比對 (LPM) 而進行。  如果有多個符合相同 LPM 的路由，則會根據其來源，以下列順序選取路由：

1. 使用者定義的路由
1. BGP 路由 (使用 ExpressRoute 時)
1. 系統路由

如需深入了解 VNet 中的路由，請閱讀[使用者定義的路由和 IP 轉送][routes]。 

如果您希望 ASE 能在強制通道 VNet 中操作，您有兩個選擇：

1. 讓 ASE 可以直接存取網際網路
1. 變更 ASE 的輸出端點

## <a name="enable-your-ase-to-have-direct-internet-access"></a>讓 ASE 可以直接存取網際網路

若 VNet 設定搭配 ExpressRoute，為了讓 ASE 能夠運作，您可以：

* 將 ExpressRoute 設定為公告 0.0.0.0/0。 根據預設，它會使用強制通道將所有輸出流量傳送至內部部署網路。
* 建立 UDR。 將它套用至包含 ASE 的子網路，並搭配 0.0.0.0/0 位址首碼及網際網路的下一個躍點類型。

如果您做了這兩項變更，則 (來自 ASE 子網路) 將出發到網際網路的流量，將不會被強制經由 ExpressRoute 傳輸，並使 ASE 能夠運作。

> [!IMPORTANT]
> UDR 中定義的路由必須足夠明確，以優先於 ExpressRoute 組態所通告的任何路由。 前面的範例使用廣泛的 0.0.0.0/0 位址範圍。 因此有可能會不小心由使用更明確位址範圍的路由通告所覆寫。
>
> 針對從公用對等互連路徑至私人對等互連路徑的路由進行交叉通告的 ExpressRoute 設定，不支援 ASE。 已設定公用對等互連的 ExpressRoute 設定，會收到來自 Microsoft 的路由通告。 通告中會包含一大組 Microsoft Azure IP 位址範圍。 如果位址範圍在私人對等互連路徑上交叉通告，來自 ASE 子網路的所有輸出網路封包都會使用強制通道傳送至客戶的內部部署網路基礎結構。 ASE 目前預設不支援這個網路流量。 此問題的一個解決方案是停止從公用對等互連路徑至私人對等互連路徑的交叉通告路由。  另一個解決辦法是要讓 ASE 可以在強制通道設定中運作。

## <a name="change-the-egress-endpoint-for-your-ase"></a>變更 ASE 的輸出端點 ##

本節描述如何藉由變更 ASE 所使用的輸出端點，讓 ASE 在強制通道設定下運作。 如果 ASE 的輸出流量經過強制通道送至內部部署網路，則您必須允許該流量來自 ASE VIP 位址以外的 IP 位址。

ASE 不只有外部相依性，也必須接聽輸入流量才能管理 ASE。 ASE 必須能夠回應這類流量，而且回覆無法從另一個位址傳送回來，因為這會中斷 TCP。  因此需執行三個步驟，才能變更 ASE 輸出端點。

1. 設定路由表，確保輸入管理流量可以傳回相同的 IP 位址
1. 新增您的 IP 位址，要用於 ASE 防火牆的輸出
1. 將 ASE 輸出流量的路由設定為通過通道

![強制通道網路流程][1]

您可以在 ASE 已啟用且運作之後替 ASE 設定不同的輸出位址，或在 ASE 部署期間設定。  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>在 ASE 運作之後變更輸出位址 ###
1. 取得用來作為 ASE 輸出 IP 的 IP 位址。 如果您使用強制通道，則這會是您的 NAT 或閘道 IP。  如果您想要透過 NVA 路由傳送 ASE 輸出流量，則輸出位址會是 NVA 的公用 IP。
2. 設定 ASE 設定資訊中的輸出位址。 請移至 resource.azure.com 並瀏覽至：Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>，您就可以看到描述您 ASE 的 json。  確定最上方寫的是 read/write。  按一下 [編輯]，捲動到底部，並將 userWhitelistedIpRanges 從  

       "userWhitelistedIpRanges": null 
      
  變更為類似以下這樣。 使用您要設為輸出位址範圍的位址。 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  按一下頂端的 PUT。 這會觸發 ASE 的調整規模作業，並調整防火牆。
   
3. 建立或編輯路由表並填入規則，允許與對應至 ASE 位置的管理位址相互存取。  管理位址在此，[App Service Environment 管理位址][management] 

4. 利用路由表或 BGP 路由調整套用至 ASE 子網路的路由。  

如果在入口網站中 ASE 沒有回應，就表示您的變更有問題。  可能是輸出位址清單不完整、流量已遺失、或已封鎖該流量。  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>以不同的輸出位址建立新的 ASE  ###

如果您的 VNet 已設定為所有流量均需通過強制通道，您必須採取一些額外的步驟來建立您的 ASE，使其可以順利運作。 這表示您必須在 ASE 建立期間啟用使用其他輸出端點。  為了這麼做，您必須使用範本建立 ASE，且此範本可指定允許的輸出位址。

1. 取得要作為 ASE 輸出位址的 IP 位址。
1. 預先建立將由 ASE 使用的子網路。 為了讓您設定路由，這是必需的，而且範本也需要它。  
1. 以對應到 ASE 位置之管理 IP 建立路由表，並將它指派給您的 ASE
1. 依照[使用範本建立 ASE][template] 的指示，並提取適當的範本
1. 編輯 azuredeploy.json 檔案的 "resources" 部分。 加入一行 **userWhitelistedIpRanges** 並加上您的值，例如：

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

如果此處設定正確，ASE 應能正常啟動沒有問題。  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
