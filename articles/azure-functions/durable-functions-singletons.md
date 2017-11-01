---
title: "Durable Functions 的單次個體 - Azure"
description: "如何在 Azure Functions 的 Durable Functions 擴充中使用單次個體。"
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
ms.openlocfilehash: 8384d17405653a29207cdfa4f6143504d0db2022
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的單次協調器

對於背景作業或執行者形式的協調流程，您通常必須確定特定的協調流程一次只執行一個執行個體。 作法就是在 [Durable Functions](durable-functions-overview.md) 中，只要在建立協調器時指派特定的執行個體識別碼即可。

## <a name="singleton-example"></a>單次個體範例

下列 C# 範例示範 HTTP 觸發程序函式如何建立單次背景作業協調流程。 程式碼可確保一個指定的執行個體識別碼只存在一個執行個體。

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

根據預設，執行個體識別碼是隨機產生的 GUID。 但在此案例中，執行個體識別碼是從 URL 傳入路由資料中。 程式碼會呼叫 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_)，來檢查具有指定識別碼的執行個體是否已在執行中。 如果沒有，則會以該識別碼建立執行個體。

協調器函式的實作細節實際上不重要。 它可能是會啟動並完成的一般協調器函式，也可能是永遠執行的函式 (也就是[永久性協調流程](durable-functions-eternal-orchestrations.md))。 重點是一次只有一個執行個體在執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何呼叫子協調流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [執行範例單次個體](durable-functions-counter.md)
