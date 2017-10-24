---
title: "OMS Log Analytics 中的警示回應 | Microsoft Docs"
description: "Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文描述如何建立警示規則及詳細說明可以採取的各種動作。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6d65480c53f905b393409dfdd9952618ab6cb64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>將動作新增至 Log Analytics 中的警示規則
[在 Log Analytics 中建立警示](log-analytics-alerts.md)後，您可以選擇[設定警示規則](log-analytics-alerts.md)以執行一或多個動作。  本文說明各種可用的動作以及設定每種動作的詳細資訊。

| 動作 | 說明 |
|:--|:--|
| [電子郵件](#email-actions) | 傳送內含警示詳細資料的電子郵件給一或多位收件者。 |
| [Webhook](#webhook-actions) | 透過單一 HTTP POST 要求叫用外部處理序。 |
| [Runbook](#runbook-actions) | 在 Azure 自動化中啟動 Runbook。 |


## <a name="email-actions"></a>電子郵件動作
電子郵件動作會傳送內含警示詳細資料的電子郵件給一或多位收件者。  您可以指定郵件的主旨，但其內容是由 Log Analytics 所編製的標準格式。  除了記錄檔搜尋所傳回的最多 10 筆記錄的詳細資料以外，其中還包括摘要資訊 (如警示名稱)。  此外，也包括 Log Analytics 中將從該查詢傳回整個記錄集的記錄檔搜尋的連結。   此郵件的寄件者為 *Microsoft Operations Management Suite Team &lt;noreply@oms.microsoft.com&gt;*。 

電子郵件動作需要下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 主旨 |電子郵件的主旨。  您無法修改郵件的內文。 |
| 收件者 |所有電子郵件收件者的地址。  如果您指定多個地址，則使用分號 (;) 分隔這些地址。 |


## <a name="webhook-actions"></a>Webhook 動作

Webhook 動作可讓您透過單一 HTTP POST 要求叫用外部處理序。  被呼叫的服務應支援 Webhook，並判斷將如何使用所接收的承載。  只要此要求是 API 所認得的格式，您也可以呼叫不會特別支援 Webhook 的 REST API。  使用 Webhook 來回應警示的範例會在 [Slack](http://slack.com) 中傳送訊息，或在 [PagerDuty](http://pagerduty.com/) 中建立事件。  如需使用 Webhook 來呼叫 Slack 以建立警示規則的完整逐步解說，請參閱 [Log Analytics 警示中的 Webhook](log-analytics-alerts-webhooks.md)。

Webhook 動作需要下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| Webhook URL |Webhook 的 URL。 |
| 自訂 JSON 承載 |要隨著 webhook 傳送的自訂承載。  如需詳細資料，請參閱下文。 |


Webhook 包括 URL 以及 JSON 格式的承載 (也就是傳送至外部服務的資料)。  根據預設，承載會包含下表中的值。  您可以選擇用自己的自訂承載來取代此承載。  在此情況下，您可以使用下表中每個參數的變數，將其值包含在您的自訂承載中。

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則 Webhook 承載已變更。  如需格式的詳細資訊，請參閱 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) \(英文\)。  若要查看範例，請參閱下面的[範例](#sample-payload)。

| 參數 | 變數 | 說明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警示規則的名稱。 |
| AlertThresholdOperator |#thresholdoperator |警示規則的臨界值運算子。  大於或等於。 |
| AlertThresholdValue |#thresholdvalue |警示規則的臨界值。 |
| LinkToSearchResults |#linktosearchresults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| ResultCount |#searchresultcount |搜尋結果中的記錄數目。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |查詢的結束時間 (UTC 格式)。 |
| SearchIntervalInSeconds |#searchinterval |警示規則的時間範圍。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |查詢的開始時間 (UTC 格式)。 |
| SearchQuery |#searchquery |警示規則所使用的記錄檔搜尋查詢。 |
| SearchResults |請參閱下方 |查詢所傳回的記錄 (JSON 格式)。  受限於前 5,000 筆記錄。 |
| WorkspaceID |#workspaceid |OMS 工作區的識別碼。 |

例如，您可以指定下列自訂承載，其中包含稱為 text 的單一參數。  此 Webhook 所呼叫的服務需要有這個參數。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此範例承載會在傳送到 Webhook 時解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若要在自訂承載中包含搜尋結果，請將下面一行新增為 json 承載中的最上層屬性。  

    "IncludeSearchResults":true

例如，若要建立只包含警示名稱和搜尋結果的自訂承載，您可以使用下列程式碼。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


您可以[在 OMS Log Analytics 中建立警示 webhook 動作以傳送訊息給 Slack](log-analytics-alerts-webhooks.md)時使用 Webhook 來啟動外部服務，逐步執行建立警示規則的完整範例。


## <a name="runbook-actions"></a>Runbook 動作
Runbook 動作可在 Azure 自動化中啟動 Runbook。  若要使用這類型的動作，您必須在 OMS 工作區中安裝並設定 [自動化解決方案](log-analytics-add-solutions.md) 。  您可以從自動化解決方案中設定的自動化帳戶中的 Runbook 進行選取。

Runbook 動作需要下表中的屬性。

| 屬性 | 說明 |
|:--- |:---|
| Runbook | 建立警示後，您想要啟動的 Runbook。 |
| 執行位置 | 指定 [Azure] 以在雲端執行 Runbook。  指定 [Hybrid Worker]，以在已安裝 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) 的代理程式上執行 Runbook。  |

Runbook 動作會使用 [Webhook](../automation/automation-webhooks.md)來啟動 Runbook。  當您建立警示規則時，系統會自動為 Runbook 建立新的 Webhook，其名稱為 **OMS 警示補救** 後面接著 GUID。  

您無法直接填入 Runbook 的任何參數，但 [$WebhookData 參數](../automation/automation-webhooks.md) 會包含警示的詳細資料，包括記錄檔搜尋 (建立此警示) 的結果。  Runbook 必須定義 **$WebhookData** 做為參數，才能存取警示的屬性。  在 **$WebhookData** 的 **RequestBody** 屬性中，警示資料以 JSON 格式儲存在一個稱為 **SearchResults** 的屬性中。  此資料具有下表中的屬性。

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則 Runbook 承載已變更。  如需格式的詳細資訊，請參閱 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) \(英文\)。  若要查看範例，請參閱下面的[範例](#sample-payload)。

| 節點 | 說明 |
|:--- |:--- |
| id |搜尋的路徑和 GUID。 |
| __metadata |警示的相關資訊，包括搜尋結果的記錄數和狀態。 |
| value |搜尋結果中每一筆記錄的個別項目。  項目的詳細資料會符合記錄的屬性和記錄。 |

例如，下列 Runbook 會擷取記錄搜尋所傳回的記錄，並根據每一筆記錄的類型來指派不同屬性。  請注意，Runbook 會開始從 JSON 轉換 **RequestBody**，讓它可以在 PowerShell 中當作物件來使用。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="sample-payload"></a>範例承載
本節將說明舊版及[已升級的 Log Analytics 工作區](log-analytics-log-search-upgrade.md)中 Webhook 和 Runbook 動作的範例承載。

### <a name="webhook-actions"></a>Webhook 動作

#### <a name="legacy-workspace"></a>舊版工作區。
以下是舊版工作區中 Webhook 動作的範例承載。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Type=Usage",
    "SearchResult": {
        "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/SearchGUID|10.1.0.7|2017-09-27T10-30-38Z",
        "__metadata": {
        "resultType": "raw",
        "total": 1,
        "top": 2147483647,
        "RequestId": "SearchID|10.1.0.7|2017-09-27T10-30-38Z",
        "CoreSummaries": [
            {
            "Status": "Successful",
            "NumberOfDocuments": 135000000
            }
        ],
        "Status": "Successful",
        "NumberOfDocuments": 135000000,
        "StartTime": "2017-09-27T10:30:38.9453282Z",
        "LastUpdated": "2017-09-27T10:30:44.0907473Z",
        "ETag": "636421050440907473",
        "sort": [
            {
            "name": "TimeGenerated",
            "order": "desc"
            }
        ],
        "requestTime": 361
        },
        "value": [
        {
            "Computer": "-",
            "SourceSystem": "OMS",
            "TimeGenerated": "2017-09-26T13:59:59Z",
            "ResourceUri": "/subscriptions/df1ec963-d784-4d11-a779-1b3eeb9ecb78/resourcegroups/mms-eus/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
            "DataType": "Operation",
            "StartTime": "2017-09-26T13:00:00Z",
            "EndTime": "2017-09-26T13:59:59Z",
            "Solution": "LogManagement",
            "BatchesWithinSla": 8,
            "BatchesOutsideSla": 0,
            "BatchesCapped": 0,
            "TotalBatches": 8,
            "AvgLatencyInSeconds": 0.0,
            "Quantity": 0.002502,
            "QuantityUnit": "MBytes",
            "IsBillable": false,
            "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
            "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
            "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
            "Type": "Usage",
            "MG": "00000000-0000-0000-0000-000000000000",
            "__metadata": {
            "Type": "Usage",
            "TimeGenerated": "2017-09-26T13:59:59Z"
            }
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Type%3DUsage",
    "Description": null,
    "Severity": "Low"
    }


#### <a name="upgraded-workspace"></a>已升級的工作區。
以下是已升級的工作區中 Webhook 動作的範例承載。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbook

#### <a name="legacy-workspace"></a>舊版工作區
以下是舊版工作區中 Runbook 動作的範例承載。

    {
        "SearchResults": {
            "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/searchGUID|10.1.0.7|TimeStamp",
            "__metadata": {
                "resultType": "raw",
                "total": 1,
                "top": 2147483647,
                "RequestId": "searchGUID|10.1.0.7|2017-09-27T10-51-43Z",
                "CoreSummaries": [{
                    "Status": "Successful",
                    "NumberOfDocuments": 135000000
                }],
                "Status": "Successful",
                "NumberOfDocuments": 135000000,
                "StartTime": "2017-09-27T10:51:43.3075124Z",
                "LastUpdated": "2017-09-27T10:51:51.1002092Z",
                "ETag": "636421063111002092",
                "sort": [{
                    "name": "TimeGenerated",
                    "order": "desc"
                }],
                "requestTime": 511
            },
            "value": [{
                "Computer": "-",
                "SourceSystem": "OMS",
                "TimeGenerated": "2017-09-26T13:59:59Z",
                "ResourceUri": "/subscriptions/AnotherSubscriptionID/resourcegroups/SampleResourceGroup/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                "DataType": "Operation",
                "StartTime": "2017-09-26T13:00:00Z",
                "EndTime": "2017-09-26T13:59:59Z",
                "Solution": "LogManagement",
                "BatchesWithinSla": 8,
                "BatchesOutsideSla": 0,
                "BatchesCapped": 0,
                "TotalBatches": 8,
                "AvgLatencyInSeconds": 0.0,
                "Quantity": 0.002502,
                "QuantityUnit": "MBytes",
                "IsBillable": false,
                "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
                "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
                "Type": "Usage",
                "MG": "00000000-0000-0000-0000-000000000000",
                "__metadata": {
                    "Type": "Usage",
                    "TimeGenerated": "2017-09-26T13:59:59Z"
                }
            }]
        }
    }

#### <a name="upgraded-workspace"></a>已升級的工作區
以下是已升級的工作區中 Runbook 動作的範例承載。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>後續步驟
- 完成 [設定 Webook](log-analytics-alerts-webhooks.md) 和警示規則的逐步解說。  
- 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。