---
title: "Azure 中的高可用性連接埠概觀 | Microsoft Docs"
description: "深入了解內部負載平衡器上的高可用性連接埠負載平衡。"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: f72f4a3a81fc3a03c86805787caeeacbe6135c5e
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="high-availability-ports-overview"></a>高可用性連接埠概觀

Azure Load Balancer Standard 可協助您在使用內部負載平衡器時，同時對所有連接埠上的 TCP 和 UDP 流量進行負載平衡。 

>[!NOTE]
> Load Balancer Standard 有提供高可用性 (HA) 連接埠功能，目前為預覽版。 在預覽階段，功能可能沒有與正式運作版功能相同層級的可用性和可靠性。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 註冊 Load Balancer Standard 預覽版，將 HA 連接埠與 Load Balancer Standard 資源搭配使用。 遵循指示以註冊 Load Balancer [Standard 預覽版](https://aka.ms/lbpreview#preview-sign-up)。

HA 連接埠規則是內部 Load Balancer Standard 上所設定之負載平衡規則的變化。 您可以藉由提供單一規則來將抵達內部 Load Balancer Standard 所有連接埠的所有 TCP 和 UDP 流量進行負載平衡，以簡化對於 Load Balancer 的使用。 每次流量都會進行負載平衡決策。 決策是根據下列五個 Tuple 連線：來源 IP 位址、來源連接埠、目的地 IP 位址、目的地連接埠和通訊協定。

HA 連接埠功能可協助您進行重要案例，例如虛擬網路內網路虛擬設備 (NVA) 的高可用性和調整。 它也可以在必須負載平衡大量連接埠時提供協助。 

當您將前端和後端設定為 [0]，以及將通訊協定設定為 [全部] 時，會設定 HA 連接埠功能。 然後內部負載平衡器資源會平衡所有 TCP 和 UDP 流量，而不會理會連接埠號碼。

## <a name="why-use-ha-ports"></a>為何要使用 HA 連接埠？

### <a name="nva"></a>網路虛擬設備

您可以使用 NVA 來保護您的 Azure 工作負載，使其不會遭受多個類型的安全性威脅。 當您在這些案例中使用 NVA 時，它們必須具有可靠性、高可用性，並可依需要相應放大。

若要達成這些目標，您只需要將 NVA 執行個體新增至 Azure 內部負載平衡器的後端集區，並設定 HA 連接埠負載平衡器規則即可。

HA 連接埠可為 NVA HA 案例提供幾個好處：
- 透過執行個體健康情況探查快速容錯移轉至狀況良好的執行個體
- 透過相應放大為 n-active 執行個體而提升效能
- N-active 和主動-被動案例
- 不需要使用複雜的解決方案 (例如，Apache ZooKeeper 節點) 來監視設備

下圖顯示中樞和支點虛擬網路部署。 支點會強制將其通道導向中樞虛擬網路並且透過 NVA，然後再退出信任的空間。 在 HA 連接埠設定中，NVA 位於內部 Load Balancer Standard 後面。 所有流量皆可據以處理並轉送。

![中樞和支點虛擬網路 (具有以 HA 模式部署的 NVA)](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> 如果您使用 NVA，請與個別提供者確認如何以最佳方式使用 HA 連接埠，以及所支援的案例有哪些。

### <a name="load-balancing-large-numbers-of-ports"></a>負載平衡大量連接埠

您也可以對需要負載平衡大量連接埠的應用程式使用 HA 連接埠。 您可以藉由使用具有 HA 連接埠的內部 [Load Balancer Standard](https://aka.ms/lbpreview)，來簡化這些案例。 單一負載平衡規則會取代多個個別的負載平衡規則，每個連接埠一個規則。

## <a name="region-availability"></a>區域可用性

HA 連接埠功能可在[和 Load Balancer Standard 相同的區域](https://aka.ms/lbpreview#region-availability)中取得。  

## <a name="preview-sign-up"></a>註冊預覽

若要參加 Load Balancer Standard 中 HA 連接埠功能的預覽，請註冊 Load Balancer [Standard 預覽](https://aka.ms/lbpreview#preview-sign-up)的訂用帳戶。 您可以使用 Azure CLI 2.0 或 PowerShell 進行註冊。

>[!NOTE]
>註冊最多需要 1 小時。

## <a name="limitations"></a>限制

以下是 HA 連接埠功能的支援設定或例外狀況：

- 單一前端 IP 設定可以有一個與 HA 連接埠搭配的單一 DSR 負載平衡器規則，或是一個與 HA 連接埠搭配的單一非 DSR 負載平衡器規則。 但兩者不能同時存在。
- 單一網路介面的 IP 設定只能在 HA 連接埠建立一個非 DSR 負載平衡器規則。 您無法針對此 ipconfig 設定任何其他規則。
- 單一網路介面的 IP 設定可以在 HA 連接埠建立一或多個 DSR 負載平衡器規則，但前提是其各自的前端 IP 設定都是唯一的。
- 如果所有負載平衡規則都是 HA 連接埠 (只有 DSR)，兩個 (或以上) 指向相同後端集區的負載平衡器規則可以同時存在。 如果所有規則都是非 HA 連接埠 (DSR 和非 DSR) 也成立。 但是，如果有 HA 連接埠和非 HA 連接埠規則的組合，則兩個此類負載平衡規則無法同時存在。
- HA 連接埠功能不適用於 IPv6。
- 只有單一 NIC 支援 NVA 案例的流程對稱。 請參閱[網路虛擬設備](#nva)的描述和圖表。 



## <a name="next-steps"></a>後續步驟

- [在內部 Load Balancer Standard 上設定 HA 連接埠](load-balancer-configure-ha-ports.md)
- [深入了解 Load Balancer Standard 預覽版](https://aka.ms/lbpreview)

