---
title: "在 Azure HDInsight 上使用資料視覺效果工具的 Spark BI | Microsoft Docs"
description: "透過 HDInsight 叢集上的 Apache Spark BI 使用分析的資料視覺效果工具"
keywords: "apache spark bi, spark bi, spark 資料視覺效果, spark 商業智慧"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 869a000909813e607620c47ef802b4043e37dfa9
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>使用資料視覺效果工具搭配 Azure HDInsight 的 Apache Spark BI

了解如何使用諸如 Power BI 和 Tableau 等資料視覺效果工具，運用 HDInsight 叢集上的 Apache Spark BI 來分析原始範例資料集。

> [!NOTE]
> Azure HDInsight 3.6 預覽版的 Spark 2.1 不支援本文所述的 BI 工具連接性。 僅支援 Spark 1.6 版和 2.0 版 (分別是 HDInsight 3.4 版和 3.5 版)。
>

本教學課程也可在 HDInsight Spark 叢集上用來作為 Jupyter Notebook。 Notebook 的體驗能讓您從 Notebook 本身執行 Python 程式碼片段。 如要從 Notebook 中執行本教學課程，請建立 Spark 叢集、啟動 Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`)，然後執行 **Python** 資料夾中的 Notebook [搭配 HDInsight.ipynb 上的 Apache Spark 來使用 BI 工具]。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。


## <a name="hivetable"></a>準備 Spark 資料視覺效果的資料

在本節中，我們會使用 HDInsight Spark 叢集中的 [Jupyter](https://jupyter.org) Notebook，執行可處理未經處理範例資料並將這些資料儲存成資料表的工作。 範例資料是所有叢集在預設情況下均有的 .csv 檔案 (hvac.csv)。 將資料儲存為資料表後，下一節我們將使用 BI 工具連線到資料表，並執行資料視覺效果。

> [!NOTE]
> 如果您是在完成[在 HDInsight Spark 叢集上執行互動式查詢](hdinsight-apache-spark-load-data-run-query.md)中的指示之後執行本文中的步驟，即可跳到下面的步驟 8。
>

1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集格圖格 (如果您已將其釘選到開始面板)。 您也可以按一下 [瀏覽全部] > [HDInsight 叢集] 來瀏覽至您的叢集。   

2. 從 Spark 叢集刀鋒視窗按一下 叢集儀表板，然後按一下Jupyter Notebook。 出現提示時，輸入叢集的系統管理員認證。

   > [!NOTE]
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您的叢集的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. 建立 Notebook。 按一下 新增，然後按一下PySpark。

    ![建立 Apache Spark BI 的 Jupyter Notebook](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "建立 Apache Spark BI 的 Jupyter Notebook")

4. 系統隨即會建立新 Notebook，並以 Untitled.pynb 的名稱開啟。 在頂端按一下 Notebook 名稱，然後輸入好記的名稱。

    ![提供 Apache Spark BI Notebook 的名稱](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "提供 Apache Spark BI Notebook 的名稱")

5. 您使用 PySpark 核心建立 Notebook，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 和 Hive 內容。 首先，您可以匯入此案例所需的類型。 若要這樣做，請將游標放在儲存格中，然後按 **SHIFT + ENTER**鍵。

        from pyspark.sql import *

6. 將範例資料載入暫存資料表。 當您在 HDInsight 中建立 Spark 叢集時，系統會將範例資料檔案 **hvac.csv** 複製到相關聯的儲存體帳戶中 (位於 **\HdiSamples\HdiSamples\SensorSampleData\hvac**)。

    將下列程式碼片段貼到空白儲存格中，然後按下 **SHIFT + ENTER**。 此程式碼片段會將資料註冊到名為 **hvac** 的資料表中。

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

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

7. 確認資料表已成功建立。 您可以使用 `%%sql` magic 直接執行 Hive 查詢。 如需 `%%sql` magic 及 PySpark 核心提供的其他 magic 的詳細資訊，請參閱 [使用 Spark HDInsight 叢集之 Jupyter Notebook 上可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)。

        %%sql
        SHOW TABLES

    您會看到如下所示的輸出：

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    只有 **isTemporary** 資料行為 false 的資料表會儲存在中繼存放區，並可以從 BI 工具存取的 Hive 資料表。 在此教學課程中，我們會連線到我們所建立的 **hvac** 資料表。

8. 請確認資料表包含預期的資料。 將下列程式碼片段貼到 Notebook 的空白儲存格中，然後按下 **SHIFT + ENTER**。

        %%sql
        SELECT * FROM hvac LIMIT 10

9. 關閉 Notebook 來釋放資源。 若要這樣做，請從 Notebook 的 [檔案] 功能表中，按一下 [關閉並停止]。

## <a name="powerbi"></a>使用 Spark 資料視覺效果的 Power BI

> [!NOTE]
> 本節只適用於 HDInsight 3.4 上的 Spark 1.6 以及 HDInsight 3.5 上的 Spark 2.0。
>
>

將資料儲存成資料表後，您可以使用 Power BI 來連線資料並以視覺化方式呈現，以便建立報表、儀表板等。

1. 確定您可存取 Power BI。 您可以從 [http://www.powerbi.com/](http://www.powerbi.com/)取得 Power BI 的免費預覽版訂用帳戶。

2. 登入 [Power BI](http://www.powerbi.com/)。

3. 從左窗格底部，按一下 [取得資料]。

4. 在 [取得資料] 頁面的 [匯入或連線至資料] 之下，針對 [資料庫] 按一下 [取得]。

    ![將資料送入 Apache Spark BI 的 Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "將資料送入 Apache Spark BI 的 Power BI")

5. 在下一個畫面中，按一下 [Azure HDInsight 上的 Spark]，然後按一下 [連線]。 在畫面出現提示時，輸入叢集的 URL (`mysparkcluster.azurehdinsight.net`)，以及要連線到叢集所需的認證。

    ![連線到 Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "連線到 Apache Spark BI")

    建立連線之後，Power BI 會開始從 Spark 叢集將資料匯入 HDInsight。

6. Power BI 會匯入資料，並在 [資料集] 標頭下方新增 **Spark** 資料集。 按一下資料集來開啟新的工作表，以便將資料視覺化。 您也可以把工作表儲存成報告。 如要儲存工作表，請按一下 [檔案] 功能表中的 [儲存]。

    ![Power BI 儀表板上的 Apache Spark BI 圖格](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Power BI 儀表板上的 Apache Spark BI 圖格")
7. 請注意，右側的 [欄位] 清單會列出您先前建立的 **hvac** 資料表。 展開資料表，查看資料表中的欄位是否與稍早在 Notebook 中定義的欄位相同。

      ![列出 Apache Spark BI 儀表板上的資料表](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "列出 Apache Spark BI 儀表板上的資料表")

8. 建置視覺效果，顯示每棟建築物之目標溫度和實際溫度間的差異。 若要將資料視覺化，請選取 [區域圖] 圖示 (在紅色方框中)。 如要定義軸線，請把 [BuildingID] 欄位拖放到 [軸] 的下方，然後把 [ActualTemp]/[TargetTemp] 欄位拖放到 [值] 的下方。

    ![使用 Apache Spark BI 建立 Spark 資料視覺效果](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "使用 Apache Spark BI 建立 Spark 資料視覺效果")

9. 根據預設，視覺效果會顯示 **ActualTemp** 和 **TargetTemp** 的總和。 對於這兩個欄位，請選取下拉式清單中的 [平均]  ，來取得這兩棟建築物的實際和目標溫度的平均值。

    ![使用 Apache Spark BI 建立 Spark 資料視覺效果](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "使用 Apache Spark BI 建立 Spark 資料視覺效果")

10. 資料視覺效果應該類似於底下螢幕擷取畫面。 在視覺效果上移動游標可取得相關資料的工具提示。

    ![使用 Apache Spark BI 建立 Spark 資料視覺效果](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "使用 Apache Spark BI 建立 Spark 資料視覺效果")

11. 按一下頂端功能表的 [儲存]  ，並提供報告名稱。 您也可以釘選視覺效果。 釘選視覺效果時，系統會將它儲存在儀表板上，以便您追蹤最的新值。

   您可以針對同一個資料集加入滿足需求數量的視覺效果，並將它們釘選在儀表板上以取得資料快照。 此外，HDInsight 上的 Spark 叢集已透過直接連線連接 Power BI。 這可確保 Power BI 隨時能取得叢集的最新資料，因此您不需要排程資料集重新整理。

## <a name="tableau"></a>使用 Spark 資料視覺效果的 Tableau Desktop

> [!NOTE]
> 本節只適用於在 Azure HDInsight 中建立的 Spark 1.5.2 叢集。
>
>

1. 在執行本 Apache Spark BI 教學課程的電腦上安裝 [Tableau Desktop](http://www.tableau.com/products/desktop)。

2. 確保這部電腦也也安裝 Microsoft Spark ODBC 驅動程式。 您可以前往 [這裡](http://go.microsoft.com/fwlink/?LinkId=616229)來安裝驅動程式。

1. 啟動 Tableau Desktop。 在左窗格上可連線的伺服器清單中，按一下 [Spark SQL] 。 如果左窗格沒有預設列出 Spark SQL，您可以按一下 [更多伺服器] 來尋找它。
2. 在 Spark SQL 連線對話方塊中，提供螢幕擷取畫面中所示的值，然後按一下確定。

    ![連線至 Apache Spark BI 的叢集](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "連線至 Apache Spark BI 的叢集")

    您必須先在電腦上安裝 [Microsoft Spark ODBC 驅動程式](http://go.microsoft.com/fwlink/?LinkId=616229)，驗證下拉式清單中才會出現 **Microsoft Azure HDInsight 服務**。
3. 在下一個畫面中，按一下 結構描述 下拉式清單的 尋找 圖示，然後按一下預設。

    ![尋找 Apache Spark BI 的結構描述](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "尋找 Apache Spark BI 的結構描述")
4. 對於 [資料表] 欄位，再次按一下 [尋找] 圖示以列出叢集中所有可用的 Hive 資料表。 您應該會看到自己先前使用 Notebook 建立的 **hvac** 資料表。

    ![尋找 Apache Spark BI 的資料表](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "尋找 Apache Spark BI 的資料表")
5. 將資料表拖放到右側頂端的方塊。 Tableau 會匯入資料，並以紅色方塊反白顯示結構描述。

    ![將資料表新增至 Apache Spark BI 的 Tableau](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "將資料表新增至 Apache Spark BI 的 Tableau")
6. 按一下左下方的 [Sheet1]  索引標籤。 針對每個日期，製作出顯示所有建築物之平均目標溫度和實際溫度的視覺效果。 請把 [日期] 和 [建築物識別碼] 拖放到 [資料行] 中，然後將 [實際溫度]/[目標溫度] 拖放到 [資料列] 中。 請選取 [標記] 下方的 [區域]，以使用 Spark 資料視覺效果的區域對應圖。

     ![新增 Spark 資料視覺效果的欄位](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "新增 Spark 資料視覺效果的欄位")
7. 根據預設，溫度欄位以彙總形式顯示。 如果您想要改為顯示平均溫度，可以從下拉式清單執行操作，如下所示。

    ![採取 Spark 資料視覺效果的平均溫度](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "採取 Spark 資料視覺效果的平均溫度")

8. 您也可以將溫度對應加諸在其他對應之上，凸顯目標溫度和實際溫度之間的差異。 將滑鼠游標移動到區域對應圖下半部的角落，直到控點出現為止 (如紅色圓圈中所示)。 把對應圖拖曳到另一個對應圖的頂端，然後在您看到紅色圓圈中所示的形狀時，放開滑鼠按鍵。

    ![合併 Spark 資料視覺效果的對應圖](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "合併 Spark 資料視覺效果的對應圖")

     資料視覺效果應會變更，如底下螢幕擷取畫面所示：

    ![Spark 資料視覺效果的 Tableau 輸出](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark 資料視覺效果的 Tableau 輸出")
9. 按一下 [儲存]  以儲存工作表。 您可以建立儀表板，並在儀表板中加入一個或多個工作表。

## <a name="next-steps"></a>後續步驟

到目前為止，您學會了如何建立叢集、建立 Spark 資料框架來查詢資料，然後從 BI 工具中存取該資料。 您現在可以看看如何管理叢集資源，以及對在 HDInsight Spark 叢集中執行的工作進行偵錯的指示。

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)


