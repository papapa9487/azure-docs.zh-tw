---
title: "Azure 建議程式效能建議 | Microsoft Docs"
description: "使用 Advisor 將 Azure 部署的效能最佳化。"
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
ms.openlocfilehash: 995a1f37a3fd68b39c14a95d46109c0f7814018d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-performance-recommendations"></a>建議程式效能建議

Advisor 效能建議有助於提升業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能] 索引標籤上，取得 Advisor 的效能建議。

![建議程式效能索引標籤](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="improve-database-performance-with-sql-db-advisor"></a>使用 SQL DB Advisor 來改善資料庫效能

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。 SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。 

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需 SQL Database Advisor 的詳細資訊，請參閱 [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/)。

![SQL Database 建議](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="improve-redis-cache-performance-and-reliability"></a>改善 Redis 快取的效能和可靠性

Advisor 會識別高記憶體使用量、伺服器負載、網路頻寬或大量用戶端連線會對其效能造成負面影響的 Redis 快取執行個體。 Advisor 也提供最佳做法建議來協助您避免潛在的問題。 如需 Redis 快取建議的詳細資訊，請參閱 [Redis 快取建議程式](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor)。


## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
* 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體。
* 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。 

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/)。
![應用程式服務建議](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側窗格中，按一下 [更多服務]。

3. 在服務功能表窗格中，於 [監視和管理] 底下，按一下 [Azure Advisor]。  
 隨即會顯示 Advisor 儀表板。

4. 在 Advisor 儀表板上，按一下 [效能] 索引標籤。

5. 選取您想要接收建議的訂用帳戶，然後按一下取得建議。

> [!NOTE]
> 若要存取 Advisor 建議，您必須先向 Advisor「註冊您的訂用帳戶」。 當「訂用帳戶擁有者」啟動 Advisor 儀表板，然後按一下 [取得建議] 按鈕時，便會註冊訂用帳戶。 此作業「只需要執行一次」。 註冊訂用帳戶之後，您能以訂用帳戶、資源群組或特定資源的 [擁有者]、[參與者] 或 [讀取者] 身分存取 Advisor 建議。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [建議程式簡介](advisor-overview.md)
* [開始使用建議程式](advisor-get-started.md)
* [建議程式成本建議](advisor-performance-recommendations.md)
* [建議程式高可用性建議](advisor-high-availability-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)

