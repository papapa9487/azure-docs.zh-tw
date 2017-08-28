---
title: "將 IoT 中樞訊息儲存至 Azure 資料儲存體 | Microsoft Docs"
description: "使用 Azure 函式應用程式將 IoT 中樞訊息儲存到 Azure 表格儲存體。 IoT 中樞訊息包含從 IoT 裝置傳送的資訊，例如感應器資料。"
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
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 06503f9564e00ef62587d02f2da4778974e246c5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>將包含感應器資料的 IoT 中樞訊息儲存到 Azure 表格儲存體

![端對端圖表](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

您會了解如何建立 Azure 儲存體帳戶和 Azure 函式應用程式，以將 IoT 中樞訊息儲存在 Azure 表格儲存體。

## <a name="what-you-do"></a>您要做什麼

- 建立 Azure 儲存體帳戶。
- 準備 IoT 中樞連線以讀取訊息。
- 建立並部署 Azure 函式應用程式。

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

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>準備 IoT 中樞連線以讀取訊息

IoT 中樞會公開內建的事件中樞相容端點，以便讓應用程式能夠讀取 IoT 中樞訊息。 同時，應用程式會使用取用者群組從 IoT 中樞讀取資料。 在建立 Azure 函式應用程式以從 IoT 中樞讀取資料之前，您必須執行下列作業︰

- 取得 IoT 中樞端點的連接字串。
- 為 IoT 中樞建立取用者群組。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>取得 IoT 中樞端點的連接字串

1. 開啟 IoT 中樞。

2. 在 [IoT 中樞] 窗格上，按一下 [傳訊] 底下的 [端點]。

3. 在右窗格中，按一下 [內建端點] 底下的 [事件]。

4. 記下 [屬性] 窗格的下列值︰
   - 事件中樞相容端點
   - 事件中樞相容名稱

   ![在 Azure 入口網站中取得 IoT 中樞端點的連接字串](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. 在 [IoT 中樞] 窗格上，按一下 [設定] 底下的 [共用存取原則]。

6. 按一下 [iothubowner]。

7. 請記下**主索引鍵**值。

8. 建立 IoT 中樞端點的連接字串，如下所示︰

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > 以您之前記下的值取代 `<Event Hub-compatible endpoint>` 和 `<Primary key>`。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>為 IoT 中樞建立取用者群組

1. 開啟 IoT 中樞。

2. 在 [IoT 中樞] 窗格上，按一下 [傳訊] 底下的 [端點]。

3. 在右窗格中，按一下 [內建端點] 底下的 [事件]。

4. 在 [屬性] 窗格中，於 [取用者群組] 底下輸入名稱，並記下該名稱。

5. 按一下 [儲存] 。

## <a name="create-and-deploy-an-azure-function-app"></a>建立並部署 Azure 函式應用程式

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] > [計算] > [函數應用程式] > [建立]。

2. 輸入函式應用程式的必要資訊。

   ![在 Azure 入口網站中建立函式應用程式](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **應用程式名稱**︰函式應用程式的名稱。 此名稱必須是全域唯一的。

   * **資源群組**︰使用 IoT 中樞所用的相同資源群組。

   * **儲存體帳戶**︰您建立的儲存體帳戶。

   * **釘選至儀表板**︰核取此選項可讓您從儀表板輕鬆地存取函式應用程式。

3. 按一下 [建立] 。

4. 建立函式應用程式之後，請開啟它。

5. 在函式應用程式中，執行下列動作建立新的函式：

   a. 按一下 [新函式]。

   b.這是另一個 C# 主控台應用程式。 選取 [JavaScript] 作為 [語言]，選取 [資料處理] 作為 [案例]。

   c. 按一下 [建立此函式]，然後按一下 [新增函式]。

   d. 選取 [JavaScript] 作為 [語言]，選取 [資料處理] 作為 [情節]。

   e. 按一下 **EventHubTrigger-JavaScript** 範本。

   f. 輸入範本的必要資訊。

      * **替您的函式命名**：函式的名稱。

      * **事件中樞名稱**：您之前記下的事件中樞相容名稱。

      * **事件中樞連線**：按一下 [新增] 以新增您所建立之 IoT 中樞端點的連接字串。

   g. 按一下 [建立] 。

6. 執行下列作業以設定函式的輸出：

   a. 按一下 [整合] > [新增輸出] > [Azure 資料表儲存體] > [選取]。

      ![在 Azure 入口網站中將表格儲存體新增至函式應用程式](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b.這是另一個 C# 主控台應用程式。 輸入必要資訊。

      * **資料表參數名稱**︰使用 `outputTable`，它會用於 Azure 函式的程式碼中。
      
      * **資料表名稱**：使用 `deviceData`。

      * **儲存體帳戶連線**︰按一下 [新增]，然後選取或輸入儲存體帳戶。 如果未顯示儲存體帳戶，請參閱[儲存體帳戶的需求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)。
      
   c. 按一下 [儲存] 。

7. 在 [觸發程序] 底下，按一下 [Azure 事件中樞 (eventHubMessages)]。

8. 在 [事件中樞取用者群組] 底下，輸入您所建立之取用者群組的名稱，然後按一下 [儲存]。

9. 按一下您在左側建立的函式，然後按一下右側的 [檢視檔案]。

10. 以下列內容取代 `index.js` 中的程式碼：

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

11. 按一下 [儲存] 。

您現在建立了函式應用程式。 它會將 IoT 中樞收到的訊息儲存在您的表格儲存體中。

> [!NOTE]
> 您可以使用 [執行] 按鈕來測試函式應用程式。 當您按一下 [執行]，系統就會將測試訊息傳送到 IoT 中樞。 訊息送達時應該會觸發函式應用程式啟動，然後將訊息儲存至您的表格儲存體。 [記錄] 窗格會記錄處理程序的詳細資料。

## <a name="verify-your-message-in-your-table-storage"></a>確認訊息位於資料表儲存體中

1. 在裝置上執行應用程式範例以將訊息傳送至 IoT 中樞。

2. [下載並安裝 Azure 儲存體總管](http://storageexplorer.com/)。

3. 開啟儲存體總管，按一下 [新增 Azure 帳戶] > [登入]，然後登入您的 Azure 帳戶。

4. 按一下您的 Azure 訂用帳戶 > [儲存體帳戶] > 您的儲存體帳戶 > [資料表] > [deviceData]。

   您應該會看到從裝置傳送到 IoT 中樞的訊息記錄在 `deviceData` 資料表中。

## <a name="next-steps"></a>後續步驟

您已成功建立 Azure 儲存體帳戶和 Azure 函式應用程式，以將您的 IoT 中樞收到的訊息儲存在表格儲存體中。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

