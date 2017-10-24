---
title: "Azure CosmosDB 圖形 API .NET SDK 和資源 | Microsoft Docs"
description: "了解 Azure CosmosDB 圖形 API，包括發行日期、停用日期，以及每個版本之間的變更。"
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: b84fe1036a50b5672f5b5f4d6df2f67a25a1be4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph .NET API：下載和版本資訊

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API 文件**|[.NET API 參考文件](https://aka.ms/acdbgraphapiref)|
|**快速入門**|[Azure Cosmos DB︰使用 .NET 和圖形 API 建立圖表應用程式](create-graph-dotnet.md)|
|**教學課程**|[Azure CosmosDB：使用圖形 API 建立容器](tutorial-develop-graph-dotnet.md)|
|**目前支援的架構**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>版本資訊

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>新功能
* 已新增 `.netstandard 1.6` 的支援
  * 需要 `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* 已新增 `gremlin-groovy` 剖析器來取代現有的剖析器。 此剖析器支援 Tinkerpop `gremlin-groovy` 語法的子集，並包含：
  * 剖析的效能改善 2 倍。
  * 已解決與字串中的逸出字元、常值處理不正確以及舊的剖析器中其他審查相關的多個問題。
* 已新增周遊邊緣述詞的最佳化。
  *  使用篩選的周遊躍點應該會看到這項改進，例如：`g.V('1').outE().has('name', 'marko').inV()`。
* 已新增包含 `limit()` 步驟的周遊最佳化。

#### <a name="breaking-changes"></a>重大變更
* 已移除 .NET Framework 4.5.1 的支援

* 新的剖析器會與 `gremlin-groovy` 文法對應。 如此一來，先前使用過的某些運算式對於新的剖析器會模稜兩可。 值得注意的其中一個案例：
  * `in` 和 `as` 是 `gremlin-groovy` 中保留的關鍵字，因此必須以 `.in()` 或 `.as()` 限定這些步驟才能避免發生語法錯誤。 例如：`g.V().repeat(in()).times(2)` -> _擲回語法錯誤_  
 `g.V().repeat(__.in()).times(2)` -> _成功_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* 初始預覽版本。

## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務將會拒絕使用已停用 SDK 的任何 Azure Cosmos DB 要求。

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [0.3.0-preview](#0.3.0-preview) |2017 年 10 月 2 日 |--- |
| [0.2.4-preview](#0.2.4-preview) |2017 年 8 月 4 日 |--- |
| [0.2.2-preview](#0.2.2-preview) |2017 年 6 月 23 日 |--- |
| [0.2.1-preview](#0.2.2-preview) |2017 年 6 月 8 日 |--- |
| [0.2.0-preview](#0.2.2-preview) |2017 年 5 月 10 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2017 年 5 月 8 日 |--- |

## <a name="see-also"></a>另請參閱
若要深入了解 Azure Cosmos DB 圖形 API，請參閱 [Azure Cosmos DB：圖形 API 簡介](graph-introduction.md)。 
