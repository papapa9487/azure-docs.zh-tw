---
title: "Azure SQL Database 計量和診斷記錄 | Microsoft Docs"
description: "了解如何設定 Azure SQL Database 資源，以儲存資源使用量、連線及查詢執行統計資料。"
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bf41aa530c68ea0e94a09d1dab63237c6f42bce7
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database 計量和診斷記錄 
Azure SQL Database 可以發出計量和診斷記錄，以便進行監視。 您可以將 Azure SQL Database 設定為將資源使用量、背景工作與工作階段及連線儲存到下列其中一項 Azure 資源：
- **Azure 儲存體**：用於封存大量遙測資料，價格低廉
- **Azure 事件中樞**：用於整合 Azure SQL Database 遙測與自訂監視解決方案或管線
- **Azure Log Analytics**：適用於具有報告、警示及緩和功能的現成監視解決方案 

    ![架構](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>啟用記錄

預設未啟用計量和診斷記錄功能。 您可以使用下列其中一種方法來啟用及管理計量和診斷記錄功能︰
- Azure 入口網站
- PowerShell
- Azure CLI
- REST API 
- Resource Manager 範本

當您啟用計量和診斷記錄功能時，您必須指定要收集所選資料的 Azure 資源。 可用的選項︰
- Log Analytics
- 事件中樞
- Azure 儲存體 

您可以佈建新的 Azure 資源，或選取現有的資源。 選取儲存體資源之後，您需要指定要收集的資料。 可用的選項包括︰

- **[1 分鐘的計量](sql-database-metrics-diag-logging.md#1-minute-metrics)** - 包含 DTU 百分比、DTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆封鎖的連線、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、XTP 儲存體百分比

如果您指定事件中樞或 Azure 儲存體帳戶，您可指定保留原則，以指定刪除早於所選期間的資料。 如果您指定 Log Analytics，則保留原則取決於所選的定價層。 深入了解 [Log Analytics 價格](https://azure.microsoft.com/pricing/details/log-analytics/)。 

建議您閱讀 [Microsoft Azure 中的計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 診斷記錄概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章，以了解如何啟用記錄和各種 Azure 服務支援的計量和記錄類別。

### <a name="azure-portal"></a>Azure 入口網站

若要在 Azure 入口網站中啟用計量和診斷記錄收集功能，請瀏覽至您的 Azure SQL 資料庫或彈性集區頁面，然後按一下 [診斷設定]。

   ![在 Azure 入口網站中啟用](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

若要使用 Powershell 啟用計量和診斷記錄功能，請使用下列 Cmdlet：

- 若要啟用儲存體帳戶中的診斷記錄檔的儲存體，使用下列命令︰

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   儲存體帳戶識別碼是您要將記錄檔傳送至此的儲存體帳戶的資源識別碼。

- 若要啟用將診斷記錄檔串流至事件中樞，使用下列命令︰

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   服務匯流排規則識別碼是此格式的字串︰

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 若要啟用將診斷記錄檔傳送到 Log Analytics 工作區，請使用此命令︰

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 您可以使用下列命令取得 Log Analytics 工作區的資源識別碼︰

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

您可以結合這些參數讓多個輸出選項。

### <a name="cli"></a>CLI

若要使用 Azure CLI 啟用計量和診斷記錄功能，請使用下列 Cmdlet：

- 若要啟用儲存體帳戶中的診斷記錄檔的儲存體，使用下列命令︰

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   儲存體帳戶識別碼是您要將記錄檔傳送至此的儲存體帳戶的資源識別碼。

- 若要啟用將診斷記錄檔串流至事件中樞，使用下列命令︰

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   服務匯流排規則識別碼是此格式的字串︰

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 若要啟用將診斷記錄檔傳送到 Log Analytics 工作區，請使用此命令︰

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

您可以結合這些參數讓多個輸出選項。

### <a name="rest-api"></a>REST API

了解如何[使用 Azure 監視器 REST API 變更診斷設定](https://msdn.microsoft.com/library/azure/dn931931.aspx)。 

### <a name="resource-manager-template"></a>Resource Manager 範本

了解如何[使用 Resource Manager 範本在建立資源時啟用診斷設定](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)。 

## <a name="stream-into-log-analytics"></a>串流到 Log Analytics 中 
使用 Azure 入口網站中內建的「傳送到 Log Analytics」選項，或透過 Azure PowerShell Cmdlet、Azure CLI 或 Azure 監視器 REST API 在診斷設定中啟用 Log Analytics，即可將 Azure SQL Database 計量和診斷記錄串流到 Log Analytics 中。

### <a name="installation-overview"></a>安裝概觀

透過 Log Analytics 可以輕易監視 Azure SQL Database Fleet。 需要三個步驟：

1.  建立 Log Analytics 資源
2.  將資料庫設定為將計量和診斷記錄記錄到已建立的 Log Analytics 中
3.  在 Log Analytics 中從資源庫安裝 **Azure SQL 分析**解決方案

### <a name="create-log-analytics-resource"></a>建立 Log Analytics 資源

1. 按一下左側功能表中的 [新增]。
2. 按一下 [監視 + 管理]
3. 按一下 [Log Analytics]
4. 在 Log Analytics 表單中填入所需的其他資訊：工作區名稱、訂用帳戶、資源群組、位置及定價層。

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>將資料庫設定為記錄計量和診斷記錄

若要設定資料庫記錄其計量的位置，最簡單的方法就是透過 Azure 入口網站。 在 Azure 入口網站中，瀏覽至您的 Azure SQL Database 資源，然後按一下 [診斷設定]。 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>從資源庫安裝 Azure SQL 分析解決方案  

1. 一旦建立 Log Analytics 資源，您的資料會流入其中，請安裝 Azure SQL 分析解決方案。 這可以透過**解決方案資源庫**完成，您可以在 OMS 首頁上和側邊功能表中找到該資源庫。 在資源庫中，尋找並按一下 [Azure SQL 分析] 解決方案，然後按一下 [新增]。

   ![監視解決方案](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. 您的 OMS 首頁上隨即出現名為 [Azure SQL 分析] 的新圖格。 選取此圖格可開啟 [Azure SQL 分析] 儀表板。

### <a name="using-azure-sql-analytics-solution"></a>使用 Azure SQL 分析解決方案

Azure SQL 分析是一個階層式儀表板，可讓您瀏覽 Azure SQL Database 資源階層。 這項功能可讓您執行高階監視，但也可讓您將監視範圍設定為適當的資源集合。
儀表板在所選的資源之下包含不同的資源清單。 例如，對於所選的訂用帳戶，您可以看到屬於所選訂用帳戶的所有伺服器、彈性集區和資料庫。 此外，對於彈性集區和資料庫，您可以看到該資源的資源使用量計量。 這包括 DTU、CPU、IO、LOG、工作階段、背景工作、連線和儲存體 (以 GB 為單位) 的圖表。

## <a name="stream-into-azure-event-hub"></a>串流到 Azure 事件中樞中

使用 Azure 入口網站中內建的「串流到事件中樞」選項，或透過 Azure PowerShell Cmdlet、Azure CLI 或 Azure 監視器 REST API 在診斷設定中啟用服務匯流排規則，即可將 Azure SQL Database 計量和診斷記錄串流到事件中樞中。 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>如何在事件中樞處理計量和診斷記錄？
一旦所選的資料串流到事件中樞，您很快就能啟用進階監視案例。 事件中樞能做為事件管線的「大門」，一旦收集的資料進入事件中樞，它可以使用任何即時分析提供者或批次/儲存配接器轉換及儲存資料。 事件中樞能分隔事件串流的生產與這些事件的使用，讓事件消費者依照自己的排程存取事件。 如需事件中樞的詳細資訊，請參閱：

- [Azure 事件中樞是什麼](../event-hubs/event-hubs-what-is-event-hubs.md)？
- [開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


這裡有一些您可以使用串流功能的方法：

-   透過將「最忙碌路徑」串流至 PowerBI 以檢視服務健康情況 – 您可以使用事件中樞、串流分析和 PowerBI，輕鬆快速地將計量和診斷資料轉換為 Azure 服務上的深入解析。 如需如何設定事件中樞、使用串流分析處理資料，以及使用 PowerBI 作為輸出的概觀，請參閱[串流分析和 Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)。
-   將記錄串流至第三方記錄和遙測資料流 – 使用事件中樞串流，您可以將計量和診斷記錄放入不同的第三方監視和記錄分析解決方案中。 
-   建置自訂遙測及記錄平台 – 如果您已有自建遙測平台或正好在考慮建置一個，事件中樞所具備的高度可調整的發佈訂閱特質可讓您靈活擷取診斷記錄。 請參閱 [Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="stream-into-azure-storage"></a>串流到 Azure 儲存體中

使用 Azure 入口網站中內建的「封存到儲存體帳戶」選項，或透過 Azure PowerShell Cmdlet、Azure CLI 或 Azure 監視器 REST API 在診斷設定中啟用 Azure 儲存體，即可將 Azure SQL Database 計量和診斷記錄儲存到 Azure 儲存體中。

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>儲存體帳戶中的計量和診斷記錄結構描述

設定計量和診斷記錄集合後，當第一批資料列可用時，系統會在您選取的儲存體帳戶中建立儲存體容器。 這些 blob 的結構為：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
或者，形式更簡單：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，1 分鐘計量的 blob 名稱可能是︰

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

如果您想要記錄彈性集區中的資料，blob 名稱會有點不同：

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>從 Azure 儲存體下載計量和記錄

請參閱[從 Azure 儲存體下載計量和診斷記錄](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

## <a name="1-minute-metrics"></a>1 分鐘計量

| |  |
|---|---|
|**Resource**|**計量**|
|資料庫|DTU 百分比、使用的 DTU、DTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、成功/失敗/防火牆封鎖的連線、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、XTP 儲存體百分比、死結 |
|彈性集區|eDTU 百分比、使用的 eDTU、eDTU 限制、CPU 百分比、實體資料讀取百分比、記錄寫入百分比、工作階段百分比、背景工作百分比、儲存體、儲存體百分比、儲存體限制、XTP 儲存體百分比 |
|||

## <a name="next-steps"></a>後續步驟

- 閱讀 [Microsoft Azure 中的計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 診斷記錄概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文章，以了解如何啟用記錄和各種 Azure 服務支援的計量和記錄類別。
- 閱讀下列文章來了解事件中樞：
   - [Azure 事件中樞是什麼](../event-hubs/event-hubs-what-is-event-hubs.md)？
   - [開始使用事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- 請參閱[從 Azure 儲存體下載計量和診斷記錄](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

