---
title: "透過 Azure HDInsight 中的事件中樞使用 Apache Spark 結構化串流 | Microsoft Docs"
description: "建立 Apache Spark 串流範例，說明如何將資料串流傳送到 Azure 事件中樞，然後使用 Scala 應用程式在 HDInsight Spark 叢集中接收這些事件。"
keywords: "apache spark 串流, spark 串流, spark 範例, apache spark 串流範例, 事件中樞 azure 範例, spark 範例"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: a542295e91a641289fa4261920a08eddbad6a217
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>使用 HDInsight 上的 Apache Spark 結構化串流處理來自事件中樞的事件

在本文中，您將了解如何使用 Spark 結構化串流處理即時遙測。 若要完成此動作，須執行下列高階步驟：

1. 在您的本機工作站上編譯並執行事件產生器應用程式範例，其會產生要傳送至事件中樞的事件。
2. 使用 [Spark 殼層](apache-spark-shell.md)定義並執行簡單的 Spark 結構化串流應用程式。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

* Azure 事件中樞命名空間。 如需詳細資訊，請參閱[建立 Azure 事件中樞命名空間](apache-spark-eventhub-streaming.md#create-an-azure-event-hub)。

* Oracle Java Development Kit。 您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安裝它。

* Apache Maven。 您可以從 [這裡](https://maven.apache.org/download.cgi)下載。 您可以在[這裡](https://maven.apache.org/install.html)取得安裝 Maven 的指示。

## <a name="build-configure-and-run-the-event-producer"></a>建置、設定和執行事件產生器
在此工作中，您可以複製應用程式範例，其會建立隨機事件並將這些事件傳送至已設定的事件中樞。 此應用程式範例可從 GitHub 取得，網址是：[https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)。

1. 請確定您已安裝 git 工具。 您可以從 [GIT 下載](http://www.git-scm.com/downloads)進行下載。 
2. 開啟命令提示字元或終端機殼層，從您選擇要複製專案的目錄執行下列命令。
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. 此動作會建立名為 eventhubs-client-sample 的新資料夾。 在殼層或命令提示字元中，瀏覽至此資料夾。
4. 執行 Maven 以使用下列命令建置應用程式：

          mvn package

5. 在殼層或命令提示字元中，瀏覽至已建立且包含 ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar`` 檔案的目標目錄。
6. 接著，您必須根據事件中樞，建置執行事件產生器的命令列。 取代命令中的值即可完成此動作，如下所示：

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. 例如，完整的命令看起來會如下所示：

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. 命令啟動，如果您的設定正確，幾分鐘後您會看到傳送到事件中樞的事件相關輸出，如下所示：

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. 事件產生器執行的同時，您可以繼續進行步驟。

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>在您的 HDInsight 叢集上執行 Spark 殼層
在此工作中，您會透過 SSH 連線至您 HDInsight 叢集的前端節點、啟動 Spark 殼層，以及執行 Spark 串流應用程式，來擷取與處理來自事件中樞的事件。 

此時，您的 HDInsight 叢集應已就緒。 如果沒有，您需要等到佈建完成。 一旦就緒，您就可以繼續執行下列步驟：

1. 使用 SSH 連線到 HDInsight 叢集。 如需指示，請參閱[使用 SSH 連線至 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)。

5. 您所建置的應用程式需要 Spark 串流事件中樞封裝。 若要執行 Spark Shell，讓它自動從 [Maven 中心](https://search.maven.org)擷取此相依性，請確定封裝，與 Maven 座標的切換供應如下所示：

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. Spark Shell 完成載入時，您應該會看到：

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. 將下列程式碼片段複製到文字編輯器並加以修改，使其具有為您事件中樞設定的合適原則金鑰及命名空間。

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. 將修改過的程式碼片段貼入等候的 scala> 提示，並按下傳回。 您應該會看到如下的輸出：

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. 接下來，開始撰寫指定來源的 Spark 結構化串流查詢。 將下列內容貼入 Spark 殼層並按下傳回。

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. 您應該會看到如下的輸出：

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. 接下來，撰寫查詢讓其將輸出寫入主控台。 您可以透過將下列內容貼入 Spark 殼層並按下傳回來完成此動作。

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. 您應該會看到開頭類似下面的一些輸出批次

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. 這後面會接著每一微批次事件的處理輸出結果。 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. 當新的事件從事件產生器送達後，便會由此結構化串流查詢進行處理。
15. 當您完成執行此範例後，請務必刪除您的 HDInsight 叢集。



## <a name="see-also"></a>另請參閱

* [Spark 串流的概觀](apache-spark-streaming-overview.md)













