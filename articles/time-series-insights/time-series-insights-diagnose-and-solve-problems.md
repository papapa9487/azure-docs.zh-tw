---
title: "在 Azure 時間序列深入解析中診斷與解決問題 | Microsoft Docs"
description: "本文說明如何在 Azure 時間序列深入解析環境中診斷、疑難排解與解決可能會遇到的常見問題。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 757d37183ad334aca462af59bad261cfa686299e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>在 Time Series Insights 環境中診斷與解決問題

## <a name="problem-1-no-data-is-shown"></a>問題 1：未顯示資料
您無法在 [Azure 時間序列深入解析總管](https://insights.timeseries.azure.com) 中看到資料有幾個常見原因：

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>可能原因 A：事件來源資料格式不是 JSON
Azure 時間序列深入解析現在只支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>可能原因 B：事件來源索引鍵遺漏必要的權限
* 針對 IoT 中樞，您必須提供具有「服務連線」權限的金鑰。

   ![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   如上圖所示，iothubowner 與 service 原則都會運作，因為兩者皆有「服務連接」權限。
   
* 針對事件中樞，您必須提供具有「接聽」權限的索引鍵。

   ![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   如上圖所示，read 與 manage 原則皆可運作，因為兩者皆有「接聽」權限。

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>可能原因 C：提供的取用者群組並非時間序列深入解析專用
在註冊 IoT 中樞或事件中樞期間，您要指定應用於讀取資料的取用者群組。 **不可**共用此取用者群組。 如果共用取用者群組，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>問題 2：顯示部分資料，但遺漏部分資料
當您可以看到部分資料，但是資料延遲時，有幾個要考慮的可能性：

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>可能原因 A：您的環境正在進行節流
節流限制會根據環境 SKU 類型和容量來強制執行。 環境中所有的事件來源皆共用此容量。 如果IoT 中樞或事件中樞的事件來源正在推送超過強制限制的資料，您就會看到節流和延遲情形。

下圖顯示有 SKU 的 S1 且容量為 3 的 Time Series Insights 環境。 該環境可以每日輸入 3 百萬個事件。

![環境 SKU 目前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

例如，假設此環境正在從事件中樞擷取訊息。 觀察下圖所示的輸入率：

![事件中樞的範例輸入率](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

如圖所示，每日輸入速率為 ~67,000 則訊息。 此速率可轉譯為大約每分鐘 46 則訊息。 如果每個事件中樞訊息被壓平合併為單一 Time Series Insights 事件，則此環境看不見節流。 如果每個事件中樞訊息被壓平合併為 100 個 Time Series Insights 事件，則每分鐘應內嵌 4,600 個事件。 容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每 3 單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。 因此您會看見延遲情形是因為節流。 

若要概略了解壓平合併邏輯的運作方式，請參閱[支援的 JSON 樣貌](time-series-insights-send-events.md#supported-json-shapes)。

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>針對過度節流的建議解決步驟
若要修正延遲情形，請增加您環境的 SKU 容量。 如需詳細資訊，請參閱[如何調整您的 Time Series Insights 環境規模](time-series-insights-how-to-scale-your-environment.md)。

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>可能原因 B：歷程記錄資料的初始擷取造成輸入緩慢
如果您正與現有事件來源連線，則您的 IoT 中樞或事件中樞裡可能已有資料。 環境會從事件來源訊息保留期間開始時開始提取資料。

此行為是預設行為且無法覆寫。 您可以進行節流，並且可能需要一段時間才能趕上正在輸入的歷程記錄資料。

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>大型初始擷取的建議解決步驟
若要修正延遲，請遵循下列步驟：
1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量後，輸入程序會開始加快速度來趕上。 您可以從[時間序列深入解析總管](https://insights.timeseries.azure.com)的可用性圖表中看到我們趕上的速度有多快。 增加容量需支付費用。
2. 趕上延遲時間後，即可將 SKU 容量降回到正常的輸入速率。

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題 3：我的事件來源的時間戳記屬性名稱設定沒有作用
請確定名稱和值符合下列規則︰
* 時間戳記屬性名稱有_區分大小寫_。
* 來自事件來源的時間戳記屬性值 (JSON 字串) 格式應是「yyyy-MM-ddTHH:mm:ss.FFFFFFFK」。 字串的範例如 “2008-04-12T12:53Z”。

## <a name="next-steps"></a>後續步驟
- 如需其他協助，請在 [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) 上啟動交談。 
- 您也可以使用 [Azure 支援](https://azure.microsoft.com/support/options/)以取得協助的支援選項。
