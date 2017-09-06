---
title: "適用於 Eclipse 的 Azure 工具組：建立適用於 HDInsight Spark 的 Scala 應用程式 | Microsoft Docs"
description: "使用 HDInsight 工具 (位於 Eclipse 的 Azure 工具組中) 來開發以 Scala 撰寫的 Spark 應用程式，並直接從 Eclipse IDE 將它們提交到 HDInsight Spark 叢集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 79b3183171e3c28276c8e4e6d4fe3998e0109643
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>使用適用於 Eclipse 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式

使用 HDInsight 工具 (位於適用於 Eclipse 的 Azure 工具組中) 來開發以 Scala 撰寫的 Spark 應用程式，並直接從 Eclipse IDE 將它們提交到 Azure HDInsight Spark 叢集。 您能以數種不同的方式使用 HDInsight 工具外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式
* 存取您的 Azure HDInsight Spark 叢集資源
* 在本機開發並執行 Scala Spark 應用程式

> [!IMPORTANT]
> 您可以使用此工具，僅針對 Linux 上的 HDInsight Spark 叢集建立並提交應用程式。
> 
> 

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱 [在 Azure HDInsight 中建立 Apache Spark 叢集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java Development Kit 第 8 版，可用於 Eclipse IDE 執行階段。 您可以從 [Oracle 網站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下載。
* Eclipse IDE。 本文使用的是 Eclipse Neon。 您可以從 [Eclipse 網站](https://www.eclipse.org/downloads/)下載。
* Spark SDK。 您可以從 [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) 下載。


## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>安裝適用於 Eclipse 和 Scala 外掛程式之 Azure 工具組中的 HDInsight 工具
### <a name="install-hdinsight-tools"></a>安裝 HDInsight 工具
適用於 Eclipse 的 HDInsight 工具是適用於 Eclipse 的 Azure 工具組的一部分。 如需安裝指示，請參閱[安裝適用於 Eclipse 的 Azure 工具組](../azure-toolkit-for-eclipse-installation.md)。
### <a name="install-the-scala-plug-in"></a>安裝 Scala 外掛程式
當您開啟 Eclipse 時，HDInsight 工具會自動偵測您是否已安裝 Scala 外掛程式。 選取 [確定] 以繼續，然後遵循指示從 Eclipse Marketplace 安裝外掛程式。

![自動安裝 Scala 外掛程式](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala.png)

## <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：
1. 啟動 Eclipse IDE，然後開啟 [Azure Explorer]。 在 [視窗] 功能表上，選取 [顯示檢視]，然後選取 [其他]。 在開啟的對話方塊中展開 [Azure]，然後依序選取 [Azure Explorer] 和 [確定]。

   ![顯示檢視對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. 以滑鼠右鍵按一下 [Azure] 節點，然後選取 [登入]。
3. 在 [Azure 登入] 對話方塊中，選擇 [驗證方法]、選取 [登入]，並輸入您的 Azure 認證。
   
   ![[Azure 登入] 對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. 登入之後，[選取訂用帳戶] 對話方塊會列出與認證相關聯的所有 Azure 訂用帳戶。 按一下 [選取] 以關閉對話方塊。

   ![[選取訂用帳戶] 對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. 在 [Azure Explorer] 索引標籤中展開 [HDInsight]，可查看您訂用帳戶下的 HDInsight Spark 叢集。
   
   ![Azure Explorer 中的 HDInsight Spark 叢集](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. 您可以進一步展開叢集名稱節點，查看與叢集相關聯的資源 (例如，儲存體帳戶)。
   
   ![展開叢集名稱以查看資源](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)



## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>設定 HDInsight Spark 叢集的 Spark Scala 專案

1. 在 Eclipse IDE 工作區中，依序選取 [檔案]、[新增]，然後選取 [專案]。 
2. 在 [新增專案] 精靈中展開 [HDInsight]、選取 [HDInsight 上的 Spark (Scala)]，然後選取 [下一步]。

   ![選取 Spark HDInsight (Scala) 專案](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Scala 專案建立精靈會自動偵測您是否已安裝 Scala 外掛程式。 選取 [確定] 以繼續下載 Scala 外掛程式，並遵循指示重新啟動 Eclipse。

   ![Scala 檢查](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. 在 [新增 HDInsight Scala 專案] 對話方塊中，提供下列值，然後選取 [下一步]：
   * 輸入專案的名稱。
   * 在 [JRE] 區域中，請確定已將 [使用執行環境 JRE] 設為 [JavaSE-1.7] 或更新版本。
   * 請確定已將 Spark SDK 設為您下載 SDK 的位置。 下載位置的連結已包含於本文稍早的[必要條件](#prerequisites)中。 您也可以從此對話方塊中的連結下載 SDK。

   ![[新增 HDInsight Scala 專案] 對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. 在下一個對話方塊中，選取 [文件庫] 索引標籤並保留預設值，然後選取 [完成]。 
   
   ![[程式庫] 索引標籤](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>建立 HDInsight Spark 叢集的 Scala 應用程式

1. 在 Eclipse IDE 中，從 [套件總管] 將您稍早建立的專案展開，以滑鼠右鍵按一下 [src]、指向 [新增]，然後選取 [其他]。
2. 在 [選取精靈] 對話方塊方塊中，展開 [Scala 精靈]、選取 [Scala 物件]，然後選取 [下一步]。
   
   ![選取精靈對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. 在 [建立新檔案] 對話方塊中輸入物件的名稱，然後選取 [完成]。
   
   ![[建立新檔案] 對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. 在文字編輯器中貼上下列程式碼：
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. 在 HDInsight Spark 叢集上執行應用程式：
   
   a. 從 [封裝總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [將 Spark 應用程式提交給 HDInsight]。        
   b. 在 [提交 Spark] 對話方塊中提供下列值，然後選取 [提交]：
      
      * 針對 **叢集名稱**，選取您要在其上執行應用程式的 HDInsight Spark 叢集。
      * 從 Eclipse 專案中選取構件，或從硬碟中選取一個。 預設值取決於您在套件總管中以滑鼠右鍵按一下的項目。
      * 在 [主要類別名稱] 下拉式清單中，提交精靈會顯示您的所有物件名稱。 選取或輸入您需要執行的物件名稱。 如果您從硬碟選取構件，就必須手動輸入主要的類別名稱。 
      * 因為此範例中的應用程式程式碼不需要任何命令列引數，或是參考 JAR 或檔案，所以您可以將其餘的文字方塊保留空白。
        
      ![[提交 Spark] 對話方塊](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. [Spark Submission (提交 Spark)]  索引標籤應會開始顯示進度。 您可以選取 [提交 Spark] 視窗中的紅色按鈕，就能將應用程式停止。 您也可以選取全球圖示 (以映像中的藍色方塊表示)，檢視此特定應用程式執行的記錄。
      
   ![[提交 Spark] 視窗](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具來存取和管理 HDInsight Spark 叢集
您可以使用 HDInsight 工具來執行各種作業，包括存取作業輸出。

### <a name="access-the-job-view"></a>存取作業檢視
1. 在 [Azure Explorer] 中，依序展開 [HDInsight] 和 Spark 叢集名稱，然後選取 [作業]。 

   ![作業檢視節點](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. 選取 [作業] 節點。 HDInsight 工具會自動偵測您是否已安裝 E(fx)clipse 外掛程式。 選取 [確定] 以繼續，然後遵循指示安裝 Eclipse Marketplace 並重新啟動 Eclipse。

   ![安裝 E(fx)clipse](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. 從 [作業] 節點開啟 [作業] 檢視。 在右窗格中，[Spark 作業檢視]  索引標籤會顯示已在叢集上執行的所有應用程式。 選取您想要查看更多詳細資料的應用程式名稱。

   ![應用程式詳細資料](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

   然後您可以採取下列任何動作：

   * 將滑鼠停留於作業圖表。 它會顯示關於執行中作業的基本資訊。 選取作業圖表，就可以看到每項作業產生的階段和資訊。

     ![作業階段詳細資料](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

   * 選取 [記錄] 索引標籤可檢視經常使用的記錄 (包括「驅動程式 Stderr」、「驅動程式 Stdout」和「目錄資訊」)。

     ![記錄詳細資料](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

   * 選取視窗頂端的超連結，在應用程式層級開啟 Spark 歷程記錄 UI 和 YARN UI。

### <a name="access-the-storage-container-for-the-cluster"></a>存取叢集的儲存體容器
1. 在 [Azure Explorer] 中展開 [HDInsight] 根節點，查看可用的 HDInsight Spark 叢集清單。
2. 展開叢集名稱以查看叢集的儲存體帳戶和預設儲存體容器。
   
   ![儲存體帳戶和預設儲存體容器](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. 選取與叢集相關聯的儲存體容器名稱。 在右窗格中，按兩下 **HVACOut** 資料夾。 開啟其中一個 **part-** 檔案，可查看應用程式的輸出。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器
1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您已指定這些項目。
2. 在 [Spark 歷程記錄伺服器] 儀表板中，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您可以使用 `val conf = new SparkConf().setAppName("MyClusterApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>啟動 Ambari 入口網站
1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]。 
2. 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您已指定這些項目。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶
根據預設，適用於 Eclipse 的 Azure 工具組中之 HDInsight 工具會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有必要，您可以指定要存取其叢集的訂用帳戶。 

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure] 根節點，然後選取 [管理訂用帳戶]。 
2. 在對話方塊中，清除您不需要存取的訂用帳戶核取方塊，然後選取 [關閉]。 如果您想要登出 Azure 訂用帳戶，也可以選取 [登出]。

## <a name="run-a-spark-scala-application-locally"></a>在本機執行 Spark Scala 應用程式
您可以使用適用於 Eclipse 的 Azure 工具組中之 HDInsight 工具，在工作站上本機執行 Spark Scala 應用程式。 一般而言，這些應用程式不需要存取叢集資源 (例如儲存體容器)，而且您可在本機上執行並測試。

### <a name="prerequisite"></a>必要條件
在 Windows 電腦上執行本機 Spark Scala 應用程式時，可能會發生如 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況。 發生這個例外狀況是因為 Windows 中遺失 **WinUtils.exe**。 

若要解決這個錯誤，您必須[下載可執行檔](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)，並將其放在 **C:\WinUtils\bin** 之類的位置。 然後，您必須新增環境變數 **HADOOP_HOME**，並將變數的值設為 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>執行本機 Spark Scala 應用程式
1. 啟動 Eclipse，然後建立專案。 在 [新增專案] 對話方塊中選取下列選項，然後選取 [下一步]。
   
   * 在左窗格中，選取 [HDInsight]。
   * 在右窗格中，選取 [HDInsight 上的 Spark 本機執行範例 (Scala)]。

   ![New Project dialog box](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. 若要提供專案的詳細資料，請遵循稍早的[設定 HDInsight Spark 叢集的 Spark Scala 專案](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)一節中的步驟 3 到 6。

3. 範本會在 **src** 資料夾下方新增可在電腦本機執行的程式碼範例 (**LogQuery**)。
   
   ![LogQuery 的位置](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
   
4. 以滑鼠右鍵按一下 **LogQuery** 應用程式、指向 [執行身分]，然後選取 [1 Scala 應用程式]。 在 [主控台] 索引標籤上隨即顯示像這樣的輸出：
   
   ![Spark 應用程式本機執行結果](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>已知的問題
若要將應用程式提交至 Azure Data Lake Store，請在 Azure 登入程序期間，選取 [互動] 模式。 如果您選取 [自動] 模式，可能會發生錯誤。

![互動式登入](./media/hdinsight-apache-spark-eclipse-tool-plugin/interactive-authentication.png)

您可以選擇要使用任何登入方法提交您應用程式的 Azure Data Lake 叢集。

目前，不支援直接檢視 Spark 輸出。

## <a name="feedback"></a>意見反應
如果您有任何意見反應，或在使用此工具時遇到任何其他問題，請傳送電子郵件到 hdivstool@microsoft.com。

## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>建立和執行應用程式
* [使用 Scala 建立獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 IntelliJ 的 Azure 工具組中來建立和提交 Spark Scala 應用程式](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 VPN 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 Spark 應用程式進行遠端偵錯](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](hdinsight-apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](hdinsight-apache-spark-job-debugging.md)


