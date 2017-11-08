---
title: "Azure DocumentDB .NET 變更摘要處理器 SDK 和資源 | Microsoft Docs"
description: "了解「變更摘要處理器 API 和 SDK」的所有相關資訊，包括發行日期、停用日期，以及「DocumentDB .NET 變更摘要處理器 SDK」每個版本之間的變更。"
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: maquaran
ms.openlocfilehash: 8c5a081105d37b8f1c78e90d9ccb8d945099b621
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>DocumentDB .NET 變更摘要處理器 SDK：下載和版本資訊
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET 變更摘要](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文件**|[變更摘要處理器程式庫 API 參考文件](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**開始使用**|[開始使用 DocumentDB 變更摘要處理器 .NET SDK](change-feed.md)|
|**目前支援的架構**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>版本資訊

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 新增 .NET Standard 2.0 的支援。 套件現在支援 `netstandard2.0` 和 `net451` Framework Moniker。
* 與 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.17.0 或以上版本相容。
* 與 [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) 1.5.1 或以上版本相容。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 變更摘要為空白或沒有已暫止的工作時，會修正計算剩餘工作估計的問題。
* 與 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 或以上版本相容。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 新增方法以取得要在變更摘要中處理之剩餘工作的估算。
* 與 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 或以上版本相容。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* 與 [DocumentDB.NET SDK](documentdb-sdk-dotnet.md) 1.14.1 或以下版本相容。

## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務將會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 

