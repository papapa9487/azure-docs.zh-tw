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
ms.openlocfilehash: b241bad7b0060551eba5e78efbb1b729bf5d0098
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的工作中樞

[Durable Functions](durable-functions-overview.md) 中的「工作中樞」是協調流程所使用之 Azure 儲存體資源的邏輯容器。 協調器和活動函式必須屬於相同的工作中樞，才能彼此互動。

每個函式應用程式都有各自的工作中樞。 如果多個函式應用程式共用儲存體帳戶，則儲存體帳戶會包含多個工作中心。 下圖說明每個函式應用程式在共用和專用儲存體帳戶中的一個工作中樞。

![顯示共用和專用儲存體帳戶的圖表。](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure 儲存體資源

工作中樞由下列儲存體資源所組成： 

* 一或多個控制佇列。
* 一個工作項目佇列。
* 一個記錄資料表。
* 一個儲存體容器，含有一或多個租用 blob。

當協調器或活動函式執行時，或已排程執行時，就會在預設 Azure 儲存體帳戶中自動建立所有這些資源。 [效能和延展性](durable-functions-perf-and-scale.md)一文說明如何使用這些資源。

## <a name="task-hub-names"></a>工作中樞名稱

工作中樞可透過 host.json 檔案中所宣告的名稱來加以識別，如下列範例所示：

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定，則預設名稱是 **DurableFunctionsHub**。

> [!NOTE]
> 當共用儲存體帳戶中有多個工作中樞時，可透過名稱來區分各個工作中樞。 如果您有多個函式應用程式共用了共用儲存體帳戶，您必須在 host.json 檔案中為每個工作中樞設定不同的名稱。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)
