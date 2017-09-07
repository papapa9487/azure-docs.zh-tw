---
title: "Azure 網路監看員中的下一個躍點簡介 | Microsoft Docs"
description: "本頁提供網路監看員下一個躍點功能的概觀"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.contentlocale: zh-tw
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Azure 網路監看員中的下一個躍點簡介

來自 VM 的流量會根據與 NIC 相關聯的有效路由來傳送到目的地。 下一個躍點會從特定虛擬機器和 NIC 取得封包的下一個躍點類型和 IP 位址。 這有助於判斷封包會被導向到目的地，還是流量會被吸收掉。 使用者若未正確設定路由，而讓流量導向內部部署位置或虛擬應用裝置，就可能會導致連線問題。 下一個躍點也會傳回與下一個躍點相關聯的路由表。 向下一個躍點查詢路由是否定義為使用者定義的路由時，便會傳回該路由。 否則，下一個躍點會傳回「系統路由」。

![下一個躍點概觀][1]

以下是查詢下一個躍點時可傳回之下一個躍點類型的清單。

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

### <a name="next-steps"></a>後續步驟

瀏覽[檢查 VM 上的下一個躍點](network-watcher-check-next-hop-portal.md)，以了解如何使用下一個躍點來尋找網路連線問題

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














