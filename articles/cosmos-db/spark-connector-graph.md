---
title: "Azure Cosmos DB︰使用 Spark 和 Apache TinkerPops Gremlin 執行圖表分析 | Microsoft Docs"
description: "本文提供在 Azure Cosmos DB 中使用 Spark 和 TinkerPop SparkGraphComputer 設定及執行圖形分析和平行計算的指引。"
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: 3ba34ac177cb9b88030be85288e3b3a429d4fd8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB︰使用 Spark 和 Apache TinkerPop Gremlin 執行圖表分析

[Azure Cosmos DB](introduction.md) 是 Microsoft 的全域分散式多模型資料庫服務。 您可以建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 Azure Cosmos DB 支援使用 [Apache TinkerPop Gremlin](graph-introduction.md) 的線上交易處理 (OLTP) 圖形工作負載。

[Spark](http://spark.apache.org/) 是著重於一般用途線上分析處理 (OLAP) 資料處理的 Apache Software Foundation 專案。 Spark 會提供混合式記憶體內/磁碟型分散式運算模型，其類似於 Hadoop MapReduce 模型。 您可以使用 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) 在雲端部署 Apache Spark。

結合 Azure Cosmos DB 與 Spark，您可以使用 Gremlin 執行 OLTP 和 OLAP 工作負載。 本快速入門文章示範如何在 Azure HDInsight Spark 叢集上對 Azure Cosmos DB 執行 Gremlin 查詢。

## <a name="prerequisites"></a>必要條件

您必須具備下列必要條件，才能執行此範例：
* Azure HDInsight Spark 叢集 2.0
* JDK 1.8+ (如果您沒有 JDK，則執行 `apt-get install default-jdk`。)
* Maven (如果您沒有 Maven，則執行 `apt-get install maven`。)
* Azure 訂用帳戶 ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

如需有關如何設定 Azure HDInsight Spark 叢集的詳細資訊，請參閱[佈建 HDInsight 叢集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="create-an-azure-cosmos-db-database-account"></a>建立 Azure Cosmos DB 資料庫帳戶

首先，執行下列動作以使用圖形 API 建立資料庫帳戶：

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>新增集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>取得 Apache TinkerPop

執行下列動作來取得 Apache TinkerPop：

1. 在 HDInsight 叢集 `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net` 的主要節點遠端。

2. 複製 TinkerPop3 來源程式碼，在本機加以建置，並將它安裝至 Maven 快取。

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. 安裝 Spark-Gremlin 外掛程式 

    a. 此外掛程式的安裝是由 Grape 處理。 填入 Grape 的存放庫資訊，才能下載外掛程式和其相依性。 

      建立 Grape 組態檔 (如果它未顯示於 `~/.groovy/grapeConfig.xml`)。 套用下列設定：

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b.這是另一個 C# 主控台應用程式。 啟動 Gremlin 主控台 `bin/gremlin.sh`。
        
    c. 使用您在先前步驟中建立的 3.3.0-SNAPSHOT 版來安裝 Spark-Gremlin 外掛程式：

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. 檢查 `Hadoop-Gremlin` 是否已啟動 `:plugin list`。 停用此外掛程式，因為它可能會干擾 Spark-Gremlin 外掛程式 `:plugin unuse tinkerpop.hadoop`。

## <a name="prepare-tinkerpop3-dependencies"></a>準備 TinkerPop3 相依性

當您在上一個步驟中建置 TinkerPop3 時，處理序也會提取目標目錄中 Spark 與 Hadoop 的所有 jar 相依性。 使用預先與 HDI 一起安裝的 jar，並視需要只提取其他相依性。

1. 移至位於 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone` 的 Gremlin 主控台目標目錄。 

2. 將 `ext/` 之下的所有 jar 移至 `lib/`：`find ext/ -name '*.jar' -exec mv {} lib/ \;`。

3. 移除 `lib/` 之下不在下列清單中的所有 jar 程式庫：

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>取得 Azure Cosmos DB Spark 連接器

1. 從 [GitHub 上的 Azure Cosmos DB Spark 連接器](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11)，取得 Azure Cosmos DB Spark 連接器`azure-documentdb-spark-0.0.3-SNAPSHOT.jar` 和 Cosmos DB Java SDK `azure-documentdb-1.10.0.jar`。

2. 或者，您可以在本機建置它。 因為最新版的 Spark-Gremlin 是使用 Spark 1.6.1 建置，且與 Azure Cosmos DB Spark 連接器中目前使用的 Spark 2.0.2 不相容，您可以建置最新的 TinkerPop3 程式碼及手動安裝 jar。 執行下列動作：

    a. 複製 Azure Cosmos DB Spark 連接器。

    b.這是另一個 C# 主控台應用程式。 建置 TinkerPop3 (已在先前步驟中完成)。 在本機安裝所有 TinkerPop 3.3.0-SNAPSHOT jar。

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. 將 `tinkerpop.version` `azure-documentdb-spark/pom.xml` 更新為 `3.3.0-SNAPSHOT`。
    
    d. 使用 Maven 建置。 所需的 jar 都會置於 `target` 和 `target/alternateLocation` 中。

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 將先前所提的 jar 複製到本機目錄 (位於 ~/azure-documentdb-spark)：

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>將相依性散發至 Spark 背景工作角色節點 

1. 因為圖形資料的轉換取決於 TinkerPop3，所以您必須將相關的相依性散發至所有 Spark 背景工作角色節點。

2. 執行下列動作，將先前所提的 Gremlin 相依性、CosmosDB Spark 連接器 jar 和 CosmosDB Java SDK 複製到背景工作節點：

    a. 將所有 jar 複製到 `~/azure-documentdb-spark` 中。

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b.這是另一個 C# 主控台應用程式。 在 `Spark2` 區段的 `Spark2 Clients` 清單中，取得所有 Spark 背景工作節點的清單 (您可以在 Ambari 儀表板上找到)。

    c. 將該目錄複製到每個節點。

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>設定環境變數

1. 尋找 Spark 叢集的 HDP 版本。 這是 `/usr/hdp/` 之下的目錄名稱 (例如 2.5.4.2-7)。

2. 設定所有節點的 hdp.version。 在 Ambari 儀表板中，移至 **YARN 區段** > [設定] > [進階]，然後執行下列動作： 
 
    a. 在 `Custom yarn-site` 中，於主要節點上新增一個屬性 `hdp.version`，其值為 HDP 版本。 
     
    b.這是另一個 C# 主控台應用程式。 儲存組態。 出現警告，您可以忽略。 
     
    c. 如通知圖示所指出，重新啟動 YARN 和 Oozie 服務。

3. 在主要節點上設定下列環境變數 (取代為適當的值)︰

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>準備圖形組態

1. 使用 Azure Cosmos DB 連線參數和 Spark 設定來建立組態檔，並將它放在 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`。

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. 更新 `spark.driver.extraClassPath` 和 `spark.executor.extraClassPath` 以包含您在上一個步驟中散發之 jar 的目錄，在此例中為 `/home/sshuser/azure-documentdb-spark/*`。

3. 提供 Azure Cosmos DB 的下列詳細資料︰

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>載入 TinkerPop 圖形，然後將它儲存到 Azure Cosmos DB
為了示範如何將圖形保存在 Azure Cosmos DB 中，此範例使用 TinkerPop 預先定義的 TinkerPop 新式圖形。 此圖形是以 Kryo 格式儲存，並且在 TinkerPop 存放庫中提供。

1. 因為您是以 YARN 模式執行 Gremlin，所以您必須在 Hadoop 檔案系統中提供圖形資料。 使用下列命令建立一個目錄，並將本機圖形檔案複製到其中。 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. 暫時更新 `gremlin-spark.properties` 檔案，以使用 `GryoInputFormat` 讀取圖形。 此外，指定 `inputLocation` 作為您建立的目錄，如下所示︰

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. 啟動 Gremlin 主控台，然後建立下列計算步驟，將資料保留於已設定的 Azure Cosmos DB 集合︰  

    a. 建立圖形 `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`。

    b.這是另一個 C# 主控台應用程式。 使用 SparkGraphComputer 寫入 `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`。

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. 在資料總管中，您可以確認資料已保存至 Cosmos DB。

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>從 Azure Cosmos DB 載入圖形，然後執行 Gremlin 查詢

1. 若要載入圖形，請編輯 `gremlin-spark.properties` 以將 `graphReader` 設定為 `DocumentDBInputRDD`：

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 執行下列動作，載入圖形、周遊資料，以及執行 Gremlin 查詢︰

    a. 啟動 Gremlin 主控台 `bin/gremlin.sh`。

    b.這是另一個 C# 主控台應用程式。 使用組態 `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')` 建立圖形。

    c. 使用 SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)` 建立圖形周遊。

    d. 執行下列 Gremlin 圖形查詢︰

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> 若要查看更詳細的記錄，請將 `conf/log4j-console.properties` 中的記錄層級設定為更詳細的層級。
>

## <a name="next-steps"></a>後續步驟

在本快速入門文章中，您已經了解如何結合 Azure Cosmos DB 與 Spark 來處理圖形。

> [!div class="nextstepaction"]
