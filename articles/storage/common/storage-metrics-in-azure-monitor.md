---
title: "Azure 監視器中的 Azure 儲存體計量 | Microsoft Docs"
description: "了解 Azure 監視器所提供的新計量。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: d30a99044e335723e5d2c4bbd71fab7e4fd51145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-metrics-in-azure-monitor-preview"></a>Azure 監視器中的 Azure 儲存體計量 (預覽)

透過 Azure 儲存體的計量，您可以分析使用量趨勢、追蹤要求，以及診斷儲存體帳戶的問題。

Azure 監視器提供了統一的使用者介面供您監視不同的 Azure 服務。 如需詳細資訊，請參閱 [Azure 監視器](../../monitoring-and-diagnostics/monitoring-overview.md)。 Azure 儲存體會藉由將計量資料傳送給 Azure 監視器平台來整合 Azure 監視器。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以從 [Azure 入口網站](https://portal.azure.com)、Azure 監視器 API (REST 和 .Net) 和分析解決方案 (例如 Operation Management Suite 和事件中樞) 存取這些計量。 如需詳細資訊，請參閱 [Azure 監視器計量](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

計量功能會依預設啟用，您可以存取最近 30 天的資料。 如果您需要延長這些資料的保留時間，您可以將計量資料封存到 Azure 儲存體帳戶。 此功能可於 Azure 監視器的[診斷設定](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)中進行設定。

### <a name="access-metrics-in-the-azure-portal"></a>在 Azure 入口網站中存取計量

您可以在 Azure 入口網站中監視不同時間的計量。 下列範例會說明如何檢視帳戶層級的 **UsedCapacity**。

![在 Azure 入口網站中存取計量的螢幕擷取畫面](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

如需支援維度的計量，您必須使用所需的維度值來進行篩選。 下列範例會說明如何檢視**成功**回應類型的帳戶層級**交易**。

![在 Azure 入口網站中存取具有維度之計量的螢幕擷取畫面](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>使用 REST API 存取計量

Azure 監視器提供了 [REST API](/rest/api/monitor/) 來讀取計量定義和值。 本節說明如何讀取儲存體計量。 所有 REST API 都會使用資源識別碼。 如需詳細資訊，請參閱[了解儲存體所含服務的資源識別碼](#understanding-resource-id-for-services-in-storage)。

下列範例說明如何在命令列使用 [ArmClient](https://github.com/projectkudu/ARMClient)，以簡化使用 REST API 來進行測試的方式。

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>使用 REST API 列出帳戶層級的計量定義

下列範例說明如何列出帳戶層級的計量定義：

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

如果您想要列出 Blob、資料表、檔案或佇列的計量定義，就必須使用 API 為每項服務指定不同的資源識別碼。

回應中會包含 JSON 格式的計量定義：

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>使用 REST API 讀取帳戶層級的計量值

下列範例說明如何讀取帳戶層級的計量資料：

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

在上述範例中，如果您想要讀取 Blob、資料表、檔案或佇列的計量值，就必須使用 API 為每項服務指定不同的資源識別碼。

下列回應包含 JSON 格式的計量值：

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

## <a name="billing-for-metrics"></a>計量的計費

在 Azure 監視器中使用計量目前是免費的服務。 不過，如果您使用其他解決方案來擷取計量資料，可能就要支付這些解決方案的使用費。 例如，如果您將計量資料封存到 Azure 儲存體帳戶，就要支付 Azure 儲存體的使用費。 或者，如果您將計量資料串流到 OMS 以進行進階分析，就要支付 Operation Management Suite (OMS) 的使用費。

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>了解 Azure 儲存體所含服務的資源識別碼

資源識別碼是 Azure 資源的唯一識別碼。 當您使用 Azure 監視器 REST API 來讀取計量定義或值時，就必須對所要操作的資源使用資源識別碼。 資源識別碼範本會遵循下列格式：

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

儲存體會對 Azure 監視器提供儲存體帳戶層級和服務層級的計量。 例如，您只能擷取 Blob 儲存體的計量。 每個層級都有自己的資源識別碼，以便用來只擷取該層級的計量。

### <a name="resource-id-for-a-storage-account"></a>儲存體帳戶的資源識別碼

以下顯示用來指定儲存體帳戶資源識別碼的格式。

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>儲存體服務的資源識別碼

以下顯示用來指定每個儲存體服務之資源識別碼的格式。

* Blob 服務資源識別碼`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* 表格服務資源識別碼`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* 佇列服務資源識別碼`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* 檔案服務資源識別碼`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Azure 監視器 REST API 中的資源識別碼

以下顯示在呼叫 Azure 監視器 REST API 時所使用的模式。

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>容量度量

容量計量值會每隔一小時就傳送給 Azure 監視器。 這些值會每天重新整理。 時間粒紋會定義用來呈現計量值的時間間隔。 所有容量計量支援的時間粒紋為一小時 (PT1H)。

Azure 儲存體會提供下列 Azure 監視器容量計量。

### <a name="account-level"></a>帳戶層級

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| UsedCapacity | 儲存體帳戶所使用的儲存體數量。 若為標準儲存體帳戶，此數量是 Blob、資料表、檔案和佇列所使用的容量總和。 若為進階儲存體帳戶和 Blob 儲存體帳戶，此數量和 BlobCapacity 相同。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |

### <a name="blob-storage"></a>Blob 儲存體

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| BlobCapacity | 儲存體帳戶中所使用的 Blob 儲存體總計。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 <br/> 維度：BlobType ([定義](#metrics-dimensions)) |
| BlobCount    | 儲存體帳戶中所儲存的 Blob 物件數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 <br/> 維度：BlobType ([定義](#metrics-dimensions)) |
| ContainerCount    | 儲存體帳戶中的容器數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

### <a name="table-storage"></a>表格儲存體

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| TableCapacity | 儲存體帳戶所使用的表格儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| TableCount   | 儲存體帳戶中的表格數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| TableEntityCount | 儲存體帳戶中的表格實體數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

### <a name="queue-storage"></a>佇列儲存體

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| QueueCapacity | 儲存體帳戶所使用的佇列儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| QueueCount   | 儲存體帳戶中的佇列數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| QueueMessageCount | 儲存體帳戶中未到期的佇列訊息數目。 <br/><br/>單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

### <a name="file-storage"></a>檔案儲存體

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| FileCapacity | 儲存體帳戶所使用的檔案儲存體數量。 <br/><br/> 單位：位元組 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| FileCount   | 儲存體帳戶中的檔案數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |
| FileShareCount | 儲存體帳戶中的檔案共用數目。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 值範例：1024 |

## <a name="transaction-metrics"></a>交易計量

交易計量會每隔一分鐘就從 Azure 儲存體傳送到 Azure 監視器。 系統會同時提供帳戶和服務層級 (Blob 儲存體、表格儲存體、Azure 檔案和佇列儲存體) 的所有交易計量。 時間粒紋會定義用來呈現計量值的時間間隔。 所有交易計量支援的時間粒紋為 PT1H 和 PT1M。

Azure 儲存體會提供下列 Azure 監視器交易計量。

| 度量名稱 | 說明 |
| ------------------- | ----------------- |
| 交易 | 向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 <br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 適用維度：ResponseType、GeoType、ApiName ([定義](#metrics-dimensions))<br/> 值範例：1024 |
| 輸入 | 輸入資料量。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| 輸出 | 輸出資料量。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。 <br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 適用維度：GeoType、ApiName ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessServerLatency | Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。 <br/><br/> 單位：毫秒 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| SuccessE2ELatency | 向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。 <br/><br/> 單位：毫秒 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName ([定義](#metrics-dimensions)) <br/> 值範例：1024 |
| Availability | 儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將可計費的要求值總計除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。 <br/><br/> 單位：百分比 <br/> 彙總類型：平均 <br/> 適用維度：GeoType、ApiName ([定義](#metrics-dimensions)) <br/> 值範例：99.99 |

## <a name="metrics-dimensions"></a>計量維度

Azure 儲存體支援下列 Azure 監視器計量維度。

| 維度名稱 | 說明 |
| ------------------- | ----------------- |
| BlobType | 只適用於 Blob 計量的 Blob 類型。 支援的值有 **BlockBlob** 和 **PageBlob**。 附加 Blob 隨附於 BlockBlob。 |
| ResponseType | 交易回應類型。 可用的值包括： <br/><br/> <li>ServerOtherError：描述項目之外的其他所有伺服器端錯誤 </li> <li> ServerBusyError：傳回 HTTP 503 狀態碼的已驗證要求。 (尚不支援) </li> <li> ServerTimeoutError：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 逾時是因為伺服器錯誤而發生。 </li> <li> ThrottlingError：用戶端和伺服器端節流錯誤的總和 (在支援 ServerBusyError 和 ClientThrottlingError 之後便會移除) </li> <li> AuthorizationError：由於未經授權存取資料或授權失敗，從而發生失敗的已驗證要求。 </li> <li> NetworkError：由於網路錯誤而失敗的已驗證要求。 當用戶端在逾時到期前就過早關閉連線時，最常會發生這個情況。 </li> <li>  ClientThrottlingError：用戶端節流錯誤 (尚不支援) </li> <li> ClientTimeoutError：已逾時並傳回 HTTP 500 狀態碼的已驗證要求。 如果用戶端的網路逾時或要求逾時設定為比儲存體服務預期的值還低，則此值是符合預期的逾時。 否則，它會回報為 ServerTimeoutError。 </li> <li> ClientOtherError：描述項目之外的其他所有用戶端錯誤。 </li> <li> Success：成功的要求|
| GeoType | 來自主要或次要叢集的交易。 可用的值包括 Primary 和 Secondary。 在從次要租用戶讀取物件時，此維度會套用到讀取權限異地備援儲存體 (RA-GRS)。 |
| ApiName | 作業的名稱。 例如： <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> 如需所有的作業名稱，請參閱[文件](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md)。 |

對於計量支援維度，您必須指定維度值才能查看對應的計量值。 例如，如果您要查看成功回應的 **Transactions** 值，則需要篩選具有 **Success** 值的 **ResponseType** 維度。 或者，如果您要查看區塊 Blob 的 **BlobCount** 值，就需要篩選具有 **BlockBlob** 值的 **BlobType** 維度。

## <a name="service-continuity-of-legacy-metrics"></a>舊版計量的服務不會中斷

舊版計量可與 Azure 監視器管理的計量並存。 在 Azure 儲存體結束舊版計量的服務之前，支援不會改變。 在正式發行 Azure 監視器管理的計量後，我們會宣布結束的計劃。

## <a name="see-also"></a>另請參閱

* [Azure 監視器](../../monitoring-and-diagnostics/monitoring-overview.md)
