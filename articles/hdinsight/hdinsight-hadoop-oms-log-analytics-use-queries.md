---
title: "查詢 Azure Log Analytics 以監視 Azure HDInsight 叢集 | Microsoft Docs"
description: "了解如何在 Log Analytics 上執行查詢，以監視在 HDInsight 叢集中執行的作業。"
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
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: e4ee80826a710bde9483d130a4d1c986a72645ca
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>查詢 Azure Log Analytics 以監視 HDInsight 叢集

了解如何使用 Azure Log Analytics 監視 Azure HDInsight 叢集的一些基本案例：

* [分析 HDInsight 叢集計量](#analyze-hdinsight-cluster-metrics)
* [搜尋特定的記錄訊息](#search-for-specific-log-messages)
* [建立事件警示](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>必要條件

* 您必須先設定好 HDInsight 叢集，才可使用 Azure Log Analytics。 如需指示，請參閱[搭配使用 Azure Log Analytics 與 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

* 您必須已將 HDInsight 叢集特定的管理解決方案新增至 OMS 工作區，如同[將 HDInsight 叢集管理解決方案新增至 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)中的描述。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 叢集計量

了解如何為您的 HDInsight 叢集尋找特定的計量。

1. 在 Azure 入口網站中，開啟您已經與 Azure Log Analytics 相關聯的 HDInsight 叢集。
2. 按一下 [監視]，然後按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在左側功能表上按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 在搜尋方塊中輸入以下查詢，針對已設定為使用 Azure Log Analytics 的所有 HDInsight 叢集搜尋所有可用的計量，然後按 **ENTER**。

        `search *` 

    ![搜尋所有計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜尋所有計量")

    輸出應該看起來像：

    ![搜尋所有計量的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "都搜尋所有計量的輸出")

5. 在左窗格中的 [類型] 下，選取您想要深入探討的計量，然後按一下 [套用]。 以下螢幕擷取畫面顯示選取了 `metrics_resourcemanager_queue_root_default_CL` 類型。 

    > [!NOTE]
    > 您可能需要按一下 [[+] 更多資訊] 按鈕來尋找您要的計量。 此外，[套用] 按鈕位於清單底部，您必須向下捲動才看的到。
    > 
    >    

    請注意，文字方塊中的查詢會如同以下螢幕擷取畫面所示：

    ![搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜尋特定計量")

6. 深入探討此特定計量。 例如，您可以使用下列查詢精簡既有輸出，探討每 10 分鐘內不同叢集名稱的平均使用資源：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 除了根據平均使用資源精簡輸出外，還可以使用下列查詢，以每 10 分鐘為範圍，根據資源使用的尖峰時間 (以及第 90 個或第 95 個百分位數) 來精簡結果：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>搜尋特定的記錄訊息

了解如何查看特定時間範圍的錯誤訊息。 這裡的步驟只是其中一個方法範例，讓您可以找到想了解的錯誤訊息。 您可以使用任何可用屬性來找出想尋找的錯誤。

1. 在 Azure 入口網站中，開啟您已經與 Azure Log Analytics 相關聯的 HDInsight 叢集。
2. 按一下 [監視]，然後按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在 OMS 儀表板的主畫面上，按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 輸入以下查詢，針對已設定為使用 Azure Log Analytics 的所有 HDInsight 叢集搜尋所有錯誤訊息，然後按 **ENTER**。 

         search "Error"

    您應該會看到如下的輸出：

    ![搜尋所有錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "搜尋所有錯誤的輸出")

5. 在左窗格中的 [類型] 類別下，選取您想要深入探討的錯誤類型，然後按一下 [套用]。  請注意結果會調整成只顯示您所選取類型的錯誤。
7. 您可以使用左側窗格提供的選項深入探討此特定錯誤。 例如， 

    - 查看特定背景工作節點的錯誤訊息：

        ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "搜尋特定錯誤的輸出")

    - 查看特定時間發生的錯誤：

        ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "搜尋特定錯誤的輸出")

9. 查看特定的錯誤。 您可以按一下 [[+] 顯示更多]，以查看實際的錯誤訊息。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "搜尋特定錯誤的輸出")

## <a name="create-alerts-for-tracking-events"></a>建立追蹤事件的警示

建立警示的第一個步驟是取得要觸發警示的查詢。 為方便說明，我們使用下列查詢，該查詢會提供在 HDInsight 叢集上執行失敗的應用程式清單。

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

您可以使用任何您想要建立警示的查詢。

1. 在 Azure 入口網站中，開啟您已經與 Azure Log Analytics 相關聯的 HDInsight 叢集。
2. 按一下 [監視]，然後按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在 OMS 儀表板的主畫面上，按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 執行下列您想要建立警示的查詢，然後按 **ENTER**。

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. 按一下頁面頂端的 [警示]。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "輸入要建立警示的查詢")

4. 在 [新增警示規則] 視窗中，輸入查詢和其他詳細資料以建立警示，然後按一下 [儲存]。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "輸入要建立警示的查詢")

    螢幕擷取畫面顯示在警示查詢傳回輸出時傳送電子郵件通知的設定。

5. 您也可以編輯或刪除現有警示。 若要這樣做，請從 OMS 入口網站中的任何頁面上，按一下 [設定] 圖示。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "輸入要建立警示的查詢")

6. 按一下 [設定] 頁面上的 [警示]，以查看您已建立的警示。 您也可以啟用、停用、編輯或刪除警示。 如需詳細資訊，請參閱[使用 Log Analytics 中的警示規則](../log-analytics/log-analytics-alerts-creating.md)。

## <a name="see-also"></a>另請參閱

* [使用 OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [在 Log Analytics 中建立警示規則](../log-analytics/log-analytics-alerts-creating.md)
