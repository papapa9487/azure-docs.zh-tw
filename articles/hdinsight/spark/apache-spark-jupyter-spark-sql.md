---
title: "在 Azure HDInsight 中建立 Apache Spark 叢集 | Microsoft Docs"
description: "HDInsight Spark 快速入門會說明如何在 HDInsight 中建立 Apache Spark 叢集。"
keywords: "spark 快速入門, 互動式 spark, 互動式查詢, hdinsight spark, azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 516c48424ef5d1256296240541fb544c1e5d9205
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>在 Azure HDInsight 中建立 Apache Spark 叢集

了解如何在 Azure HDInsight 上建立 Apache Spark 叢集，以及如何對 Hive 資料表執行 Spark SQL 查詢。 如需 Spark on HDInsight 相關資訊，請參閱[概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)。

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 請參閱[建立免費的 Azure 帳戶](https://azure.microsoft.com/free)。

## <a name="create-hdinsight-spark-cluster"></a>建立 HDInsight Spark 叢集

使用 [Azure Resource Manager 範本](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)來建立 HDInsight Spark 叢集。 此範本可在 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/) 中找到。 如需其他叢集建立方法，請參閱 [建立 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。

1. 按一下以下影像，在 Azure 入口網站中開啟範本。         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 輸入下列值：

    ![使用 Azure Resource Manager 範本建立 HDInsight Spark 叢集](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "在 HDInsight 中使用 Azure Resource Manager 範本建立 Spark 叢集")

    * **訂用帳戶**：選取用來建立此叢集的 Azure 訂用帳戶。
    * **資源群組**：建立資源群組，或選取現有的資源群組。 資源群組用來管理專案的 Azure 資源。
    * **位置**：選取資源群組的位置。 此範本會使用這個位置，用於建立叢集以及預設叢集儲存體。
    * **ClusterName**：輸入您想要建立的 HDInsight 叢集名稱。
    * 叢集登入名稱和密碼：預設登入名稱是 admin。
    * SSH 使用者名稱和密碼。

3. 選取 [我同意上方所述的條款及條件]，選取 [釘選到儀表板]，然後按一下 [購買]。 您可以看到新的圖格，標題為「提交範本部署的部署」。 大約需要 20 分鐘的時間來建立叢集。

如果您在建立 HDInsight 叢集時遇到問題，可能是您沒有這麼做的適當權限。 如需詳細資訊，請參閱[存取控制需求](../hdinsight-administer-use-portal-linux.md#create-clusters)。

> [!NOTE]
> 本文建立使用 [Azure 儲存體 Blob 做為叢集儲存體](../hdinsight-hadoop-use-blob-storage.md)的 Spark 叢集。 您也可以建立使用 [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) 作為預設儲存體的 Spark 叢集。 如需指示，請參閱 [建立具有 Data Lake Store 的 HDInsight 叢集](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。
>
>

## <a name="create-a-jupyter-notebook"></a>建立 Jupyter Notebook

[Jupyter Notebook](http://jupyter.org) 是支援各種程式設計語言的互動式筆記本環境，可讓您與資料互動、結合程式碼與 Markdown 文字，以及執行簡單的視覺效果。 Spark on HDInsight 也包含 [Zeppelin Notebook](apache-spark-zeppelin-notebook.md)。 本教學課程使用 Jupyter Notebook。

**建立 Jupyter Notebook**

1. 開啟 [Azure 入口網站](https://portal.azure.com/)。

2. 開啟您所建立的 Spark 叢集。 如需相關指示，請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。

3. 從 [快速連結]，按一下 [叢集儀表板]，然後按一下 [Jupyter Notebook]。 出現提示時，輸入叢集的系統管理員認證。

   ![開啟 Jupyter Notebook 來執行互動式 Spark SQL 查詢](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "開啟 Jupyter Notebook 來執行互動式 Spark SQL 查詢")

   > [!NOTE]
   > 您也可以在瀏覽器中開啟下列 URL，來存取您叢集的 Jupyter Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 按一下 [新增]，然後按一下 [Pyspark] 來建立 Notebook。 HDInsight 叢集上的 Jupyter Notebook 支援三個核心：**PySpark**、**PySpark3** 和 **Spark**。 本教學課程使用 **PySpark** 核心。 如需核心的詳細資訊，以及使用 **PySpark**，請參閱[在 HDInsight 中搭配使用 Jupyter Notebook 核心與 Apache Spark 叢集](apache-spark-jupyter-notebook-kernels.md)。

   ![建立 Jupyter Notebook 來執行互動式 Spark SQL 查詢](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "建立 Jupyter Notebook 來執行互動式 Spark SQL 查詢")

   新的 Notebook 隨即建立並以 Untitled(Untitled.pynb) 名稱開啟。

4. 按一下頂端的 Notebook 名稱，然後輸入好記的名稱。

    ![為要執行互動式 Spark 查詢的 Jupyter Notebook 命名](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "為要執行互動式 Spark 查詢的 Jupyter Notebook 命名")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>在 Hive 資料表上執行 Spark SQL 陳述式

SQL (結構化查詢語言) 是最常見且廣泛使用的語言，可用於查詢及定義資料。 Spark SQL 可作為 Apache Spark 的擴充功能，可讓您使用熟悉的 SQL 語法來處理結構化資料。

Spark SQL 支援 SQL 和 HiveQL 查詢語言。 其功能包括 Python、Scala 和 Java 繫結。 您可以透過它，查詢儲存在許多位置的資料，例如外部資料庫、結構化資料檔案 (範例：JSON) 和 Hive 資料表。

如需從 csv 檔案 (而非 Hive 資料表) 讀取資料的範例，請參閱[在 HDInsight 中的 Spark 叢集上執行互動式查詢](apache-spark-load-data-run-query.md)。

**執行 Spark SQL**

1.  從 Notebook，將以下程式碼貼入空白儲存格，然後按 **SHIFT + ENTER** 鍵以執行此程式碼。 

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    ![HDInsight Spark 中的 Hive 查詢](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight Spark 中的 Hive 查詢")

    當您使用 Jupyter Notebook 搭配 HDInsight Spark 叢集時，您可取得預設的 `sqlContext`，用來執行使用 Spark SQL 的 Hive 查詢。 `%%sql` 會告知 Jupyter Notebook 使用預設的 `sqlContext` 來執行 Hive 查詢。 此查詢會擷取 Hive 資料表 (**hivesampletable**) 中的前 10 個資料列，依預設所有 HDInsight 叢集均隨附該資料表。 如需有關 `%%sql` magic 和預設內容的詳細資訊，請參閱 [HDInsight 叢集可用的 Jupyter 核心](apache-spark-jupyter-notebook-kernels.md)。

    每當您在 Jupyter 中執行查詢時，網頁瀏覽器視窗標題將會顯示 Notebook 標題和 **(忙碌)** 狀態。 您也會在右上角的 **PySpark** 文字旁看到一個實心圓。 作業完成後，實心圓將變成空心圓。
    
    畫面應會重新整理以顯示查詢輸出。

    ![HDInsight Spark 中的 Hive 查詢輸出](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight Spark 中的 Hive 查詢輸出")

2. 從 Notebook 的 [檔案] 功能表中，按一下 [關閉並停止]。 關閉 Notebook 可釋出叢集資源。

3. 如果您打算稍後完成後續步驟，請務必刪除在本文中建立的 HDInsight 叢集。 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>後續步驟 

在本文中，您已了解如何建立 HDInsight Spark 叢集和執行基本的 Spark SQL 查詢。 前往下篇文章，了解如何使用 HDInsight Spark 叢集執行簡單資料的互動查詢。

> [!div class="nextstepaction"]
>[在 HDInsight Spark 叢集上執行互動查詢](apache-spark-load-data-run-query.md)



