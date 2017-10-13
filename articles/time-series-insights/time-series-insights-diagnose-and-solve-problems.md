---
title: "診斷並解決問題 | Microsoft Docs"
description: "本教學課程說明如何在 Time Series Insights 環境中診斷並解決問題"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.openlocfilehash: 4e10a009eb67706d927ece5692134d802094cdf9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>在 Time Series Insights 環境中診斷與解決問題

## <a name="i-dont-see-my-data"></a>我看不見我的資料
以下是您無法在 [Azure Time Series Insights 入口網站](https://insights.timeseries.azure.com)環境中看到資料的可能原因。

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>您事件來源的資料不是 JSON 格式
Azure Time Series Insights 現在只支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>當您註冊事件來源時，您未提供具有必要權限的索引鍵
* 針對 IoT 中樞，您必須提供具有「服務連接」權限的索引鍵。

   ![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如上圖所示，iothubowner 與 service 原則都會運作，因為兩者皆有「服務連接」權限。
* 針對事件中樞，您必須提供具有「接聽」權限的索引鍵。

   ![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如上圖所示，read 與 manage 原則皆可運作，因為兩者皆有「接聽」權限。

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>所提供的取用者群組不是 Time Series Insights 專用
對於 IoT 中樞或事件中樞，在註冊期間，我們會要求您指定應用於讀取資料的取用者群組。 不可共用此取用者群組。 如果共用，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。

## <a name="i-see-my-data-but-theres-a-lag"></a>我可以看到我的資料，但有延遲情形
以下是您在 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)環境中看到部分資料的原因。

### <a name="your-environment-is-getting-throttled"></a>您的環境正在進行節流
節流限制會根據環境 SKU 類型和容量來強制執行。 環境中所有的事件來源皆共用此容量。 如果IoT 中樞或事件中樞的事件來源正在推送超過強制限制的資料，您會看到節流和延遲情形。

下圖顯示有 SKU 的 S1 且容量為 3 的 Time Series Insights 環境。 該環境可以每日輸入 3 百萬個事件。

![環境 SKU 目前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

假設此環境正以下圖所示的輸入速率從事件中樞輸入訊息：

![事件中樞的範例輸入率](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如圖所示，每日輸入速率為 ~67,000 則訊息。 此速率可轉譯為大約每分鐘 46 則訊息。 如果每個事件中樞訊息被壓平合併為單一 Time Series Insights 事件，則此環境看不見節流。 如果每個事件中樞訊息被壓平合併為 100 個 Time Series Insights 事件，則每分鐘應內嵌 4,600 個事件。 容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每 3 單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。 因此您會看見延遲情形是因為節流。 

若要概略了解壓平合併邏輯的運作方式，請參閱[支援的 JSON 樣貌](time-series-insights-send-events.md#supported-json-shapes)。

#### <a name="recommended-steps"></a>建議的步驟
若要修正延遲情形，請增加您環境的 SKU 容量。 如需詳細資訊，請參閱[如何調整您的 Time Series Insights 環境規模](time-series-insights-how-to-scale-your-environment.md)。

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>您正在推送歷程記錄資料並造成輸入緩慢
如果您正與現有事件來源連線，則您的 IoT 中樞或事件中樞裡可能已有資料。 因此環境會從事件來源訊息保留期間開始時開始提取資料。 

此行為是預設行為且無法覆寫。 您可以進行節流，並且可能需要一段時間才能趕上正在輸入的歷程記錄資料。

#### <a name="recommended-steps"></a>建議的步驟
若要修正延遲，請遵循下列步驟：
1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量後，輸入程序會開始加快速度來趕上。 您可以從 [Time Series Insights 入口網站](https://insights.timeseries.azure.com)的可用性圖表中看到我們趕上的速度有多快。 增加容量需支付費用。
2. 趕上延遲時間後，即可將 SKU 容量降回到正常的輸入速率。

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>我的事件來源的時間戳記屬性名稱設定沒有作用
請確定名稱和值符合下列規則︰
* 時間戳記屬性名稱有_區分大小寫_。
* 來自事件來源的時間戳記屬性值 (JSON 字串) 格式應是「yyyy-MM-ddTHH:mm:ss.FFFFFFFK」。 字串的範例如 “2008-04-12T12:53Z”。
