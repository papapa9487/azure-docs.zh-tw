---
title: "使用 Azure 網路監看員下一個躍點來尋找下一個躍點 - Azure 入口網站 | Microsoft Docs"
description: "本文會說明如何使用 Azure 入口網站，利用下一個躍點功能來得知下一個躍點類型和 IP 位址"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5434b7972346821985c459fc4620805adb88676b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/26/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>使用入口網站，利用 Azure 網路監看員的下一個躍點功能找出下一個躍點類型

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

下一個躍點是網路監看員的一項功能，可根據指定的虛擬機器取得下一個躍點類型和 IP 位址。 這項功能可用於判斷離開虛擬機器的流量是否會周遊閘道、網際網路或虛擬網路，以抵達其目的地。

## <a name="before-you-begin"></a>開始之前

此案例假設您已依照[建立網路監看員](network-watcher-create.md)中的步驟建立網路監看員。 此案例也假設已有具有有效虛擬機器的資源群組可供使用。

## <a name="scenario"></a>案例

本文涵蓋的案例會使用下一個躍點，以找出資源的下一個躍點類型和 IP 位址。 若要深入了解下一個躍點，請造訪[下一個躍點概觀](network-watcher-next-hop-overview.md)。

在此案例中，您將會：

* 從虛擬機器中擷取下一個躍點。

## <a name="get-next-hop"></a>取得下一個躍點

### <a name="step-1"></a>步驟 1

在 Azure 入口網站中瀏覽至您的網路監看員資源。

### <a name="step-2"></a>步驟 2

按一下瀏覽窗格中的 [下一個躍點]、選取虛擬機器和網路介面、填寫來源和目的地 IP，然後按 [下一個躍點] 按鈕。

> [!NOTE]
> 下一個躍點需要配置 VM 資源以供執行。

![取得下一個躍點概觀][1]

### <a name="step-3"></a>步驟 3

一旦工作完成後，會提供結果。 會顯示 IP 位址和下一個躍點的裝置類型。 下表會在入口網站中顯示可用的傳回值。

**下一個躍點類型**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

如果自訂路由用來路由傳送此流量，則使用者定義的路由 (UDR) 也會與結果顯示。

![取得下一個躍點結果][2]

## <a name="next-steps"></a>後續步驟

請造訪[使用網路監看員稽核 NSG](network-watcher-nsg-auditing-powershell.md)，以了解如何以程式設計方式檢閱網路安全性群組設定

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png















