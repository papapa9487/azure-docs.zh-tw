---
title: "適用於資料表 API 的 Azure Cosmos DB 全域散發教學課程 | Microsoft Docs"
description: "了解如何使用「資料表 API」來設定 Azure Cosmos DB 全域散發。"
services: cosmos-db
keywords: "全域散發, 資料表"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>如何使用資料表 API 來設定 Azure Cosmos DB 全域散發

本文涵蓋下列工作： 

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用[資料表 API](table-introduction.md) 來設定全域散發

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用資料表 API 來連線到慣用的區域

為了充分運用 [全球發佈](distribute-data-globally.md)，用戶端應用程式可以指定已排序的區域喜好設定清單，以用來執行文件作業。 透過在 Azure Cosmos DB 資料表 API SDK 的 app.config 中設定 `TablePreferredLocations` 組態值，即可達到此目的。 Azure Cosmos DB 資料表 API SDK 將會根據帳戶組態、目前的區域可用性及所指定的喜好設定清單，挑選最適合通訊的端點。

`TablePreferredLocations` 應該包含一個供讀取使用的慣用 (多路連接) 位置逗號分隔清單。 每個用戶端執行個體可以依慣用順序指定這些區域的子集，供低延遲讀取使用。 指定這些區域時，必須使用其[顯示名稱](https://msdn.microsoft.com/library/azure/gg441293.aspx)，例如 `West US`。

所有讀取都會傳送到 `TablePreferredLocations` 清單中的第一個可用區域。 如果要求失敗，用戶端將無法往下到清單中的下一個區域，依此類推。

SDK 會嘗試從 `TablePreferredLocations` 中指定的區域讀取。 因此，舉例來說，如果在三個區域中有「資料庫帳戶」可供使用，但用戶端只為 `TablePreferredLocations` 指定其中兩個非寫入區域，這樣就不會從寫入區域為任何讀取提供服務，即使發生容錯移轉時也一樣。

SDK 會自動將所有寫入傳送至目前的寫入區域。

如果未設定 `TablePreferredLocations` 屬性，將會從目前的寫入區域為所有要求提供服務。

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

就這麼簡單，這樣便已完成本教學課程。 您可以透過閱讀 [Azure Cosmos DB 中的一致性層級](consistency-levels.md)，來了解如何管理全域複寫帳戶的一致性。 如需有關 Azure Cosmos DB 中全域資料庫複寫運作方式的詳細資訊，請參閱[使用 Azure Cosmos DB 來全域散發資料](distribute-data-globally.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用 Azure Cosmos DB 資料表 API 來設定全域散發

