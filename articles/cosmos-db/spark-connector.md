---
title: "將 Apache Spark 連接到 Azure Cosmos DB | Microsoft Docs"
description: "使用本教學課程來了解 Azure Cosmos DB Spark 連接器，此連接器可讓您將 Apache Spark 連線到 Azure Cosmos DB，以在 Microsoft 針對雲端設計的多租用戶全域分散式資料庫系統上，執行分散式彙總和資料科學。"
keywords: Apache Spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: denlee
ms.openlocfilehash: 8ecbb478c81cde25bbd0d1c9ee07ae02b07f8cc7
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>使用「Spark 至 Azure Cosmos DB」連接器來加速即時巨量資料分析

「Spark 至 Azure Cosmos DB」連接器可讓 Azure Cosmos DB 作為 Apache Spark 作業的輸入來源或輸出接收器。 將 [Spark](http://spark.apache.org/) 連線到 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 可讓您加速解決瞬息萬變的資料科學問題，其中可以使用 Azure Cosmos DB 來快速保存及查詢資料。 Spark 至 Azure Cosmos DB 連接器可有效率地利用原生 Azure Cosmos DB 受管理索引。 該索引可在針對快速變更的全域分散式資料 (範圍涵蓋物聯網 (IoT)、資料科學及分析案例) 執行分析和向下推展的述詞篩選時，更新資料行。

如需使用 Azure Cosmos DB 的 Spark GraphX 和 Gremlin 圖形 API，請參閱[使用 Spark 和 Apache TinkerPop Gremlin 執行圖形分析](spark-connector-graph.md)。

## <a name="download"></a>下載

若要開始進行，請從 GitHub 上的 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) 儲存機制下載「Spark 至 Azure Cosmos DB」連接器 (預覽)。

## <a name="connector-components"></a>連接器元件

連接器使用下列元件：

