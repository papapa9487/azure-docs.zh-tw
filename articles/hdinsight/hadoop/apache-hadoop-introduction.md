---
title: "什麼是 HDInsight、Hadoop 和 Spark 技術堆疊？ - Azure | Microsoft Docs"
description: "HDInsight、Hadoop 和 Spark 技術堆疊和元件簡介，包括用於巨量資料分析的 Kafka、Hive、Storm、HBase。"
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
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: ba2a14e3f327fd8ab4f1ebfe329a64b4aa1a25a1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-and-spark-technology-stack"></a>Azure HDInsight、Hadoop 和 Spark 技術堆疊簡介
本文提供 Azure HDInsight 簡介，這是可供企業使用的完全受控、全方位的開放原始碼分析服務。 您可以使用開放原始碼架構，例如 Hadoop、Spark、Hive、LLAP、Kafka、Storm、R 等等。 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) 是原始的開放原始碼建構，用於分散式處理和分析叢集上的巨量資料集。 Hadoop 技術堆疊包含相關的軟體和公用程式，其中包括 Apache Hive、HBase、Spark、Kafka 和其他許多軟體。 若要查看 HDInsight 上可用的 Hadoop 技術堆疊元件，請參閱 [HDInsight 可用的元件和版本][component-versioning]。 若要深入了解 HDInsight 中的 Hadoop，請參閱 [HDInsight 的 Azure 功能頁面](https://azure.microsoft.com/services/hdinsight/)。

[Apache Spark](http://spark.apache.org) 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。 如需 Spark in HDInsight 的詳細資訊，請參閱 [Spark on Azure HDInsight 簡介](../spark/apache-spark-overview.md)。 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>什麼是 HDInsight 和 Hadoop 技術堆疊？ 
Azure HDInsight 是 [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) 中 Hadoop 元件的雲端發佈。  Azure HDInsight 可輕鬆快速地處理大量資料，同時節省成本。 您可以使用最常見的開放原始碼架構 (例如 Hadoop、Spark、Hive、LLAP、Kafka、Storm、R 等等) 來啟用廣泛的案例，例如擷取、轉換及載入 (ETL)；資料倉儲；機器學習服務 和 IoT。

## <a name="what-is-big-data"></a>什麼是巨量資料？

巨量資料的收集量快速增加，收集速度加快，收集格式也愈來愈多。 它可以屬於過去 (亦即已儲存) 或即時 (亦即從來源串流而來)。 請參閱[使用 HDInsight 的案例](#scenarios-for-using-hdinsight)，了解巨量資料的最常見使用案例。

## <a name="why-should-i-use-hdinsight"></a>為什麼應該使用 HDInsight？

此區段會列出 Azure HDInsight 的功能。


|功能  |說明  |
|---------|---------|
|雲端原生     |     Azure HDInsight 可讓您在 Azure 上建立 [Hadoop](apache-hadoop-linux-tutorial-get-started.md)、 [Spark](../spark/apache-spark-jupyter-spark-sql.md)、 [互動式查詢 (LLAP)](../interactive-query/apache-interactive-query-get-started.md)、 [Kafka](../kafka/apache-kafka-get-started.md)、 [Storm](../storm/apache-storm-tutorial-get-started-linux.md)、 [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) 和  [R Server](../r-server/r-server-get-started.md) 的最佳化叢集。 HDInsight 也提供所有生產工作負載的端對端 SLA。  |
|低成本且可調整     | HDInsight 可讓您[相應](../hdinsight-administer-use-portal-linux.md) 增加或減少工作負載。 您可以 [依照需求建立群集](../hdinsight-hadoop-create-linux-clusters-adf.md) 來降低成本，且只支付您所使用的部分。 您也可以建置資料管線來施行您的作業。 低耦合的計算和儲存體可提供更好的效能和彈性。 |
|安全且符合規範    | HDInsight 可讓您使用 [Azure 虛擬網路](../hdinsight-extend-hadoop-virtual-network.md)、[加密](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)，以及與 [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md)整合來保護企業資料資產。 HDInsight 也符合最受歡迎的產業和政府[合規性標準](https://azure.microsoft.com/overview/trusted-cloud)。        |
|監視    | Azure HDInsight 與 [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 整合，以提供單一介面來監視您所有的叢集。        |
|正式上市 | HDInsight 的適用 [區域](https://azure.microsoft.com/regions/services/) 超過任何其他巨量資料分析服務。 Azure HDInsight 也會適用於 Azure Government、中國和德國，可讓您符合您在重要主權區域中的企業需求。 |  
|生產力     |  Azure HDInsight 可讓您在 [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)、[Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) 和 [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) 等慣用開發環境中，使用適用於 Hadoop 和 Spark 的豐富生產力工具，以便支援 Scala、Python、R、Java 和 .NET。 資料科學家也可以使用熱門 Notebook (例如 [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) 和 [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)) 共同作業。    |
|擴充性     |  您可以使用[指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)安裝元件 (Hue、Presto 等)、[新增邊緣節點](../hdinsight-apps-use-edge-node.md)，或[與其他巨量資料認證的應用程式整合](../hdinsight-apps-install-applications.md)，藉此擴充 HDInsight 叢集。 透過[單鍵](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)部署，HDInsight 即可與最受歡迎的巨量資料解決方案緊密整合。|

## <a name="scenarios-for-using-hdinsight"></a>使用 HDInsight 的案例

Azure HDInsight 可用於巨量資料處理的各種使用案例。 巨量資料的收集量快速增加，收集速度加快，收集格式也愈來愈多。 它可以是歷程資料 (已收集及儲存的資料) 或即時資料 (從來源直接串流處理的資料)。 下列類別概述處理這類資料的使用案例： 

### <a name="batch-processing-etl"></a>批次處理 (ETL)

在擷取、轉換及載入 (ETL) 程序中，非結構化或結構化資料會擷取自異質資料來源、轉換成結構化格式，並載入資料存放區中。 已轉換的資料可使用於資料科學或資料倉儲。

### <a name="internet-of-things-iot"></a>物聯網 (IoT)

您可以使用 HDInsight 來處理從各種裝置收到的即時串流資料。 如需詳細資訊，請參閱[此部落格](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)。

![HDInsight 架構：物聯網](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>資料科學

您可以使用 HDInsight 建置應用程式，從資料中擷取重要的深入解析。 您也可以在其上使用 Azure Machine Learning，以預測您未來的業務趨勢。 如需詳細資訊，請參閱[此客戶案例](https://customers.microsoft.com/story/pros)。

![HDInsight 架構：資料科學](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>資料倉儲

您可以使用 HDInsight 對任何格式的結構化或非結構化資料執行 PB 規模的互動式查詢。 您也可以建置模型，將這些查詢連線至 BI 工具。 如需詳細資訊，請參閱[此客戶案例](https://customers.microsoft.com/story/milliman)。 

![HDInsight 架構：資料倉儲](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>混合式

您可以使用 HDInsight 將現有的內部部署巨量資料基礎結構延伸至 Azure，充分利用雲端的進階分析功能。

![HDInsight 架構：混合式](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>HDInsight 中的叢集類型
HDInsight 包含特定叢集類型和叢集自訂功能，例如新增元件、公用程式及語言。

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark、Kafka、互動式查詢、HBase、自訂和其他叢集類型
HDInsight 提供下列叢集類型：

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**︰使用 [HDFS](#hdfs)、[YARN](#yarn) 資源管理，以及簡單的 [MapReduce](#mapreduce) 程式設計模型來平行處理和分析批次資料。
* **[Apache Spark](http://spark.apache.org/)**：一種平行處理架構，可支援記憶體內的處理，大幅提升巨量資料分析應用程式、SQL 之 Spark 工作、串流資料與機器學習的效能。 請參閱[什麼是 HDInsight 中的 Apache Spark？](../spark/apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**：建置於 Hadoop 上的 NoSQL 資料庫，可針對大量非結構化及半結構化資料 (可能是數十億的資料列乘以數百萬的資料行)，提供隨機存取功能和強大一致性。 請參閱[什麼是 HDInsight 上的 HBase？](../hbase/apache-hbase-overview.md)
* **[Microsoft R 伺服器](https://msdn.microsoft.com/microsoft-r/rserver)**︰可用來裝載和管理並行、分散式 R 程序的伺服器。 這項新功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。 請參閱 [HDInsight 中的 R 伺服器概觀](../r-server/r-server-overview.md)。
* **[Apache Storm](https://storm.incubator.apache.org/)**：分散式、即時的運算系統，可快速處理大型的資料串流。 Storm 以受管理叢集的形式在 HDInsight 中提供。 請參閱＜ [使用 Storm 和 Hadoop 來分析即時感應器資料](../storm/apache-storm-sensor-data-analysis.md)＞。
* **[Apache 互動式查詢預覽 (也稱為︰Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**︰更快速之互動式 Hive 查詢的記憶體內快取。 請參閱[在 HDInsight 中使用互動式查詢](../interactive-query/apache-interactive-query-get-started.md)。
* **[Apache Kafka](https://kafka.apache.org/)**︰用來建立串流資料管線和應用程式的開放原始碼平台。 Kafka 也提供訊息佇列功能，可讓您發佈和訂閱資料串流。 請參閱 [HDInsight 上的 Apache Kafka 簡介](../kafka/apache-kafka-introduction.md)。

## <a name="open-source-components-in-hdinsight"></a>HDInsight 中的開放原始碼元件

Azure HDInsight 可讓您透過開放原始碼架構 (例如 Hadoop、Spark、Hive、LLAP、Kafka、Storm、HBase 和 R) 建立叢集。根據預設，這些叢集隨附其他開放原始碼元件，例如 [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)、[Avro](http://avro.apache.org/docs/current/spec.html)、[Hive](http://hive.apache.org)、[HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/)、[Mahout](https://mahout.apache.org/)、[MapReduce](http://wiki.apache.org/hadoop/MapReduce)、[YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Phoenix](http://phoenix.apache.org/)、[Pig](http://pig.apache.org/)、[Sqoop](http://sqoop.apache.org/)、[Tez](http://tez.apache.org/)、[Oozie](http://oozie.apache.org/)、[ZooKeeper](http://zookeeper.apache.org/)。  


## <a name="programming-languages-on-hdinsight"></a>HDInsight 上的程式設計語言
HDInsight 叢集 (Spark、HBase、Kafka、Hadoop 和其他叢集) 支援許多種程式設計語言，但某些語言並未預設安裝。 針對未預設安裝的程式庫、模組或套件，請[使用指令碼動作來安裝元件](../hdinsight-hadoop-script-actions-linux.md)。 

### <a name="default-programming-language-support"></a>預設的程式設計語言支援
根據預設，HDInsight 叢集可支援：

* Java
* Python

其他語言則可以使用[指令碼動作](../hdinsight-hadoop-script-actions-linux.md)來加以安裝。

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

## <a name="business-intelligence-on-hdinsight"></a>HDInsight 上的商業智慧
熟悉的商業智慧 (BI) 工具可整合使用 Power Query 增益集或 Microsoft Hive ODBC Driver 的 HDInsight 來擷取、分析和報告資料：

* [使用資料視覺效果工具搭配 Azure HDInsight 的 Apache Spark BI](../spark/apache-spark-use-bi-tools.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Hive 資料視覺化](apache-hadoop-connect-hive-power-bi.md) 
* [使用 Power Query 將 Excel 連線到 Hadoop](apache-hadoop-connect-excel-power-query.md)：了解如何使用 Microsoft Power Query for Excel，將 Excel 連線到儲存 HDInsight 叢集資料的 Azure 儲存體帳戶。 必要的 Windows 工作站。 
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](apache-hadoop-connect-excel-hive-odbc-driver.md)：了解如何使用 Microsoft Hive ODBC 驅動程式從 HDInsight 匯入資料。 必要的 Windows 工作站。 
* [Microsoft 雲端平台](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx)：了解 Power BI for Office 365、下載 SQL Server 試用版，以及設定 SharePoint Server 2013 和 SQL Server BI。
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>後續步驟

* [開始使用 HDInsight 中的 Hadoop](apache-hadoop-linux-tutorial-get-started.md)
* [開始使用 HDInsight 中的 Spark](../spark/apache-spark-jupyter-spark-sql.md)
* [開始使用 HDInsight 上的 Kafka](../kafka/apache-kafka-get-started.md)
* [開始在 HDInsight 上使用 Storm](../storm/apache-storm-tutorial-get-started-linux.md)
* [開始在 HDInsight 上使用 HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [開始在 HDInsight 上使用互動式查詢 (LLAP)](../interactive-query/apache-interactive-query-get-started.md)
* [開始在 HDInsight 上使用 R 伺服器](../r-server/r-server-get-started.md)
* [管理 HDInsight 叢集](../hdinsight-administer-use-portal-linux.md)
* [管理 HDInsight 叢集](../domain-joined/apache-domain-joined-introduction.md)
* [監視 HDInsight 叢集](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/