---
title: "Azure 事件中樞是什麼及為何使用它 | Microsoft Docs"
description: "Azure 事件中樞概觀和簡介 - 從網站、應用程式和裝置內嵌雲端級別的遙測"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm; babanisa
ms.openlocfilehash: 236d49b7d8104b410e2a4c768820c3c9ebcdce96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-event-hubs"></a>何謂事件中樞？

Azure 事件中樞是可高度調整的資料串流平台，以及每秒能夠接收和處理數百萬個事件的事件內嵌服務。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 藉由提供大規模的低延遲[發佈訂閱功能](/biztalk/core/publish-and-subscribe-architecture)，事件中樞能成為引進巨量資料的途徑。

## <a name="why-use-event-hubs"></a>為何使用事件中樞？

事件中樞的事件和遙測處理功能特別適用於︰

* 應用程式檢測
* 使用者經驗或工作流程處理
* 物聯網 (IoT) 案例

例如，事件中樞能追蹤行動應用程式中的行為、接收 Web 伺服陣列的流量資訊、擷取遊戲機遊戲中的遊戲內部事件，或是從產業用機器、連接的車輛或其他裝置收集遙測。

## <a name="azure-event-hubs-overview"></a>Azure 事件中樞概觀

事件中樞在解決方案架構中經常扮演事件管線的「大門」角色 (通常稱為「事件擷取器」)。 事件擷取器是介於事件發佈者和事件取用者之間的元件或服務，它能將事件串流的生產與這些事件的取用彼此脫鉤。 下圖說明此架構︰

![事件中樞](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

事件中樞提供訊息串流處理能力，但特性迴異於傳統企業傳訊。 事件中樞功能是以高輸送量和事件處理案例為重點。 因此，事件中樞不同於 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)傳訊，並不會實作一些可用於[服務匯流排傳訊](/azure/service-bus-messaging/)實體 (例如主題) 的功能。

## <a name="event-hubs-features"></a>事件中樞功能

事件中樞包含下列重要元素：

- [**事件產生者/發行者**](event-hubs-features.md#event-publishers)：任何將資料傳送至事件中樞的實體就稱為「事件發佈者」。 透過 AMQP 1.0 或 HTTPS 發佈事件。
- [**擷取**](event-hubs-features.md#capture)：可讓您擷取事件中樞串流資料並將它儲存在 Azure Blob 儲存體帳戶中。
- [**資料分割**](event-hubs-features.md#partitions)︰可讓每個取用者只讀取事件資料流的特定子集或資料分割。
- [**SAS 權杖**](event-hubs-features.md#sas-tokens)︰識別及驗證事件發行者。
- [**事件取用者**](event-hubs-features.md#event-consumers)：任何從事件中樞讀取事件資料的實體。 事件取用者會透過 AMQP 1.0 連線。 
- [**取用者群組**](event-hubs-features.md#consumer-groups)︰為每個多重取用應用程式提供不同的事件資料流檢視，讓者取用者能夠獨立執行動作。
- [**輸送量單位**](event-hubs-features.md#capacity)：預先購買的容量單位。 每個磁碟分割有 1 個輸送量單位的規模上限。

如需這些和其他事件中樞功能的技術詳細資訊，請參閱[事件中樞功能概觀](event-hubs-features.md)。 

## <a name="next-steps"></a>後續步驟

如需詳細的事件中樞價格資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

如需事件中樞的詳細資訊，請造訪下列連結：

* 開始使用[事件中樞教學課程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [使用事件中樞的範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

