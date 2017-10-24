---
title: "Durable Functions 中的計時器 - Azure"
description: "了解如何在 Azure Functions 的 Durable Functions 擴充中實作永久性計時器。"
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
ms.openlocfilehash: 27312846caf4f51e708b48655578846a52cbd1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="timers-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的計時器

[Durable Functions](durable-functions-overview.md) 提供「永久性計時器」，用於協調器函式中實作延遲，或在非同步動作上設定逾時。 永久性計時器應該用於協調器函式中，以代替 `Thread.Sleep` 或 `Task.Delay`。

您可以呼叫[CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) 來建立永久性計時器。 此方法會傳回一個在指定日期和時間繼續執行的工作。

## <a name="timer-limitations"></a>計時器限制

當您建立一個在下午 4:30 過期的計時器時，基礎「永久性工作架構」會將訊息加入佇列，而此訊息只在下午 4:30 出現。 在 Azure Functions 取用量方案中執行時，新出現的計時器訊息可確保函式應用程式會在適當的虛擬機器上啟動。

> [!WARNING]
> * 由於 Azure 儲存體中的限制，永久性計時器無法持續超過 7 天。 我們正努力解決[將計時器延長到 7 天以上的功能要求](https://github.com/Azure/azure-functions-durable-extension/issues/14)。
> * 在計算永久性計時器的相對期限時，請一律使用 [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) 代替 `DateTime.UtcNow`，如下列範例所示。

## <a name="usage-for-delay"></a>延遲的使用方式

下列範例說明如何使用永久性計時器來延遲執行。 此範例會在十天內每天發出帳單通知。

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallFunctionAsync("SendBillingEvent");
    }
}
```

> [!WARNING]
> 在協調器函式中避免無限迴圈。 如需有關如何安全而有效率地實作無限迴圈案例的資訊，請參閱[永久性協調流程](durable-functions-eternal-orchestrations.md)。 

## <a name="usage-for-timeout"></a>逾時的使用方式

此範例說明如何使用永久性計時器來實作逾時。

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallFunctionAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!WARNING]
> 如果程式碼不會等候永久性計時器完成，請使用 `CancellationTokenSource` 來取消它。 等到所有未完成的工作都完成或取消之後，「永久性工作架構」才會將協調流程的狀態變更為「已完成」。

這項機制並不會實際終止進行中的活動函式執行。 只是讓協調器函式略過結果並繼續執行。 如果函式應用程式使用取用量方案，您仍然要為已放棄的活動函式所耗用的任何時間和記憶體付費。 根據預設，在取用量方案中執行的函式會在五分鐘後逾時。 如果超過此限制，Azure Functions 主機會重新開機來停止所有執行，以避免計費失控狀況發生。 [函式逾時可設定](functions-host-json.md#functiontimeout)。

如需如何在協調器函式中實作逾時的更深入範例，請參閱[人為互動和逾時 - 電話驗證](durable-functions-phone-verification.md)逐步解說。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何引發和處理外部事件](durable-functions-external-events.md)

