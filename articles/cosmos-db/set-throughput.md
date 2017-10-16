---
title: "Azure Cosmos DB 的佈建輸送量 | Microsoft Docs"
description: "了解如何設定 Azure Cosmos DB 容器、集合、圖表和資料表的佈建輸送量。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: mimig
ms.openlocfilehash: d541bb19ba7e5ecb44c9fe91b1e232d4d9c2170e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-throughput-for-azure-cosmos-db-containers"></a>設定 Azure Cosmos DB 容器的輸送量

您可以在 Azure 入口網站，或是使用用戶端 SDK，設定 Azure Cosmos DB 容器的輸送量。 

下表列出容器可使用的輸送量：

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一資料分割容器</strong></p></td>
            <td valign="top"><p><strong>已資料分割的容器</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量下限</p></td>
            <td valign="top"><p>每秒 400 個要求單位</p></td>
            <td valign="top"><p>每秒 2,500 個要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>輸送量上限</p></td>
            <td valign="top"><p>每秒 10,000 個要求單位</p></td>
            <td valign="top"><p>無限</p></td>
        </tr>
    </tbody>
</table>

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>使用 Azure 入口網站設定輸送量

1. 在新的視窗中，開啟 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側的 Azure Cosmos DB，或按一下底部的 更多服務，捲動至 資料庫，然後按一下Azure Cosmos DB。
3. 選取您的 Cosmos DB 帳戶。
4. 在新視窗中，按一下導覽功能表中的 [資料總管 (預覽)]。
5. 在新視窗中，展開您的資料庫和容器，然後按一下規模與設定。
6. 在新視窗中，在 輸送量 方塊中輸入新的輸送量，然後按一下儲存。

<a id="set-throughput-sdk"></a>

## <a name="to-set-the-throughput-by-using-the-documentdb-api-for-net"></a>使用 DocumentDB API for .NET 設定輸送量

```C#
//Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to the new value, for example 12,000 request units per second
offer = new OfferV2(offer, 12000);

//Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

## <a name="throughput-faq"></a>輸送量常見問題集

**我可以將輸送量設定為小於 400 RU/s 嗎？**

400 RU/s 是 Cosmos DB 單一資料分割集合上可用的最小輸送量 (2500 RU/s 是資料分割集合的最小值)。 要求單位是設定為 100 RU/s 時間間隔，但不能將輸送量設定為 100 RU/s 或任何小於 400 RU/s 的值。 如果您正在尋找符合成本效益的方法來開發和測試 Cosmos DB，您可以使用免費的 [Cosmos DB 模擬器](local-emulator.md)，於本機免費部署此模擬器。 

**如何使用 MongoDB API 設定輸送量？**

MongoDB API 沒有可以設定輸送量的擴充功能。 建議使用 DocumentDB API，如[使用 DocumentDB API for .NET 設定輸送量](#set-throughput-sdk)中所述。

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB 的佈建和全球規模化，請參閱 [Cosmos DB 的資料分割和規模調整](partition-data.md)。
