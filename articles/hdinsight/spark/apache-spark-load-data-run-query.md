---
title: "在 Azure HDInsight Spark 叢集上執行互動式查詢 | Microsoft Docs"
description: "HDInsight Spark 快速入門會說明如何在 HDInsight 中建立 Apache Spark 叢集。"
keywords: "spark 快速入門, 互動式 spark, 互動式查詢, hdinsight spark, azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>在 HDInsight 中的 Spark 叢集上執行互動式查詢

了解如何使用 Jupyter Notebook，針對 Spark 叢集執行互動式 Spark SQL 查詢。 

[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) \(英文\) 為瀏覽器型應用程式，能將以主控台為基礎的互動式體驗延伸至網路上。 HDInsight 上的 Spark 也包含 [Zeppelin Notebook](apache-spark-zeppelin-notebook.md)。 本教學課程會使用 Jupyter Notebook。

HDInsight 叢集上的 Jupyter Notebook 支援三種核心：**PySpark**、**PySpark3** 和 **Spark**。 本教學課程會使用 **PySpark** 核心。 如需核心的詳細資訊，以及使用 **PySpark**，請參閱[在 HDInsight 中搭配使用 Jupyter Notebook 核心與 Apache Spark 叢集](apache-spark-jupyter-notebook-kernels.md)。 若要使用 Zeppelin Notebook，請參閱[在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集](./apache-spark-zeppelin-notebook.md)。

在本教學課程中，您會以 csv 檔案查詢資料。 您必須先將該資料載入到 Spark 作為資料框架。 然後您可以使用 Jupyter Notebook 在資料框架上執行查詢。 

## <a name="prerequisites"></a>必要條件

* **Azure HDInsight Spark 叢集**。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* **使用 PySpark 的 Jupyter Notebook**。 如需指示，請參閱[建立 Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)。

## <a name="create-a-dataframe-from-a-csv-file"></a>從 csv 檔案建立資料框架

透過 SQLContext，應用程式可從現有的 RDD、Hive 資料表，或是資料來源來建立資料框架。 

**從 csv 檔案建立資料框架**

1. 如果您沒有 Jupyter Notebook，請使用 PySpark 建立一個。 如需指示，請參閱[建立 Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)。

2. 將以下程式碼貼入 Notebook 的空白儲存格，然後按 **SHIFT + ENTER** 以執行此程式碼。 此程式碼會匯入此案例所需的類型：

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    透過使用 PySpark 核心來建立 Notebook，當您執行第一個程式碼儲存格時，系統便會自動為您建立 Spark 和 Hive 內容。 您不需要明確建立任何內容。

    在 Jupyter 中執行互動式查詢時，網頁瀏覽器視窗或索引標籤標題都會顯示 [(忙碌)] 狀態，以及 Notebook 的標題。 您也會在右上角的 **PySpark** 文字旁看到一個實心圓。 作業完成後，實心圓將變成空心圓。

    ![互動式 Spark SQL 查詢的狀態](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "互動式 Spark SQL 查詢的狀態")

3. 執行下列程式碼來建立資料框架與暫存資料表 (**hvac**)，該程式碼不會擷取 CSV 檔案中的所有可用資料行： 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    下列螢幕擷取畫面顯示 HVAC.csv 檔案的快照集。 該 csv 檔案會隨附於所有 HDInsight Spark 叢集。 資料會擷取一棟建築物的溫度變化。

    ![互動式 Spark SQL 查詢的資料快照集](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "互動式 Spark SQL 查詢的資料快照集")

## <a name="run-queries-on-the-dataframe"></a>在資料框架上執行查詢

建立資料表之後，您可以對資料執行互動式查詢。

**執行查詢**

1. 在 Notebook 的空白儲存格中，執行下列程式碼：

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   由於 PySpark 核心用於 Notebook 中，因此您現在可直接在剛才使用 `%%sql` magic 建立的暫存資料表 **hvac** 上執行互動式 SQL 查詢。 如需 `%%sql` magic 及 PySpark 核心提供的其他 magic 的詳細資訊，請參閱 [使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

   預設會顯示下列表格式輸出。

     ![互動式 Spark 查詢結果的資料表輸出](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "互動式 Spark 查詢結果的資料表輸出")

3. 您也可以查看其他視覺效果中的結果。 若要查看相同輸出的區域圖表，請選取 [區域]，然後設定其他值，如下所示。

    ![互動式 Spark 查詢結果的區域圖表](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "互動式 Spark 查詢結果的區域圖表")

10. 從 Notebook 的 [檔案] 功能表中，按一下 [儲存與檢查點]。 

11. 如果您現在要開始進行[下一個教學課程](apache-spark-use-bi-tools.md)，請讓 Notebook 保持開啟。 如果尚不進行，請關閉 Notebook 以釋出叢集資源：從 Notebook 上的 [檔案] 功能表，按一下 [關閉並停止]。

## <a name="next-step"></a>後續步驟

在本文中，您已學會如何使用 Jupyter Notebook 在 Spark 中執行互動式查詢。 前往下一篇文章，以查看如何將您在 Spark 中註冊的資料提取至 BI分析工具，例如 Power BI 和 Tableau BI。 

> [!div class="nextstepaction"]
>[使用資料視覺效果工具搭配 Azure HDInsight 的 Spark BI](apache-spark-use-bi-tools.md)




