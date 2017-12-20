---
title: "Azure Advisor成本建議 | Microsoft Docs"
description: "使用 Azure Advisor 程式將 Azure 部署的成本最佳化。"
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
# <a name="advisor-cost-recommendations"></a>Azure Advisor 成本建議

Advisor 可找出閒置和未充分利用的資源，協助您減少 Azure 的整體費用並加以最佳化。您可以從 Advisor 儀表板上的 [成本] 索引標籤取得成本建議。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>關閉使用量過低的執行個體，或者重新調整其大小或，從而將虛擬機器的費用最佳化
雖然特定應用程式的設計情境可能導致低使用率，但您通常可以藉由調整虛擬機器的大小和數量來節省費用。Advisor 可監視過去 14 天的虛擬機器使用量，並找出低使用率的虛擬機器。CPU 使用率小於 (含) 5% 且網路使用量小於 (含) 7 MB 長達 4 天 (含) 以上的虛擬機器，將會視為低使用率虛擬機器。

Advisor 會顯示運行中的虛擬機器預估成本，讓您可以選擇將它關閉或調整其大小。

如果您想要更深入的辨識使用量過低的虛擬機器，您可以依據個別訂用帳戶調整平均 CPU 使用率規則。

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>根據成本效益來管理多個 SQL 資料庫的效能
Azure Advisor 會找出可從彈性資料庫集區的建立中獲益的 SQL Server 執行個體。 彈性資料庫集區提供簡單且符合成本效益的解決方案，來為使用模式各有不同的多個資料庫管理其效能目標。如需更多有關 Azure 彈性集區的資訊，請參閱[什麼是 Azure 彈性集區？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>移除未佈建的 ExpressRoute 線路來降低費用
Advisor 發現 ExpressRoute 線路的提供者狀態為「未佈建」已超過一個月，並建議如果您不打算透過連線提供者佈建該線路，請將其刪除。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何存取 Azure Advisor 中的成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [成本] 索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [快速入門](advisor-get-started.md)
* [Advisor 效能建議](advisor-cost-recommendations.md)
* [Advisor 高可用性建議](advisor-cost-recommendations.md)
* [Advisor 安全性建議](advisor-cost-recommendations.md)
