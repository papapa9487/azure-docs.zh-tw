---
title: "適用於 MongoDB 的 Azure Cosmos DB 功能支援 | Microsoft Docs"
description: "了解 Azure Cosmos DB MongoDB API 為 MongoDB 3.4 提供的功能支援。"
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB 功能和語法的 MongoDB API 支援

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以透過任何開放原始碼 MongoDB 用戶端[驅動程式](https://docs.mongodb.org/ecosystem/drivers)與資料庫的 DocumentDB API 通訊。 MongoDB API 藉由遵循 MongoDB [有線通訊協定](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)，來使用現有用戶端驅動程式。

藉由使用 Azure Cosmos DB MongoDB API，您除了可享有慣用的 MongoDB API 權益外，還可使用所有 Azure Cosmos DB 的企業功能：[全域發佈](distribute-data-globally.md)、[自動分區](partition-data.md)、可用性和延遲保證、每個欄位的自動編製索引，待用加密和備份等多能。

## <a name="mongodb-query-language-support"></a>MongoDB 查詢語言支援

Azure Cosmos DB MongoDB API 可完整支援 MongoDB 查詢語言建構。 目前支援的作業、運算子、階段、命令和選項詳細清單如下所示。


## <a name="database-commands"></a>資料庫命令

Azure Cosmos DB 支援在所有 MongoDB API 帳戶上使用下列資料庫命令。 

### <a name="query-and-write-operation-commands"></a>查詢和寫入作業命令
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>驗證命令
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>系統管理命令
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>診斷命令
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>彙總管線 </a>

Azure Cosmos DB 支援公開預覽中的彙總管線。 請參閱 [Azure 部落格](https://aka.ms/mongodb-aggregation)，以取得如何開始使用公開預覽版的指示。

### <a name="aggregation-commands"></a>彙總命令
- aggregate
- 計數
- distinct

### <a name="aggregation-stages"></a>彙總階段
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>彙總運算式

#### <a name="boolean-expressions"></a>布林運算式
- $and
- $or
- $not

#### <a name="set-expressions"></a>Set expressions
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>比較運算式
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>算術運算式
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>字串運算式
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>陣列運算式
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>日期運算式
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>條件運算式
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>彙總累加器
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>運算子

下列運算子可透過其相對應用法範例支援使用。 請將下列查詢中使用的此範例文件列入考量：

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

運算子 | 範例 |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>注意事項

在 $regex 查詢中，左側錨點運算式允許索引搜尋。 不過，使用 'i' 修飾詞 (不區分大小寫) 和 'm' 修飾詞 (多行) 會在所有運算式中造成集合掃描。
當需要包含 '$' 或 '|' 時，最好先建立兩個 (或以上) regex 查詢。 例如，假設原始查詢如下：```find({x:{$regex: /^abc$/})```，則必須修改為：```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```。
第一個部分會使用索引來僅限搜尋以 ^abc 開頭的文件，而第二個部分會比對完整項目。 垂直線運算子 '|' 會作為 "or" 函式使用 - 查詢 ```find({x:{$regex: /^abc|^def/})``` 會比對的文件是其中欄位 'x' 中的值以 "abc" 或 "def" 開頭。 若要利用索引，則建議將查詢分成兩個以 $or 運算子聯結的不同查詢：```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```。


### <a name="geospatial-operators"></a>地理空間運算子

運算子 | 範例 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | 是
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | 是
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | 是
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是

## <a name="additional-operators"></a>其他運算子

運算子 | 範例 | 注意事項 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | 不支援。 改用 $regex 

### <a name="methods"></a>方法

支援下列方法：

#### <a name="cursor-methods"></a>指標方法

方法 | 範例 | 注意事項 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 不會傳回沒有排序索引鍵的文件

## <a name="unique-indexes"></a>唯一索引

Azure Cosmos DB 會為文件中預設寫入資料庫的每個欄位編制索引。 唯一索引可確保集合中所有文件的特定欄位沒有重複值，類似於預設 "_id" 索引鍵上會保留唯一性的方式。 現在您可以使用 createIndex 命令在 Azure Cosmos DB 中建立自訂索引 (包括「唯一」限制)。

唯一索引可用於所有 MongoDB API 帳戶。

## <a name="time-to-live-ttl"></a>存留時間 (TTL)

Azure Cosmos DB 支援以文件時間戳記為基礎的相對存留時間 (TTL)。 您可以透過 [Azure 入口網站](https://portal.azure.com)，為 MongoDB API 集合啟用 TTL。

## <a name="user-and-role-management"></a>使用者和角色管理

Azure Cosmos DB 尚不支援使用者和角色。 Azure Cosmos DB 支援角色型存取控制 (RBAC) 及讀寫和唯讀密碼/金鑰，其可透過 [Azure 入口網站](https://portal.azure.com)取得 (連接字串頁面)。

## <a name="replication"></a>複寫

Azure Cosmos DB 支援最低層級的自動、原生複寫。 此邏輯也可延伸至達到低延遲且全域的複寫。 Azure Cosmos DB 不支援手動複寫命令。

## <a name="sharding"></a>分區化

Azure Cosmos DB 支援自動與伺服器端的分區化。 Azure Cosmos DB 不支援手動分區化命令。

## <a name="next-steps"></a>後續步驟

- 了解如何[使用 Studio 3T](mongodb-mongochef.md) 和 MongoDB 資料庫 API。
- 了解如何[使用 Robo 3T](mongodb-robomongo.md) 和 MongoDB 資料庫 API。
- 探索具有 MongoDB 通訊協定支援的 Azure Cosmos DB [範例](mongodb-samples.md)。
