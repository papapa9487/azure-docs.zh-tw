---
title: "網際網路面向的負載平衡器概觀 | Microsoft Docs"
description: "網際網路面向的負載平衡器及其功能的概觀。 負載平衡器如何作用於使用虛擬機器和雲端服務的 Azure。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5b9ffeadf6b1ffc4eaf4f49b85ba752c27da0e46
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="internet-facing-load-balancer-overview"></a>網際網路面向的負載平衡器概觀

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 負載平衡器將連入流量的公用 IP 位址和連接埠號碼對應至虛擬機器的私人 IP 位址和連接埠號碼，來自虛擬機器的回應流量也是如此。 負載平衡規則可讓您將特定類型的流量分配至多個虛擬機器或服務。 例如，您可以將 Web 要求的流量負載分散在多個 Web 伺服器或 Web 角色。

對於包含 Web 角色或背景工作角色執行個體的雲端服務，您可以在服務定義 (.csdef) 檔案中定義公用端點。

*servicedefinition.csdef* 檔案將包含端點組態，而當一個 Web 或背景工作角色部署具有多個角色執行個體時，負載平衡器將會針對它進行設定。 將執行個體加入至雲端部署的方法就是變更服務組態檔 (.csfg) 上的執行個體計數。

## <a name="example-of-an-internet-facing-load-balancer"></a>網際網路對向負載平衡器範例

下圖顯示在三部虛擬機器中共用，且公用和私人 TCP 通訊埠均為 80 的 Web 流量負載平衡端點。 這三部虛擬機器均位在負載平衡集合中。

![建立負載平衡器範例](./media/load-balancer-internet-overview/IC727496.png)

圖 1 - Web 流量的負載平衡端點

當網際網路用戶端在 TCP 通訊埠 80 上傳送網頁要求至雲端服務的公用 IP 位址時，Azure Load Balancer 會在負載平衡集中，將要求分配至這三部虛擬機器。 如需負載平衡器演算法的詳細資訊，請參閱[負載平衡器概觀頁面](load-balancer-overview.md#load-balancer-features)。

根據預設，Azure Load Balancer 會在多個虛擬機器執行個體之間均分網路流量。 您也可以設定工作階段親和性。如需詳細資訊，請參閱[負載平衡器分配模式](load-balancer-distribution-mode.md)。

## <a name="next-steps"></a>後續步驟

了解[內部負載平衡器](load-balancer-internal-overview.md)，以深入了解哪一種負載平衡器較適合您的雲端部署。

您也可以[開始建立網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)，以及為特定負載平衡器的網路流量行為設定[分配模式](load-balancer-distribution-mode.md)類型。

如果您的應用程式需要讓負載平衡器後方的伺服器保持連接狀態，您可以深入了解 [負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)。 當您使用 Azure 負載平衡器時，該文章可幫助您了解閒置連接行為。

