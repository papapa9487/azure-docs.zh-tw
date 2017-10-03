---
title: "什麼是 HDInsight、Hadoop 技術堆疊和叢集？ - Azure | Microsoft Docs"
description: "HDInsight 和 Hadoop 技術堆疊和元件簡介，包括用於巨量資料分析的 Spark、Kafka、Hive、HBase。"
keywords: "azure hadoop, hadoop azure, hadoop 簡介, hadoop 簡介, hadoop 技術堆疊, hadoop 簡介, hadoop 簡介, 什麼是 hadoop 叢集, 什麼是 hadoop 叢集, 什麼是 hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Azure HDInsight、Hadoop 技術堆疊和 Hadoop 叢集簡介
 本文提供 Azure HDInsight (Hadoop 技術堆疊的雲端發佈) 的簡介。 它也涵蓋了 Hadoop 叢集的定義及其使用時機。 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>什麼是 HDInsight 和 Hadoop 技術堆疊？ 
Azure HDInsight 是 [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) 中 Hadoop 元件的雲端發佈。 [Apache Hadoop](http://hadoop.apache.org/) 是原始的開放原始碼建構，用於分散式處理和分析電腦叢集上的巨量資料集。 


Hadoop 技術堆疊包含相關的軟體和公用程式，其中包括 Apache Hive、HBase、Spark、Kafka 和其他許多軟體。 若要查看 HDInsight 上可用的 Hadoop 技術堆疊元件，請參閱 [HDInsight 可用的元件和版本][component-versioning]。 若要深入了解 HDInsight 中的 Hadoop，請參閱 [HDInsight 的 Azure 功能頁面](https://azure.microsoft.com/services/hdinsight/)。

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>什麼是 Hadoop 叢集，以及何時使用它？
*Hadoop* 也是具有下列各項的叢集類型：

* 用於作業排程和資源管理的 YARN
* 用於平行處理的 MapReduce
* Hadoop 分散式檔案系統 (HDFS)
  
Hadoop 叢集最常用於已儲存資料的批次處理。 HDInsight 中的其他叢集類型具有其他功能：Spark 因為其更快速的記憶體內部處理而日益普及。 如需詳細資訊，請參閱 [HDInsight 上的叢集類型](#overview)。

## <a name="what-is-big-data"></a>什麼是巨量資料？
巨量資料是指任何極為龐大的數位資訊，例如︰

* 工業設備的感應器資料
* 從網站收集的客戶活動
* Twitter 新聞摘要

巨量資料的收集量快速增加，收集速度加快，收集格式也愈來愈多。 它可以屬於過去 (亦即已儲存) 或即時 (亦即從來源串流而來)。 

## <a name="overview"></a>HDInsight 中的叢集類型
HDInsight 包含特定叢集類型和叢集自訂功能，例如新增元件、公用程式及語言。

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark、Kafka、互動式查詢、HBase、自訂和其他叢集類型
HDInsight 提供下列叢集類型：

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**︰使用 [HDFS](#hdfs)、[YARN](#yarn) 資源管理，以及簡單的 [MapReduce](#mapreduce) 程式設計模型來平行處理和分析批次資料。
* **[Apache Spark](http://spark.apache.org/)**：一種平行處理架構，可支援記憶體內的處理，大幅提升巨量資料分析應用程式、SQL 之 Spark 工作、串流資料與機器學習的效能。 請參閱[什麼是 HDInsight 中的 Apache Spark？](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**：建置於 Hadoop 上的 NoSQL 資料庫，可針對大量非結構化及半結構化資料 (可能是數十億的資料列乘以數百萬的資料行)，提供隨機存取功能和強大一致性。 請參閱[什麼是 HDInsight 上的 HBase？](hdinsight-hbase-overview.md)
* **[Microsoft R 伺服器](https://msdn.microsoft.com/microsoft-r/rserver)**︰可用來裝載和管理並行、分散式 R 程序的伺服器。 這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。 請參閱 [HDInsight 中的 R 伺服器概觀](hdinsight-hadoop-r-server-overview.md)。
* **[Apache Storm](https://storm.incubator.apache.org/)**：分散式、即時的運算系統，可快速處理大型的資料串流。 Storm 以受管理叢集的形式在 HDInsight 中提供。 請參閱＜ [使用 Storm 和 Hadoop 來分析即時感應器資料](hdinsight-storm-sensor-data-analysis.md)＞。
* **[Apache 互動式查詢預覽 (也稱為︰Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**︰更快速之互動式 Hive 查詢的記憶體內快取。 請參閱[在 HDInsight 中使用互動式查詢](hdinsight-hadoop-use-interactive-hive.md)。
* **[Apache Kafka](https://kafka.apache.org/)**︰用來建立串流資料管線和應用程式的開放原始碼平台。 Kafka 也提供訊息佇列功能，可讓您發佈和訂閱資料串流。 請參閱 [HDInsight 上的 Apache Kafka 簡介](hdinsight-apache-kafka-introduction.md)。

您也可以使用下列方法來設定叢集：
* **[已加入網域的叢集預覽](hdinsight-domain-joined-introduction.md)**︰Active Directory 網域中加入的叢集，如此您可以控制存取並提供對資料的管理。
* **[具指令碼動作的自訂叢集](hdinsight-hadoop-customize-cluster-linux.md)**：具指令碼的叢集，該指令碼於佈建期間執行，並安裝其他元件。

### <a name="example-cluster-customization-scripts"></a>範例叢集自訂指令碼
指令碼動作是在叢集佈建期間在 Linux 上執行的 Bash 指令碼，而且可用來在叢集上安裝其他元件。 

下列指令碼範例由 HDInsight 小組提供：

* **[Hue](hdinsight-hadoop-hue-linux.md)**：一組 Web 應用程式，可用來與叢集互動。 僅限於 Linux 叢集。
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**：物品與人員間之模型關聯性的圖形處理。
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**：一種企業級搜尋平台，可對資料進行全文檢索搜尋。

如需開發您自己的指令碼動作相關資訊，請參閱 [使用 HDInsight 開發指令碼動作](hdinsight-hadoop-script-actions-linux.md)。

## <a name="components-and-utilities-on-hdinsight-clusters"></a>HDInsight 叢集上的元件和公用程式
HDInsight 叢集包含下列元件和公用程式：

* **[Ambari](#ambari)**：叢集佈建、管理、監視和公用程式。
* **[Avro](#avro)** (Microsoft .NET Library for Avro)：Microsoft.NET 環境的資料序列化。 
* **[Hive & HCatalog](#hive)**：類似 SQL 的查詢，以及資料表和儲存體管理層。
* **[Mahout](#mahout)**：適用於可調整的機器學習應用程式。
* **[MapReduce](#mapreduce)**：Hadoop 分散式處理和資源管理的傳統架構。 請參閱 [YARN](#yarn)。
* **[Oozie](#oozie)**：工作流程管理。
* **[Phoenix](#phoenix)**：對 HBase 的關聯式資料庫層。
* **[Pig](#pig)**：簡單撰寫 MapReduce 轉換的指令碼。
* **[Sqoop](#sqoop)**：資料匯入和匯出。
* **[Tez](#tez)**：允許資料密集程序有效率地大規模執行。
* **[YARN](#yarn)**：屬於Hadoop 核心程式庫一部分的資源管理。
* **[ZooKeeper](#zookeeper)**：協調分散式系統中的程序。

> [!NOTE]
> 如需特定元件及版本的相關資訊，請參閱 [HDInsight 中的 Hadoop 元件和版本][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari 可用來佈建、管理及監視 Apache Hadoop 叢集。 其中包含一組直接易懂的操作員工具和健全的 API 集，可消除 Hadoop 的複雜性，並簡化叢集作業。 Linux 上的 HDInsight 叢集同時提供 Ambari Web UI 和 Ambari REST API。 HDInsight 叢集上的 Ambari 檢視允許外掛程式 UI 功能。
請參閱[使用 Ambari 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)和 <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API 參考</a>。

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)
Microsoft .NET Library for Avro 針對 Microsoft .NET 環境的序列化，實作 Apache Avro 壓縮二進位資料交換格式。 它會定義不限語言的結構描述，以某種語言序列化的資料便可使用另一個語言讀取。 如需此格式的詳細資訊，請參閱 <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro 規格</a>。 Avro 檔案格式支援分散式 MapReduce 程式設計模型：檔案「可分割」，這表示您可以從檔案中的任何一點開始讀取特定區塊。 如需了解作法，請參閱[使用 Microsoft .NET Library for Avro 將資料序列化](hdinsight-dotnet-avro-serialization.md)。 即將提供以 Linux 為基礎的叢集支援。

### <a name="hdfs"></a>HDFS
Hadoop Distributed File System (HDFS) 是一套檔案系統，加上 YARN 和 MapReduce，成為 Hadoop 技術的核心。 它是 HDInsight 上的 Hadoop 叢集的標準檔案系統。 請參閱[從 HDFS 相容儲存體查詢資料](hdinsight-hadoop-use-blob-storage.md)。

### <a name="hive"></a>Hive 和 HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> 是以 Hadoop 為基礎的資料倉儲軟體，可讓您使用一種稱為 HiveQL 的 SQL 式語言，以查詢和管理分散式儲存體中的大型資料集。 如同 Pig，Hive 是 MapReduce 的抽象概念，可將查詢轉譯成一系列的 MapReduce 作業。 Hive 比 Pig 更接近關聯式資料庫管理系統，而且適用於更具結構化的資料。 對於非結構化資料，Pig 是較好的選擇。 請參閱 [在 HDInsight 上將 Hive 與 Hadoop 搭配使用](hdinsight-use-hive.md)。

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> 是 Hadoop 的資料表和儲存體管理層，可為您提供資料的關聯式檢視。 在 HCatalog 中，您可以任何適用於 Hive SerDe (序列化程式-還原序列化程式) 的格式來讀寫和撰寫檔案。

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> 是在 Hadoop 上執行的機器學習演算法程式庫。 機器學習應用程式採用統計學的原理，教導系統從資料中學習，根據過去的結果來研判未來的行為。 請參閱＜ [在 Hadoop 上使用 Mahout 來產生電影推薦](hdinsight-mahout.md)＞。

### <a name="mapreduce"></a>MapReduce
MapReduce 是供 Hadoop 撰寫應用程式來以平行方式批次處理巨量資料集的傳統軟體架構。 MapReduce 工作可分割大型資料集，並將資料組織成機碼值組以便於處理。 MapReduce 作業會在 [YARN](#yarn) 上執行。 請參閱 Hadoop Wiki 中的 <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a>。

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> 是可管理 Hadoop 工作的工作流程協調系統。 它可與 Hadoop 堆疊相整合，並支援 MapReduce、Pig、Hive 和 Sqoop 的 Hadoop 工作。 它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。 請參閱 [搭配使用 Oozie 與 Hadoop](hdinsight-use-oozie-linux-mac.md)。

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> 是對 HBase 的關聯式資料庫層。 Phoenix 包含 JDBC 驅動程式，可讓您直接查詢和管理 SQL 資料表。 Phoenix 會將查詢和其他陳述式轉譯為原生 NoSQL API 呼叫，而不是使用 MapReduce。因此能更快速地在 NoSQL 存放區上套用。 請參閱[搭配 HBase 叢集使用 Phoenix 和 SQuirreL](hdinsight-hbase-phoenix-squirrel.md)。

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> 是一個高階平台，可讓您在大型資料集上，使用稱為 Pig Latin 的簡單指令碼語言來執行複雜的 MapReduce 轉換。 Pig 會將 Pig Latin 指令碼轉換成可在 Hadoop 內執行。 您可以建立使用者定義的函式 (UDF) 來延伸 Pig Latin。 請參閱 [搭配使用 Pig 與 Hadoop](hdinsight-use-pig.md)。

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> 是在 Hadoop 與 SQL 之類的關聯式資料庫或其他結構化資料儲存之間盡可能以最高效率傳輸大量資料的工具。 請參閱＜ [使用 Sqoop 與 Hadoop](hdinsight-use-sqoop.md)＞。

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> 是以 Hadoop YARN 為建置基礎的應用程式架構，可執行一般資料處理的複雜非循環圖。 它是更有彈性且功能更強大的 MapReduce 架構後繼版本，可讓資料密集程序 (例如 Hive) 更有效率地大規模執行。 請參閱 [＜使用 Hive 和 HiveQL＞中的＜使用 Apache Tez 以提升效能＞](hdinsight-use-hive.md#usetez)。

### <a name="yarn"></a>YARN
Apache YARN 是新一代的 MapReduce (MapReduce 2.0，簡稱 MRv2)，可支援比 MapReduce 批次處理還要多的資料處理案例，並具有更佳的延展性和即時處理能力。 YARN 能提供資源管理和分散式應用程式架構。 MapReduce 作業會在 YARN 上執行。 請參閱 <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>。

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> 使用資料暫存器 (znode) 的共用階層式命名空間，協調大型分散式系統中的程序。 Znodes 包含協調程序所需的少量中繼資訊：狀態、位置、組態等。 請參閱[搭配使用 HBase 叢集與 Apache Phoenix 的 ZooKeeper](hdinsight-hbase-phoenix-squirrel-linux.md) 範例。 

## <a name="programming-languages-on-hdinsight"></a>HDInsight 上的程式設計語言
HDInsight 叢集 (Spark、HBase、Kafka、Hadoop 和其他叢集) 支援許多種程式設計語言，但某些語言並未預設安裝。 針對未預設安裝的程式庫、模組或套件，請[使用指令碼動作來安裝元件](hdinsight-hadoop-script-actions-linux.md)。 

### <a name="default-programming-language-support"></a>預設的程式設計語言支援
根據預設，HDInsight 叢集可支援：

* Java
* Python

其他語言則可以使用[指令碼動作](hdinsight-hadoop-script-actions-linux.md)來加以安裝。

### <a name="java-virtual-machine-jvm-languages"></a>Java 虛擬機器 (JVM) 語言
可以在 Java 虛擬機器 (JVM) 上執行 Java 以外的許多語言；不過，若要執行其中的某些語言，您可能必須在叢集上安裝其他元件。

HDInsight 叢集上支援下列以 JVM 為基礎的語言：

* Clojure
* Jython (適用於 Java 的 Python)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop 專屬語言
HDInsight 叢集支援下列 Hadoop 技術堆疊專屬語言：

* 適用於 Pig 工作的 Pig Latin
* 適用於 Hive 工作和 SparkSQL 的 HiveQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard 和 HDInsight Premium
HDInsight 提供兩種類型的巨量資料雲端提供項目：Standard 和 Premium。 HDInsight Standard 提供組織可用來執行其巨量資料工作負載的企業規模叢集。 HDInsight Premium 以標準功能為基礎，並提供 HDInsight 叢集的進階分析與安全性功能。 如需詳細資訊，請參閱 [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft 商業智慧和 HDInsight
熟悉的商業智慧 (BI) 工具可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料：

* [使用 Power Query 將 Excel 連線到 Hadoop](hdinsight-connect-excel-power-query.md)：了解如何使用 Microsoft Power Query for Excel，將 Excel 連線到儲存 HDInsight 叢集資料的 Azure 儲存體帳戶。 必要的 Windows 工作站。 
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何使用 Microsoft Hive ODBC 驅動程式從 HDInsight 匯入資料。 必要的 Windows 工作站。 
* [Microsoft 雲端平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)：了解 Power BI for Office 365、下載 SQL Server 試用版，以及設定 SharePoint Server 2013 和 SQL Server BI。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>後續步驟

* [開始使用 HDInsight 中的 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)：佈建 HDInsight Hadoop 叢集以及執行範例 Hive 查詢的快速入門教學課程。
* [開始使用 HDInsight 中的 Spark](hdinsight-apache-spark-jupyter-spark-sql.md)︰建立 Spark 叢集和執行互動式 Spark SQL 查詢的快速入門教學課程。
* [在 HDInsight 上使用 R 伺服器](hdinsight-hadoop-r-server-get-started.md)︰開始在 HDInsight Premium 中使用 R 伺服器。
* [佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何透過 Azure 入口網站、Azure CLI 或 Azure PowerShell 佈建 HDInsight Hadoop 叢集。


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
