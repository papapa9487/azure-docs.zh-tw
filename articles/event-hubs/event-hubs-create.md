---
title: "建立 Azure 事件中樞 | Microsoft Docs"
description: "使用 Azure 入口網站來建立「Azure 事件中樞」命名空間和事件中樞"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: sethm
ms.openlocfilehash: 816bf1426704d3391550e80c0700f1b011683a94
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>使用 Azure 入口網站來建立事件中樞命名空間和事件中樞

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
1. 登入 [Azure 入口網站][Azure portal]，然後按一下畫面左上方的 [新增]。
1. 按一下 [物聯網]，然後按一下 [事件中樞]。
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. 在 [建立命名空間]  刀鋒視窗中，輸入命名空間名稱。 系統會立即檢查此名稱是否可用。
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. 確定命名空間名稱可用之後，請選擇定價層 ([基本] 或 [標準])。 此外，選擇要在其中建立資源的 Azure 訂用帳戶、資源群組和位置。 
1. 按一下 [建立]  來建立命名空間。 您可能必須等候幾分鐘，讓系統完整佈建資源。
2. 在入口網站的命名空間清單中，按一下新建立的命名空間。
2. 按一下 [共用存取原則]，然後按一下 [RootManageSharedAccessKey]。
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. 按一下複製按鈕，將 **RootManageSharedAccessKey** 連接字串複製到剪貼簿。 將這個連接字串儲存在暫存位置，例如 [記事本]，供以日後使用。
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>建立事件中心

1. 在事件中樞命名空間清單中，按一下新建立的命名空間。      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. 在命名空間刀鋒視窗中，按一下 [事件中樞] 。
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. 在刀鋒視窗頂端，按一下 [新增事件中樞] 。
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. 輸入您的事件中樞名稱，然後按一下 [建立]。
   
    ![](./media/event-hubs-create/create-event-hub5.png)

現已建立事件中樞，您已具有傳送及接收事件所需的連接字串。

## <a name="next-steps"></a>後續步驟
若要深入了解事件中樞，請造訪下列連結：

* [事件中樞概觀](event-hubs-what-is-event-hubs.md)
* [事件中樞 API 概觀](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/