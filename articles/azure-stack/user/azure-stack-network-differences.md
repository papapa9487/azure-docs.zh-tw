---
title: "Azure Stack 網路服務：差異與注意事項"
description: "使用 Azure Stack 中的網路服務時，瞭解有關差異和注意事項。"
services: azure-stack
keywords: 
author: ScottNapolitan
ms.author: victorh
ms.date: 9/25/2017
ms.topic: article
ms.service: azure-stack
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7b7bac508a759a1367ac7328840848efe17ea3c5
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="considerations-for-azure-stack-networking"></a>Azure Stack 網路服務的注意事項

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 的網路功能提供了 Azure Stack 中的許多功能，但在開始部署之前應該瞭解一些差異。


本文提供網路服務及其在 Azure Stack 中功能的獨特考量概觀。 若要深入瞭 Azure Stack 與 Azure Stack 之間的高階差異，請參閱[主要考量](azure-stack-considerations.md)主題。


## <a name="cheat-sheet-networking-differences"></a>速查表：網路服務差異

|服務 | 功能 | Azure (全域) | Azure Stack |
| --- | --- | --- | --- |
| DNS | 多租用戶 DNS | 支援| 尚不支援|
| |DNS AAAA 記錄|支援|不支援|
| |每一訂用帳戶的 DNS 區域|100 (預設值)<br>可在要求時增加。|100|
| |每一區域的 DNS 記錄集|5000 (預設值)<br>可在要求時增加。|5000|
||區域委派的名稱伺服器|Azure 為建立的每個使用者 (租用戶) 區域提供四部名稱伺服器。|Azure Stack 為建立的每個使用者 (租用戶) 區域提供兩部名稱伺服器。|
| 虛擬網路|虛擬網路對等互連|透過 Azure 骨幹網路來連線同一區域中兩個虛擬網路。|尚不支援|
| |IPv6 位址|您可以指派 IPv6 位址作為[網路介面設定](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)的一部分。|僅支援 IPv4。|
|VPN 閘道|點對站 VPN 閘道|支援|尚不支援|
| |Vnet 對 Vnet 閘道|支援|尚不支援|
| |VPN 閘道 SKU|支援基本、GW1、GW2、GW3、標準的高效能、超高效能。 |支援基本、標準和高效能 SKU。|
|負載平衡器|IPv6 公用 IP 位址|將 IPv6 公用 IP 位址指派給負載平衡器的支援。|僅支援 IPv4。|
|網路監看員|網路監看員租用戶網路監視功能|支援|尚不支援|
|CDN|內容傳遞網路設定檔|支援|尚不支援|
|應用程式閘道|第 7 層負載平衡|支援|尚不支援|
|流量管理員|針對最佳應用程式效能和可靠性路由連入流量。|支援|尚不支援|
|ExpressRoute|設定從內部部署基礎結構或共置設備到 Microsoft 雲端服務的快速私人連線。|支援|將 Azure Stack 連接到 Express Route 電路的支援。|

## <a name="next-steps"></a>後續步驟

[Azure Stack 中的 DNS](azure-stack-dns.md)

