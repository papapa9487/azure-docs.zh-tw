---
title: "使用 Azure HDInsight 來為 YARN 進行疑難排解 | Microsoft Docs"
description: "取得有關使用 Apache Hadoop YARN 和 Azure HDInsight 的常見問題解答。"
keywords: "Azure HDInsight, YARN, 常見問題集, 疑難排解指南, 常見問題"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: a30d7a8e3f2e11a0c5ed05c5b68d75d32b138fb9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>使用 Azure HDInsight 進行 YARN 的疑難排解

了解在 Apache Ambari 中使用 Apache Hadoop YARN 承載時最常發生的問題及其解決方法。

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>如何在叢集上建立新的 YARN 佇列？


### <a name="resolution-steps"></a>解決步驟 

在 Ambari 使用下列步驟建立新的 YARN 佇列，然後平衡所有佇列之間的容量配置。 

在本例中，兩個現有的佇列 (**預設**和 **thriftsvr**) 都從 50% 的容量變更為 25% 的容量，讓新的佇列 (spark) 有 50% 的容量。
| 佇列 | 容量 | 最大容量 |
| --- | --- | --- | --- |
| 預設值 | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. 選取 [Ambari 檢視] 圖示，然後選取格線模式。 接著，選取 [YARN 佇列管理員]。

    ![選取 Ambari 檢視圖示](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. 選取 [預設] 佇列。

    ![選取預設佇列](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. 對於 [預設] 佇列，將 [容量] 從 50% 變更為 25%。 對於 [thriftsvr] 佇列，將 [容量] 變更為 25%。

    ![將預設和 thriftsvr 佇列的容量變更為 25%](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. 若要建立新的佇列，請選取 [新增佇列]。

    ![選取 [新增佇列]](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. 命名新的佇列。

    ![將佇列命名為 Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. 將 [容量] 值保持在 50%，然後選取 [動作] 按鈕。

    ![選取 [動作] 按鈕](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. 選取 [Save and Refresh Queues] \(儲存並重新整理佇列)。

    ![選取 [Save and Refresh Queues] \(儲存並重新整理佇列)。](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

這些變更都會立即顯示在 YARN 排程器 UI 中。

### <a name="additional-reading"></a>其他閱讀資料

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>如何下載叢集的 YARN 記錄？


### <a name="resolution-steps"></a>解決步驟 

1. 使用安全殼層 (SSH) 用戶端連線到 HDInsight 叢集。 如需詳細資訊，請參閱[其他閱讀資料](#additional-reading-2)。

2. 若要列出目前執行中的 YARN 應用程式本身的應用程式識別碼，請執行下列命令：

    ```apache
    yarn top
    ```
    識別碼會列在 [APPLICATIONID] 資料行。 您可以從 [APPLICATIONID] 資料行下載記錄檔。

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

3. 若要下載所有應用程式主機的 YARN 容器記錄，請使用下列命令：
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    此命令會建立名為 amlogs.txt 的記錄檔。 

4. 若要下載僅有最新應用程式主機的 YARN 容器記錄，請使用下列命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    此命令會建立名為 latestamlogs.txt 的記錄檔。 

4. 若要下載前兩部應用程式主機的 YARN 容器記錄，請使用下列命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    此命令會建立名為 first2amlogs.txt 的記錄檔。 

5. 若要下載所有 YARN 容器記錄，請使用下列命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    此命令會建立名為 logs.txt 的記錄檔。 

6. 若要下載特定容器的 YARN 容器記錄，請使用下列命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    此命令會建立名為 containerlogs.txt 的記錄檔。

### <a name="additional-reading-2"></a>其他閱讀資料

- [使用 SSH 連線到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN 概念與應用程式](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>另請參閱
[使用 Azure HDInsight 進行疑難排解](hdinsight-troubleshoot-guide.md)







