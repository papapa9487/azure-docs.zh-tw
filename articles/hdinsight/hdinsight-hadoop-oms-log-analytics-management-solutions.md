---
title: "將 HDInsight 叢集管理解決方案新增至 Azure Log Analytics | Microsoft Docs"
description: "了解如何使用 Azure Log Analytics 以建立 HDInsight 叢集的自訂檢視。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>將 HDInsight 叢集管理解決方案新增至 Log Analytics (預覽)

HDInsight 提供叢集特定的管理解決方案，您可以為 Azure Log Analytics 新增。 [管理解決方案](../log-analytics/log-analytics-add-solutions.md)可將功能新增至 OMS，以提供其他資料和分析工具給 Log Analytics。 這些解決方案會從您的 HDInsight 叢集收集重要效能計量，並且提供工具以搜尋計量。 這些解決方案也會針對 HDInsight 中支援的大部分叢集類型，提供視覺效果和儀表板。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。 

在本文中，您會學習如何將叢集特定的管理解決方案新增至 OMS 工作區。

## <a name="prerequisites"></a>必要條件

* 您必須先設定好 HDInsight 叢集，才可使用 Azure Log Analytics。 如需指示，請參閱[搭配使用 Azure Log Analytics 與 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="add-cluster-specific-management-solutions"></a>新增叢集特定的管理解決方案

在本節中，您會將 HBase 叢集管理解決方案新增至現有的 OMS 工作區。 其他 HDInsight 叢集類型的類似解決方案即將推出。

1. 開啟 OMS 儀表板。 在 Azure 入口網站中，開啟與 Azure Log Analytics 相關聯的 HDInsight 叢集刀鋒視窗，按一下 [監視] 索引標籤，接著按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

1. 在 OMS 儀表板中，按一下 [解決方案資源庫]，或是左窗格的 [檢視設計工具] 圖示。

    ![在 OMS 中新增管理解決方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "在 OMS 中新增管理解決方案")

2. 在 [解決方案資源庫] 中，尋找 [HDInsight HBase 監控]，然後按一下圖格。

    ![尋找 HBase 管理解決方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "尋找 HBase 管理解決方案")

3. 在下一個畫面中，按一下 [新增]。

     ![新增 HBase 管理解決方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "新增 HBase 管理解決方案")

4. 您現在應該會在 HBase 管理解決方案的 OMS 儀表板上看到圖格。 如果您與 OMS (這篇文章必要條件的一部分) 相關聯的叢集是 HBase 叢集，則會並排顯示叢集名稱和叢集中的節點數目。

    ![已新增 HBase 管理解決方案](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "已新增HBase 管理解決方案")

## <a name="next-steps"></a>後續步驟

* [查詢 Azure Log Analytics 以監視 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>另請參閱

* [使用 OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [在 Log Analytics 中建立警示規則](../log-analytics/log-analytics-alerts-creating.md)

