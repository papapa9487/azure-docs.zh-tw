---
title: "適用於 Apache Kafka 的 Log Analytics - Azure HDInsight | Microsoft Docs"
description: "了解如何在 Azure HDInsight 上使用 Operations Management Suite 分析 Apache Kafka 叢集的記錄。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/11/2017
ms.author: larryfr
ms.openlocfilehash: 856314cafde6059c35963b067287b66c566364a2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-logs-for-apache-kafka-preview-on-hdinsight"></a>在 HDInsight 上分析 Apache Kafka 的記錄 (預覽)

了解如何在 HDInsight 上使用 Microsoft Operations Management Suite 分析 Apache Kafka 產生的記錄。

## <a name="enable-oms-for-kafka"></a>針對 Kafka 啟用 OMS

針對 HDInsight 啟用 Log Analytics 的步驟，對於所有 HDInsight 叢集來說都一樣。 若要了解如何建立和設定所需的服務，請使用下列連結：

1. 建立 Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics)文件。

2. 在 HDInsight 叢集上建立 Kafka。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md) 文件。

3. 設定 Kafka 叢集以使用 Log Analytics。 如需詳細資訊，請參閱[使用 Log Analytics 來監視 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) 文件。

    > [!NOTE]
    > 您也可以使用 `Enable-AzureRmHDInsightOperationsManagementSuite` Cmdlet，將叢集設定為使用 Log Analytics。 這個 Cmdlet 需要下列資訊︰
    >
    > * HDInsight 叢集名稱。
    > * Log Analytics 的工作區識別碼。 您可以在 Log Analytics 工作區的 OMS 工作區中找到工作區識別碼。
    > * OMS 連線的主索引鍵。 若要尋找主索引鍵，請依序選取 Log Analytics 執行個體和 [OMS 入口網站]。 在 OMS 入口網站中，依序選取 [設定]、[連接的來源] 及 [Linux 伺服器]。


> [!IMPORTANT]
> 供 Log Analytics 使用的資料可能需要約 20 分鐘後才能準備完畢。

## <a name="query-logs"></a>查詢記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Log Analytics 工作區。

2. 選取 [記錄搜尋]。 您可以在這裡搜尋從 Kafka 收集而來的資料。 以下是一些範例搜尋：

    * 磁碟使用量：`Type=Perf ObjectName="Logical Disk" (CounterName="Free Megabytes")  InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by   Computer interval 1HOUR`
    * CPU 使用率：`Type:Perf CounterName="% Processor Time" InstanceName="_Total" Computer='hn*-*' or Computer='wn*-*' | measure avg(CounterValue) by Computer interval 1HOUR`
    * 每秒傳入訊息：`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s interval 1HOUR`
    * 每秒傳入位元組：`Type=metrics_kafka_CL HostName_s="wn0-kafka" InstanceName_s="kafka-BrokerTopicMetrics-BytesInPerSec-Count" | measure avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) interval 1HOUR`
    * 每秒傳出位元組：`Type=metrics_kafka_CL ClusterName_s="your_kafka_cluster_name" InstanceName_s="kafka-BrokerTopicMetrics-BytesOutPerSec-Count" |  measure avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) interval 1HOUR`

    > [!IMPORTANT]
    > 請將查詢值取代為叢集特有的資訊。 例如，`ClusterName_s` 必須設定為您的叢集名稱。 `HostName_s` 必須設定為叢集中背景工作角色節點的網域名稱。

    您也可以輸入 `*` 來搜尋所有記錄的類型。 目前我們提供以下記錄的查詢：

    | 記錄類型 | 說明 |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![CPU 使用率搜尋的影像](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>後續步驟

如需 Log Analytics 的詳細資訊，請參閱[開始使用 Log Analytics 工作區](../../log-analytics/log-analytics-get-started.md)文件。

如需使用 Kafka 的詳細資訊，請參閱下列文件：

 * [ HDInsight 叢集之間製作 Kafka 的鏡像](apache-kafka-mirroring.md)
 * [在 HDInsight 上增加 Kafka 的 延展性](apache-kafka-scalability.md)
 * [搭配 Kafka 使用 Spark 串流 (DStream)](../hdinsight-apache-spark-with-kafka.md)
 * [搭配 Kafka 使用 Spark 結構化串流 (DStream)](../hdinsight-apache-kafka-spark-structured-streaming.md)