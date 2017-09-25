---
title: "建立網際網路對向負載平衡器 - Azure 入口網站傳統 | Microsoft Docs"
description: "了解如何使用 Azure 傳統入口網站在傳統部署模型中建立網際網路面向的負載平衡器"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: fa3e93c0-968a-472d-a17c-65665c050db2
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: a022154f5eca6de2d2dbfc1b9aa30d2ea0a7d650
ms.contentlocale: zh-tw
ms.lasthandoff: 01/24/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>開始在 Azure 傳統入口網站中建立網際網路面向的負載平衡器 (傳統)

> [!div class="op_single_selector"]
> * [Azure 傳統入口網站](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure 雲端服務](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> 使用 Azure 資源之前，請務必了解 Azure 目前有 Azure Resource Manager 和「傳統」兩種部署模型。 在使用任何 Azure 資源之前，請先確認您了解 [部署模型和工具](../azure-classic-rm.md) 。 您可以按一下本文頂端的索引標籤，檢視不同工具的文件。 本文涵蓋之內容包括傳統部署模型。 您也可以 [了解如何使用 Azure 資源管理員建立網際網路面向的負載平衡器](load-balancer-get-started-internet-arm-ps.md)。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>為虛擬機器設定網際網路面向的負載平衡器

為了使雲端服務的各虛擬機器的網際網路流量達到負載平衡，您必須建立負載平衡的集合。 此程序假設您已建立虛擬機器，而且全都在相同的雲端服務內。

**設定虛擬機器的負載平衡集合**

1. 在 Azure 傳統入口網站中，按一下 [虛擬機器] ，然後按一下負載平衡集合中虛擬機器的名稱。
2. 選取 [端點]，然後按一下 [新增]。
3. 在 [將端點加入至虛擬機器]  頁面上，按一下向右箭頭。
4. 在 [指定端點的詳細資料]  頁面上：

   * 在 [名稱] 中輸入端點的名稱，或從常見通訊協定的預先定義端點清單中選取名稱。
   * 在 [通訊協定] 中，選取端點類型所需的通訊協定 (視需要選擇 TCP 或 UDP)。
   * 在 [公用連接埠和私人連接埠] 中，視需要輸入要虛擬機器使用的連接埠號碼。 您可以在虛擬機器上使用私人連接埠和防火牆規則，以適合應用程式的方式來重新導向流量。 私人連接埠可與公用連接埠相同。 例如，對於 Web (HTTP) 端點的流量，您可以將連接埠 80 同時指派給公用和私人連接埠。

5. 選取 [Create a load-balanced set] ，然後按一下向右箭頭。
6. 在 [設定負載平衡集合]  頁面上，輸入負載平衡集合的名稱，然後指派 Azure 負載平衡器探查行為的值。 負載平衡器會使用探查，來判斷負載平衡集合中的虛擬機器是否可用於接收連入流量。
7. 按一下核取記號以建立負載平衡端點。 您會在虛擬機器的 [端點] 頁面的 [Load-balanced set name] 欄中看見 [是]。
8. 在入口網站中，依序按一下 [虛擬機器]、負載平衡集中其他虛擬機器的名稱、[端點]，然後按一下 [新增]。
9. 在 [將端點加入至虛擬機器] 頁面上，按一下 [將端點加入至現有的負載平衡集]、選取負載平衡集的名稱，然後按一下向右箭頭。
10. 在 [指定端點的詳細資料]  頁面上，輸入端點的名稱，然後按一下核取記號。

針對負載平衡集合中的其他虛擬機器，重複步驟 8-10。

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

