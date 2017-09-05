---
title: "Spark on Azure HDInsight 簡介 | Microsoft Docs"
description: "本文提供 Spark on HDInsight 簡介以及您可以在 HDInsight 上使用 Spark 叢集的各種案例。"
keywords: "什麼是 apache spark,spark 叢集,spark 簡介,spark on hdinsight"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b8955acc83b0fbb0612e7042d62170ae8078b9ad
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="introduction-to-spark-on-hdinsight"></a>Spark on HDInsight 簡介

本文為您提供 Spark on HDInsight 簡介。 <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。 HDInsight 上的 Spark 叢集也能與 Azure 儲存體 (WASB) 以及 Azure Data Lake Store 相容。 因此，您可以輕鬆地透過 Spark 叢集處理儲存在 Azure 中的現有資料。

當您在 HDInsight 上建立 Spark 叢集時，就是建立了已安裝及設定 Spark 的 Azure 計算資源。 在 HDInsight 中建立 Spark 叢集只需要約 10 分鐘。 系統會將要處理的資料儲存在 Azure 儲存體或 Azure Data Lake Store 中。 請參閱[搭配 HDInsight 使用 Azure 儲存體](hdinsight-hadoop-use-blob-storage.md)。

**若要在 HDInsight 上建立 Spark 叢集**，請參閱[快速入門：在 HDInsight 上建立 Spark 叢集並使用 Jupyter 執行互動式查詢](hdinsight-apache-spark-jupyter-spark-sql.md)。


## <a name="what-is-apache-spark-on-azure-hdinsight"></a>什麼是 Apache Spark on Azure HDInsight？
HDInsight 上的 Spark 叢集可提供完全受管理的 Spark 服務。 在 HDInsight 上建立 Spark 叢集的優點如下所列。

