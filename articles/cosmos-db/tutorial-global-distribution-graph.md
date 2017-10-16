---
title: "適用於圖形 API 的 Azure Cosmos DB 全域散發教學課程 | Microsoft Docs"
description: "了解如何使用「圖形 API」來設定 Azure Cosmos DB 全域散發。"
services: cosmos-db
keywords: "全域散發, 圖形, gremlin"
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: d05295f6b952209e6df88d9015153893fbbb9903
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>如何使用圖形 API 來設定 Azure Cosmos DB 全域散發

在本文中，我們會說明如何使用 Azure 入口網站來設定 Azure Cosmos DB 全域散發，然後使用「圖形 API」(預覽) 來進行連線。

本文涵蓋下列工作： 

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用[圖形 API](graph-introduction.md) (預覽) 來設定全域散發

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>使用 .NET SDK 搭配圖形 API 來連線到慣用的區域

「圖形 API」會公開為 DocumentDB SDK 上的延伸模組程式庫。

為了充分運用 [全球發佈](distribute-data-globally.md)，用戶端應用程式可以指定已排序的區域喜好設定清單，以用來執行文件作業。 這可透過設定連接原則來完成。 SDK 將會根據 Azure Cosmos DB 帳戶組態、目前的區域可用性及所指定的喜好設定清單，選擇最適合的端點來執行寫入和讀取作業。

這份喜好設定清單是在使用 SDK 將連線初始化時即已指定。 SDK 會接受選擇性參數 "PreferredLocations"，也就是已排序的 Azure 區域清單。

* **寫入**：SDK 會自動將所有寫入傳送到目前的寫入區域。
* **讀取**：所有讀取都會傳送到 PreferredLocations 清單中的第一個可用區域。 如果要求失敗，用戶端將無法往下到清單中的下一個區域，依此類推。 SDK 只會嘗試從 PreferredLocations 中指定的區域讀取。 因此，舉例來說，如果在三個區域中有 Cosmos DB 帳戶可供使用，但用戶端只為 PreferredLocations 指定其中兩個非寫入區域，這樣就不會從寫入區域為任何讀取提供服務，即使發生容錯移轉時也一樣。

應用程式可以藉由檢查兩個屬性 (WriteEndpoint 和 ReadEndpoint，適用於 SDK 1.8 版和以上版本) 來確認 SDK 目前所選擇的寫入端點和讀取端點。 如果未設定 PreferredLocations 屬性，將會從目前的寫入區域為所有要求提供服務。

### <a name="using-the-sdk"></a>使用 SDK

例如，在 .NET SDK 中，`DocumentClient` 建構函式的 `ConnectionPolicy` 參數會具有名為 `PreferredLocations` 的屬性。 此屬性可以設定為區域名稱清單。 指定 `PreferredLocations` 時可以一併指定 [Azure 區域][regions]的顯示名稱。

> [!NOTE]
> 不應將端點的 URI 視為長時間執行的常數。 服務可能會隨時更新這些項目。 SDK 會自動處理此變更。
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

就這麼簡單，這樣便已完成本教學課程。 您可以透過閱讀 [Azure Cosmos DB 中的一致性層級](consistency-levels.md)，來了解如何管理全域複寫帳戶的一致性。 如需有關 Azure Cosmos DB 中全域資料庫複寫運作方式的詳細資訊，請參閱[使用 Azure Cosmos DB 來全域散發資料](distribute-data-globally.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用 DocumentDB API 來設定全域散發

您現在可以繼續進行到下一個教學課程，以了解如何使用 Azure Cosmos DB 本機模擬器在本機進行開發。

> [!div class="nextstepaction"]
> [使用模擬器在本機進行開發](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

