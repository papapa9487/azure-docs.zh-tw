---
title: "藉由依狀態計算工作以監視作業的進度 - Azure Batch | Microsoft Docs"
description: "藉由呼叫「取得工作計數」來計算某個作業的工作，以監視作業的進度。 您可以取得作用中、執行中和已完成工作的計數，並依照成功或失敗的工作計算。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: tamram
ms.openlocfilehash: ceff59d7063b60a1344a47489d3d73e0e8ee07df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>依狀態計算工作以監視作業的進度 (預覽)

Azure Batch 提供有效的方式來監視作業在執行其工作時的進度。 您可以呼叫[取得工作計數][rest_get_task_counts]作業，找出多少工作處於作用中、執行中或已完成狀態，以及有多少成功或失敗的工作。 藉由計算每個狀態的工作數目，您可以更輕鬆地向使用者顯示作業的進度，或偵測可能會影響作業的未預期延遲或失敗。

> [!IMPORTANT]
> 「取得工作計數」作業目前為預覽狀態，尚未在 Azure Government、Azure China 和 Azure Germany 中提供。 
>
>

## <a name="how-tasks-are-counted"></a>工作的計算方式

「取得工作計數」作業會依狀態計算工作，如下所示：

- 已排入佇列的工作會算作是**作用中**的工作並且能夠執行，但目前尚未指派至任何計算節點。 如果工作相依於尚未完成的父工作，也會算作是**作用中**的工作。 如需有關工作相依性的詳細資訊，請參閱[建立工作相依性，以執行相依於其他工作的工作](batch-task-dependencies.md)。 
- 已指派至計算節點的工作會算作是**執行中**的工作，但尚未完成。 處於 `preparing` 或 `running` 狀態的工作會算作是**執行中**的工作，如[取得工作相關資訊][rest_get_task]作業所示。
- 不再有資格執行的工作會算作是**已完成**的工作。 算作是**已完成**的工作通常已順利完成，或未順利完成且也已達到其重試限制。 

「取得工作計數」作業也會回報有多少成功或失敗的工作。 Batch 會檢查 [executionInfo](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo) 的**結果**屬性 屬性，以判斷工作成功或失敗：

    - 如果工作執行結果為 `success`，則工作會算作是**成功**。
    - 如果工作執行結果為 `failure`，則工作會算作是**失敗**。

如需工作狀態的詳細資訊，請參閱[取得工作相關資訊][rest_get_task]。

下列 .NET 程式碼範例示範如何依狀態擷取工作計數： 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> 您可以使用類似 REST 的模式和其他支援的語言來取得作業的工作計數。 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>工作計數的一致性檢查

Batch 服務會從非同步分散式系統的多個部分收集資料，進而彙總工作計數。 為了確保工作計數正確無誤，Batch 可對多個系統元件執行一致性檢查，以提供狀態計數的額外驗證。 只要作業中的工作少於 200,000 個，Batch 就會執行這些一致性檢查。 在一致性檢查找到錯誤的極罕見事件中，Batch 會根據一致性檢查的結果來更正「取得工作計數」作業的結果。 一致性檢查是額外的措施，可確保依賴「取得工作計數」作業的客戶，取得其所需的正確解決方案資訊。

回應中的 **ValidationStatus** 屬性會指出 Batch 是否已執行一致性檢查。 如果 Batch 無法對系統中保留的實際狀態檢查狀態計數，則 **validationStatus** 屬性會設定為 `unvalidated`。 基於效能考量，如果作業包括 200,000 個以上的工作，Batch 將不會執行一致性檢查，所以在此情況下 **validationStatus** 屬性可能會設定為 `unvalidated`。 不過，在此情況下，工作計數不一定錯誤，因為即使是非常有限的資料遺失也不太可能。 

當工作變更狀態時，彙總管線會在幾秒鐘內處理變更。 「取得工作計數」作業會反映在該期間內更新的工作計數。 不過，如果彙總管線遺漏工作狀態變更，則在下一次進行驗證前，不會登錄該變更。 在此期間，工作計數可能會因為遺漏事件而稍微不正確，但是會在下一次進行驗證時更正。

## <a name="best-practices-for-counting-a-jobs-tasks"></a>計算作業之工作的最佳做法

呼叫「取得工作計數」作業是依狀態傳回作業之工作基本計數的最有效方式。 如果您使用 Batch 服務版本 2017-06-01.5.1，建議您撰寫或更新程式碼以使用「取得工作計數」。

「取得工作計數」作業不適用於早於 2017-06-01.5.1 的 Batch 服務版本。 如果您使用舊版的服務，則改用清單查詢來計算作業中的工作。 如需詳細資訊，請參閱[建立查詢以便有效率地列出 Batch 資源](batch-efficient-list-queries.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Batch 服務概念和功能，請參閱 [Batch 功能概觀](batch-api-basics.md) 。 本文討論主要 Batch 資源 (例如集區、計算節點、作業和工作)，並提供服務功能的概觀。
* 了解使用 [Batch .NET 用戶端程式庫](batch-dotnet-get-started.md)或 [Python](batch-python-tutorial.md) 開發啟用 Batch 之應用程式的基本概念。 這些簡介文章會介紹使用 Batch 服務在多個計算節點上執行工作負載的可行應用程式。


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
