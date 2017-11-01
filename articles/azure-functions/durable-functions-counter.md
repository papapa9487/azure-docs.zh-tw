---
title: "Durable Functions 中的具狀態單次個體 - Azure"
description: "了解如何在 Azure Functions 的 Durable Functions 擴充中實作具狀態單次個體。"
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
ms.openlocfilehash: ec7d51d3f30eb3417a48fbf8d31a9b8359e39ab9
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Durable Functions 中的具狀態單次個體 - 計數器範例

具狀態單次個體是長時間執行 (可能是永久) 的協調器函式，可以儲存狀態，並由其他函式叫用和查詢。 具狀態單次個體類似於分散式計算中的[執行者模型](https://en.wikipedia.org/wiki/Actor_model)。

雖然協調器函式不是真正的「執行者」實作，但卻有許多相同的執行階段特性。 例如，具狀態、可靠、單一執行緒、位置透明化，而且可全域定址。 這些特性讓真正執行者實作變得非常有用，而且不需要另外的架構。

本文示範如何執行「計數器」範例。 此範例示範單次物件，此物件支援「遞增」和「遞減」作業，然後據以更新其內部狀態。

## <a name="prerequisites"></a>必要條件

* 請依照[安裝 Durable Functions](durable-functions-install.md) 中的指示來設定範例。
* 本文假設您已經完成 [Hello Sequence](durable-functions-sequence.md) 範例逐步解說。

## <a name="scenario-overview"></a>案例概觀

使用一般無狀態函式很難實作計數器情節。 您面臨的主要挑戰之一就是管理**並行**。 「遞增」和「遞減」之類的作業必須是不可部分完成，否則會發生競爭情形而導致作業彼此覆寫。

使用單一虛擬機器來裝載計數器資料是一種選擇，但成本較高，而且管理**可靠性**頗為困難，因為單一虛擬機器可能會定期重新開機。 或者，您可以使用分散式平台，利用 blob 租用之類的工具來協助管理並行，但這樣會變得相當**複雜**。

Durable Functions 就能輕鬆實作這種情節，因為協調流程執行個體近似於單一虛擬機器，而且協調器函式執行永遠採用單一執行緒。 不僅如此，更是長時間執行、具狀態，而且可以對外部事件做出反應。 下列範例程式碼示範如何以長時間執行的協調器函數來實作這種計數器。

## <a name="the-sample-function"></a>範例函式

本文會逐步解說範例應用程式中的 **E3_Counter**函式。

下列各節說明用於 Visual Studio 開發的程式碼。 Azure 入口網站開發的程式碼很類似。

## <a name="the-counter-orchestration"></a>計數器協調流程

以下是實作協調器函式的程式碼：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

此協調器函式基本上會執行下列動作：

1. 使用 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 接聽名為 *operation* 的外部事件。
2. 根據所要求的作業來遞增或遞減 `counterState` 區域變數。
3. 使用 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) 方法來重新啟動協調器，並將最新的 `counterState` 值設為新的輸入。
4. 永遠執行下去，或直到收到 *end* 訊息為止。

這是「永久性協調流程」的例子 &mdash; 也就是說，可能永遠不會結束。 它會以 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法來回應收到的訊息，任何非協調器函式都可以呼叫此方法。

此協調器函式有一項特質，就是實際上沒有記錄：`ContinueAsNew` 方法會在每處理完一個事件後就重設記錄。 在實作存留期多變的協調器時，這是慣用的方式。 使用 `while` 迴圈可能會造成協調器函式的記錄失控變大，只是無謂地耗用大量記憶體。

> [!NOTE]
> 除了永久性協調流程，`ContinueAsNew` 方法還有其他使用案例。 如需詳細資訊，請參閱[永久性協調流程](durable-functions-eternal-orchestrations.md)。

## <a name="run-the-sample"></a>執行範例

您可以藉由傳送下列 HTTP POST 要求來啟動協調流程。 為了讓 `counterState` 從零開始 (`int` 的預設值)，這個要求中沒有內容。

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

**E3_Counter** 執行個體啟動後，就開始等待使用 `RaiseEventAsync` 或使用 202 回應中所參考的 **sendEventUrl** HTTP POST Webhook 送來的事件。 有效的 `eventName` 值包括 *incr*、*decr* 和 *end*。

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

您可以在 Azure Functions 入口網站中查看函式記錄，以就能看到 "incr" 作業的結果。

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

同樣地，如果您檢查協調器狀態，您會看到 `input` 欄位已設定為更新的值 (1)。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

您可以繼續將新的作業傳送至這個執行個體，並觀察到其狀態會隨之更新。 如果想要終止執行個體，您可以傳送 *end* 作業。

> [!WARNING]
> 在編寫本文時，已知在處理訊息 (例如外部事件或終止要求) 時呼叫 `ContinueAsNew` 會發生競爭情形。 如需這些競爭情形的最新資訊，請參閱此 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/67)。

## <a name="next-steps"></a>後續步驟

此範例已示範如何處理[外部事件](durable-functions-external-events.md)並在[具狀態 Singleton](durable-functions-singletons.md) 中實作[永久性協調流程](durable-functions-eternal-orchestrations.md)。 下一個範例會說明如何使用外部事件和[長期計時器](durable-functions-timers.md)來處理人為互動。

> [!div class="nextstepaction"]
> [執行人為互動範例](durable-functions-phone-verification.md)
