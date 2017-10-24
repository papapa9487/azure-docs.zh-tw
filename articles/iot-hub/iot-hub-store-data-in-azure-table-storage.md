---
title: "將 IoT 中樞訊息儲存至 Azure 資料儲存體 | Microsoft Docs"
description: "使用 IoT 中樞訊息路由將 IoT 中樞訊息儲存至 Azure Blob 儲存體。 IoT 中樞訊息包含從 IoT 裝置傳送的資訊，例如感應器資料。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 資料儲存體, iot 感應器資料存儲存體"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: aa33800de82b27d4819fe0eade127c2a40e3a493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>將包含感應器資料的 IoT 中樞訊息儲存至 Azure Blob 儲存體

![端對端圖表](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

您會了解如何建立 Azure 儲存體帳戶和 Azure 函式應用程式，以將 IoT 中樞訊息儲存在 Azure 表格儲存體。

## <a name="what-you-do"></a>您要做什麼

- 建立 Azure 儲存體帳戶。
- 準備 IoT 中樞以將訊息路由傳送至儲存體。

## <a name="what-you-need"></a>您需要什麼

- [設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)以涵蓋下列需求︰
  - 作用中的 Azure 訂用帳戶
  - 您訂用帳戶下的 IoT 中樞 
  - 將訊息傳送到 IoT 中樞的執行中應用程式

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] > [儲存體] > [儲存體帳戶] > [建立]。

2. 輸入儲存體帳戶的必要資訊︰

   ![在 Azure 入口網站中建立儲存體帳戶](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **名稱**︰儲存體帳戶的名稱。 此名稱必須是全域唯一的。

   * **資源群組**︰使用 IoT 中樞所用的相同資源群組。

   * **釘選到儀表板**：選取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。

3. 按一下 [建立] 。

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>準備 IoT 中樞以將訊息路由傳送至儲存體

「IoT 中樞」原生支援將訊息路由傳送至 Azure 儲存體作為 Blob。

### <a name="add-storage-as-a-custom-endpoint"></a>新增儲存體作為自訂端點

瀏覽至您在 Azure 入口網站中的 IoT 中樞。 按一下 [端點] > [新增]。 為端點命名，然後選取 [Azure 儲存體容器] 作為端點類型。 使用選擇器來選取您在上一節中建立的儲存體帳戶。 建立一個儲存體容器並選取它，然後按一下 [確定]。

  ![在「IoT 中樞」內建立一個自訂端點](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>新增路由以將資料路由傳送到儲存體

按一下 [路由] > [新增]，然後輸入路由的名稱。 選取 [裝置訊息] 作為資料來源，然後選取您剛才建立的儲存體端點作為路由中的端點。 輸入 `true` 作為查詢字串，然後按一下 [儲存]。

  ![在「IoT 中樞」內建立一個路由](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>新增最忙碌路徑遙測路由 (選擇性)

根據預設，「IoT 中樞」會將所有不符合任何其他路由的訊息遞送至內建的端點。 由於所有遙測訊息現在都符合會將訊息路由傳送至儲存體的規則，因此您必須新增另一個路由，以將訊息寫入至內建的端點。 將訊息路由傳送至多個端點並不額外收費。

> [!NOTE]
> 如果您不對遙測訊息進行額外的處理，則可以略過此步驟。

從 [路由] 窗格按一下 [新增]，然後輸入路由的名稱。 選取 [裝置訊息] 作為資料來源和 [事件] 作為端點。 輸入 `true` 作為查詢字串，然後按一下 [儲存]。

  ![在「IoT 中樞」內建立一個最忙碌路徑路由](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>確認儲存體容器中的訊息

1. 在裝置上執行應用程式範例以將訊息傳送至 IoT 中樞。

2. [下載並安裝 Azure 儲存體總管](http://storageexplorer.com/)。

3. 開啟儲存體總管，按一下 [新增 Azure 帳戶] > [登入]，然後登入您的 Azure 帳戶。

4. 按一下您的 Azure 訂用帳戶 > [儲存體帳戶] > 您的儲存體帳戶 > [Blob 容器] > 您的容器。

   您應該會看到從裝置傳送到 IoT 中樞的訊息記錄在 Blob 容器中。

## <a name="next-steps"></a>後續步驟

您已成功建立 Azure 儲存體帳戶，並已將訊息從「IoT 中樞」路由傳送至該儲存體帳戶中的 Blob 容器。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
