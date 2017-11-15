---
title: "Azure 建議程式成本建議 | Microsoft Docs"
description: "使用 Azure 建議程式將 Azure 部署的成本最佳化。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>建議程式成本建議

建議程式可找出閒置和未充分利用的資源，協助您最佳化並減少 Azure 整體費用。 您可以從 Advisor 儀表板上的 [成本] 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>將使用量過低的執行個體調整大小或關閉，以將虛擬機器費用最佳化 
雖然特定應用程式案例的設計可能導致低使用率，但您通常可藉由管理虛擬機器的大小和數目來節省金錢。 Advisor 可監視 14 天的虛擬機器使用量，然後找出低使用率的虛擬機器。 CPU 使用率等於或低於 5% 且網路使用量等於或低於 7 MB 長達 4 天 (含) 以上的虛擬機器，會被視為低使用率虛擬機器。

Advisor 會顯示繼續執行虛擬機器的預估成本，以便您可以選擇將它關閉或調整其大小。

如果您想要更積極地識別使用量過低的虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>使用符合成本效益的解決方案來管理多個 SQL Database 的效能目標
建議程式會找出可受益於建立彈性資料庫集區的 SQL Server 執行個體。 彈性資料庫集區提供符合成本效益的簡單解決方案，以管理多個具有不同使用模式之資料庫的效能目標。 如需 Azure 彈性集區的詳細資訊，請參閱[什麼是 Azure 彈性集區？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低成本
Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [成本] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [建議程式簡介](advisor-overview.md)
* [快速入門](advisor-get-started.md)
* [建議程式效能建議](advisor-cost-recommendations.md)
* [建議程式高可用性建議](advisor-cost-recommendations.md)
* [建議程式安全性建議](advisor-cost-recommendations.md)
