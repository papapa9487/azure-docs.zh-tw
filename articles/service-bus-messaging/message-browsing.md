---
title: "Azure 服務匯流排訊息瀏覽 | Microsoft Docs"
description: "瀏覽及預覽服務匯流排訊息"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: b0bc1ef7570ccac07975e2560a1d0501d3cde2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="message-browsing"></a>訊息瀏覽

訊息瀏覽 (「預覽」) 可讓用戶端列舉佇列或訂用帳戶中的所有訊息，通常是針對診斷及偵錯目的。

預覽作業會傳回佇列或訂用帳戶訊息記錄中的所有訊息，不僅僅只是可使用 *Receive()* 或 *OnMessage()* 迴圈立即取得的訊息。 每則訊息的 *State* 屬性能告訴您訊息是作用中 (可接收)、延遲 (請參閱「延遲」[TBD link]) 或已排程 (請參閱「排定的訊息」[TBD link])。

非同步的「記憶體回收」執行會清除已使用和已到期的訊息，但不一定會在訊息到期的當下執行。因此預覽確實會傳回已到期，而且即將在下次針對佇列或訂用帳戶叫用接收作業時移除或失效的訊息。

當您嘗試從佇列復原延遲的訊息時，請務必記住這點。 經過 [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) 時刻的訊息不再能以其他任何方式正常擷取，即便是預覽傳回時也不例外。 傳回這些訊息需要經過深思熟慮，因為預覽是反映記錄目前狀態的診斷工具。

預覽也能傳回先前遭到鎖定，但其他接收者正在處理的訊息，不過這個功能尚未完成。 然而，由於預覽會傳回中斷連線的快照集，訊息的鎖定狀態無法從預覽中的訊息觀察得知，因此當應用程式嘗試讀取它們時，[LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) 和 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 屬性會擲回 [InvalidOperationException](/dotnet/api/system.invalidoperationexception)。

## <a name="peek-apis"></a>預覽 API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 和 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 方法存在於所有 .NET 和 Java 用戶端程式庫及所有接收者物件上：**MessageReceiver**、**MessageSession**、**QueueClient** 及 **SubscriptionClient**。 預覽適用於所有佇列、訂用帳戶與各自的無效信件佇列。

當重複叫用時，Peek 方法會列舉出佇列或訂用帳戶記錄中的所有訊息，並依照序號順序從最小序號排列到最大序號。 這是訊息加入佇列時的順序，不是最終的擷取順序。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) 能擷取多個訊息並當作列舉傳回。 如果沒有訊息可用，列舉物件會是空的 (而不是 Null)。

您也可以利用起始 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) 植入方法的多載，然後再呼叫無參數方法多載來進一步列舉。 **PeekBatch** 的功能相同，不過它會一次擷取一組訊息。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)