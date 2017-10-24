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
ms.openlocfilehash: e82cc53d53a6d0296aaab2c3a76ad4e2f6c12c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的單次協調器

對於背景作業或執行者形式的協調流程，您通常必須確定特定的協調流程一次只執行一個執行個體。 作法就是在 [Durable Functions](durable-functions-overview.md) 中，只要在建立協調器時指派特定的執行個體識別碼即可。

## <a name="singleton-example"></a>單次個體範例

下列 C# 範例示範 HTTP 觸發程序函式如何建立單次背景作業協調流程。 範例中使用已知的執行個體識別碼，以確保只有一個執行個體存在。

```cs
[FunctionName("EnsureSingletonTrigger")]
public static async Task<HttpResponseMessage> Ensure(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    // Ensure only one instance is ever running at a time
    const string OrchestratorName = "MySingletonOrchestrator";
    const string InstanceId = "MySingletonInstanceId";

    var existingInstance = await starter.GetStatusAsync(InstanceId);
    if (existingInstance == null)
    {
        log.Info($"Creating singleton instance with ID = {InstanceId}...");
        await starter.StartNewAsync(OrchestratorName, InstanceId, input: null);
    }

    return starter.CreateCheckStatusResponse(req, InstanceId);
}
```

根據預設，執行個體識別碼是隨機產生的 GUID。 但請注意，在此案例中，觸發程序函式使用預先定義的 `InstanceId` 變數，值為 `MySingletonInstanceId`，以預先指派執行個體識別碼給協調器函式。 這可讓觸發程序呼叫 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_)，以檢查已知的執行個體是否已在執行中。

協調器函式的實作細節實際上不重要。 它可能是會啟動並完成的一般協調器函式，也可能是永遠執行的函式 (也就是[永久性協調流程](durable-functions-eternal-orchestrations.md))。 重點是一次只有一個執行個體在執行。

> [!NOTE]
> 單次協調流程執行個體終止、失敗或完成後，就無法以相同的識別碼重新建立。 在這些情況下，您應該準備好以新的執行個體識別碼來重新建立執行個體。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何呼叫子協調流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [執行範例單次個體](durable-functions-counter.md)
