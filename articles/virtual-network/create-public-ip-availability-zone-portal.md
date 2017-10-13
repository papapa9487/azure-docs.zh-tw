---
title: "使用 Azure 入口網站建立分區公用 IP 位址 | Microsoft Docs"
description: "使用 Azure 入口網站在可用性區域中建立公用 IP 位址。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>使用 Azure 入口網站在可用性區域中建立公用 IP 位址

您可以在 Azure 可用性區域 (預覽) 中部署公用 IP 位址。 可用性區域是 Azure 區域內實際分開的區域。 了解如何：

> * 在可用性區域中建立公用 IP 位址
> * 識別可用性區域中所建立的相關資源

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

> [!NOTE]
> 可用性區域為預覽版功能，可供開發和測試案例使用。 此功能支援選取 Azure 資源和區域以及 VM 大小系列。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 https://portal.azure.com。 

## <a name="create-a-zonal-public-ip-address"></a>建立區域性公用 IP 位址

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [網路]，然後選取 [公用 IP 位址]。
3. 輸入或選取下列設定值、選取訂用帳戶、接受其餘設定的預設值，然後按一下 [建立]：

    |設定|值|
    |---|---|
    |SKU| **基本**：使用靜態或動態配置方法來指派。 可指派給任何可以指派公用 IP 位址的 Azure 資源，例如網路介面、VPN 閘道、應用程式閘道和網際網路對應負載平衡器。 您可以在 [可用性區域] 設定內，將公用 IP 位址指派給特定區域。 沒有區域備援。 若要深入了解可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 **標準**：只會使用靜態配置方法來指派。 可指派給網路介面或標準的網際網路對應負載平衡器。 如果您將公用 IP 位址指派給標準的網際網路對向負載平衡器，則必須選取 [標準]。 如需 Azure 負載平衡器 SKU 的詳細資訊，請參閱 [Azure 負載平衡器的標準 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。 預設有區域備援。 可以建立為區域型，並保證在特定可用性區域中。 標準 SKU 目前為預覽版本。 您必須先註冊預覽版，才可以建立標準 SKU 公用 IP 位址。 若要註冊預覽版本，請參閱[註冊標準 SKU 預覽版本](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up)。 您只能在受支援的位置中建立標準 SKU。  如需支援位置 (區域) 清單，請參閱[區域可用性](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability)並監看 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)頁面以取得其他區域支援。|   
    |名稱|名稱必須是您選取的資源群組中唯一的名稱。|
    |資源群組|按一下 [建立新的]，然後輸入 myResourceGroup|
    |位置|西歐|
    |可用性區域|如果您已選取 [標準] SKU，並且想要讓 IP 位址能夠跨區域復原，則可以選取 [區域備援]。 如果您選取 [基本] SKU，則 IP 位址無法跨區域復原。 不論您選擇哪種 SKU，您都可以選擇將位址指派給特定區域。 |

    設定會出現在入口網站中，如下圖所示：

    ![建立區域性公用 IP 位址](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> 當您將標準 SKU 的公用 IP 位址指派給虛擬機器的網路介面時，必須使用[網路安全性群組](security-overview.md#network-security-groups)明確地允許預定的流量。 在建立和關聯網路安全性群組並明確地允許所要流量前，與資源進行的通訊都會失敗。

## <a name="next-steps"></a>後續步驟

- 深入了解[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)
- 深入了解[公用 IP 位址](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)