| 功能 | 說明 |
| --- | --- |
| 容易建立 Spark 叢集 |您可以使用 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，在幾分鐘之內於 HDInsight 上建立新的 Spark 叢集。 請參閱 [開始使用 HDInsight 中的 Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md) |
| 容易使用 |HDInsight 上的 Spark 叢集包含 Jupyter 和 Zeppelin Notebook。 您可以使用這些 Notebook 來進行互動式的資料處理和視覺化。|
| REST API |HDInsight 中的 Spark 叢集包含 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)，它是 REST-API 型 Spark 作業伺服器，可用來遠端提交及監視作業。 |
| 支援 Azure Data Lake Store | HDInsight 上的 Spark 叢集可以設定為使用 Azure Data Lake Store 作為額外的儲存體以及主要儲存體 (只適用於 HDInsight 3.5 叢集)。 如需有關 Data Lake Store 的詳細資訊，請參閱 [Azure Data Lake Store 概觀](../data-lake-store/data-lake-store-overview.md)。 |
| Azure 服務整合 |HDInsight 上的 Spark 叢集附有連線 Azure 事件中樞的連接器。 除了 Spark 已經提供的 [Kafka](http://kafka.apache.org/)之外，客戶還可以使用事件中樞來建置串流應用程式。 |
| 支援 R 伺服器 | 您可以在 HDInsight Spark 叢集上設定 R 伺服器，以 Spark 叢集所承諾的速度執行分散式 R 計算。 如需詳細資訊，請參閱 [開始使用 HDInsight 上的 R 伺服器](hdinsight-hadoop-r-server-get-started.md)。 |
| 第三方 IDE 整合 | HDInsight 會提供 IDEs like IntelliJ IDEA 和 Eclipse 的外掛程式，以供您建立應用程式並將其提交至 HDInsight Spark 叢集。 如需詳細資訊，請參閱[使用 Azure Toolkit for IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md)和[使用 Azure Toolkit for Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md)。|
| 並行查詢 |HDInsight 中的 Spark 叢集支援並行查詢。 它能讓一位使用者執行多個查詢，或讓不同的使用者執行多個查詢，以及讓應用程式共用相同的叢集資源。 |
| SSD 快取 |您可以選擇將資料快取在記憶體中，或快取在連接叢集節點的 SSD 中。 記憶體快取能提供最高的查詢效能，但可能所費不疵。SSD 快取是改善查詢效能的絕佳選項，而且您不需要根據記憶體中的整個資料集建立滿足其需求的叢集規模。 |
| BI 工具整合 |HDInsight 上的 Spark 叢集會為 BI 工具 (例如 [Power BI](http://www.powerbi.com/) 和 [Tableau](http://www.tableau.com/products/desktop)) 提供資料分析所需的連接器。 |
| 預先載入的 Anaconda 程式庫 |HDInsight 上的 Spark 叢集附有預先安裝的 Anaconda 程式庫。 [Anaconda](http://docs.continuum.io/anaconda/) 為機器學習、資料分析、視覺化等主題提供將近 200 個程式庫。 |
| 延展性 |雖然您可以在建立時指定叢集的節點數，但您可以擴大或縮小叢集以配合工作負載。 所有 HDInsight 叢集都允許您變更叢集中的節點數目。 此外，由於所有資料都儲存在 Azure 儲存體或 Data Lake Store 內，因此您可以在不遺失資料的情況下卸除 Spark 叢集。 |
| 全天候支援 |HDInsight 上的 Spark 叢集附有企業級的全天候支援和保證正常運作時間達 99.9% 的 SLA。 |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>HDInsight 上的 Spark 有哪些使用案例？
HDInsight 中的 Spark 叢集適用於下列重要案例。

### <a name="interactive-data-analysis-and-bi"></a>互動式資料分析和 BI
[觀看教學課程](hdinsight-apache-spark-use-bi-tools.md)

HDInsight 中的 Apache Spark 會將資料儲存在 Azure 儲存體或 Azure Data Lake Store 中。 商務專家和重要決策者可以利用這些資料來進行分析及建立報告，並使用 Microsoft Power BI 來根據分析資料建置互動式報告。 分析師可以從叢集儲存體中的非結構化/半結構化資料著手、使用 Notebook 來定義資料的結構描述，然後再使用 Microsoft Power BI 來建置資料模型。 HDInsight 中的 Spark 叢集也支援 Tableau 等多個第三方 BI 工具，因此能成為資料分析師、商務專家及重要決策者的理想平台。

### <a name="spark-machine-learning"></a>Spark 機器學習服務
[看看教學課程：利用 HVAC 資料來預測建築物的溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[看看教學課程：預測食物檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark 隨附 [MLlib](http://spark.apache.org/mllib/)，這是以 Spark 為基礎的機器學習程式庫，您可以從 HDInsight 中的 Spark 叢集使用。 HDInsight 上的 Spark 叢集也包含 Anaconda，這是提供各種機器學習套件的 Python 散發。 搭配內建的 Jupyter 和 Zeppelin Notebook 支援，您將擁有最先進的機器學習應用程式建立環境。

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark 串流和即時資料分析
[觀看教學課程](hdinsight-apache-spark-eventhub-streaming.md)

HDInsight 上的 Spark 叢集提供豐富的支援供您建置即時分析解決方案。 雖然 Spark 已附有從 Kafka、Flume、Twitter、ZeroMQ 或 TCP 通訊端等眾多來源擷取資料的連接器，不過 HDInsight 中的 Spark 仍加入首屈一指的支援，供您從 Azure 事件中樞擷取資料。 事件中樞是 Azure 上最廣泛使用的佇列服務。 擁有立即可用的事件中樞支援，讓 HDInsight 中的 Spark 叢集成為建置即時分析管線的理想平台。

## <a name="next-steps"></a>Spark 叢集包含哪些元件？
依預設，HDInsight 中的 Spark 叢集能經由叢集提供下列元件。

* [Spark Core](https://spark.apache.org/docs/1.5.1/)。 包括 Spark Core、Spark SQL、Spark 串流 API、GraphX 及 MLlib。
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight 中的 Spark 叢集也提供 [ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)，讓您能從 BI 工具 (例如 Microsoft Power BI 和 Tableau) 連線到 HDInsight 中的 Spark 叢集。

## <a name="where-do-i-start"></a>我該從哪裡開始？
請從在 HDInsight 上建立 Spark 叢集開始。 請參閱[快速入門：在 HDInsight Linux 上建立 Spark 叢集並利用 Jupyter 執行互動式查詢](hdinsight-apache-spark-jupyter-spark-sql.md)。 

## <a name="next-steps"></a>後續步驟
### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)
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
* [Azure HDInsight 中 Apache Spark 的已知問題](hdinsight-apache-spark-known-issues.md)。

