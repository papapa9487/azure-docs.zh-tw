---
title: "檢視 Azure Data Lake Analytics 的診斷記錄 | Microsoft Docs"
description: "了解如何設定及存取 Azure Data Lake Analytics 的診斷記錄  "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 6c74db1659742aa41306388273bec46800ba7609
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>存取 Azure Data Lake Analytics 的診斷記錄

診斷記錄可讓您收集資料存取稽核線索。 這些記錄檔可提供如下資訊︰

* 資料的存取使用者清單。
* 資料的存取頻率。
* 帳戶中儲存的資料量。

## <a name="enable-logging"></a>啟用記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 開啟 Data Lake Analytics 帳戶，然後從 [監視] 區段選取 [診斷記錄]。 接下來，選取 [開啟診斷]。

    ![開啟診斷以收集稽核和要求記錄](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. 從 [診斷設定] 中，將狀態設為 [啟用]，然後選取記錄選項。

    ![開啟診斷以收集稽核和要求記錄](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "啟用診斷記錄")

   * 將 [狀態] 設定為 [開啟] 以啟用診斷記錄。

   * 您可以選擇三種不同的資料儲存/處理方法。

     * 選取 [封存至儲存體帳戶] 可將記錄儲存到 Azure 儲存體帳戶。 如果您想要封存資料，請使用此選項。 如果您選取此選項，必須提供用來儲存記錄的 Azure 儲存體帳戶。

     * 選取 [串流至事件中樞] 可將記錄資料串流到 Azure 事件中樞。 如果您有即時分析內送記錄的下游處理管線，請使用此選項。 如果您選取此選項，必須提供要使用的 Azure 事件中樞詳細資料。

     * 選取 [傳送至 Log Analytics] 可將資料傳送至 Log Analytics 服務。 如果您想要使用 Log Analytics 來收集和分析記錄，請使用此選項。
   * 指定要取得稽核記錄、要求記錄或兩者。  要求記錄會擷取每個應用程式開發介面 (API) 的要求。 稽核記錄則會記錄該 API 要求觸發的所有作業。

   * 針對 [封存至儲存體帳戶]，請指定要保留資料的天數。

   * 按一下 [檔案] 。

        > [!NOTE]
        > 您必須先選取 [封存至儲存體帳戶]、[串流至事件中樞] 或 [傳送至 Log Analytics]，再按一下 [儲存] 按鈕。

一旦您啟用了診斷設定，即可返回 [診斷記錄] 刀鋒視窗來檢視記錄。

## <a name="view-logs"></a>檢視記錄檔

### <a name="use-the-data-lake-analytics-view"></a>使用 Data Lake Analytics 檢視

1. 從 [Data Lake Analytics 帳戶] 刀鋒視窗的 [監視] 下，選取 [診斷記錄]，然後選取要顯示記錄的項目。

    ![檢視診斷記錄](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "檢視診斷記錄")

2. 記錄類別分為 [稽核記錄] 和 [要求記錄]。

    ![記錄項目](./media/data-lake-analytics-diagnostic-logs/diagnostic-log-entries.png)

   * 要求記錄能擷取所有以 Data Lake Analytics 帳戶提出的 API 要求。
   * 稽核記錄與要求記錄相似，不過能針對作業提供更詳細的明細。 例如，要求記錄中的一個上傳 API 呼叫可能會致使其稽核記錄出現多個「附加」作業。

3. 針對記錄項目按一下 [下載]  連結來下載該記錄。

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>使用包含記錄資料的 Azure 儲存體帳戶

1. 開啟與用於記錄之 Data Lake Analytics 建立關聯的Azure 儲存體帳戶刀鋒視窗，然後按一下 [Blob]。 [Blob 服務]  刀鋒視窗會列出兩個容器。

    ![檢視診斷記錄](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "檢視診斷記錄")

   * 容器 **insights-logs-audit** 包含稽核記錄檔。
   * 容器 **insights-logs-requests** 包含要求記錄檔。
2. 在這些容器中，記錄會儲存在下列結構底下：

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > [Blob 服務] `##` 項目包含記錄檔的建立年、月、日和小時。 Data Lake Analytics 每小時會建立一個檔案，因此 `m=` 一律會包含 `00` 值。

    例如，稽核記錄檔的完整路徑可能是：

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    同樣的，要求記錄檔的完整路徑可能是：

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>記錄檔結構

稽核和要求記錄採用結構化 JSON 格式。

### <a name="request-logs"></a>要求記錄

以下是採用 JSON 格式之要求記錄中的範例項目。 每個 Blob 會一個名為 **記錄** 的根物件，其中包含記錄檔物件的陣列。

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>要求記錄的結構描述

| Name | 類型 | 說明 |
| --- | --- | --- |
| 分析 |String |記錄的時間戳記 (UTC 時間) |
| resourceId |String |執行作業所在資源的識別碼 |
| category |String |記錄類別。 例如， **要求**。 |
| operationName |String |記錄的作業名稱。 例如，GetAggregatedJobHistory。 |
| resultType |String |作業的狀態。例如，200。 |
| callerIpAddress |String |提出要求之用戶端的 IP 位址 |
| correlationId |String |角色的識別碼。 此值可用來群組一組相關的記錄項目。 |
| 身分識別 |Object |產生記錄的身分識別 |
| properties |JSON |請參閱下一節 (要求記錄檔屬性結構描述) 以取得詳細資訊 |

#### <a name="request-log-properties-schema"></a>要求記錄屬性結構描述

| Name | 類型 | 說明 |
| --- | --- | --- |
| HttpMethod |String |作業使用的 HTTP 方法。 例如，GET。 |
| Path |String |執行作業的所在路徑 |
| RequestContentLength |int |HTTP 要求的內容長度 |
| ClientRequestId |String |可唯一識別此要求的識別碼 |
| StartTime |String |伺服器接收到要求的時間 |
| EndTime |String |伺服器傳送回應的時間 |

### <a name="audit-logs"></a>稽核記錄檔

以下是採用 JSON 格式之稽核記錄中的範例項目。 每個 Blob 會一個名為 **記錄** 的根物件，其中包含記錄檔物件的陣列。

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>稽核記錄的結構描述

| Name | 類型 | 說明 |
| --- | --- | --- |
| 分析 |String |記錄的時間戳記 (UTC 時間) |
| resourceId |String |執行作業所在資源的識別碼 |
| category |String |記錄類別。 例如， **稽核**。 |
| operationName |String |記錄的作業名稱。 例如，JobSubmitted。 |
| resultType |String |作業狀態 (operationName) 的子狀態。 |
| resultSignature |String |作業狀態 (operationName) 的其他詳細資料。 |
| 身分識別 |String |要求作業的使用者。 例如，susan@contoso.com。 |
| properties |JSON |請參閱下一節 (稽核記錄檔屬性結構描述) 以取得詳細資訊 |

> [!NOTE]
> **resultType** 和 **resultSignature** 會提供作業結果的相關資訊，並且只會在作業完成時才包含值。 例如，只有當 **operationName** 包含 **JobStarted** 或 **JobEnded** 的值時，它們才會包含值。
>
>

#### <a name="audit-log-properties-schema"></a>稽核記錄屬性結構描述

| Name | 類型 | 說明 |
| --- | --- | --- |
| JobId |String |指派給作業的識別碼 |
| JobName |String |為作業提供的名稱 |
| JobRunTime |String |用來處理作業的執行階段 |
| SubmitTime |String |作業提交時間 (UTC 格式) |
| StartTime |String |作業在提交後開始執行的時間 (UTC 格式) |
| EndTime |String |作業結束時間 |
| 平行處理原則 |String |在提交期間為此作業要求的 Data Lake Analytics 單位數目 |

> [!NOTE]
> **SubmitTime**、**StartTime**、**EndTime** 和 **Parallelism** 提供作業的相關資訊。 這些項目只會在作業啟動或完成時才包含值。 例如，**SubmitTime** 只會在 **operationName** 具有 **JobSubmitted** 值之後包含值。

## <a name="process-the-log-data"></a>處理記錄資料

Azure Data Lake Analytics 會提供有關如何處理和分析記錄資料的範例。 您可以在 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)找到範例。

## <a name="next-steps"></a>後續步驟
* [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)

