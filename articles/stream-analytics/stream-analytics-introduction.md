---
title: "串流分析簡介 | Microsoft Docs"
description: "了解串流分析，這是可協助您即時分析物聯網 (IoT) 資料流的受管理服務。"
keywords: "分析服務, 受管理服務, 串流處理, 串流分析, 什麼是串流分析"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: samacha
ms.openlocfilehash: 889aaa697a04a81075b85c834d534bfeb4faf79b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="what-is-stream-analytics"></a>什麼是串流分析？

Azure 串流分析是受管理的事件處理引擎，可讓您設定串流資料的即時分析計算。 資料可來自裝置、感應器、網站、社交媒體摘要、應用程式和基礎結構系統等等。 

使用串流分析檢查來自裝置或處理程序的大量資料流、從資料流擷取資訊，以及識別模式、趨勢和關聯性。 使用這些模式來觸發其他程序或動作 (例如警示)、自動化工作流程、將資訊提供給報告工具，或是儲存資訊以便之後進行調查。 

部分範例如下：

* 股票交易分析和警示。
* 詐騙偵測、資料，以及識別保護。 
* 內嵌感應器和傳動器分析。
* Web 點選流分析。

## <a name="how-does-stream-analytics-work"></a>串流分析如何運作？

此圖說明串流分析的流程，您將了解如何擷取、分析，並將資料傳送出去進行簡報或執行動作。 

![串流分析流程](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

串流分析會從串流資料的來源開始。 透過 Azure 事件中樞或 IoT 中樞，您可以將資料從裝置擷取至 Azure。 也可以從 Azure Blob 儲存體等資料存放區提取資料。 

若要檢查資料流，您可以建立可指定資料來源的串流分析作業。 此作業也會指定「轉換」功能；如何尋找資料、模式或關聯性。 針對這項工作，串流分析支援類似 SQL 的查詢語言，以便篩選、排序、彙總和聯結一段時間內的串流資料。

最後，該作業會指定該轉換資料的輸出。 您可控制要執行什麼工作來回應分析的資訊。 例如，您可以執行以下動作來回應分析：

* 傳送命令以變更裝置設定。 
* 將資料傳送到受監視的佇列，以便根據調查結果進行進一步動作。 
* 將資料傳送至 Power BI 儀表板。
* 將資料傳送至儲存體，例如 Data Lake Store、Azure SQL Database 或 Azure Blob 儲存體。

您可以調整執行作業時，每秒處理的事件數目。 您也可以產生診斷記錄，以便進行移難排解。

## <a name="key-capabilities-and-benefits"></a>主要功能和優點

串流分析的設計訴求是方便使用、具靈活性，以及適用於任何作業大小。

### <a name="connect-inputs-and-outputs"></a>連線輸入和輸出

串流分析會直接連線到 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)進行串流擷取，並連線到 [Azure Blob 儲存體服務](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts)以取得歷史資料。 利用事件中樞的資料，結合串流分析與其他資料來源和處理引擎。 作業輸入也可以包含參考資料 (靜態或變更緩慢的資料)。 您可以將串流資料加入此參考資料，並使用與進行資料庫查詢相同的方式執行查閱作業。

以許多方向路由傳送串流分析作業輸出。 寫入至儲存體，例如 Azure Blob、Azure SQL Database、Azure Data Lake Store 或 Azure Cosmos DB。 從該處，您可以透過 Azure HDInsight 執行批次分析。 或者將輸出傳送至另一個服務，以便另一個程序 (例如事件中樞、Azure 服務匯流排、佇列) 使用，或傳送至 Power BI 進行視覺化。

### <a name="simple-to-use"></a>用法簡單

若要定義轉換，可以使用簡單及敘述性的[串流分析查詢語言](https://msdn.microsoft.com/library/azure/dn834998.aspx)，讓您無須程式設計即可建立複雜的分析。 查詢語言會採用串流資料作為輸入。 接著您可以篩選和排序資料、彙總值、執行計算、加入資料 (加入至資料流或參考資料)，並使用地理空間函式。 您可以在入口網站中編輯查詢 (使用 IntelliSense 和語法檢查)，然後使用從即時資料流中擷取的範例資料測試查詢。

### <a name="extensible-query-language"></a>可延伸的查詢語言

您可以透過定義和叫用其他函式來延伸查詢語言的功能。 您可以在 Azure 機器學習服務中定義函式呼叫，以充分運用 Azure 機器學習解決方案。 若要執行作為串流分析查詢一部分的複雜計算，也可以整合 JavaScript 使用者定義函式 (UDFs)。

### <a name="scalable"></a>可調整

串流分析每秒可處理 1 GB 的傳入資料。 與 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)整合，可讓作業每秒擷取數百萬來自連線裝置、點擊資料流及記錄檔等項目的事件。 透過事件中樞的分割功能，您可以將計算分割為邏輯步驟，而每個步驟都可以再進一步地分割來增加延展性。

### <a name="low-cost"></a>低成本

作為雲端服務，串流分析已進行成本最佳化。 您可根據串流單位使用量和資料量執行付款。 使用量衍生自所處理的事件量，以及佈建在作業叢集內的計算能力。

### <a name="reliable"></a>可靠

作為受管理的服務，串流分析可協助防止資料遺失，並提供業務續航力。 如果發生失敗，該服務會提供內建的修復功能。 此服務具備內部維護狀態的能力，因此會提供可重複的結果，確保能封存事件並在日後重新套用處理，且一律取得相同的結果。 這項功能可讓您及時回頭檢視，並在執行根本原因分析和假設狀況分析等案例時調查計算方式。

## <a name="next-steps"></a>後續步驟

* 先從[嘗試從 IoT 裝置執行匯入和查詢](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md)開始。
* 建置[端對端串流分析解決方案](stream-analytics-real-time-fraud-detection.md)，透過檢查電話中繼資料以找出詐騙電話。
* 在 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)中尋找串流分析問題的答案。

