---
title: "將 Azure 診斷記錄串流至事件中樞命名空間 | Microsoft Docs"
description: "了解如何將 Azure 診斷記錄串流至事件中樞命名空間。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.openlocfilehash: 01ba8ddfcf90e1368ac147296fd180f99420d96f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hubs-namespace"></a>將 Azure 診斷記錄串流至事件中樞命名空間
您可以使用入口網站中內建的「匯出至事件中樞」選項，或透過 Azure PowerShell Cmdlet 或 Azure CLI 來啟用診斷設定中服務匯流排規則識別碼的方式，以近乎即時的速度將 **[Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)**串流至任何應用程式。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>您可以使用診斷記錄和事件中樞執行的項目
這裡有一些您可以使用診斷日誌串流功能的方法：

* **串流至第三方記錄與遙測系統** – 經過一段時間，事件中樞串流會成為將活動記錄輸送到第三方 SIEM 與記錄分析解決方案的機制。
* **將「最忙碌路徑」串流至 PowerBI 以檢視服務健全狀況** – 您可以使用事件中樞、串流分析和 PowerBI，輕鬆快速地將診斷資料轉換為 Azure 服務上的深入解析。 [此文件文章提供絕佳概觀，說明如何設定事件中樞、使用串流分析處理資料，以及使用 PowerBI 作為輸出](../stream-analytics/stream-analytics-power-bi-dashboard.md)。 以下是設定診斷記錄的一些祕訣︰
  
  * 當您勾選入口網站中的選項，或透過 PowerShell 進行啟用時，就會自動建立診斷記錄類別的事件中樞，因此您需要選取命名空間中名稱開頭為 **insights-** 的事件中樞。
  * 下列 SQL 程式碼是您可以使用的範例串流分析查詢，能將所有記錄資料剖析至 PowerBI 表格：

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **建置自訂遙測及記錄平台** – 如果您已有自建遙測平台或正好在考慮建置一個，事件中樞所具備的高度可調整的發佈訂閱特質可讓您靈活擷取診斷記錄檔。 [請參閱此處的 Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="enable-streaming-of-diagnostic-logs"></a>啟用診斷記錄的串流
您可以透過入口網站或使用 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings)，啟用以程式控制方式對診斷記錄進行串流的功能。 無論如何，您所建立的診斷設定可讓您指定事件中樞命名空間，以及記錄類別和您需要傳送至命名空間的計量。 針對您所啟用的每個記錄類別，會在命名空間中建立事件中樞。 診斷**記錄類別**是一種資源可以收集的記錄類型。

> [!WARNING]
> 啟用和串流來自計算資源 (例如，VM 或 Service Fabric) 的診斷記錄 [需要一組不同的步驟](../event-hubs/event-hubs-streaming-azure-diags-data.md)。
> 
> 

服務匯流排或事件中樞命名空間不一定要和資源發出記錄檔屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權。

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用入口網站串流診斷記錄
1. 在入口網站中，瀏覽至 Azure 監視器，然後按一下 [診斷設定]

    ![Azure 監視器的監視區段](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. 選擇性地依資源群組或資源類型篩選清單，然後按一下您要設定診斷設定的資源。

3. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「開啟診斷」。

   ![新增診斷設定 - 無現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   如果資源上已有設定，您將會看此資源上已設定的設定清單。 按一下「新增診斷設定」。

   ![新增診斷設定 - 現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. 為設定提供名稱並核取 [串流至事件中樞] 方塊，然後選取 [事件中樞命名空間]。
   
   ![新增診斷設定 - 現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   選取的命名空間將會是事件中樞的建立所在 (如果這是您第一次的串流診斷記錄) 或串流處理的目的地 (如果已存在將該記錄檔分類串流至此命名空間的資源)，而原則會定義串流機制擁有的權限。 目前，將事件串流到中樞需要管理、傳送和接聽的權限。 您可以在入口網站的 [設定] 索引標籤下，為您的命名空間建立或修改事件中樞命名空間共用存取原則。 若要更新其中一個診斷設定，用戶端必須擁有事件中樞授權規則的 ListKey 權限。

4. 按一下 [儲存] 。

過了幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且每次產生新的事件資料，都會將診斷記錄串流至該儲存體帳戶。

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
若要透過 [Azure PowerShell Cmdlet](insights-powershell-samples.md) 啟用串流功能，您可以使用 `Set-AzureRmDiagnosticSetting` Cmdlet 搭配下列參數︰

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -ServiceBusRuleId [your Service Bus rule ID] -Enabled $true
```

服務匯流排規則識別碼是此格式的字串︰`{Service Bus resource ID}/authorizationrules/{key name}`，例如，`/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey`。

### <a name="via-azure-cli"></a>透過 Azure CLI
若要透過 [Azure CLI](insights-cli-samples.md) 啟用串流功能，您可以使用如下的 `insights diagnostic set` 命令︰

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

如 PowerShell Cmdlet 所述，對服務匯流排規則識別碼使用相同的格式。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>我要如何透過事件中樞取用記錄檔資料？
此處是來自事件中樞的範例輸出資料：

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| 元素名稱 | 說明 |
| --- | --- |
| 記錄數 |此承載中的所有記錄事件的陣列。 |
| 分析 |事件發生的時間。 |
| category |此事件的記錄檔分類。 |
| resourceId |產生此事件之資源的資源識別碼。 |
| operationName |作業名稱。 |
| 層級 |選用。 表示記錄事件層級。 |
| properties |事件的屬性。 |

您可以在[這裡](monitoring-overview-of-diagnostic-logs.md)檢視支援串流至事件中樞的所有資源提供者清單。

## <a name="stream-data-from-compute-resources"></a>從計算資源中串流資料
您也可以使用 Windows Azure 診斷代理程式，從計算資源中串流診斷記錄。 [請參閱本文章](../event-hubs/event-hubs-streaming-azure-diags-data.md)了解如何設定。

## <a name="next-steps"></a>後續步驟
* [深入了解 Azure 診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)
* [開始使用事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

