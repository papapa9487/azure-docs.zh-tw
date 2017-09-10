---
title: "啟用 Batch 事件的診斷記錄 - Azure | Microsoft Docs"
description: "記錄並分析 Azure Batch 帳戶資源 (如集區和工作) 的診斷記錄事件。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>記錄事件以便對 Batch 解決方案進行診斷評估和監視

和許多 Azure 服務一樣，Batch 服務會在資源存留期間發出特定資源的記錄事件。 您可以啟用 Azure Batch 診斷記錄來記錄資源 (如集區和工作) 的事件，接著使用記錄進行診斷評估和監視。 集區建立、集區刪除、工作開始、工作完成及其他事件等，都會包含在 Batch 診斷記錄中。

> [!NOTE]
> 本文討論 Batch 帳戶資源本身的記錄事件，而非作業和工作輸出資料。 如需儲存作業和工作輸出資料的詳細資訊，請參閱[保存 Azure Batch 作業和工作輸出](batch-task-output.md)。
> 
> 

## <a name="prerequisites"></a>必要條件
* [Azure Batch 帳戶](batch-account-create-portal.md)
* [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  若要保留 Batch 診斷記錄，您必須建立 Azure 將用來儲存記錄的 Azure 儲存體帳戶。 當您為您的 Batch 帳戶[啟用診斷記錄](#enable-diagnostic-logging)時，請指定此儲存體帳戶。 您啟用記錄收集時指定的儲存體帳戶不同於[應用程式封裝](batch-application-packages.md)和[工作輸出持續性](batch-task-output.md)文件中提及的連結儲存體帳戶。
  
  > [!WARNING]
  > 儲存在您的 Azure 儲存體帳戶中的資料需要**收費**。 這包括本文討論的診斷記錄。 在設計您的[記錄保留原則](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)時請牢記這一點。
  > 
  > 

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄
您的 Batch 帳戶預設未啟用診斷記錄。 您必須為每個您想要監視的 Batch 帳戶明確啟用診斷記錄︰

[如何啟用診斷記錄的收集](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

建議您閱讀完整的 [Azure 診斷記錄概觀](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)文件，以了解如何啟用記錄和各種 Azure 服務支援的記錄類別。 例如，Azure Batch 目前支援一種記錄類別︰**服務記錄檔**。

## <a name="service-logs"></a>服務記錄檔
Azure Batch 服務記錄檔包含 Batch 資源 (如集區或工作) 的存留期間，由 Azure Batch 服務發出的事件。 Batch 所發出的每個事件會以 JSON 格式儲存在指定的儲存體帳戶。 例如，**集區建立事件**範例的主體為：

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

每個事件主體位於指定的 Azure 儲存體帳戶中的 .json 檔案內。 如果您想要直接存取記錄檔，您可能想要檢閱 [儲存體帳戶中的診斷記錄結構描述](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)。

## <a name="service-log-events"></a>服務記錄檔事件
Batch 服務目前會發出下列服務記錄檔事件。 這份清單可能不完整，因為自上次更新此文件後可能已新增額外的事件。

| **服務記錄檔事件** |
| --- |
| [建立集區][pool_create] |
| [開始刪除集區][pool_delete_start] |
| [完成集區刪除][pool_delete_complete] |
| [開始調整集區大小][pool_resize_start] |
| [完成集區大小調整][pool_resize_complete] |
| [開始工作][task_start] |
| [完成工作][task_complete] |
| [工作失敗][task_fail] |

## <a name="next-steps"></a>後續步驟
除了將診斷記錄事件儲存在 Azure 儲存體帳戶外，您也可以將 Batch 服務記錄檔事件串流處理到 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)，並傳送至 [Azure Log Analytics](../log-analytics/log-analytics-overview.md)。

* [將 Azure 診斷記錄檔串流至事件中樞](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  將 Batch 診斷事件串流處理至高擴充性的資料輸入服務：事件中樞。 事件中樞每秒可輸入數百萬個事件，您可以使用任何即時分析提供者來轉換和儲存。
* [使用 Log Analytics 分析 Azure 診斷記錄](../log-analytics/log-analytics-azure-storage.md)
  
  將您的診斷記錄傳送至 Log Analytics，您可以在 Operations Management Suite (OMS) 入口網站中分析它們，或是匯出它們以在 Power BI 或 Excel 中分析。

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx

