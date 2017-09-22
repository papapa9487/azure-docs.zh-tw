---
title: "Azure HDInsight 中的 Spark 串流是什麼| Microsoft Docs"
description: "如何在 HDInsight Spark 叢集上使用 Spark 串流應用程式。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 7608951ffb79d945ce3271792e7cf1a0d0b254b3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="overview-of-spark-streaming"></a>Spark 串流的概觀

Spark 串流可在 HDInsight Spark 叢集上提供資料流處理，並保證無論是否發生節點錯誤，任何輸入事件都可確實地處理一次。 Spark 串流是從廣泛來源接收輸入資料的長時間執行作業，來源包括 Azure 事件中樞、Azure IoT 中樞、Kafka、Flume、Twitter、ZeroMQ、原始 TCP 通訊端，或來自監視 HDFS 檔案系統。 不同於完全事件驅動的流程，Spark 串流會依據時間範圍分批處理輸入資料 (例如 2 秒配量)，然後使用 map、reduce、join 和 extract 作業來轉換每個批次。 接著將轉換的資料寫出至檔案系統、資料庫、儀表板和主控台。

![使用 HDInsight 和 Spark 串流處理資料流](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark 串流應用程式必須等待幾毫秒，才可收集每個*微批次*事件，然後才能轉送該批次以進行處理。 相較之下，事件驅動的應用程式會立即處理每個事件。 而 Spark 串流通常會延遲幾秒鐘。 微批次方法的優點是可更有效率的處理資料和更簡易的彙總運算。

## <a name="introducing-the-dstream"></a>DStream 簡介

Spark 串流會使用名為 DStream 的*離散化資料流*表示傳入資料的連續資料流。 DStream 可以從事件中樞或 Kafka 的輸入來源建立，或將轉換套用至另一個 DStream，來建立 DStream。

DStream 會在原始事件資料的頂端提供抽象層。 

先以單一事件為例，例如從連接的控溫器讀取溫度。 當此事件抵達 Spark 串流處理應用程式時，事件會以可靠的方式儲存，也就是在多個節點上複寫事件。 此容錯方式可確保任何單一節點的錯誤不會造成事件遺失。 Spark 核心使用的資料結構會將資料散布到叢集中多個節點上，其中每個節點通常都會在記憶體內部維護自己的資料，以達到最好的效能。 此資料結構稱為*彈性分散式資料集* (resilient distributed dataset, RDD)。

每個 RDD 皆代表在使用者定義的時間範圍 (稱為批次間隔) 內收集的事件。 當每個批次間隔過去後，新的 RDD 就會產生，並包含該間隔中的所有資料。 一組連續的 RDD 會被收集到 DStream。 例如，如果批次間隔長度為一秒，您的 DStream 就會每秒發出包含一個 RDD 的批次，該 RDD 會包含在這一秒期間內嵌的所有資料。 處理 DStream 時，溫度事件就會出現在這些批次的其中一個。 Spark 串流應用程式會處理包含事件的批次，並在最後處理儲存在每個 RDD 中的資料。

![DStream 與溫度事件的範例 ](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark 串流應用程式的結構
Spark 串流應用程式是長時間執行的應用程式，其會接收來自內嵌來源的資料，接著套用轉換以處理資料，然後將資料推送至一個或多個目的地。 Spark 串流應用程式的結構有兩個主要部分。 第一，定義資料來源、資料的處理動作和結果的去處。 第二，無限期執行應用程式，等待停止訊號。

我們以下列的簡易應用程式為例，該應用程式會透過 TCP 通訊端接收一行文字，並計算每個字彙出現的次數。

### <a name="define-the-application"></a>定義應用程式

應用程式邏輯定義包含四個步驟：

1. 建立 StreamingContext。
2. 從 StreamingContext 建立 DStream。
3. 將轉換套用至 DStream。
4. 輸出結果。

此定義是靜態的，而且在您執行應用程式之前不會處理任何資料。

#### <a name="create-a-streamingcontext"></a>建立 StreamingContext
從指向您叢集的 SparkContext 建立 StreamingContext。 建立 StreamingContext 時，您可以指定批次大小 (以秒為單位)，例如：

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>建立 DStream
使用您建立的 StreamingContext 執行個體，為您的輸入來源建立輸入 DStream。 在此案例中，我們會監視新檔案在預設儲存體 (已連結至 HDInsight 叢集) 中出現的狀況。

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>套用轉換
在 DStream 上套用轉換後，您就可以實作處理作業。 我們的應用程式會從檔案一次接收一行文字，再將每一行切割成字彙，然後使用 map-reduce 模式來計算每個字彙出現的次數。

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>輸出結果
藉由套用輸出作業，將轉換結果推送至目的地系統。 在此案例中，我們會透過主控台輸出中的計算，顯示每個執行的結果。

    wordCounts.print()

### <a name="run-the-application"></a>執行應用程式
啟動串流應用程式並執行，直到收到終止訊號。

    ssc.start()            
    ssc.awaitTermination()

如需深入了解 Spark 串流 API 和其支援的事件來源、轉換及輸出作業，請參閱 [Spark 串流程式設計指南](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)。

下列的範例應用程式皆各自獨立，因此您可以在 [Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md) 中加以執行。 在範例中，我們在 DummySource 類別中建立虛擬資料來源，DummySource 會輸出計數器的值，以及每五秒輸出目前時間 (以毫秒計算)。 我們會建立新的 StreamingContext 物件，其中有 30 秒的批次間隔。 每當批次建立時，產生的 RDD 會經過檢查並轉換為 Spark DataFrame，然後透過 DataFrame 建立暫存資料表。

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

然後，我們可以定期查詢 DataFrame，以查看目前在批次中的一組值。 在此案例中，我們使用下列命令：

    %%sql
    SELECT * FROM demo_numbers

輸出結果顯示如下：

| value | 分析 |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

我們預期會看見六個值，因為 DummySource 每隔 5 秒會建立值，而我們每隔 30 秒發出批次。

## <a name="sliding-windows"></a>滑動時間範圍
如果您要在 DStream 上執行某一段時間的彙總計算，例如想取得過去 2 秒間的平均溫度，您可以使用 Spark 串流中的滑動時間範圍作業。 滑動時間範圍定義為有期間 (範圍長度) 及間隔，而時間範圍的內容會在該間隔期間受到評估 (滑動間隔)。

滑動時間範圍可以重疊，例如，您可以定義長度為 2 秒且每 1 秒滑動的時間範圍。 這表示每次執行彙總計算時，該時間範圍會包含前一個時間範圍的最後 1 秒資料和下 1 秒鐘的任何新資料。

![初始時間範圍和溫度事件範例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![滑動後的時間範圍和溫度事件範例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

在下列範例中，我們會更新使用 DummySource 的程式碼，將批次集合到期間為一分鐘和滑動為一分鐘的時間範圍內。

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

這會在第一分鐘後產生 12 個項目 － 該時間範圍內收集的兩個批次中，每一個批次有六個項目。

| value | 分析 |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

可在 Spark 串流 API 中使用的滑動時間範圍函式包括 window、countByWindow、reduceByWindow 和 countByValueAndWindow。 如需這些函式的詳細資訊，請參閱 [DStream 上的轉換](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="checkpointing"></a>檢查點
為了提供備援和容錯，Spark 串流會藉由檢查點來確保資料流處理持續不中斷 (即使發生節點錯誤)。 在 HDInsight 中，Spark 會在可靠的儲存體 (Azure 儲存體或 Data Lake Store) 上建立檢查點。 這些檢查點會儲存有關串流應用程式的中繼資料，例如組態、由應用程式定義的作業，以及任何在佇列中尚未處理的批次。 在某些情況下，檢查點也會儲存 RDD 中的資料，以避免還要費時地從 Spark 管理的 RDD 內容重建資料狀態。

## <a name="deploying-spark-streaming-applications"></a>部署 Spark 串流處理應用程式
您通常會在本機建立 Spark 串流應用程式，然後藉由將包含您應用程式的 JAR 檔案複製到預設儲存體 (已連結至您的 HDInsight 叢集)，來將其部署至 HDInsight 上的 Spark。 然後您就可以使用叢集適用的 LIVY REST API 來啟動應用程式。 這是 POST 作業，其中 POST 的主體包含 JSON 文件，該文件會提供 JAR 路徑、類別名稱 (該類別的主要行為會定義和執行串流應用程式)，和選擇性的作業資源要求 (如執行程式、記憶體和核心數目)，以及您應用程式程式碼所需的任何組態設定。

![滑動後的時間範圍和溫度事件範例](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

您也可以藉由 LIVY 端點，使用 GET 要求來檢查所有應用程式的狀態。 最後，您可以藉由 LIVE 端點來發出 DELETE 要求，終止執行應用程式。 如需 LIVY API 的詳細資訊，請參閱[使用 LIVY 執行遠端作業](hdinsight-apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>後續步驟

* [在 HDInsight 中建立 Apache Spark 叢集](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark 串流程式設計指南](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [使用 LIVY 從遠端啟動 Spark](hdinsight-apache-spark-livy-rest-interface.md)
