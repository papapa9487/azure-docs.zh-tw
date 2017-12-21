---
title: "在 .NET 中開始使用 Azure 轉送混合式連接 | Microsoft Docs"
description: "為 Azure 轉送混合式連線撰寫 C# 主控台應用程式。"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 647f0705500c708680f000654b343ef6937d60f1
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>開始使用轉送混合式連線
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

本教學課程提供 [Azure 轉送混合式連線](relay-what-is-it.md#hybrid-connections)的指示，並示範如何使用 .NET 來建立用戶端應用程式，以將訊息傳送至對應的接聽端應用程式。 

## <a name="what-will-be-accomplished"></a>將會完成的工作
因為混合式連線需要用戶端和伺服器元件，所以本教學課程會建立兩個主控台應用程式。 步驟如下：

1. 使用 Azure 入口網站建立轉送命名空間。
2. 使用 Azure 入口網站，在該命名空間中建立混合式連線。
3. 撰寫伺服器 (接聽端) 主控台應用程式來接收訊息。
4. 撰寫用戶端 (傳送端) 主控台應用程式來傳送訊息。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

1. [Visual Studio 2015 或更新版本](http://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2017。
2. Azure 訂用帳戶。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 入口網站建立命名空間
如果您已經建立轉送命名空間，請跳至[使用 Azure 入口網站建立混合式連線](#2-create-a-hybrid-connection-using-the-azure-portal)一節。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.使用 Azure 入口網站建立混合式連線
如果您已經建立混合式連線，請跳至[建立伺服器應用程式](#3-create-a-server-application-listener)一節。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.建立伺服器應用程式 (接聽程式)
為了接聽並接收來自轉送的訊息，我們會使用 Visual Studio 撰寫 C# 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.建立用戶端應用程式 (傳送者)
為了將訊息傳送到轉送，我們會使用 Visual Studio 撰寫 C# 主控台應用程式。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.執行應用程式
1. 執行伺服器應用程式。
2. 執行用戶端應用程式並輸入一些文字。
3. 確定伺服器應用程式主控台有將用戶端應用程式中所輸入的文字輸出。

![執行應用程式](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

恭喜您，您已建立端對端混合式連線應用程式。

## <a name="next-steps"></a>後續步驟：
* [轉送常見問題集](relay-faq.md)
* [建立命名空間](relay-create-namespace-portal.md)
* [開始使用 Node](relay-hybrid-connections-node-get-started.md)

