---
title: "Azure 服務匯流排進階和標準傳訊定價層概觀 | Microsoft Docs"
description: "服務匯流排進階和標準傳訊層級"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: sethm
ms.openlocfilehash: 613bb074063e436cdbd54fe5aee9c49109a2d8f2
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服務匯流排進階和標準傳訊層級

服務匯流排傳訊 (包含佇列和主題等實體) 結合雲端級別的企業傳訊功能與豐富的發佈-訂閱語意。 服務匯流排傳訊作為許多縝密雲端解決方案的通訊骨幹。

服務匯流排傳訊的「進階」層級可滿足一般客戶對於關鍵任務應用程式的規模、效能和可用性要求。 雖然功能組幾乎完全相同，但這兩層級的服務匯流排傳訊設計用來服務不同的使用案例。

下表強調大致的一些差異。

| 高級 | 標準 |
| --- | --- |
| 高輸送量 |變動輸送量 |
| 可預測的效能 |變動延遲 |
| 固定價格 |隨用隨付變動價格 |
| 相應增加和相應減少工作負載的能力 |N/A |
| 訊息大小上限為 1 MB |訊息大小上限為 256 KB |

**服務匯流排進階傳訊**提供 CPU 和記憶體層級的資源隔離，讓每個客戶工作負載能隔離執行。 此資源容器稱為「傳訊單位」 。 每個進階命名空間都會被配置至少一個傳訊單位。 您可以為每個服務匯流排進階命名空間購買 1、2 或 4 個傳訊單位。 單一工作負載或實體可以跨越多個傳訊單位，而傳訊單位數目可以隨意變更，雖然計費是依 24 小時或每日費率收費。 結果是您的服務匯流排方案的效能可預測並可重複。

此效能不僅更可預測並可取得，而且還更快速。 服務匯流排進階傳訊是以 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)中引進的儲存引擎為建置基礎。 使用進階訊息，尖峰效能的速度比標準層級的速度快很多。

## <a name="premium-messaging-technical-differences"></a>進階傳訊技術差異

下列各節討論進階與標準傳訊層之間的一些差異。

### <a name="partitioned-queues-and-topics"></a>分割的佇列和主題

進階傳訊支援分割的佇列和主題；事實上這些實體一律會進行分割 (且無法停用)。 不過，進階分割的佇列和主題在服務匯流排傳訊的標準層中的運作方式不同。 進階傳訊不會使用 SQL 做為資料存放區，而且不可能再有與共用平台相關聯的資源競爭。 因此，資料分割不一定能夠改善效能。 此外，資料分割計數已從標準傳訊中的 16 個資料分割變更為進階傳訊中的 2 個資料分割。 擁有 2 個資料分割可確保可用性，而且是比較適合進階執行階段環境的數字。 

透過進階傳訊，當您使用 [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes) 指定實體的大小時，會將 2 個分割的大小平均分割，這不同於[標準分割的實體](service-bus-partitioning.md#standard)，其中總大小是 16 乘以指定的大小。 

如需分割的詳細資訊，請參閱[分割的佇列和主題](service-bus-partitioning.md)。

### <a name="express-entities"></a>快速實體

因為進階傳訊是在完全隔離的執行階段環境中執行，所以在進階命名空間中並不支援快速實體。 如需快速功能的詳細資訊，請參閱 [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性。

如果您的程式碼是在標準傳訊下執行，而您想要將它移植到高階層，請確定 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) 屬性設為 **false** (預設值)。

## <a name="get-started-with-premium-messaging"></a>開始使用進階傳訊

開始使用進階訊息非常簡單，其程序類似於標準傳訊。 首先在 [Azure 入口網站](https://portal.azure.com)中[建立命名空間](service-bus-create-namespace-portal.md)。 務必選取 [選擇您的定價層] 之下的 [進階]。

![create-premium-namespace][create-premium-namespace]

您也可以[使用 Azure Resource Manager 範本建立進階命名空間](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/)。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排訊息，請參閱下列主題。

* [Azure 服務匯流排進階傳訊簡介 (部落格文章)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服務匯流排進階傳訊簡介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