* [Azure Cosmos DB](http://documentdb.com) 可讓客戶在任意數目的全體地理區域，佈建及彈性地調整輸送量和儲存空間。 該服務提供下列項目：
   * 讓金鑰可供[全域發佈](distribute-data-globally.md)和水平調整
   * 保證 99 百分位數的單一數字延遲
   * [多個定義完善的一致性模型](consistency-levels.md)
   * 保證具有多路連接功能的高可用性
   * 所有功能都受領先業界的完整[服務等級協定](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA) 支援。

* [Apache Spark](http://spark.apache.org/) 是功能強大的開放原始碼處理引擎，專為速度、易用性和精密分析所打造。

* [Apache Spark on Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) 可讓您使用 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) 在雲端部署適用於任務關鍵性部署的 Apache Spark。

正式支援的版本：

| 元件 | 版本 |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Azure DocumentDB Java SDK | 1.10.0 |

本文將協助您使用 Python (透過 pyDocumentDB) 和 Scala 介面，執行一些簡單範例。

有兩種方法可以連接 Apache Spark 與 Azure Cosmos DB：
- 透過 [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python) 使用 pyDocumentDB。
- 利用 [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java)，建立以 Java 為基礎的「Spark 至 Azure Cosmos DB」連接器。

## <a name="pydocumentdb-implementation"></a>pyDocumentDB 實作
目前 [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) 可讓您將 Spark 連線到 Azure Cosmos DB，如下圖所示：

![透過 pyDocumentDB DB 的 Spark 至 Azure Cosmos DB 資料流程](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>pyDocumentDB 實作的資料流程

資料流程如下︰

1. Spark 主要節點透過 pyDocumentDB 連線到 Azure Cosmos DB 閘道節點。 使用者指定僅限 Spark 和 Azure Cosmos DB 連線。 對於主要和閘道節點的個別連線對使用者皆為透明。
2. 閘道節點針對 Azure Cosmos DB 執行查詢，其中該查詢後續會針對資料節點中集合的分割區執行。 這些查詢的回應會傳回給閘道節點，並將該結果集傳回給 Spark 主要節點。
3. 後續查詢 (例如，針對 Spark DataFrame) 皆會傳送到 Spark 背景工作角色節點進行處理。

Spark 與 Azure Cosmos DB 之間的通訊，僅限在 Spark 主要節點和 Azure Cosmos DB 閘道節點才能進行。  由於系統允許這兩個節點之間的傳輸層，因此查詢速度很快。

### <a name="install-pydocumentdb"></a>安裝 pyDocumentDB
您可以使用 **pip**，在驅動程式節點上安裝 pyDocumentDB，例如：

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>透過 pyDocumentDB 將 Spark 連線到 Azure Cosmos DB
通訊傳輸的簡單性，讓使用 pyDocumentDB 執行從 Spark 到 Azure Cosmos DB 的查詢相對較為簡易。

下列程式碼片段示範如何在 Spark 內容中使用 pyDocumentDB。

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

如程式碼片段所述：

* Azure Cosmos DB Python SDK (`pyDocumentDB`) 包含所有必要的連線參數。 例如，慣用的位置參數會選擇讀取複本和優先順序。
*  匯入必要的程式庫並設定 **masterKey** 和 **host**，以建立 Azure Cosmos DB「用戶端」(**pydocumentdb.document_client**)。


### <a name="execute-spark-queries-via-pydocumentdb"></a>透過 pyDocumentDB 執行 Spark 查詢
下列範例會使用上一個程式碼片段中，利用指定的唯讀金鑰建立的 Azure Cosmos DB 執行個體。 下列程式碼片段會連線至 **airports.codes** 集合 (在先前指定的 DoctorWho 帳戶中)，並執行查詢以擷取華盛頓州的機場城市。

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

透過 **query** 執行查詢之後，會產生已轉換成 Python 清單的 **query_iterable.QueryIterable**。 使用下列程式碼，可以輕鬆地將 Python 清單轉換成 Spark DataFrame：

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>為什麼要使用 pyDocumentDB 將 Spark 連線到 Azure Cosmos DB？
使用 pyDocumentDB 將 Spark 連線到 Azure Cosmos DB 一般適用於下列情況：

* 您想要使用 Python。
* 您要將相當小的結果集從 Azure Cosmos DB 傳回 Spark。 請注意，在 Azure Cosmos DB 中的基礎資料集可能相當大。 您要將篩選條件套用至 Azure Cosmos DB 來源，即執行述詞篩選條件。  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark 至 Azure Cosmos DB 連接器

「Spark 至 Azure Cosmos DB」連接器會利用 [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java)，並在 Spark 背景工作角色節點與 Azure Cosmos DB 之間移動資料，如下圖所示：

![「Spark 至 Azure Cosmos DB」連接器中的資料流程](./media/spark-connector/spark-connector.png)

資料流程如下︰

1. 從 Spark 主要節點連線到 Azure Cosmos DB 閘道節點，以取得分割區對應。 使用者指定僅限 Spark 和 Azure Cosmos DB 連線。 對於主要和閘道節點的個別連線對使用者皆為透明。
2. 此資訊會提供回 Spark 主要節點。  此時，您應該可以剖析查詢，來判斷您需要存取 Azure Cosmos DB 中的哪些分割區和其位置。
3. 此資訊會傳輸到 Spark 背景工作角色節點。
4. Spark 背景工作角色節點會直接連線到 Azure Cosmos DB 分割區以擷取資料，並將資料傳回 Spark 背景工作角色節點中的 Spark 分割區。

Spark 與 Azure Cosmos DB 之間的通訊速度大幅提升，這是因為資料移動是在 Spark 背景工作角色節點與 Azure Cosmos DB 資料節點 (分割區) 之間進行。

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>建置 Spark 至 Azure Cosmos DB 連接器
目前，連接器專案會使用 maven。 若要建置沒有相依性的連接器，您可以執行︰
```
mvn clean package
```
您也可以從 *releases* 資料夾下載最新版 JAR。

### <a name="include-the-azure-cosmos-db-spark-jar"></a>納入 Azure Cosmos DB Spark JAR
在執行任何程式碼之前，您需要納入 Azure Cosmos DB Spark JAR。  如果您是使用 **spark-shell**，則可以使用 [--jars] 選項來納入 JAR。  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

如果您想要執行不含相依性的 JAR，請使用下列程式碼：

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

如果您是使用 Notebook 服務 (例如 Azure HDInsight Jupyter Notebook 服務)，則可以使用 **spark magic** 命令：

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**jars** 命令可讓您納入 **azure-cosmosdb-spark** 所需的兩個 JAR (本身和 Azure DocumentDB Java SDK)，並排除 **scala-reflect**，讓它不要干擾 Livy 呼叫 (Jupyter Notebook > Livy > Spark)。

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>使用連接器將 Spark 連線到 Azure Cosmos DB
雖然通訊傳輸的複雜性稍微變高，但是使用連接器執行從 Spark 到 Azure Cosmos DB 的查詢速度獲得大幅提升。

下列程式碼片段示範如何在 Spark 內容中使用連接器。

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

如程式碼片段所述：

- **azure-cosmosdb-spark** 包含所有必要的連接參數，包括慣用的位置。 例如，您可以選擇讀取複本和優先順序。
- 直接匯入必要的程式庫，並設定 masterKey 和 host 以建立 Azure Cosmos DB 用戶端。

### <a name="execute-spark-queries-via-the-connector"></a>透過連接器執行 Spark 查詢

下列範例會使用上一個程式碼片段中，利用指定的唯讀金鑰建立的 Azure Cosmos DB 執行個體。 下列程式碼片段會連線至 DepartureDelays.flights_pcoll 集合 (在先前指定的 DoctorWho 帳戶中)，並執行查詢以擷取從西雅圖出發之航班的航班延遲資訊。

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>為什麼要使用 Spark 至 Azure Cosmos DB 連接器實作？

使用連接器將 Spark 連線到 Azure Cosmos DB 一般適用於下列情況：

* 您想要使用 Scala 並將它更新成包括 Python 包裝函式，如[問題 3︰新增 Python 包裝函式和範例](https://github.com/Azure/azure-cosmosdb-spark/issues/3) (英文) 中所述。
* 您有大量的資料要在 Apache Spark 與 Azure Cosmos DB 之間傳輸。

為了讓您了解查詢效能差異，請參閱[查詢測試回合 Wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs) (英文)。

## <a name="distributed-aggregation-example"></a>分散式彙總範例
本節提供一些範例，示範如何搭配使用 Apache Spark 與 Azure Cosmos DB 來執行分散式彙總和分析。 Azure Cosmos DB 已支援彙總，[全球級規模彙總與 Azure Cosmos DB 部落格文章](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) (英文) 中已進行相關討論。 您可以透過以下方法，使用 Apache Spark 讓其更上一層樓。

請注意，這些彙總皆與 [Spark 至 Azure Cosmos DB 連接器 Notebook](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb) 相關。

### <a name="connect-to-flights-sample-data"></a>連線至航班範例資料
這些彙總範例會存取儲存在 **DoctorWho** Azure Cosmos DB 資料庫中的一些航班效能資料。 若要與它連線，您需要使用下列程式碼片段︰

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

我們也會使用此程式碼片段來執行基底查詢，以將一組已篩選資料從 Azure Cosmos DB 傳輸到 Spark (後者可以執行分散式彙總)。 在此情況下，我們要求從西雅圖 (SEA) 出發的航班資料。

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

已從 Jupyter Notebook 服務執行查詢時，會產生下列結果。  請注意，所有程式碼片段都是泛型的，不是任何服務特有的。

### <a name="running-limit-and-count-queries"></a>執行 LIMIT 和 COUNT 查詢
就像在 SQL/Spark SQL 中使用一樣，讓我們開始使用 **LIMIT** 查詢︰

![Spark LIMIT 查詢](./media/spark-connector/spark-sql-query.png)

下一個查詢是簡單且快速的 **COUNT** 查詢：

![Spark COUNT 查詢](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY 查詢
在接下來的這一組中，我們可以對 Azure Cosmos DB 資料庫輕鬆執行 **GROUP BY** 查詢：

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY 查詢圖形](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>DISTINCT, ORDER BY 查詢
以下是 **DISTINCT, ORDER BY** 查詢：

![Spark GROUP BY 查詢圖形](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>繼續分析航班資料
您可以使用下列範例查詢來繼續分析航班資料︰

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>從西雅圖出發的前 5 個延遲目的地 (城市)
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark 前幾個延遲圖形](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>計算從西雅圖出發的目的地城市的中間延遲
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark 中間幾個延遲圖形](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>後續步驟

如果您還沒有從 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存放庫下載「Spark 至 Azure Cosmos DB」連接器，請進行下載並探索存放庫中的其他資源：

* [分散式彙總範例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples) (英文)
* [指令碼和 Notebook 範例](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (英文)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](http://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure HDInsight 上的 Apache Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) 文章。
