---
title: "Azure 服務匯流排的訊息處理架構 | Microsoft Docs"
description: "描述 Azure 服務匯流排的訊息處理架構。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: b810618b485b631e1d72b24c2a9587017d635cc4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---
# <a name="service-bus-architecture"></a>服務匯流排架構
本文章描述 Azure 服務匯流排的訊息處理架構。

## <a name="service-bus-scale-units"></a>服務匯流排縮放單位
服務匯流排依 *縮放單位*組織。 縮放單位是部署單位，並包含執行服務所需的所有元件。 每個區域都會部署一或多個服務匯流排縮放單位。

服務匯流排命名空間會對應到一個縮放單位。 縮放單位會處理所有類型的服務匯流排實體 (佇列、主題、訂用帳戶)。 服務匯流排縮放單位是由下列元件所組成：

* **一組閘道器節點。** 閘道節點會驗證傳入要求。 每個閘道器節點都有一個公用 IP 位址。
* **一組訊息代理程式節點。** 訊息代理程式節點會處理關於訊息實體的要求。
* **一個閘道器存放區。** 閘道器存放區會保存此縮放單位中定義的每個實體的資料。 閘道器存放區是在 SQL Azure 資料庫上實作。
* **多個訊息存放區。** 訊息存放區會保存此縮放單位中定義的所有佇列、主題和訂用帳戶的訊息。 它也包含所有訂用帳戶資料。 除非啟用了[分割傳訊實體](service-bus-partitioning.md)，佇列或主題才會對應至一個傳訊存放區。 訂用帳戶是儲存在與父項主題相同的訊息存放區。 除了服務匯流排[進階傳訊](service-bus-premium-messaging.md)以外，訊息存放區會在 SQL Azure 資料庫之上實作。

## <a name="containers"></a>容器
每個訊息實體都會指派給特定的容器。 容器是一種邏輯建構，僅使用一個訊息存放區來儲存此容器所有相關資料。 每個容器都會被指派至訊息代理程式節點。 通常，容器較訊息代理程式節點多。 因此，每個訊息代理程式節點會載入多個容器。 訊息代理程式節點的容器分配會經過組織，使得平均地載入所有的訊息代理程式節點。 如果載入模式變更 (例如其中一個容器變得十分忙碌)，或訊息代理程式節點會變成暫時無法使用，容器會在訊息代理程式節點間重新分配。

## <a name="processing-of-incoming-messaging-requests"></a>處理內送訊息要求
當用戶端傳送要求至服務匯流排時，Azure 負載平衡器會將其路由到任何一個閘道器節點。 閘道器節點會授權要求。 如果要求考量訊息實體 (佇列、主題、訂用帳戶)，則閘道器節點會在閘道器存放區中在尋找實體，並判斷實體位在哪個訊息存放區。 它接著會查詢哪些訊息代理程式節點目前正在服務此容器，並將要求傳送至該訊息代理程式節點。 訊息代理程式節點會處理要求並更新容器存放區中的實體狀態。 訊息代理程式節點接著會傳送回應給閘道器節點，閘道器節點會傳送適當的回應給發出原始要求的用戶端。

![處理內送訊息要求](./media/service-bus-architecture/ic690644.png)

## <a name="next-steps"></a>後續步驟
既然您已閱讀服務匯流排架構的概觀，請參閱下列連結取得詳細資訊：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [使用服務匯流排佇列的佇列訊息解決方案](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



