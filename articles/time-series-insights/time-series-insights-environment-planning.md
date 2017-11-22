---
title: "規劃調整您的 Azure 時間序列深入解析環境規模 | Microsoft Docs"
description: "本文說明在規劃 Azure 時間序列深入解析環境 (包括儲存容量、資料保留、輸入容量和監視) 時，如何遵循最佳做法。"
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>規劃 Azure 時間序列深入解析環境

本文以預期的輸入速率和資料保留需求作為基礎，說明如何規劃 Azure 時間序列深入解析環境。

## <a name="best-practices"></a>最佳作法

若要開始使用時間序列深入解析，您最好知道以分鐘數計您需要推送多少資料，以及需要儲存資料多久。  

如需這兩個時間序列深入解析 SKU 的容量和保留之詳細資訊，請參閱[時間序列深入解析定價](https://azure.microsoft.com/pricing/details/time-series-insights/)。

請考慮下列的屬性，以最佳方式規劃能長期獲致成功的環境： 
- 儲存體容量
- 資料保留期間
- 輸入容量 

## <a name="understand-storage-capacity"></a>了解儲存體容量
依預設，時間序列深入解析會保留資料 以您已佈建的儲存體數量 (單位乘以每個單位的存放裝置數量) 和輸入。

## <a name="understand-data-retention"></a>了解資料保留
您可以設定時間序列深入解析環境的**資料保留時間**設定，從而啟用最多 400 天的保留期。  時間序列深入解析有兩個模式，其中一個進行最佳化以確保您的環境具有最新的資料 (依預設)，另一個最佳化以確保符合保留期限制，其中如果叫用環境的整體儲存體容量，輸入就會暫停。  您可以調整保留期，並在 Azure 入口網站中環境組態頁面的兩個模式之間切換。

您可以在時間序列深入解析環境中設定最大值為 400 天的資料保留期。

## <a name="configure-data-retention"></a>設定資料保留期

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取您的時間序列深入解析環境。

2. 在 [時間序列深入解析環境] **頁面**的 [設定]標題下，選取 [設定]。 

3. 在 [資料保留時間 (天數)] 方塊中，輸入介於 1 到 400 的值。

   ![設定保留期](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>了解輸入容量

在規劃時所需著重的另一個區域是輸入容量，也就是每分鐘配置的衍生。 

就節流的觀點而言，會將封包大小為 32 KB 的輸入資料封包視為 32 個事件，每個大小為 1 KB。 允許的事件大小上限為 32 KB；大於 32 KB 的資料封包會加以截斷。

下表摘要說明每個 SKU 的輸入容量：

|SKU  |每個單位每個月的事件計數  |每個單位每個月的事件大小  |每個單位每分鐘的事件計數  | 每個單位每分鐘的大小   |
|---------|---------|---------|---------|---------|
|S1     |   3,000 萬     |  30 GB     |  700    |  700 KB   |
|S2     |   3 億    |   300 GB   | 7,000   | 7,000 KB  |

您可以在單一環境中將 S1 或 S2 SKU 的容量增加至 10 個單位。 您無法從 S1 環境移轉至 S2，或從 S2 環境移轉至 S1。 

針對輸入容量，您應該先以每月為基礎判斷所需要的總輸入。 接下來，判斷您每分鐘的需求，因為這是節流和延遲扮演的角色。

如果您資料輸入的高峰持續時間低於 24 小時，時間序列深入解析可能會以上列速率 2 倍的輸入速率來「趕上」。 

例如，如果您有單一 S1 SKU 和以每分鐘 700 個事件速率的輸入資料，且高峰以 1400 個或更少事件的速率低於 1 小時，對您的環境就沒有明顯的延遲。 不過，如果一小時以上超過每分鐘 1400 個事件，您可能會遇到視覺化的資料發生延遲，而且可供您在環境中查詢。 

您可能不會事先知道需要推送多少資料。 在此情況下，您可以在 Azure 入口網站中找到 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)和 [Azure 事件中樞](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/)的資料遙測。 此遙測可協助您判斷如何佈建環境。 在 Azure 入口網站中使用個別事件來源的 [計量] 頁面以檢視其遙測。 如果您了解事件來源的計量，就可以更有效地規劃並佈建時間序列深入解析環境。

## <a name="calculate-ingress-requirements"></a>計算輸入需求

- 確認您的輸入容量高於您每分鐘平均速率，且您的環境夠大，可在 1 小時內處理您預期的輸入，相當於您容量的 2 倍。

- 如果發生持續超過 1 小時的輸入高峰，請使用高峰率作為平均值，並使用處理高峰率的容量來佈建環境。
 
## <a name="mitigate-throttling-and-latency"></a>減少節流和延遲

如需關於如何避免節流和延遲的資訊，請參閱[減少節流和延遲](time-series-insights-environment-mitigate-latency.md)。 

## <a name="next-steps"></a>後續步驟
- [如何新增事件中樞的事件來源](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [如何新增 IoT 中樞的事件來源](time-series-insights-how-to-add-an-event-source-iothub.md)
