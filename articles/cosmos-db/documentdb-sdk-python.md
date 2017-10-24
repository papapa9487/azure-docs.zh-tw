---
title: "Azure Cosmos DB Python API、SDK 和資源 | Microsoft Docs"
description: "了解所有 Python API 和 SDK 相關資訊，包括 發行日期、停用日期及 Azure Cosmos DB Python SDK 每個版本之間所做的變更。"
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/27/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43f957d8549adb8bbc94111ee9f9eb216f5d8210
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-python-sdk-release-notes-and-resources"></a>Azure Cosmos DB Python SDK︰版本資訊與資源
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
> 
> 

<table>

<tr><td>**下載 SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**API 文件**</td><td>[Python API 參考文件](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**SDK 安裝指示**</td><td>[SDK 安裝指示](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**參與 SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**開始使用**</td><td>[開始使用 Python SDK](documentdb-python-application.md)</td></tr>

<tr><td>**目前支援的平台**</td><td>[Python 2.7](https://www.python.org/downloads/) 和 [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊
### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 彙總字典的修正錯誤。
* 資源連結中的修剪斜線修正錯誤。
* 已新增 Unicode 編碼的測試。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* 已新增對新一致性層級 ConsistentPrefix 的支援。


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。
* 新增針對 Cosmos DB 模擬器執行時停用 SSL 驗證的選項。
* 移除相依要求模組必須為 2.10.0 的限制。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。
* 在預存程序執行期間，加入支援指令碼記錄功能。
* REST API 版本在此版本中提升至 '2017-01-19'。

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* 對文件註解進行編輯性的變更。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* 新增 Python 3.5 的支援。
* 新增使用要求模組的連接集區支援。
* 新增工作階段一致性的支援。
* 新增對已分割集合的 TOP/ORDERBY 查詢支援。

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* 新加入已節流處理要求的重試原則支援。 (已節流處理的要求會收到要求率太大的例外狀況，即錯誤碼 429。)根據預設，發生錯誤碼 429 時，Azure Cosmos DB 會遵守回應標頭中的 retryAfter 時間，並針對每個要求重試九次。 如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。 Azure Cosmos DB 現在會針對每個要進行節流處理的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。 您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。
* Cosmos DB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。
* 移除 RetryPolicy 類別和 document_client 類別上公開的對應屬性 (retry_policy)，改為引進公開 ConnectionPolicy 類別上的 RetryOptions 屬性，它可以用來覆寫某些預設的重試選項。

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 新增對多重區域資料庫帳戶的支援。

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* 新加入文件的存留時間 (TTL) 功能支援。

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* 伺服器端資料分割相關錯誤修正，允許在 partitionkey 路徑中使用特殊字元。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* 實作[已分割的集合](partition-data.md)和[使用者定義的效能等級](performance-levels.md)。 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* 實作 Upsert。 已新增新的 UpsertXXX 方法以支援 Upsert 功能。
* 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 支援地理空間索引。
* 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、\, 字元，或以空格作為結尾。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 實作 V2 索引原則。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 支援 Proxy 連線。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務將會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

> [!WARNING]
> 適用於 Python 之所有 **1.0.0** 之前的 Azure DocumentDB SDK 版本都將於 **2016 年 2 月 29 日**停用。 
> 
> 

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.2.1](#2.2.1) |2017 年 9 月 29 日 |--- |
| [2.2.0](#2.2.0) |2017 年 5 月 10 日 |--- |
| [2.1.0](#2.1.0) |2017 年 5 月 1 日 |--- |
| [2.0.1](#2.0.1) |2016 年 10 月 30 日 |--- |
| [2.0.0](#2.0.0) |2016 年 9 月 29 日 |--- |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.1](#1.6.1) |2016 年 4 月 8 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.0](#1.5.0) |2016 年 1 月 3 日 |--- |
| [1.4.2](#1.4.2) |2015 年 10 月 6 日 |--- |
| [1.4.1](#1.4.1) |2015 年 10 月 6 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 6 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 25 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |--- |
| 0.9.4-prelease |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 

