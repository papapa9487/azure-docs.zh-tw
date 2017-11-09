---
title: "使用動態遙測 | Microsoft Docs"
description: "遵循本教學課程以了解如何使用動態遙測搭配遠 Azure IoT 套件端監視預先設定解決方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>搭配使用動態遙測與遠端監視預先設定解決方案

動態遙測可讓您將傳送至遠端監視預先設定解決方案的任何遙測視覺化。 使用預先設定解決方案部署的模擬裝置會傳送溫度與濕度遙測，您可以在儀表板上將這些遙測視覺化。 如果您自訂現有的模擬裝置、建立新的模擬裝置，或將實體裝置連線到預先設定解決方案，您可以傳送其他的遙測值，例如外部溫度、RPM 或風速。 然後，您可以在儀表板上將此額外遙測視覺化。

本教學課程使用簡單的 Node.js 模擬裝置，您可以輕鬆地修改它來實驗動態遙測。

若要完成本教學課程，您需要：

* 有效的 Azure 訂用帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。
* [Node.js][lnk-node] 0.12.x 版或更新版本。

您可以在任何作業系統上完成本教學課程，例如 Windows 或 Linux，只要您可以在其中安裝 Node.js。

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>新增遙測類型

下一個步驟是將 Node.js 模擬裝置產生的遙測以新的一組值取代：

1. 停止 Node.js 模擬裝置，方法是在命令提示字元或殼層中輸入 **Ctrl+C** 。
2. 在 remote_monitoring.js 檔案中，您可以查看現有溫度、濕度和外部溫度遙測的基底資料值。 加入 **rpm** 的基底資料值，如下所示：

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js 模擬裝置會使用 remote_monitoring.js 檔案中的 **generateRandomIncrement** 函式，對基底資料值加上隨機增量。 在現有的隨機設定後方加上一行程式碼，將 **rpm** 的值隨機化，如下所示：

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. 將新的 rpm 值加入到裝置送給 IoT 中樞的 JSON 承載：

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. 使用下列命令，執行 Node.js 模擬裝置：

    `node remote_monitoring.js`

6. 觀察儀表板圖表上所顯示的新 RPM 遙測類型：

![新增 RPM 到儀表板][image3]

> [!NOTE]
> 您可能需要在儀表板中的 [裝置] 頁面停用 Node.js 裝置，然後再啟用以立即查看變更。

## <a name="customize-the-dashboard-display"></a>自訂儀表板顯示

**Device-Info** 訊息可能包含裝置可傳送給 IoT 中樞的遙測相關中繼資料。 此中繼資料可指定裝置傳送的遙測類型。 修改 remote_monitoring.js 檔案中的 **deviceMetaData** 值，以在 **Commands** 定義後方包含 **Telemetry** 定義。 以下程式碼片段會顯示 **Commands** 定義 (請務必在 **Commands** 定義後方新增 `,`)：

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> 遠端監視解決方案會使用區分大小寫的配對，來比對中繼資料定義與遙測串流中的資料。


以上方程式碼片段所示方式加入 **Telemetry** 定義，並不會變更儀表板的行為。 不過，中繼資料也可以包含 **DisplayName** 屬性來自訂儀表板中的顯示。 更新 **Telemetry** 中繼資料定義，如下列程式碼片段所示：

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

下列螢幕擷取畫面說明這項變更如何修改儀表板上的圖表圖例：

![自訂圖表圖例][image4]

> [!NOTE]
> 您可能需要在儀表板中的 [裝置] 頁面停用 Node.js 裝置，然後再啟用以立即查看變更。

## <a name="filter-the-telemetry-types"></a>篩選遙測類型

根據預設，儀表板上的圖表會顯示遙測串流中的每個資料數列。 您可以使用 **Device-Info** 中繼資料，來隱藏圖表上特定遙測類型的顯示。 

若要讓圖表只顯示溫度和濕度遙測，請從 **Device-Info** **Telemetry** 中繼資料省略 **ExternalTemperature**，如下所示：

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature (戶外溫度)** 不會再顯示在圖表上：

![篩選儀表板上的遙測][image5]

這項變更只會影響圖表顯示。 **ExternalTemperature** 資料值仍會儲存，並可供任何後端處理使用。

> [!NOTE]
> 您可能需要在儀表板中的 [裝置] 頁面停用 Node.js 裝置，然後再啟用以立即查看變更。

## <a name="handle-errors"></a>處理錯誤

針對要在圖表上顯示的資料串流，其在 **Device-Info** 中繼資料中的 **Type** 必須符合遙測值的資料類型。 例如，如果中繼資料指定濕度資料的 **Type** 必須為 **int**，而在遙測串流中找到 **double**，則濕度遙測將不會顯示在圖表上。 不過，**濕度**的值仍會儲存，並可供任何後端處理使用。

## <a name="next-steps"></a>後續步驟

既然您已了解如何使用動態的遙測，您可以進一步了解預先設定的解決方案如何使用裝置資訊︰[遠端監視預先設定解決方案中的裝置資訊中繼資料][lnk-devinfo]。

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
