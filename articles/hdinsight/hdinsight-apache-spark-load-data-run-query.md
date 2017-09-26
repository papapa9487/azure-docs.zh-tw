---
title: "在 Azure HDInsight Spark 叢集上執行互動式查詢 | Microsoft Docs"
description: "HDInsight Spark 快速入門會說明如何在 HDInsight 中建立 Apache Spark 叢集。"
keywords: "spark 快速入門, 互動式 spark, 互動式查詢, hdinsight spark, azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: ada1c3d1482c68834dbbf5eabbd045a7e0c01f9f
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 叢集上執行互動式查詢

在本文中，您會使用 Jupyter Notebook，針對 Spark 叢集執行互動式 Spark SQL 查詢。 Jupyter Notebook 是一個瀏覽器型應用程式，會將以主控台為基礎的互動式體驗延伸至 Web。 如需詳細資訊，請參閱 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) \(英文\)。

在本教學課程中，您會使用 Jupyter Notebook 中的 **PySpark** 核心來執行互動式 Spark SQL 查詢。 HDInsight 叢集上的 Jupyter Notebook 也支援其他兩個核心：**PySpark3** 和 **Spark**。 如需核心的詳細資訊，以及使用 **PySpark**，請參閱[在 HDInsight 中搭配使用 Jupyter Notebook 核心與 Apache Spark 叢集](hdinsight-apache-spark-jupyter-notebook-kernels.md)。

## <a name="prerequisites"></a>必要條件

* **Azure HDInsight Spark 叢集**。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>建立 Jupyter Notebook 執行互動式查詢

若要執行查詢，我們會使用預設可在叢集相關聯的儲存體中取得的範例資料。 不過，您必須先將該資料載入到 Spark 作為資料框架。 一旦具備資料框架之後，您就能使用 Jupyter Notebook 在其上執行查詢。 在本節中，您要查看如何：

* 註冊範例資料集作為 Spark 資料框架。
* 在資料框架上執行查詢。

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。 如果您選擇將叢集釘選至儀表板，從儀表板按一下 [叢集] 圖格以啟動叢集刀鋒視窗。

    如果您未將叢集釘選至儀表板，從左窗格中按一下 [HDInsight 叢集]，然後按一下您建立的叢集。

3. 從 [快速連結]，按一下 [叢集儀表板]，然後按一下 [Jupyter Notebook]。 出現提示時，輸入叢集的系統管理員認證。

   ![開啟 Jupyter Notebook 來執行互動式 Spark SQL 查詢](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "開啟 Jupyter Notebook 來執行互動式 Spark SQL 查詢")

   > [!NOTE]
   > 您也可以在瀏覽器中開啟下列 URL，來存取您叢集的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 建立 Notebook。 按一下 [新增]，然後按一下 [PySpark]。

   ![建立 Jupyter Notebook 來執行互動式 Spark SQL 查詢](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "建立 Jupyter Notebook 來執行互動式 Spark SQL 查詢")

   新的 Notebook 隨即建立並以 Untitled(Untitled.pynb) 名稱開啟。

4. 按一下頂端的 Notebook 名稱，然後輸入好記的名稱。

    ![為要執行互動式 Spark 查詢的 Jupter Notebook 命名](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "為要執行互動式 Spark 查詢的 Jupter Notebook 命名")

5. 將以下程式碼貼入空白儲存格，然後按下 **SHIFT + ENTER** 鍵以執行此程式碼。 此程式碼會匯入此案例所需的類型：

        from pyspark.sql import *
        from pyspark.sql.types import *

    您使用 PySpark 核心建立 Notebook，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 和 Hive 內容。

    ![互動式 Spark SQL 查詢的狀態](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "互動式 Spark SQL 查詢的狀態")

    每當您在 Jupyter 中執行互動式查詢時，網頁瀏覽器視窗標題都會顯示 Notebook 標題和 **(忙碌)** 狀態。 您也會在右上角的 **PySpark** 文字旁看到一個實心圓。 作業完成後，實心圓將變成空心圓。

6. 將資料載入到 Spark 叢集之前，讓我們先看看它的快照集。 本教學課程中所使用的範例資料可用來作為 **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** 上所有 HDInsight Spark 叢集上的 CSV 檔案。 資料會擷取一棟建築物的溫度變化。 以下是資料的前幾個資料列。

    ![互動式 Spark SQL 查詢的資料快照集](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "互動式 Spark SQL 查詢的資料快照集")

6. 執行下列程式碼，以建立資料框架和暫存資料表 (**hvac**)。 在本教學課程中，相較於原始 CSV 資料中的資料行，我們不會建立暫存資料表中的所有資料行。 

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

7. 建立資料表之後，對資料執行互動式查詢，請使用下列程式碼。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   由於您使用的是 PySpark 核心，因此現在可在您剛才使用 `%%sql` magic 建立的暫存資料表 **hvac** 上執行互動式 SQL 查詢。 如需 `%%sql` magic 及 PySpark 核心提供的其他 magic 的詳細資訊，請參閱 [使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

   預設會顯示下列表格式輸出。

     ![互動式 Spark 查詢結果的資料表輸出](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "互動式 Spark 查詢結果的資料表輸出")

    您也可以查看其他視覺效果中的結果。 例如，相同輸出的區域圖看起來會如下所示。

    ![互動式 Spark 查詢結果的區域圖表](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "互動式 Spark 查詢結果的區域圖表")

9. 應用程式執行完畢之後，請關閉 Notebook 來釋放叢集資源。 若要這樣做，請從 Notebook 的 [檔案] 功能表中，按一下 [關閉並停止]。

## <a name="next-step"></a>後續步驟

在本文中，您學會如何使用 Jupyter Notebook，在 Spark 中執行互動式查詢。 前往下一篇文章，以查看如何將您在 Spark 中註冊的資料提取至 BI分析工具，例如 Power BI 和 Tableau BI。 

> [!div class="nextstepaction"]
>[使用資料視覺效果工具搭配 Azure HDInsight 的 Spark BI](hdinsight-apache-spark-use-bi-tools.md)





