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
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>建議程式成本建議

建議程式可找出閒置和未充分利用的資源，協助您最佳化並減少 Azure 整體費用。 您可以從 Advisor 儀表板上的 [成本] 索引標籤取得成本建議。

![Advisor [成本] 索引標籤](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>將未充分利用的執行個體調整大小以最佳化虛擬機器費用 
雖然特定應用程式案例的設計可能導致低使用率，但您通常可藉由管理虛擬機器的大小和數目來節省金錢。 Advisor 可監視 14 天的虛擬機器使用量，然後找出低使用率的虛擬機器。 CPU 使用率等於或低於 5% 且網路使用量等於或低於 7 MB 長達 4 天 (含) 以上的虛擬機器，會被視為低使用率虛擬機器。

Advisor 會顯示繼續執行虛擬機器的預估成本，以便您可以選擇將它關閉或調整其大小。  

![調整虛擬機器大小的建議程式成本建議](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>使用符合成本效益的解決方案來管理多個 SQL Database 的效能目標
建議程式會找出可受益於建立彈性資料庫集區的 SQL Server 執行個體。 彈性資料庫集區提供符合成本效益的簡單解決方案，以管理多個具有不同使用模式之資料庫的效能目標。 如需 Azure 彈性集區的詳細資訊，請參閱[什麼是 Azure 彈性集區？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)。

![彈性資料庫集區的建議程式成本建議](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何在 Azure 建議程式中存取成本建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，按一下 [更多服務]。

3. 在服務功能表窗格中，於 [監視和管理] 底下，按一下 [Azure Advisor]。  
 隨即會顯示 Advisor 儀表板。

4. 在 Advisor 儀表板上，按一下 [成本] 索引標籤。

5. 選取您想要接收建議的訂用帳戶，然後按一下取得建議。

> [!NOTE]
> 若要存取 Advisor 建議，您必須先向 Advisor「註冊您的訂用帳戶」。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，您能以訂用帳戶、資源群組或特定資源的 [擁有者]、[參與者] 或 [讀取者] 身分存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：
* [建議程式簡介](advisor-overview.md)
* [快速入門](advisor-get-started.md)
* [建議程式效能建議](advisor-cost-recommendations.md)
* [建議程式高可用性建議](advisor-cost-recommendations.md)
* [建議程式安全性建議](advisor-cost-recommendations.md)
