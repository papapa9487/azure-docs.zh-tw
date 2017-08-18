---
title: "Azure HDInsight 文件 - 疑難排解指南 | Microsoft Docs"
description: "對 HDInsight 上的 Hadoop 工作負載進行逐步疑難排解的文件會顯示如何解決 HDInsight 上的常見 Hive、Spark、HBase、Storm、Kafka 問題。"
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 9313f0713818f80bae99c32a438d97ba544a7a6b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Azure HDInsight 疑難排解

| Apache 工作負載 | 最常見問題 |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[HBASE 疑難排解](hdinsight-troubleshoot-hbase.md)|<br>[如何執行回報多個未指派區域的 hbck 命令](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[如何修正區域指派的 hbck 命令逾時問題](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[如何在叢集中強制停用 HDFS 安全模式](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[如何修正與 Apache Phoenix 的 JDBC 或 sqlline 連線問題](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[什麼情況導致主要伺服器無法啟動](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[什麼情況導致區域伺服器上的重新啟動失敗](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[HDFS 疑難排解](hdinsight-troubleshoot-hdfs.md)|<br>[如何從叢集內部存取本機 HDFS](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[如何在叢集中強制停用 HDFS 安全模式](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[HIVE 疑難排解](hdinsight-troubleshoot-hive.md)|<br>[如何匯出 Hive 中繼存放區並匯入另一個叢集](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[如何找出叢集上的 Hive 記錄](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[如何在叢集上以特定設定啟動 Hive Shell](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[如何分析叢集關鍵路徑上的 Tez DAG 資料](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[如何從叢集下載 Tez DAG 資料](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Spark 疑難排解](hdinsight-troubleshoot-SPARK.md)|<br>[如何透過 Ambari 在叢集上設定 Spark 應用程式](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[如何透過 Jupyter 筆記本在叢集上設定 Spark 應用程式](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[如何透過 LIVY 在叢集上設定 Spark 應用程式](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[如何透過 spark-submit 在叢集上設定 Spark 應用程式](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[造成 Spark 應用程式 OutOfMemoryError 例外狀況的原因](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[STORM 疑難排解](hdinsight-troubleshoot-STORM.md)|<br>[如何在叢集上存取 Storm UI](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[如何將 Storm EventHub Spout 檢查點資訊從一個拓撲傳輸到另一個拓撲](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[如何找出叢集上的 Storm 二進位檔](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[如何判斷 Storm 叢集的部署拓撲](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[如何找出用於開發的 Storm-EventHub-Spout 二進位檔](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[如何找出叢集上的 Storm Log4J 組態檔](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN 疑難排解](hdinsight-troubleshoot-YARN.md)|<br>[如何在叢集上建立新的 Yarn 佇列](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[如何下載叢集的 Yarn 記錄](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 疑難排解資源
如需其他疑難排解說明，請參閱下列文章。

| 如需下列資訊 | 請參閱下列主題 |
| --- | --- |
| Linux 上的 HDInsight 與最佳化 | [在 Linux 上使用 HDInsight 的相關資訊](hdinsight-hadoop-linux-information.md)<br>[Hadoop 記憶體和效能](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Hive 查詢效能](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 記錄和傾印 | [存取Linux 上的 YARN 應用程式記錄](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[啟用適用於 Hadoop 服務的堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| 錯誤數 | [了解並解決 WebHCat 錯誤](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[記憶體不足錯誤的 Hive 設定修正](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 工具 | [使用 Ambari 檢視為 Tez 作業偵錯](hdinsight-debug-ambari-tez-view.md)<br>[最佳化 Hive 查詢](hdinsight-hadoop-optimize-hive-query.md) |



