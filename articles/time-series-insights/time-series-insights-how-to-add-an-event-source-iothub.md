---
title: "如何將 IoT 中樞的事件來源新增至 Azure Time Series Insights 環境 | Microsoft Docs"
description: "本教學課程將說明如何將與 IoT 中樞連線的事件來源新增至 Time Series Insights 環境"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>如何新增 IoT 中樞的事件來源

本教學課程將說明如何使用 Azure 入口網站，將從 IoT 中樞讀取的事件來源新增至 Time Series Insights 環境。

## <a name="prerequisites"></a>必要條件

您已建立 IoT 中樞，並正在對其寫入事件。 如需有關 IoT 中樞的詳細資訊，請參閱 <https://azure.microsoft.com/services/iot-hub/>

> [取用者群組] 每個 Time Series Insights 事件必須有自身專屬的取用者群組，且不可與任何其他取用者共用。 如果多個讀取器從同一個取用者群組取用事件，則所有讀取器都可能會看到錯誤。 如需詳細資料，請參閱 [IoT 中樞開發人員指南](../iot-hub/iot-hub-devguide.md)。

## <a name="choose-an-import-option"></a>選擇匯入選項

事件來源的設定可以手動輸入，或您可以從可用的 IoT 中樞選取 IoT 中樞。
在 [匯入選項] 選取器中，選擇以下其中一個選項：

* 手動提供 IoT 中樞設定
* 從可用的訂用帳戶使用 IoT 中樞

### <a name="select-an-available-iot-hub"></a>選取可用的 IoT 中樞

下表將說明 [新增事件來源] 索引標籤中的每個選項和選項描述，以便您選取可用的 IoT 中樞作為事件來源：

| 屬性名稱 | 說明 |
| --- | --- |
| 事件來源名稱 | 事件來源的名稱。 此名稱在 Time Series Insights 中必須是唯一的。
| 來源 | 選擇 [IoT 中樞] 以建立 IoT 中樞事件來源。
| 訂用帳戶識別碼 | 選取建立此 IoT 中樞的訂用帳戶。
| IoT 中樞名稱 | 選取 IoT 中樞的名稱。
| IoT 中樞原則名稱 | 選取 [IoT 中樞設定] 索引標籤下的共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**服務連線**權限。
| IoT 中樞取用者群組 | 從 IoT 中樞讀取資料的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。

### <a name="provide-iot-hub-settings-manually"></a>手動提供 IoT 中樞設定

下表將說明 [新增事件來源] 索引標籤中的每個屬性和屬性描述，以便您手動輸入設定：

| 屬性名稱 | 說明 |
| --- | --- |
| 事件來源名稱 | 事件來源的名稱。 此名稱在 Time Series Insights 中必須是唯一的。
| 來源 | 選擇 [IoT 中樞] 以建立 IoT 中樞事件來源。
| 訂用帳戶識別碼 | 建立此 IoT 中樞的訂用帳戶。
| 資源群組 | 建立此 IoT 中樞的訂用帳戶。
| IoT 中樞名稱 | IoT 中樞的名稱。 當您建立 IoT 中樞時，您也會指定特定名稱
| IoT 中樞原則名稱 | 共用存取原則，可在 [IoT 中樞設定] 索引標籤下加以建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**服務連線**權限。
| IoT 中樞原則金鑰 | 用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 在此輸入主要金鑰或次要金鑰。
| IoT 中樞取用者群組 | 從 IoT 中樞讀取資料的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。

## <a name="next-steps"></a>後續步驟

1. 將資料存取原則新增至您的環境[定義資料存取原則](time-series-insights-data-access.md)
1. 在[入口網站](https://insights.timeseries.azure.com)中存取您的環境