---
title: "Log Analytics 中的 Azure SQL Analytics 解決方案 | Microsoft Docs"
description: "Azure SQL Analytics 解決方案可協助您管理 Azure SQL Database。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: banders
ms.openlocfilehash: 0b0d91b130172eb3506fdebb9547ab6ba5cc3780
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>使用 Azure SQL Database (預覽) 監視 Log Analytics 中的 Azure SQL Database

![Azure SQL 分析符號](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics 中的 Azure SQL 分析解決方案會收集並以視覺化方式檢視重要的 SQL Azure 效能計量。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。 而且，您可以跨多個 Azure 訂用帳戶和彈性集區監視 Azure SQL Database 和彈性集區計量，並將其視覺化。 解決方案也可協助您找出應用程式堆疊中每個層級的問題。  它會使用 [Azure 診斷計量](log-analytics-azure-storage.md)與 Log Analytics 檢視來呈現單一 Log Analytics 工作區中所有 Azure SQL Database 和彈性集區的相關資料。

目前，此預覽解決方案針對每個工作區支援高達 150,000 個 Azure SQL Database 和 5,000 個 SQL 彈性集區。

Azure SQL 分析解決方案，如同其他可用的 Log Analytics，可協助您監視並接收您 Azure 資源健康情況的相關通知，在此情況下為 Azure SQL Database。 Microsoft Azure SQL Database 是可調整的關聯式資料庫服務，可對 Azure 雲端中執行的應用程式提供熟悉的 SQL Server 類似功能。 Log Analytics 可協助您收集、相互關聯，並以視覺化方式檢視結構化和非結構化資料。

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析解決方案不使用代理程式連線至 Log Analytics 服務。

下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 說明 |
| --- | --- | --- |
| [Windows 代理程式](log-analytics-windows-agents.md) | 否 | 解決方案不使用直接 Windows 代理程式。 |
| [Linux 代理程式](log-analytics-linux-agents.md) | 否 | 解決方案不使用直接 Linux 代理程式。 |
| [SCOM 管理群組](log-analytics-om-agents.md) | 否 | 解決方案不使用從 SCOM 代理程式直接連線到 Log Analytics。 |
| [Azure 儲存體帳戶](log-analytics-azure-storage.md) | 否 | Log Analytics 不會從儲存體帳戶讀取資料。 |
| [Azure 診斷](log-analytics-azure-storage.md) | 是 | Azure 會將 Azure 計量與記錄資料直接傳送至 Log Analytics。 |

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有帳戶，您可以[免費](https://azure.microsoft.com/free/)建立一個。
- Log Analytics 工作區。 您可以使用現有的帳戶，或者您可以在開始使用此解決方案之前[建立一個新的](log-analytics-get-started.md)。
- 針對您的 Azure SQL Database 和彈性集區啟用 Azure 診斷，並[將其設定為傳送資料至 Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)。

## <a name="configuration"></a>組態

執行下列步驟將 Azure SQL 分析解決方案新增至您的工作區。

1. 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) 或使用[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)中所述的程序，將 Azure SQL Analytics 解決方案新增至您的工作區。
2. 在 Azure 入口網站中，按一下 [新增] \(+ 符號)，然後在資源的清單中，選取 [監視 + 管理]。  
    ![監視 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. 在 [監視 + 管理] 清單中，按一下 [檢視全部]。
4. 在 [建議]清單中，按一下 [詳細]，然後在新的清單中，尋找 **Azure SQL 分析 (預覽)**，然後選取它。  
    ![Azure SQL 分析解決方案](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. 在 [Azure SQL 分析 (預覽)] 窗格中，按一下 [建立]。  
    ![建立](./media/log-analytics-azure-sql/portal-create.png)
6. 在 建立新方案 窗格中，選取您想要新增解決方案的工作區，然後按一下建立。  
    ![新增到工作區](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>若要設定多個 Azure 訂用帳戶

若要支援多個訂用帳戶，從[使用 PowerShell 啟用 Azure 資源計量記錄](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)使用 PowerShell 指令碼。 當執行指令碼以將診斷資料從一個 Azure 訂用帳戶中的資源傳送至另一個 Azure 訂用帳戶中的工作區時，提供工作區資源識別碼做為參數。

**範例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>使用解決方案

>[!NOTE]
> 升級 Log Analytics 工作區以取得最新版的 Azure SQL 分析。
>

當您將解決方案新增至您的工作區時，Azure SQL 分析圖格會新增至您的工作區，而且會顯示在 [概觀] 中。 圖格會顯示 Azure SQL Database 和解決方案所連接之 Azure SQL 彈性集區的數目。

![Azure SQL 分析圖格](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

按一下 [Azure SQL 分析] 圖格以開啟 Azure SQL 分析儀表板。 儀表板包含透過不同檢視方塊監視之所有資料庫的概觀。 若要讓不同的檢視方塊運作，您必須在要串流處理至 Azure Log Analytics 工作區的 SQL 資源上，啟用適當的計量或記錄。 

![Azure SQL 分析概觀](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。

![Azure SQL 分析逾時](./media/log-analytics-azure-sql/azure-sql-sol-timeouts.png)

每個檢視方塊都會提供訂用帳戶、伺服器、彈性集區和資料庫層級的摘要。 此外，每個檢視方塊都會在右側顯示檢視方塊專屬的報表。 從清單中選取訂用帳戶、伺服器、集區或資料庫可繼續往下鑽研。

| 檢視方塊 | 說明 |
| --- | --- |
| 資源 (依類型) | 可計算所有受監視資源的檢視方塊。 向下鑽研可提供 DTU 及 GB 計量的摘要。 |
| 深入解析 | 可透過階層的方式，向下鑽研至 Intelligent Insights。 深入了解 Intelligent Insights。 |
| Errors | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 錯誤。 |
| 逾時 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 逾時。 |
| 封鎖 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 封鎖。 |
| 資料庫等候 | 可透過階層的方式，向下鑽研至資料庫層級的 SQL 等候統計資料。 包含總等候時間及每種等候類型等候時間的摘要。 |
| 查詢持續時間 | 可透過階層的方式，向下鑽研至查詢執行統計資料，例如查詢持續時間、CPU 使用量、資料 IO 使用量、記錄 IO 使用量。 |
| 查詢等候 | 可透過階層的方式，依等候類別，向下鑽研至查詢等候統計資料。 |

### <a name="intelligent-insights-report"></a>Intelligent Insights 報表

收集的所有 Intelligent Insights 都可以透過 Insights 檢視方塊視覺化及存取。 [按一下這裡可深入了解 Intelligent Insights](../sql-database/sql-database-intelligent-insights.md)

![Azure SQL 分析見解](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>彈性集區和資料庫報表

彈性集區和資料庫都有自己特定的報表，可顯示在指定的時間，針對資源收集的所有資料。

![Azure SQL 分析資料庫](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Azure SQL 分析彈性集區](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查詢報表

您可以透過查詢持續時間和查詢等候檢視方塊，將任何查詢的效能透過查詢報表相互關聯。 此報表會比較不同資料庫上的查詢效能，並可讓您輕鬆地找出所選查詢執行速度良好與緩慢的資料庫。

![Azure SQL 分析查詢](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>分析資料並建立警示

您可以使用來自 Azure SQL Database 資源的資料，輕鬆建立警示。 以下是您可用於警示的一些實用[記錄搜尋](log-analytics-log-searches.md)查詢：

Azure SQL Database 上的高 DTU

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

Azure SQL Database 彈性集區上的高 DTU

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

您可以針對 Azure SQL Database 和彈性集區，使用這些警示型查詢發出特定閾值警示。 若要設定您 OMS 工作區的警示：

#### <a name="to-configure-an-alert-for-your-workspace"></a>若要設定您工作區的警示

1. 前往 [OMS 入口網站](http://mms.microsoft.com/)並登入。
2. 開啟您針對解決方案所設定之工作區。
3. 在 [概觀] 頁面上，按一下 [Azure SQL 分析 (預覽)] 圖格。
4. 執行其中一個範例查詢。
5. 在記錄搜尋中，按一下 [警示]。  
![在搜尋中建立警示](./media/log-analytics-azure-sql/create-alert01.png)
6. 在 新增警示規則 頁面上，設定您要的適當屬性和特定臨界值，然後按一下儲存。  
![新增警示規則](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="see-also"></a>另請參閱

- 使用 Log Analytics 中的[記錄搜尋](log-analytics-log-searches.md)來檢視詳細的 Azure SQL 資料。
- [建立您自己的儀表板](log-analytics-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](log-analytics-alerts.md)。
