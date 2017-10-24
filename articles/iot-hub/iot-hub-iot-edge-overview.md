---
title: "Azure IoT Edge 概觀 | Microsoft Docs"
description: "說明諸如閘道、模組和代理程式等 Azure IoT Edge 中的重要架構概念。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.openlocfilehash: 11f6375c319c8945b0278003ee08215715f7ac42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-edge-architectural-concepts"></a>Azure IoT Edge 架構概念

檢查任何範例程式碼或使用 IoT Edge 建立專屬現場閘道之前，您應該了解可加強 IoT Edge 架構的重要概念。

## <a name="iot-edge-modules"></a>IoT Edge 模組

建立和組合「IoT Edge 模組」，即可使用 Azure IoT Edge 來建置閘道。 模組使用「訊息」  來彼此交換資料。 模組會接收訊息、對其執行某個動作、選擇性地將其轉換為新的訊息，然後發佈它，以供其他模組處理。 某些模組可能只會產生新的訊息，且永遠不會處理內送訊息。 一連串的模組可建立資料處理管線，而每個模組都會執行該管線中某個點的資料轉換。

![使用 Azure IoT Edge 所建置之閘道中的模組鏈結][1]

IoT Edge 包含下列元件：

* 可執行常見閘道函式的預先撰寫模組。
* 開發人員可用來撰寫自訂模組的介面。
* 部署和執行一組模組所需的基礎結構。

SDK 提供一個抽象層，可讓您建置要在各種作業系統和平台上執行的閘道。

![Azure IoT Edge 抽象層][2]

## <a name="messages"></a>訊息

雖然考量到彼此傳遞訊息的模組是概念化閘道運作方式的便利方式，但是它不會精確地反映所發生的事情。 IoT Edge 模組會使用訊息代理程式來互相進行通訊。 模組會將訊息發佈到訊息代理程式 (使用諸如匯流排或發佈/訂閱等傳訊模式)，然後讓訊息代理程式將訊息路由至與它連線的模組。

模組使用 **Broker_Publish** 函式將訊息發佈到訊息代理程式。 訊息代理程式會叫用回呼函式，以將訊息傳遞到模組。 訊息包含一組索引鍵/值屬性以及傳遞為記憶體區塊的內容。

![Azure IoT Edge 中的訊息代理程式角色][3]

## <a name="message-routing-and-filtering"></a>訊息路由和篩選

有兩種方式可將訊息導向正確的 IoT Edge 模組：

* 您可將一組連結傳遞給訊息代理程式，讓訊息代理程式知道每個模組的來源和接收。
* 模組可以篩選訊息的屬性。

模組只應對其為適用對象的訊息採取動作。 連結和訊息篩選可有效地建立訊息管線。

## <a name="next-steps"></a>後續步驟

若要查看這些套用在您可以執行之範例中的概念，請參閱[瀏覽 Azure IoT Edge 架構][lnk-hello-world]。

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
