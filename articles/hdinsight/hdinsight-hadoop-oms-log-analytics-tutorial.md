---
title: "使用 Log Analytics 以監視 Azure HDInsight 叢集 | Microsoft Docs"
description: "了解如何使用 Azure Log Analytics，以監視在 HDInsight 叢集中執行的作業。"
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
ms.date: 11/27/2017
ms.author: nitinme
ms.openlocfilehash: ca2cf642cfff2961dcb0dd18f0e712f61d6915c2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>使用 Azure Log Analytics 監視 HDInsight 叢集

了解如何使用 Azure Log Analytics，監視 HDInsight 中的 Hadoop 叢集作業。

[Log Analytics](../log-analytics/log-analytics-overview.md) 是 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中的一項服務，可監視您的雲端和內部部署環境，以維護其可用性和效能。 它會收集您的雲端和內部部署環境中的資源所產生的資料，以及從其他監視工具提供橫跨多個來源的分析。 

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**。 開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 請參閱[立即建立免費的 Azure 帳戶](https://azure.microsoft.com/free)。

* **Azure HDInsight 叢集**。 目前，您可以使用具有以下 HDInsight 叢集類型的 Azure Operations Management Suite：

    * Hadoop
    * HBase
    * 互動式查詢
    * Kafka
    * Spark
    * Storm

    如需如何建立 HDInsight 叢集的指示，請參閱[開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* **Log Analytics 工作區**。 您可以將此工作區視為唯一的 Log Analytics 環境，有其自己的資料存放庫、資料來源和方案。 您必須擁有一個已建立的此類工作區，您可以讓該工作區與 Azure HDInsight 叢集產生關聯。 如需指示，請參閱[建立 Log Analytics 工作區](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)。

## <a name="enable-log-analytics-by-using-the-portal"></a>使用入口網站啟用 Log Analytics

在本節中，您會設定現有 HDInsight Hadoop 叢集，以使用 Azure Log Analytics 工作區來監視作業、偵錯記錄等等。

1. 在 Azure 入口網站中，開啟 HDInsight 叢集。
2. 在左窗格中，按一下 [監視]。
3. 在右窗格中，按一下 [啟用]，選取現有的 Log Analytics 工作區，然後按一下 [儲存]。

    ![針對 HDInsight 叢集啟用監視](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "針對 HDInsight 叢集啟用監視")

    需要一些時間來儲存設定。  完成後，您可以在頂端看到 [開啟 OMS 儀表板] 按鈕。 

    ![開啟 Operations Management Suite 儀表板](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "開啟 OMS 儀表板")

5. 按一下 [開啟 OMS 儀表板]。
6. 出現提示時，請輸入您的 Azure 認證。

    ![Operations Management Suite 入口網站](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite 入口網站")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>使用 Azure PowerShell 啟用 Log Analytics

您可以使用 Azure PowerShell 啟用 Log Analytics。 此 Cmdlet 是：

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

請參閱 [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)。

若要停用，則 Cmdlet 是 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

請參閱 [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)。


## <a name="next-steps"></a>後續步驟
* [將 HDInsight 叢集管理解決方案新增至 Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
