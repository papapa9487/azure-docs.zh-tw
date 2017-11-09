---
title: "適用於 IntelliJ 的 Azure 工具組：透過 SSH 對 Spark 應用程式進行遠端偵錯 | Microsoft Docs"
description: "逐步指引如何使用適用於 IntelliJ 之 Azure 工具組中的 HDInsight 工具，透過 SSH 對 HDInsight 叢集上的應用程式進行遠端偵錯"
keywords: "遠端偵錯 intellij, 進行 intellij 遠端偵錯, ssh, intellij, hdinsight, 偵錯 intellij, 偵錯"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: jejiang
ms.openlocfilehash: cebbe2a0e28d49c93d0ebf12cc04b3d201dcec97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用適用於 IntelliJ 的 Azure 工具組透過 SSH 對 HDInsight 叢集上的 Spark 應用程式進行偵錯

本文提供的逐步指引，是關於如何使用適用於 IntelliJ 之 Azure 工具組中的 HDInsight 工具，對 HDInsight 叢集上的應用程式進行遠端偵錯。 若要對您的專案進行偵錯，您也可以觀看 [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用適用於 IntelliJ 的 Azure 工具組對 HDInsight Spark 應用程式進行偵錯) 影片。

**必要條件**

* **適用於 IntelliJ 的 Azure 工具組中的 HDInsight 工具**。 此工具是適用於 IntelliJ 之 Azure 工具組的一部分。 如需詳細資訊，請參閱[安裝適用於 IntelliJ 的 Azure 工具組](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)。
* **適用於 IntelliJ 的 Azure 工具組**。 使用此工具組建立適用於 HDInsight 叢集的 Spark 應用程式。 如需詳細資訊，請遵循[使用適用於 IntelliJ 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)中的指示進行。
* **HDInsight SSH 服務與使用者名稱和密碼管理**。 如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 和[使用 SSH 通道來存取 Ambari Web UI、JobHistory、NameNode、Oozie 及其他 Web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>建立 Spark Scala 應用程式，並設定它以進行遠端偵錯

1. 啟動 IntelliJ IDEA，然後建立專案。 在 [新增專案]  對話方塊中，執行下列操作：

   a. 選取 [HDInsight]。 

   b. 根據您的喜好設定選取 Java 或 Scala 範本。 在下列選項之間進行選取：

      - **Spark on HDInsight (Scala) \(HDInsight 上的 Spark (Scala)\)**

      - **Spark on HDInsight (Java) \(HDInsight 上的 Spark (Java)\)**

      - Spark on HDInsight Cluster Run Sample (Scala) \(HDInsight 叢集上的 Spark 執行範例 (Scala)\)

      此範例會使用 [Spark on HDInsight Cluster Run Sample (Scala)] \(HDInsight 叢集上的 Spark 執行範例 (Scala)) 範本。

   c. 在 [建置工具] 清單中，根據您的需求選取下列任一項目：

      - **Maven**，以支援 Scala 專案建立精靈

      -  **SBT**，以管理相依性並建置 Scala 專案 

      ![建立偵錯專案](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. 選取 [下一步] 。     
 
3. 在下一個 [新增專案] 視窗中，執行下列動作：

   ![選取 Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. 輸入專案名稱和專案位置。

   b. 在 [專案 SDK] 下拉式清單中，選取 [Java 1.8] \(適用於 **Spark 2.x** 叢集)，或選取 [Java 1.7] \(適用於 **Spark 1.x** 叢集)。

   c. 在 [Spark 版本] 下拉式清單中，Scala 專案建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集是 2.0 以前的版本，請選取 [Spark 1.x]。 否則，請選取 **Spark 2.x**。 此範例使用 **Spark 2.0.2 (Scala 2.11.8)**。

   d. 選取 [完成]。

4. 選取 **src** > **main \(主要\)** > scala 以在專案中開啟您的程式碼。 此範例使用 **SparkCore_wasbloTest** 指令碼。

5. 若要存取 [編輯設定] 功能表，請選取右上角的圖示。 從這個功能表中，您可以建立或編輯遠端偵錯的設定。

   ![編輯設定](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

6. 在 [Run/Debug Configurations] \(執行/偵錯設定) 對話方塊中，選取加號 (**+**)。 然後選取 [Submit Spark Job] \(提交 Spark 作業\) 選項。

   ![新增設定](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
7. 輸入 [Name] \(名稱\)、**[Spark cluster] \(Spark 叢集\)** 和 [Main class name] \(主要類別名稱\)。 然後選取 [Advanced configuration] \(進階設定\)。 

   ![[Run/Debug Configurations] \(執行/偵錯設定\)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

8. 在 [Spark Submission Advanced Configuration] \(Spark 提交進階設定\) 對話方塊中，選取 [Enable Spark remote debug] \(啟用 Spark 遠端偵錯\)。 輸入 SSH 使用者名稱，然後輸入密碼或使用私密金鑰檔案。 若要儲存設定，請選取 [確定]。

   ![[Enable Spark remote debug] \(啟用 Spark 遠端偵錯\)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

9. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)。 

10. 完成組態設定之後，您可以針對遠端叢集執行專案，或執行遠端偵錯。

## <a name="learn-how-to-perform-remote-debugging"></a>了解如何執行遠端偵錯
### <a name="scenario-1-perform-remote-run"></a>案例 1：執行遠端執行

在本節中，我們將示範如何對驅動程式和執行程式進行偵錯。

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. 設定一個中斷點，然後選取**偵錯**圖示。

   ![選取偵錯圖示](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. 當程式執行觸及中斷點時，您會在 [偵錯工具] 窗格中看到一個 [驅動程式] 索引標籤和兩個 [執行程式] 索引標籤。 選取**繼續程式**圖示以繼續執行程式碼，然後到達下一個中斷點並著重於對應的 [執行程式] 索引標籤。您可以在對應的 [主控台] 索引標籤中檢閱執行記錄。

   ![[Debug] \(偵錯\) 索引標籤](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>案例 2：執行遠端偵錯和錯誤修正
在本節中，我們示範如何使用 IntelliJ 偵錯功能進行簡單修正，以動態更新變數值。 在下列程式碼範例中，因為目標檔案已存在，所以會擲回例外狀況。
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>執行遠端偵錯和錯誤修正
1. 設定兩個中斷點，然後選取**偵錯**圖示以啟動遠端偵錯程序。

2. 程式碼會在第一個中斷點停止，然後在 [變數] 窗格中顯示參數和變數資訊。 

3. 選取**繼續程式**圖示以繼續。 程式碼會在第二個點停止。 正如預期，會攔截到例外狀況。

  ![擲回錯誤](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. 再次選取**繼續程式**圖示。 [HDInsight Spark Submission] \(HDInsight Spark 提交) 視窗會顯示「作業執行失敗」錯誤。

  ![提交錯誤](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. 若要使用 IntelliJ 偵錯功能動態更新變數值，請再次選取 [偵錯]。 [變數] 窗格會再次出現。 

6. 以滑鼠右鍵按一下 [Debug] \(偵錯\) 索引標籤上的目標，然後選取 [Set Value] \(設定值\)。 接下來，輸入變數的新值。 然後選取 **Enter** 儲存值。 

  ![設定值](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. 選取**繼續程式**圖示以繼續執行程式。 此時，不會攔截到任何例外狀況。 您可以看到專案成功執行而未發生任何例外狀況。

  ![偵錯而未發生例外狀況](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>接續步驟
* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>示範
* 建立 Scala 專案 (影片)：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用適用於 IntelliJ 的 Azure 工具組對 HDInsight 叢集上的 Spark 應用程式進行遠端偵錯)

### <a name="scenarios"></a>案例
* [Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [Spark 串流：使用 HDInsight 中的 Spark 來建置即時串流應用程式](apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 進行網站記錄分析](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式
* [使用 Scala 建立獨立應用程式](../hdinsight-apache-spark-create-standalone-application.md)
* [利用 Livy 在 Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 IntelliJ 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ 的 Azure 工具組透過 VPN 對 Spark 應用程式進行遠端偵錯](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具建立 Spark 應用程式](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [利用 HDInsight 上的 Spark 叢集來使用 Zeppelin Notebook](apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Spark 叢集中 Jupyter Notebook 可用的核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
