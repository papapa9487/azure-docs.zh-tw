---
title: "透過 Azure HDInsight 中的事件中樞使用 Apache Spark 串流 | Microsoft Docs"
description: "建立 Apache Spark 串流範例，說明如何將資料串流傳送到 Azure 事件中樞，然後使用 Scala 應用程式在 HDInsight Spark 叢集中接收這些事件。"
keywords: "apache spark 串流, spark 串流, spark 範例, apache spark 串流範例, 事件中樞 azure 範例, spark 範例"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 14d969ecaf1f24a0bb34da4abe78d83f08627796
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Apache Spark 串流：在 HDInsight 上使用 Spark 叢集處理來自 Azure 事件中樞的資料

在本文中，您可以建立 Apache Spark 串流範例，包含下列步驟︰

1. 您可以使用獨立的應用程式，將訊息內嵌到 Azure 事件中樞。

2. 透過兩種不同的方法，您可以使用 Azure HDInsight 上 Spark 叢集中執行的應用程式，即時擷取 [事件中樞] 中的訊息。

3. 您可以建置串流分析管線，將資料保存至不同的儲存系統，或從即時資料中取得見解。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="spark-streaming-concepts"></a>Spark 串流處理概念

如需 Spark 串流的詳細說明，請參閱 [Apache Spark 串流概觀](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)。 HDInsight 會將相同的串流功能帶入 Azure 上的 Spark 叢集。  

## <a name="what-does-this-solution-do"></a>此解決方案有哪些功能？

在本文中，若要建立 Spark 串流範例，請執行下列步驟：

1. 建立會接收事件串流的 Azure 事件中樞。

2. 執行會產生事件，並將其推送至 Azure 事件中樞的本機獨立應用程式。 會執行此作業的範例應用程式發佈於： [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)。

3. 在 Spark 叢集上遠端執行串流應用程式，該應用程式可從 Azure 事件中樞讀取串流事件，並執行各種資料處理/分析。

## <a name="create-an-azure-event-hub"></a>建立 Azure 事件中樞

