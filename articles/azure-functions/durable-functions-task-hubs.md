---
title: "Durable Functions 中的工作中樞 - Azure"
description: "了解在 Azure Functions 的 Durable Functions 擴充中，什麼是工作中樞。 了解如何設定工作中樞。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 313daf1c105caa8569ed43e59d9e18f184599214
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的工作中樞

[Durable Functions](durable-functions-overview.md) 的「工作中樞」是單一 Azure 儲存體帳戶內協調流程和活動的邏輯容器。 同一工作中樞裡可以有多個函式，甚至是函式應用程式，而且工作中樞通常當作應用程式容器。

不需要明確建立工作中樞。 執行階段會利用 *host.json* 檔案中宣告的名稱，自動初始化工作中樞。 在單一儲存體帳戶內，每個工作中樞有其自己的儲存體佇列、資料表和 blob。 在指定工作中樞裡執行的所有函式應用程式，共用相同的儲存體資源。 協調器和活動函式必須屬於相同的工作中樞，才能彼此互動。

## <a name="configuring-a-task-hub-in-hostjson"></a>在 host.json 中設定工作中樞

您可以在函式應用程式的 *host.json* 檔案中設定工作中樞名稱。

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定函式應用程式的工作中樞名稱，則預設為 **DurableFunctionsHub**。

> [!NOTE]
> 如果您有多個函式應用程式共用一個儲存體帳戶，建議為每個函式應用程式設定不同的工作中樞名稱。 這可確保每個函式應用程式彼此適當地隔離。

## <a name="azure-storage-resources"></a>Azure 儲存體資源

工作中樞由數個 Azure 儲存體資源所組成：

* 一或多個控制佇列。
* 一個工作項目佇列。
* 一個記錄資料表。
* 一個儲存體容器，含有一或多個租用 blob。

當協調器或活動函式執行時，或已排程執行時，就會在預設 Azure 儲存體帳戶中自動建立所有這些資源。 [效能和級別](durable-functions-perf-and-scale.md)一文說明如何使用這些資源。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)

