---
title: "什麼是 Azure 轉送和為什麼使用的概觀 | Microsoft Docs"
description: "Azure 轉送的概觀"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 77ee85db0bcc701514a1a98da9405a79d658d49d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="what-is-azure-relay"></a>什麼是 Azure 轉送？

Azure 轉送服務可執行混合式應用程式，方法是讓您以安全的方式向公用雲端公開位於企業網路內部的服務，而無需開啟防火牆連線或要求對企業網路基礎結構的進行侵入式變更。 轉送支援各種不同的傳輸通訊協定和 Web 服務標準。

轉送服務支援傳統的單向、要求/回應以及對等式流量。 它也支援網際網路範圍內的事件散發，以啟用發佈/訂閱案例和雙向通訊端通訊來提高點對點效率。 

在轉送的資料傳輸模式中，內部部署服務會透過輸出連接埠連接到轉送服務，並且針對繫結至特定聚合位址的通訊建立雙向通訊端。 用戶端接著可將流量傳送至以會合位址為目標的轉送服務，藉此與內部部署服務通訊。 轉送服務接著會透過每個用戶端專用的雙向通訊端，將資料「轉送」至內部部署服務。 用戶端不需要直接連線到內部部署服務，不需要知道服務所在的位置，而且內部部署服務不需要在防火牆上開啟任何輸入連接埠。

轉送所提供的重要功能元素是利用類似 TCP 的節流、端點探索、連線狀態和重疊端點安全性，進行跨越網路界限且未經緩衝處理的雙向通訊。 轉送功能與網路層級整合技術 (例如 VPN) 不同，轉送的範圍可以限定於單一電腦上的單一應用程式端點，而 VPN 技術則比較屬於侵入式，因為其依賴於改變網路環境。

Azure 轉送有兩項功能︰

1. [混合式連線](#hybrid-connections) - 使用開放式標準 Web 通訊端來啟用多平台案例。
2. [WCF 轉送](#wcf-relays) - 使用 Windows Communication Foundation (WCF) 來啟用遠端程序呼叫。 WCF 轉送是舊版的轉送服務，許多客戶已將該服務用於其 WCF 程式設計模型。

混合式連線和 WCF 轉送都能夠對存在於企業網路內的資產進行安全的連線。 視您的特定需求使用其中一項功能，詳述於下表︰

|  | WCF 轉送 | 混合式連線 |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **標準型開放式通訊協定** | |x |
| **多個 RPC 程式設計模型** | |x |

## <a name="hybrid-connections"></a>混合式連線

[Azure 轉送混合式連線](relay-hybrid-connections-protocol.md)功能是現有轉送功能的安全、開放式通訊協定演化，可以在任何平台上以任何具有基本 WebSocket 功能的語言實作，而基本 WebSocket 功能會明確地將 WebSocket API 納入一般網頁瀏覽器中。 混合式連線是以 HTTP 和 Websocket 為基礎。

### <a name="service-history"></a>服務歷程記錄

「混合式連線」會取代先前的功能，該功能同樣名為「BizTalk 服務」功能且建置於 Azure 服務匯流排 WCF 轉送。 新的混合式連線功能可補充現有的 WCF 轉送功能，而這兩個服務功能會並存於 Azure 轉送服務中。 它們共用通用的閘道，但有不同的實作方式。

## <a name="wcf-relays"></a>WCF 轉送

WCF 轉送適用於完整的 .NET Framework (NETFX) 和 WCF。 您在內部部署服務與使用一組 WCF「轉送」繫結的轉送服務之間起始連線。 在幕後，轉送繫結會對應至新的傳輸繫結元素，其設計來建立與雲端中服務匯流排整合的 WCF 通道元件。

## <a name="architecture-processing-of-incoming-relay-requests"></a>架構：處理內送轉送要求
當用戶端傳送要求至 [Azure 轉送](/azure/service-bus-relay/)服務時，Azure Load Balancer 會將其路由到任何一個閘道器節點。 如果要求為接聽要求，閘道器節點會建立新的轉送。 如果要求為特定轉送的連線要求，閘道器節點會轉送連線要求給擁有轉送的閘道器節點。 擁有轉送的閘道器節點會傳送會合要求到接聽用戶端，要求接聽程式建立接收連線要求的閘道器節點的暫時通道。

建立轉送連線時，用戶端可以透過用來會合的閘道節點交換訊息。

![處理內送 WCF 轉送要求](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>後續步驟

* [轉送常見問題集](relay-faq.md)
* [建立命名空間](relay-create-namespace-portal.md)
* [開始使用 .NET](relay-hybrid-connections-dotnet-get-started.md)
* [開始使用 Node](relay-hybrid-connections-node-get-started.md)

