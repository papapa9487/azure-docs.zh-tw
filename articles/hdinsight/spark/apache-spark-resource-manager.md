---
title: "在 Azure HDInsight 上管理 Apache Spark 叢集的資源| Microsoft Docs"
description: "了解如何在 Azure HDInsight 上管理 Spark 叢集的資源，以便提升效能。"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: 3c98150239134c686ac8edebd3c477bec8be7dd8
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上管理 Apache Spark 叢集的資源 

在本文中，您將學習如何存取介面，例如 Ambari UI、YARN UI 以及與您 Spark 叢集相關聯的 Spark 歷程記錄伺服器。 您也將了解如何調整叢集組態，以獲得最佳效能。

**先決條件：**

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="how-do-i-launch-the-ambari-web-ui"></a>如何啟動 Ambari Web UI？
1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集格圖格 (如果您已將其釘選到開始面板)。 您也可以按一下 [瀏覽全部] > [HDInsight 叢集] 來瀏覽至您的叢集。
2. 針對 Spark 叢集，請按一下 [儀表板]。 出現提示時，輸入 Spark 叢集的系統管理員認證。

    ![啟動 Ambari](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "啟動 Resource Manager")
3. 這應會啟動 Ambari Web UI，如螢幕擷取畫面中所示。

    ![Ambari Web UI](./media/apache-spark-resource-manager/ambari-web-ui.png "Ambari Web UI")   

