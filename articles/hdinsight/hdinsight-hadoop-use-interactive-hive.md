---
title: "搭配 Azure HDInsight 使用互動式 Hive | Microsoft Docs"
description: "了解如何搭配 HDInsight 使用互動式 Hive (Hive LLAP)。"
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>搭配 Azure HDInsight 使用互動式 Hive (預覽)
互動式 Hive (又稱為 Hive LLAP 或 [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) 為新的 Azure HDInsight [叢集類型](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。 互動式 Hive 支援記憶體內快取，可讓 Hive 查詢速度更快且更具互動性。 

互動式 Hive 叢集與 Hadoop 叢集不同。 它只包含 Hive 服務。 

> [!NOTE]
> 很快地，MapReduce、Pig、Sqoop、Oozie 和其他服務便會從這個叢集類型中移除。
> 您只能透過 Ambari Hive 檢視、Beeline 和 Microsoft Hive 開放式資料庫連接驅動程式 (Hive ODBC)，存取互動式 Hive 叢集中的 Hive 服務。 您無法透過 Hive 主控台、Templeton、Azure 命令列工具 (Azure CLI) 或 Azure PowerShell 來存取它。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>建立互動式 Hive 叢集
您只能在以 Linux 為基礎的叢集上使用互動式 Hive 叢集。 如需如何建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="execute-hive-queries-from-interactive-hive"></a>從互動式 Hive 執行 Hive 查詢
若要執行 Hive 查詢，您有下列選項：

* 使用 Ambari Hive 檢視執行 Hive。
  
    如需使用 Hive 檢視的詳細資訊，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md)。
* 使用 Beeline 執行 Hive。
  
    如需在 HDInsight 中使用 Beeline 的詳細資訊，請參閱[利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop](hdinsight-hadoop-use-hive-beeline.md)。
  
    您可以從前端節點或空白邊緣節點使用 Beeline。 我們的建議是從空白邊緣節點使用 Beeline。 如需使用空白邊緣節點建立 HDInsight 叢集的詳細資訊，請參閱[在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 執行 Hive。
  
    如需使用 Hive ODBC 的詳細資訊，請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。

若要尋找 Java 資料庫連線 (JDBC) 連接字串：

1. 使用下列 URL 登入 Ambari：https://\<cluster name\>.AzureHDInsight.net。
2. 在左側功能表中，選取 **Hive**。
3. 若要複製 URL，選取剪貼簿圖示：
   
   ![HDInsight Hadoop 互動式 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>後續步驟
* 了解如何[在 HDInsight 中建立互動式 Hive 叢集](hdinsight-hadoop-provision-linux-clusters.md)。
* 了解如何[使用 Beeline 在 HDInsight 中執行 Hive 查詢](hdinsight-hadoop-use-hive-beeline.md)。
* 了解如何[使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。


