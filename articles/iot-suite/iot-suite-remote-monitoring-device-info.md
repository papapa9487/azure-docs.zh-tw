---
title: "遠端監視方案中的裝置資訊中繼資料 | Microsoft Docs"
description: "說明 Azure IoT 預先設定解決方案遠端監視及其架構。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1b334769-103b-4eb0-a293-184f3d1ba9a3
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: f8fd452806a0a0b98cf8e434c9bd55700083a6c5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>遠端監視預先設定方案中的裝置資訊中繼資料

Azure IoT 套件遠端監視預先設定方案會示範用於管理裝置中繼資料的方法。 本文將概述此方案為了讓您了解而採用的方法︰

* 方案會儲存哪些裝置中繼資料。
* 方案如何管理裝置中繼資料。

## <a name="context"></a>Context

遠端監視預先設定方案會使用 [Azure IoT 中樞][lnk-iot-hub]，讓您的裝置將資料傳送至雲端。 此解決方案會在三個不同位置儲存裝置的相關資訊︰

| 位置 | 儲存的資訊 | 實作 |
| -------- | ------------------ | -------------- |
| 身分識別登錄 | 裝置識別碼、驗證金鑰和啟用狀態 | 內建於 IoT 中樞 |
| 裝置對應項 | 中繼資料：報告屬性、所需屬性、標記 | 內建於 IoT 中樞 |
| Cosmos DB | 命令和方法歷程記錄 | 針對解決方案自訂 |

IoT 中樞包含可管理 IoT 中樞存取權的[裝置身分識別登錄][lnk-identity-registry]，並使用[裝置對應項][lnk-device-twin]來管理裝置中繼資料。 還有遠端監視解決方案專用*裝置登錄*可儲存命令和方法歷程記錄。 遠端監視解決方案會使用 [Cosmos DB][lnk-docdb] 資料庫來實作命令和方法歷程記錄的自訂存放區。

> [!NOTE]
> 預先設定的遠端監視解決方案會讓裝置身分識別登錄與 Cosmos DB 資料庫中的資訊保持同步。 兩者都使用相同的裝置識別碼來唯一識別連線到您的 IoT 中樞的每個裝置。

## <a name="device-metadata"></a>裝置中繼資料

IoT 中樞會針對連線至遠端監視解決方案的每個模擬與實體裝置，維護[裝置對應項][lnk-device-twin]。 此解決方案會使用裝置對應項來管理與裝置相關聯的中繼資料。 裝置對應項是 IoT 中樞所維護的 JSON 文件，而解決方案會使用 IoT 中樞 API 來與裝置對應項互動。

裝置對應項可儲存三種中繼資料︰

- 「報告屬性」是由裝置傳送至 IoT 中樞。 在遠端監視解決方案中，模擬裝置會在啟動時傳送報告屬性，以回應**變更裝置狀態**命令和方法。 您可以在解決方案入口網站的 [裝置清單] 和 [裝置詳細資料] 中檢視報告屬性。 報告屬性是唯讀的。
- 「所需屬性」是由裝置從 IoT 中樞擷取。 在裝置上進行任何必要的組態變更是裝置的責任。 以報告屬性形式向中樞回報變更也是裝置的責任。 您可以透過解決方案入口網站設定所需屬性值。
- *標記*只存在於裝置對應項中，永遠不會與裝置同步處理。 您可以在解決方案入口網站中設定標記值，並且在篩選裝置清單時加以使用。 解決方案也會使用標記來識別在解決方案入口網站中要針對裝置顯示的圖示。

模擬裝置的範例報告屬性包括製造商、型號、緯度和經度。 模擬裝置也會以報告屬性形式傳回支援的方法清單。

> [!NOTE]
> 模擬裝置程式碼只會使用 **Desired.Config.TemperatureMeanValue** 和 **Desired.Config.TelemetryInterval** 所需屬性來更新送回 IoT 中樞的報告屬性。 所有其他所需屬性變更要求都會被忽略。

儲存在裝置登錄 Cosmos DB 資料庫中的裝置資訊中繼資料 JSON 文件具有下列結構︰

```json
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

> [!NOTE]
> 裝置訊息也可包含中繼資料，以描述裝置傳送給 IoT 中樞的遙測。 遠端監視方案會使用此遙測中繼資料，來自訂儀表板顯示[動態遙測][lnk-dynamic-telemetry]的方式。

## <a name="lifecycle"></a>生命週期

當您第一次在解決方案入口網站中建立裝置時，解決方案會在 Cosmos DB 資料庫中建立一個項目，以儲存命令和方法歷程記錄。 此時，方案也會在裝置身分識別登錄中為裝置建立一個項目，以產生裝置用來向「IoT 中樞」進行驗證的金鑰。 它也會建立裝置對應項。

當裝置第一次連線至解決方案時，它會傳送報告屬性和裝置資訊訊息。 報告屬性值會自動儲存在裝置對應項中。 報告屬性包括裝置製造商、型號、序號及支援的方法清單。 裝置資訊訊息包含裝置支援的命令清單，其中包括任何命令參數的相關資訊。 當解決方案收到此訊息時，它會更新 Cosmos DB 資料庫中的裝置資訊。

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>在方案入口網站中檢視和編輯裝置資訊

解決方案入口網站中的裝置清單預設會將下列裝置屬性顯示為資料行︰[狀態]、[裝置識別碼]、[製造商]、[型號編號]、[序號]、[韌體]、[平台]、[處理器] 和 [已安裝的 RAM]。 您可以按一下 [資料行編輯器] 來自訂資料行。 裝置屬性 [緯度] 和 [經度] 會支配儀表板上「Bing 地圖」中的位置。

![裝置清單中的資料行編輯器][img-device-list]

在解決方案入口網站的 [裝置詳細資料] 窗格中，您可以編輯所需屬性和標記 (報告屬性是唯讀)。

![裝置詳細資料窗格][img-device-edit]

您可以使用方案入口網站，從您的方案中移除裝置。 當您移除裝置時，解決方案會從身分識別登錄中移除此裝置項目，然後刪除裝置對應項。 解決方案也會從 Cosmos DB 資料庫中移除裝置相關資訊。 您必須先停用裝置，才可以將它移除。

![移除裝置][img-device-remove]

## <a name="device-information-message-processing"></a>裝置資訊訊息處理

裝置所傳送的裝置資訊訊息與遙測訊息不同。 裝置資訊訊息包含裝置可以回應的命令以及任何命令歷程記錄。 IoT 中樞本身不知道裝置資訊訊息中內含的中繼資料，它會以處理任何裝置對雲端訊息的相同方式處理訊息。 在遠端監視方案中，[Azure 串流分析][lnk-stream-analytics] (ASA) 作業會從「IoT 中樞」讀取訊息。 **DeviceInfo** 串流分析作業會篩選包含 **"ObjectType":"DeviceInfo"** 的訊息，並將這些訊息轉送至在 Web 作業中執行的 **EventProcessorHost** 主機執行個體。 **EventProcessorHost** 執行個體中的邏輯會使用裝置識別碼，來尋找特定裝置的 Cosmos DB 記錄並更新該記錄。

> [!NOTE]
> 裝置資訊訊息是標準的裝置對雲端訊息。 方案會使用 ASA 查詢，以區分裝置資訊訊息與遙測訊息。

## <a name="next-steps"></a>後續步驟

現在您已完成了解如何自訂預先設定的解決方案，您可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [預先設定的預防性維護解決方案概觀][lnk-predictive-overview]
* [IoT 套件的常見問題集][lnk-faq]
* [從頭建立 IoT 安全性][lnk-security-groundup]

<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