## <a name="how-do-i-launch-the-spark-history-server"></a>如何啟動 Spark 歷程記錄伺服器？
1. 在 [Azure 入口網站](https://portal.azure.com/)的開始面板中，按一下您的 Spark 叢集格圖格 (如果您已將其釘選到開始面板)。
2. 從叢集刀鋒視窗的 [快速連結] 下，按一下 [叢集儀表板]。 在 [叢集儀表板] 刀鋒視窗中，按一下 [Spark 歷程記錄伺服器]。

    ![Spark 歷程記錄伺服器](./media/apache-spark-resource-manager/launch-history-server.png "Spark 歷程記錄伺服器")

    出現提示時，輸入 Spark 叢集的系統管理員認證。

## <a name="how-do-i-launch-the-yarn-ui"></a>如何啟動 Yarn UI？
您可以使用 YARN UI 來監視目前在 Spark 叢集上執行的應用程式。

1. 從叢集刀鋒視窗按一下 [叢集儀表板]，然後按一下 [YARN]。

    ![啟動 YARN UI](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > 或者，您也可以從 Ambari UI 啟動 YARN UI。 若要啟動 Ambari UI，請從叢集刀鋒視窗中按一下 [叢集儀表板]，然後按一下 [HDInsight 叢集儀表板]。 從 Ambari UI 中，依序按一下 [YARN]、[快速連結]、作用中的 Resource Manager，以及 [Resource Manager UI]。
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>什麼是執行 Spark 應用程式的最佳叢集組態？
根據應用程式需求，可用於 Spark 組態的三個主要參數為 `spark.executor.instances`、`spark.executor.cores` 和 `spark.executor.memory`。 執行程式是針對 Spark 應用程式啟動的程序。 它會在背景工作角色節點上執行，並負責執行應用程式的工作。 執行程式的預設數目和每個叢集的執行程式大小，是根據背景工作角色節點數目和背景工作角色節點大小計算。 這項資訊會儲存在叢集前端節點上的 `spark-defaults.conf`。

這三個組態參數可以在叢集層級設定 (適用於在叢集執行的所有應用程式)，或者也可以針對每個個別應用程式指定。

### <a name="change-the-parameters-using-ambari-ui"></a>使用 Ambari UI 變更參數
1. 從 Ambari UI 按一下 [Spark]、按一下 [設定]，然後展開 [自訂 spark-defaults]。

    ![使用 Ambari UI 設定參數](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. 在叢集上有 4 個 Spark 應用程式同時執行的預設值是良好的。 您可以從使用者介面變更這些值，如下所示。

    ![使用 Ambari UI 設定參數](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. 按一下 [儲存]  以儲存組態變更。 在頁面頂端，系統會提示您重新啟動所有受影響的服務。 按一下 [重新啟動] 。

    ![重新啟動服務](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>變更在 Jupyter Notebook 中執行的應用程式的參數
對於在 Jupyter Notebook 中執行的應用程式，您可以使用 `%%configure` magic 進行組態變更。 在理想情況下，您必須在應用程式開頭進行此類變更，才能執行您的第一個程式碼儲存格。 這樣可確保組態會在 Livy 工作階段建立時套用至其中。 如果您想要變更應用程式中稍後的階段的組態，您必須使用 `-f` 參數。 不過，這麼做會讓應用程式中的所有進度遺失。

下列程式碼片段顯示如何變更在 Jupyter 中執行的應用程式的組態。

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

組態參數必須以 JSON 字串傳遞，且必須在 magic 之後的下一行，如範例資料行中所示。

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>使用 spark-submit 變更已提交應用程式的參數
下列命令是如何針對使用 `spark-submit`提交的批次應用程式變更組態參數的範例。

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>使用 cURL 變更已提交應用程式的參數
下列命令是如何針對使用 cURL 提交的批次應用程式變更組態參數的範例。

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>如何在 Spark Thrift 伺服器變更這些參數？
Spark Thrift 伺服器提供對 Spark 叢集的 JDBC/ODBC 存取，並且用來服務 Spark SQL 查詢。 Power BI、Tableau 等工具 使用 ODBC 通訊協定與 Spark Thrift 伺服器通訊，將 Spark SQL 查詢當作 Spark 應用程式執行。 建立 Spark 叢集時，會啟動 Spark Thrift 伺服器的兩個執行個體，每個前端節點上一個執行個體。 每個 Spark Thrift 伺服器會顯示為 YARN UI 中的 Spark 應用程式。

Spark Thrift 伺服器會使用 Spark 動態執行程式配置，因此不會使用 `spark.executor.instances` 。 而是 Spark Thrift 伺服器會使用 `spark.dynamicAllocation.minExecutors` 和 `spark.dynamicAllocation.maxExecutors` 來指定執行程式計數。 會使用組態參數 `spark.executor.cores` 和 `spark.executor.memory` 以修改執行程式大小。 您可以變更這些參數，如下列步驟所示。

* 展開**進階 spark-thrift-sparkconf** 類別以更新參數 `spark.dynamicAllocation.minExecutors`、`spark.dynamicAllocation.maxExecutors` 和 `spark.executor.memory`。

    ![設定 Spark Thrift 伺服器](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* 展開**自訂 spark-thrift-sparkconf** 類別以更新參數 `spark.executor.cores`。

    ![設定 Spark Thrift 伺服器](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>如何變更 Spark Thrift 伺服器的驅動程式記憶體？
Spark Thrift 伺服器驅動程式記憶體是設定為前端節點 RAM 大小的 25%，假設前端節點 RAM 的大小總計大於 14 GB。 您可以使用 Ambari UI 變更驅動程式記憶體組態，如下所示。

* 從 Ambari UI 按一下 [Spark]、按一下 [設定]、展開 [進階 spark-env]，然後提供 [spark_thrift_cmd_opts] 的值。

    ![設定 Spark Thrift 伺服器 RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>我沒有搭配使用 BI 和 Spark 叢集。 如何回收資源？
因為我們會使用 Spark 動態配置，Thrift 伺服器所使用的唯一資源是兩個應用程式主機的資源。 若要回收這些資源，您必須停止叢集上執行的 Thrift 伺服器服務。

1. 從 Ambari UI 的左窗格中，按一下 [Spark] 。
2. 在下一個頁面上，按一下 [Spark Thrift 伺服器] 。

    ![重新啟動 Thrift 伺服器](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. 您應該會看到 Spark Thrift 伺服器正在上面執行的兩個前端節點。 按一下其中一個前端節點。

    ![重新啟動 Thrift 伺服器](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. 下一個頁面列出在該前端節點上執行的所有服務。 從清單中，按一下 Spark Thrift 伺服器旁邊的下拉式按鈕，然後按一下 [停止] 。

    ![重新啟動 Thrift 伺服器](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. 對其他前端節點重複這些步驟。

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>我的 Jupyter Notebook 並未如預期般執行。 如何重新啟動服務？
如上所示啟動 Ambari Web UI。 從左側導覽窗格，依序按一下 [Jupyter]、[服務動作] 和 [全部重新啟動]。 這會在所有前端節點上啟動 Jupyter 服務。

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>如何知道我是否用盡資源？
如上所示啟動 Yarn UI。 在螢幕頂端的叢集計量資料表中，檢查[使用的記憶體] 的值和 [記憶體總計] 資料行。 如果非常接近 2 的值，資源可能不足以啟動下一個應用程式。 這同樣適用於 [使用的 VCores] 和 [VCores 總計] 資料行。 此外，在主要檢視中，如果應用程式一直維持在 [已接受] 狀態並未轉換成 [執行中] 或 [失敗] 狀態時，這也可能是表示其未取得足夠的資源來啟動。

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>如何終止執行中的應用程式以釋出資源？
1. 在 Yarn UI 中，從左窗格中，按一下 [執行中]。 從執行中應用程式的清單，決定要終止的應用程式，然後按一下 [識別碼]。

    ![終止 App1](./media/apache-spark-resource-manager/kill-app1.png "終止 App1")

2. 按一下右上角的 [終止應用程式]，然後按一下 [確定]。

    ![終止 App2](./media/apache-spark-resource-manager/kill-app2.png "終止 App2")

## <a name="see-also"></a>另請參閱
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>針對資料分析師

* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)
* [HDInsight 中使用 Spark 的 Application Insight 遙測資料分析](apache-spark-analyze-application-insight-logs.md)
* [在 Azure HDInsight Spark 上使用 Caffe 進行分散式深入學習](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>針對 Spark 開發人員

* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](apache-spark-eventhub-streaming.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式遠端偵錯 Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)
