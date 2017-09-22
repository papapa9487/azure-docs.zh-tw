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
ms.date: 09/11/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>查詢 Azure Log Analytics 以監視 HDInsight 叢集 (預覽)

在本文中，您會看到一些搭配使用 Azure Log Analytics 與 Azure HDInsight 的案例。 三個最常見的案例為：

* 在 OMS 中分析 HDInsight 叢集計量
* 搜尋 HDInsight 叢集的特定記錄訊息
* 根據叢集中發生的事件建立警示

## <a name="prerequisites"></a>必要條件

* 您必須先設定好 HDInsight 叢集，才可使用 Azure Log Analytics。 如需指示，請參閱[搭配使用 Azure Log Analytics 與 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

* 您必須已將 HDInsight 叢集特定的管理解決方案新增至 OMS 工作區，如同[將 HDInsight 叢集管理解決方案新增至 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)中的描述。

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>在 OMS 中分析 HDInsight 叢集計量

在本節中，我們會逐步解說如何為您的 HDInsight 叢集尋找特定計量。

1. 開啟 OMS 儀表板。 在 Azure 入口網站中，開啟與 Azure Log Analytics 相關聯的 HDInsight 叢集刀鋒視窗，按一下 [監視] 索引標籤，接著按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在 OMS 儀表板的主畫面上，按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 在 [記錄搜尋] 視窗中的 [在此開始搜尋] 文字方塊中輸入 `*`，會搜尋到所有 HDInsight 叢集 (已設定為可使用 Azure Log Analytics) 的可用計量。 按 ENTER 鍵。

    ![搜尋所有計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜尋所有計量")

4. 您應該會看到如下的輸出。

    ![搜尋所有計量的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "都搜尋所有計量的輸出")

5. 在左側窗格中的 [類型] 類別下，搜尋您想要深入探討的計量。 在本教學課程中，我們選擇 `metrics_resourcemanager_queue_root_default_CL`。 選取計量的對應核取方塊，然後按一下 [套用]。

    > [!NOTE]
    > 您可能需要按一下 [[+] 更多資訊] 按鈕來尋找您要的計量。 此外，[套用] 按鈕位於清單底部，您必須向下捲動才看的到。
    > 
    >    
    請注意，[文字] 方塊中的查詢現在會如同以下螢幕擷取畫面所示：

    ![搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜尋特定計量")

6. 您現在可以深入探討此特定計量。 例如，您現在可以精簡既有輸出，探索每 10 分鐘內不同叢集名稱的平均使用資源。 在查詢文字方塊中，輸入下列查詢。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "搜尋特定計量")

7. 除了根據平均使用資源精簡輸出外，您可以使用下列查詢，以每 10 分鐘為範圍，根據資源使用的尖峰時間 (以及 90 個或第 95 個百分位數) 來精簡結果。

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![搜尋特定計量](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "搜尋特定計量")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>搜尋 HDInsight 叢集中的特定記錄訊息

在本節中，我們會逐步解說如何尋找特定時間範圍內的錯誤訊息。 這裡的步驟只是其中一個方法範例，讓您可以找到想了解的錯誤訊息。 您可以使用任何可用屬性來找出想尋找的錯誤。

1. 開啟 OMS 儀表板。 在 Azure 入口網站中，開啟與 Azure Log Analytics 相關聯的 HDInsight 叢集刀鋒視窗，按一下 [監視] 索引標籤，接著按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在 OMS 儀表板的主畫面上，按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 在 [記錄搜尋] 視窗中的 [在此開始搜尋] 文字方塊中輸入 `"Error"` (包含引號)，會搜尋到所有 HDInsight 叢集 (已設定為可使用 Azure Log Analytics) 的錯誤訊息。 按 ENTER 鍵。

    ![搜尋所有錯誤](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "搜尋所有錯誤")

4. 您應該會看到如下的輸出。

    ![搜尋所有錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "搜尋所有錯誤的輸出")

5. 在左側窗格中的 [類型] 類別下，搜尋您想要深入探討的錯誤類型。 在本教學課程中，我們選擇 `log_sparkappsexecutors_CL`。 選取計量的對應核取方塊，然後按一下 [套用]。

    ![搜尋特定錯誤](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "搜尋特定錯誤")

        
6. 請注意，[文字] 方塊中的查詢現在會如同下列方框所標示，而結果會精簡為僅顯示所選類型的錯誤。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "搜尋特定錯誤的輸出")

7. 您現在可使用左側窗格提供的選項深入探討此特定錯誤。 例如，您可以將查詢精簡為僅查看特定背景工作節點的錯誤訊息。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "搜尋特定錯誤的輸出")

8. 您也可以從左窗格中選取相關的時間值，進一步將範圍限制在您認為發生錯誤的時間。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "搜尋特定錯誤的輸出")

9. 現在，移至所搜尋的特定錯誤底部。 您可以按一下 [[+] 顯示更多]，以查看實際的錯誤訊息。

    ![搜尋特定錯誤的輸出](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "搜尋特定錯誤的輸出")

## <a name="create-alerts-to-track-events"></a>建立追蹤事件的警示

建立警示的第一個步驟是取得要觸發警示的查詢。 為方便說明，我們使用下列查詢，該查詢會提供在 HDInsight 叢集上執行失敗的應用程式清單。

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

您可以使用任何您想要建立警示的查詢。

1. 開啟 OMS 儀表板。 在 Azure 入口網站中，開啟與 Azure Log Analytics 相關聯的 HDInsight 叢集刀鋒視窗，按一下 [監視] 索引標籤，接著按一下 [開啟 OMS 儀表板]。

    ![開啟 OMS 儀表板](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "開啟 OMS 儀表板")

2. 在 OMS 儀表板的主畫面上，按一下 [記錄搜尋]。

    ![開啟記錄搜尋](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "開啟記錄搜尋")

3. 在 [記錄搜尋] 視窗中的 [在此開始搜尋] 文字方塊中，貼上您要建立警示的查詢，然後按 ENTER，接著按一下 [警示] 按鈕。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "輸入要建立警示的查詢")

4. 在 [新增警示規則] 視窗中，輸入查詢和其他詳細資料以建立警示，然後按一下 [儲存]。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "輸入要建立警示的查詢")

    在此螢幕擷取畫面中，我們只在警示查詢擷取到輸出時傳送電子郵件。

5. 您也可以編輯或刪除現有警示。 若要這樣做，請從 OMS 入口網站中的任何頁面上，按一下 [設定] 圖示。

    ![輸入要建立警示的查詢](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "輸入要建立警示的查詢")

6. 按一下 [設定] 頁面上的 [警示]，以查看您已建立的警示。 您也可以啟用、停用、編輯或刪除警示。 如需詳細資訊，請參閱[使用 Log Analytics 中的警示規則](../log-analytics/log-analytics-alerts-creating.md)。

## <a name="see-also"></a>另請參閱

* [使用 OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [在 Log Analytics 中建立警示規則](../log-analytics/log-analytics-alerts-creating.md)

