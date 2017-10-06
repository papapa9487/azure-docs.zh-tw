---
title: "Azure 轉送連接埠設定 | Microsoft Docs"
description: "Azure 轉送連接埠值的相關詳細資料。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5906495c565dad583e74a43b2e5eed57e0c68df1
ms.contentlocale: zh-tw
ms.lasthandoff: 07/06/2017

---

# <a name="azure-relay-port-settings"></a>Azure 轉送連接埠設定

下表說明 Azure 轉送的連接埠值所需的設定。

## <a name="hybrid-connections"></a>混合式連線
混合式連線使用 WebSockets 做為基礎傳輸機制，僅使用 **HTTPS**。 

## <a name="wcf-relays"></a>WCF 轉送
  
|繫結|傳輸安全性|連接埠|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (用戶端)|是|HTTPS| 
| |" |否|HTTP|  
|[BasicHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (服務)|無論是|9351/HTTP|  
|[NetEventRelayBinding 類別](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (用戶端)|是|9351/HTTPS|  
||" |否|9350/HTTP|  
|[NetEventRelayBinding 類別](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (服務)|無論是|9351/HTTP|  
|[NetTcpRelayBinding 類別](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (用戶端/服務)|無論是|5671/9352/HTTP (如果使用混合式則為 9352/9353)|  
|[NetOnewayRelayBinding 類別](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (用戶端)|是|9351/HTTPS|  
||" |否|9350/HTTP|  
|[NetOnewayRelayBinding 類別](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (服務)|無論是|9351/HTTP|  
|[WebHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (用戶端)|是|HTTPS|  
||" |否|HTTP|  
|[WebHttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (服務)|無論是|9351/HTTP|  
|[WS2007HttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (用戶端)|是|HTTPS|  
||" |否|HTTP|  
|[WS2007HttpRelayBinding 類別](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (服務)|無論是|9351/HTTP|

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 轉送，請造訪下列連結：
* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [轉送常見問題集](relay-faq.md)