1. 登入 [Azure 入口網站](https://ms.portal.azure.com)，然後按一下畫面左上方的 [新增]。

2. 按一下 [物聯網]，然後按一下 [事件中樞]。

    ![建立 Spark 串流範例的事件中樞](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "建立 Spark 串流範例的事件中樞")

3. 在 [建立命名空間]  刀鋒視窗中，輸入命名空間名稱。 選擇定價層 (基本或標準)。 此外，選擇要在其中建立資源的 Azure 訂用帳戶、資源群組和位置。 按一下 [建立]  來建立命名空間。

      ![提供 Spark 串流範例的事件中樞名稱](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "提供 Spark 串流範例的事件中樞名稱")

    > [!NOTE]
    > 您應該選取與 HDInsight 中 Apache Spark 叢集相同的 **位置** ，以便降低延遲的情況和成本。
    >
    >

4. 在事件中樞命名空間清單中，按一下新建立的命名空間。      


5. 在 命名空間 刀鋒視窗中，按一下 事件中樞，然後按一下+ 事件中樞 來建立新的事件中樞。
   
    ![建立 Spark 串流範例的事件中樞](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "建立 Spark 串流範例的事件中樞")

6. 輸入事件中樞的名稱、將分割區計數設為 10，並將訊息保留期設為 1。 我們並未在此解決方案中保存訊息，因此您可以讓其餘項目保留為預設值，然後按一下建立。
   
    ![提供 Spark 串流範例的事件中樞詳細資料](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "提供 Spark 串流範例的事件中樞詳細資料")

7. 新建立的事件中樞會列在 [事件中樞] 刀鋒視窗中。
    
     ![檢視 Spark 串流範例的事件中樞](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "檢視 Spark 串流範例的事件中樞")

8. 回到命名空間刀鋒視窗 (而不是特定事件中樞刀鋒視窗)，按一下 共用存取原則，然後按一下RootManageSharedAccessKey。
    
     ![設定 Spark 串流範例的事件中樞原則](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "設定 Spark 串流範例的事件中樞原則")

9. 按一下複製按鈕，將 **RootManageSharedAccessKey** 主要金鑰和連接字串複製到剪貼簿。 儲存這些項目，以便稍後在本教學課程中使用。
    
     ![檢視 Spark 串流範例的事件中樞原則金鑰](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "檢視 Spark 串流範例的事件中樞原則金鑰")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>使用範例 Scala 應用程式將訊息傳送至 Azure 事件中樞

在本節中，您會使用獨立的本機 Scala 應用程式來產生事件的串流，並將它傳送至您稍早所建立的 Azure 事件中樞。 此應用程式可從 GitHub 取得，網址是： [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)。 以下步驟假設您已分接此 GitHub 儲存機制。

1. 請務必在您執行此應用程式的電腦上安裝下列項目。

    * Oracle Java Development Kit。 您可以從[這裡](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安裝它。
    * Apache Maven。 您可以從 [這裡](https://maven.apache.org/download.cgi)下載。 您可以在[這裡](https://maven.apache.org/install.html)取得安裝 Maven 的指示。

2. 開啟命令提示字元並瀏覽至您針對範例 Scala 應用程式複製 GitHub 存放庫的位置，並執行下列命令來建置應用程式。

        mvn package

3. 應用程式的輸出 jar **com-microsoft-azure-eventhubs-client-example-0.2.0.jar** 會建立在 **/target** 目錄下。 您稍後可以使用本文中的這個 JAR 來測試完整的解決方案。

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>建立應用程式以接收事件中樞傳入 Spark 叢集的訊息 

我們有兩種方法來連接 Spark 串流和 Azure 事件中樞，分別是收件者型連線與 Direct-DStream 型連線。 Direct-DStream 型在 2017 年 1 月引進，版本為 2.0.3。 它應該取代原始收件者型連線，因為它的效能更好且更節省資源。 如需更多詳細資料，請參閱 [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs)。 Direct DStream 只支援 Spark 2.0+。

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>建置具有 spark-eventhubs 連接器相依性的應用程式

我們也會在 GitHub 中發行臨時版本的 Spark-EventHubs。 若要使用臨時版本的 Spark-EventHubs，第一個步驟是透過在 pom.xml 加入下列項目，將 GitHub 指定為來源存放庫：

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

然後，您可以將下列相依性加入您的專案，以取得發行前版本。

Maven 相依性

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

SBT 相依性

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Direct DStream 連線

包含使用 Direct DStream 範例的預先建立 jar 檔案可在 [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar) 中下載。

Jar 檔案包含三個範例，其原始程式碼位於 [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream)。

將 [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) 作為範例：

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

在上面的範例中，`eventhubParameters` 是單一 EventHubs 執行個體特有的參數，您必須將它傳遞給 `createDirectStreams` API，該 API 會建構對應至 Event Hubs 命名空間的 Direct DStream 物件。 透過 Direct DStream 物件，您可以呼叫由 Spark 串流 API 架構提供的任何 DStream API。 在此範例中，我們會計算最後 3 個微批次間隔內每個字的頻率。

### <a name="receiver-based-connection"></a>收件者型連線

以 Scala 撰寫的 Spark 串流範例應用程式 (它會接收事件並路由傳送至不同的目的地) 可在下列位置取得：[https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)。 請遵循下列步驟來更新事件中樞設定的應用程式，並建立輸出 jar。

1. 啟動 IntelliJ IDEA，並在啟動畫面中選取 從版本控制簽出，然後按一下Git。
   
    ![Apache Spark 串流範例 - 從 Git 取得來源](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Apache Spark 串流範例 - 從 Git 取得來源")

2. 在 複製儲存機制 對話方塊中，提供要從中複製之 Git 儲存機制的 URL、指定要複製到的目錄，然後按一下複製。
   
    ![Apache Spark 串流範例 - 從 Git 複製](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Apache Spark 串流範例 - 從 Git 複製")
3. 依照提示操作，直到專案複製完成。 按 **Alt + 1** 以開啟 [專案檢視]。 其內容應如下所示。
   
    ![Apache Spark 串流範例 - 專案檢視](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Apache Spark 串流範例 - 專案檢視")
4. 請確定以 Java8 編譯應用程式程式碼。 若要這樣做，請按一下 [檔案]，按一下 [專案結構]，然後在 [專案] 索引標籤上，確定 [專案語言層級] 設定為 [8 - Lambda、類型註解等]。
   
    ![Apache Spark 串流範例 - 設定編譯器](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Apache Spark 串流範例 - 設定編譯器")
5. 開啟 **pom.xml** ，並確定 Spark 版本是正確的。 在 `<properties>` 節點下尋找下列程式碼片段，並確認 Spark 版本。

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. 應用程式需要稱為 **JDBC 驅動程式 jar** 的相依性 jar。 必須要有此項目，才能將接收自事件中樞的訊息寫入至 Azure SQL 資料庫。 您可以從[這裡](https://msdn.microsoft.com/sqlserver/aa937724.aspx)下載此 jar (4.1 或更新版本)。 在專案程式庫中新增此 jar 的參考。 執行下列步驟：
     
     1. 在已開啟應用程式的 [IntelliJ IDEA] 視窗中，依序按一下 [檔案]、[專案結構] 和 [程式庫]。 
     2. 按一下 [新增] 圖示 (![新增圖示](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png))、按一下 [Java]，然後導覽至您下載 JDBC 驅動程式 jar 的位置。 依照提示，將 jar 檔案新增至專案程式庫。

         ![新增遺失的相依性](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "新增遺失的相依性 jar")
     3. 按一下 [Apply (套用)] 。

7. 建立輸出 jar 檔案。 請執行下列步驟：

   1. 在 [專案結構] 對話方塊中，按一下 [構件]，然後按一下加號。 在快顯對話方塊中按一下 JAR，然後按一下從具有相依性的模組。      
       
       ![Apache Spark 串流範例 - 建立 jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Apache Spark 串流範例 - 建立 jar")
   2. 在 [從模組建立 JAR] 對話方塊中，對 [主要類別] 按一下省略符號 (![ellipsis](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png))。
   3. 在 選取主要類別 對話方塊中，選取任何可用的類別，然後按一下確定。
      
       ![Apache Spark 串流範例 - 選取 jar 的類別](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Apache Spark 串流範例 - 選取 jar 的類別")
   4. 在 從模組建立 JAR 對話方塊中，確定已選取 擷取至目標 JAR選項，然後按一下確定。 這會建立具有所有相依性的單一 JAR。
      
       ![Apache Spark 串流範例 - 從模組建立 jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Apache Spark 串流範例 - 從模組建立 jar")
   5. [輸出配置] 索引標籤會列出所有納入 Maven 專案中的 jar。 您可以選取並刪除 Scala 應用程式未直接依存的 jar。 對於我們在這裡建立的應用程式，您可以移除最後一個 jar (**spark-streaming-data-persistence-examples 編譯輸出**) 以外的所有 jar。 選取要刪除的 jar，然後按一下刪除 圖示 (![delete icon](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png))。
      
       ![Apache Spark 串流範例 - 將擷取的 jar 刪除](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Apache Spark 串流範例 - 將擷取的 jar 刪除")
      
       請確實選取 [在建置時建立]  方塊，以確保在每次建置或更新專案時都會建立 jar。 按一下 [Apply (套用)] 。
   6. 在 [輸出配置] 索引標籤中的 [可用的項目] 方塊右下方，會有您先前新增至專案程式庫的 SQL JDBC jar。 您必須將此新增至 輸出配置  索引標籤。以滑鼠右鍵按一下 jar 檔案，然後按一下解壓縮到輸出根目錄中 。
      
       ![Apache Spark 串流範例 - 擷取相依性 jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Apache Spark 串流範例 - 擷取相依性 jar")  
      
       [輸出配置]  索引標籤此時應顯示如下。
      
       ![Apache Spark 串流範例 - 最終輸出索引標籤](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Apache Spark 串流範例 - 最終輸出索引標籤")        
      
       在 專案結構 對話方塊中，按一下 套用，然後按一下確定。    
   7. 在功能表列中按一下 建置，然後按一下建立專案。 您也可以按一下 [建置構件]，以建立 jar。 輸出 jar 會建立在 **\classes\artifacts** 下。
      
       ![Apache Spark 串流範例 - 輸出 jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Apache Spark 串流範例 - 輸出 jar")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>使用 Livy 在 Spark 叢集上遠端執行應用程式

在本文中，您會使用 Livy，在 Apache Spark 叢集上從遠端執行串流應用程式。 如需如何搭配使用 Livy 與 HDInsight Spark 叢集的詳細討論，請參閱 [從遠端將作業提交到 Azure HDInsight 上的 Apache Spark 叢集](hdinsight-apache-spark-livy-rest-interface.md)。 您必須先完成若干作業後，才能開始執行 Spark 串流應用程式：

1. 啟動本機獨立應用程式以產生事件，並將其傳送至事件中樞。 請使用下列命令來執行此動作：

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. 將串流處理 jar (**spark-streaming-data-persistence-examples.jar**) 複製到與叢集相關聯的 Azure Blob 儲存體。 如此，jar 即可供 Livy 存取。 您可以使用命令列公用程式 [**AzCopy**](../storage/common/storage-use-azcopy.md) 來執行此動作。 此外也有很多用戶端可用來上傳資料。 您可以在 [在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)中找到其詳細資訊。
3. 將 CURL 安裝在您用來執行這些應用程式的電腦上。 我們使用 CURL 來叫用 Livy 端點，以從遠端執行作業。

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>執行 Spark 串流應用程式，以文字形式將事件接收到 Azure 儲存體 Blob 中

開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputBlob.txt** 中的參數定義如下：

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

我們要了解，輸入檔案中的參數為何：

* **file** 是與叢集相關聯的 Azure 儲存體帳戶上的應用程式 jar 檔案的路徑。
* **className** 是 jar 中的類別名稱。
* **args** 是類別所需的引數清單
* **numExecutors** 是 Spark 用來執行串流應用程式的核心數目。 此數目一律至少應為事件中樞資料分割數目的兩倍。
* **executorMemory**、**executorCores**、**driverMemory** 是用來將必要資源指派給串流應用程式的參數。

> [!NOTE]
> 您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10)。 串流應用程式會為您建立。
>
>

執行命令時，您應該會看到如下的輸出：

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

請記下位於輸出中最後一行的批次識別碼 (在此範例中為 '1')。 若要確認應用程式已成功執行，您可以查看與叢集相關聯的 Azure 儲存體帳戶，您應該會看到該處已建立 **/EventCount/EventCount10** 資料夾。 此資料夾應包含相關 blob，其中擷取在為參數 **batch-interval-in-seconds**指定的時段內所處理的事件數目。

Spark 串流應用程式會繼續執行，直到您終止為止。 若要這麼做，請使用下列命令：

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>執行應用程式，以將事件以 JSON 的形式接收到 Azure 儲存體 Blob 中
開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputJSON.txt** 中的參數定義如下：

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

這些參數類似於您在先前的步驟中為文字輸出指定的參數。 同樣地，您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10)。 串流應用程式會為您建立。

 在執行命令之後，您可以查看與叢集相關聯的 Azure 儲存體帳戶，您應該會看到該處已建立 **/EventStore10** 資料夾。 開啟任何以 **part-** 開頭的檔案，您應該會看到以 JSON 格式處理的事件。

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>執行應用程式，以將事件接收到 Hive 資料表中
若要執行會將事件串流至 Hive 資料表中的 Spark 串流應用程式，您需要一些其他元件。 它們是：

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

這些 **.jar** 檔案位於您的 HDInsight Spark 叢集上：`/usr/hdp/current/spark-client/lib`。 **hive-site.xml** 位於 `/usr/hdp/current/spark-client/conf`。

您可以使用 [WinScp](http://winscp.net/eng/download.php) 將這些檔案從叢集複製到您的本機電腦。 接著，您可以使用工具，將這些檔案複製到與叢集相關聯的儲存體帳戶。 如需關於如何將檔案上傳至儲存體帳戶的詳細資訊，請參閱 [在 HDInsight 上將 Hadoop 作業的資料上傳](hdinsight-upload-data.md)。

將檔案複製到您的 Azure 儲存體帳戶之後，請開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令 (取代使用者名稱/密碼與叢集名稱)：

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputHive.txt** 中的參數定義如下：

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

這些參數類似於您在先前的步驟中為文字輸出指定的參數。 同樣地，您不需要建立做為參數的輸出資料夾 (EventCheckpoint、EventCount/EventCount10) 或輸出 Hive 資料表 (EventHiveTable10)。 串流應用程式會為您建立。 請注意，**jars** 和 **files** 選項會包含您已複製到儲存體帳戶的 .jar 檔案和 hive-site.xml 的路徑。

若要確認已成功建立 hive 資料表，請使用 [Ambari Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md)。 您也可以執行 SELECT 查詢，以檢視資料表的內容。

    SELECT * FROM eventhivetable10 LIMIT 10;

您應該會看到如下的輸出：

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>執行應用程式，以將事件接收到 Azure SQL Database 資料表中
執行此步驟之前，請先確定您已建立 Azure SQL Database。 如需指示，請參閱[快速建立 SQL 資料庫](../sql-database/sql-database-get-started.md)。 為了完成本節，您需要資料庫名稱、資料庫伺服器名稱和資料庫系統管理員認證的值做為參數。 但您不需要建立資料庫資料表。 Spark 串流應用程式會為您建立。

開啟命令提示字元，導覽至您安裝 CURL 的目錄，然後執行下列命令：

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

檔案 **inputSQL.txt** 中的參數定義如下：

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

若要驗證應用程式是否順利執行，您可以使用 SQL Server Management Studio 連接到 Azure SQL Database。 如需如何執行該動作的指示，請參閱 [使用 SQL Server Management Studio 連接到 SQL Database](../sql-database/sql-database-connect-query-ssms.md)。 連接到資料庫之後，您可以導覽至串流應用程式所建立的 **EventContent** 資料表。 您可以執行快速查詢，以取得該資料表中的資料。 請執行下列查詢：

    SELECT * FROM EventCount

您應該會看到如下所示的輸出：

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)
* [收件者型連線和 Direct DStream 的設計](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式
* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式遠端偵錯 Spark 應用程式](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
