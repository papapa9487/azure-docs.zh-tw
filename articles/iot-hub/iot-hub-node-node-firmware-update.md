---
title: "使用 Azure IoT 中樞進行裝置韌體更新 | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 Node.js 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以觸發韌體更新。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 6879f51527802591d45ee56aa47ab304623a4323
ms.contentlocale: zh-tw
ms.lasthandoff: 09/08/2017

---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>使用裝置管理來起始裝置韌體更新 (節點/節點)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>簡介
在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程會使用相同的 IoT 中樞基礎，並且提供指引和示範如何進行端對端模擬韌體更新。  此模式用於 Intel Edison 裝置範例的韌體更新實作。

本教學課程說明如何：

* 建立 Node.js 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 firmwareUpdate 直接方法。
* 建立會實作 **firmwareUpdate** 直接方法的模擬裝置應用程式。 此方法會起始多階段程序，此程序會等候下載韌映像、下載韌體映像，最後再套用韌體映像。 在更新的每個階段，裝置都會使用回報的屬性來回報進度。

在本教學課程結尾處，您會有兩個 Node.js 主控台應用程式：

**dmpatterns_fwupdate_service.js**：在模擬裝置應用程式中呼叫直接方法、顯示回應，並定期 (每 500 毫秒) 顯示更新的報告屬性。

**dmpatterns_fwupdate_device.js**，這會將您的 IoT 中樞連接至稍早建立的裝置身分識別，接收 firmwareUpdate 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

若要完成此教學課程，您需要下列項目：

* Node.js 4.0.x 版或更新版本， <br/>  [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Node.js。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

請依照[開始使用裝置管理](iot-hub-node-node-device-management-get-started.md)文件，以建立 IoT 中樞及取得 IoT 中樞連接字串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在此節中，您會建立 Node.js 主控台應用程式，此應用程式會在裝置上起始遠端韌體更新。 此應用程式使用直接方法來起始更新，並使用裝置對應項查詢來定期取得作用中韌體更新的狀態。

1. 建立稱為 **triggerfwupdateondevice** 的空資料夾。  在 **triggerfwupdateondevice** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。  接受所有預設值：
   
    ```
    npm init
    ```
2. 在 **triggerfwupdateondevice** 資料夾中，於命令提示字元執行下列命令以安裝 **azure-iothub** 套件：
   
    ```
    npm install azure-iothub --save
    ```
3. 使用文字編輯器，在 **triggerfwupdateondevice** 資料夾中建立 **dmpatterns_getstarted_service.js** 檔案。
4. 在 **dmpatterns_getstarted_service.js** 檔案開頭新增下列 'require' 陳述式：
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 新增下列變數宣告，並取代預留位置值︰
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. 新增下列函式來尋找並顯示 firmwareUpdate 報告屬性的值。
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 新增下列函式來叫用 firmwareUpdate 方法，以重新啟動目標裝置︰
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 最後，新增下列函式至程式碼，以啟動韌體更新順序，並且定期顯示報告屬性︰
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. 儲存並關閉 **dmpatterns_fwupdate_service.js** 檔案。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **manageddevice** 資料夾中，於命令提示字元中執行下列命令以開始接聽重新啟動直接方法。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 在命令提示字元中，於 **triggerfwupdateondevice** 資料夾執行下列命令以觸發裝置對應項的遠端重新啟動，以及查詢裝置對應項來尋找上次重新啟動時間。
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. 您會在主控台中看到直接方法的裝置回應。

## <a name="next-steps"></a>後續步驟
在此教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且使用回報的屬性追蹤韌體更新的進度。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

