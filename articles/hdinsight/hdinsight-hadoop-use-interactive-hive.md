---
title: "在 HDInsight 中使用互動式 Hive - Azure | Microsoft Docs"
description: "了解如何在 HDInsight 中使用互動式 Hive (LLAP 上的 Hive)。"
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: fa8b4126865788549217d89f19627f20739f8540
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>在 HDInsight 中使用互動式 Hive (預覽)
互動式 Hive (又稱為. [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) 為新的 HDInsight [叢集類型](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。  互動式 Hive 允許記憶體內快取，可讓 Hive 查詢更具互動性且速度更快。 這項新功能讓 HDInsight 成為全世界效能、彈性和開放性最佳的雲端巨量資料方案之一，因其具有記憶體內快取 (使用 Hive 和 Spark) 與進階分析功能，而這些功能是透過與 R 服務深入整合而來。 

互動式 Hive 叢集與 Hadoop 叢集不同。 它只包含 Hive 服務。 

> [!NOTE]
> 不久之後，MapReduce、Pig、Sqoop、Oozie 和其他服務便會從這個叢集類型中移除。
> 互動式 Hive 叢集中的 Hive 服務只能透過 Ambari Hive 檢視、Beeline 和 Hive ODBC 存取。 無法透過 Hive 主控台、Templeton、Azure CLI 和 Azure PowerShell 存取。 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>建立互動式 Hive 叢集
只有以 Linux 為基礎的叢集可支援互動式 Hive 叢集。 如需建立 HDInsight 叢集的相關資訊，請參閱[在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="execute-hive-from-interactive-hive"></a>從互動式 Hive 執行 Hive
Hive 查詢的執行方式有不同選項可供您選擇︰

* 使用 Ambari Hive 檢視執行 Hive
  
    如需使用 Hive 檢視的相關資訊，請參閱[在 HDInsight 中搭配 Hadoop 使用 Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md)。
* 使用 Beeline 執行 Hive
  
    如需在 HDInsight 上使用 Beeline 的相關資訊，請參閱[利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop](hdinsight-hadoop-use-hive-beeline.md)。
  
    您可以從前端節點或空白邊緣節點使用 Beeline。  我們的建議是從空白邊緣節點使用 Beeline。  如需使用空白邊緣節點建立 HDInsight 叢集的相關資訊，請參閱[在 HDInsight 中使用空白邊緣節點](hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 執行 Hive
  
    如需使用 Hive ODBC 的相關資訊，請參閱[使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)。

**若要尋找 JDBC 連接字串︰**

1. 使用下列 URL 登入 Ambari：https://<ClusterName>.AzureHDInsight.net。
2. 按一下左側功能表中的 [Hive]  。
3. 按一下醒目提示的圖示複製 URL：
   
   ![HDInsight Hadoop 互動式 Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>另請參閱
* [在 HDInsight 中建立以 Linux 為基礎的 Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)︰了解如何在 HDInsight 中建立互動式 Hive 叢集。
* [利用 Beeline 搭配使用 Hive 與 HDInsight 中的 Hadoop](hdinsight-hadoop-use-hive-beeline.md)︰了解如何使用 Beeline 提交 Hive 查詢。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)：了解如何將 Excel 連接到 Hive。


