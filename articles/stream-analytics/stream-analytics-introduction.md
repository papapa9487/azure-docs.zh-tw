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
ms.date: 08/08/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 6bb70ba7588b8c6e9261fca097403e7fccb041eb
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="what-is-stream-analytics"></a>什麼是串流分析？

Azure 串流分析是可完全管理的事件處理引擎，可讓您設定串流資料的即時分析計算。 資料可來自裝置、感應器、網站、社交媒體摘要、應用程式和基礎結構系統等等。 

## <a name="what-can-i-do-with-stream-analytics"></a>如何使用串流分析？

使用串流分析檢查來自裝置或處理程序的大量資料流、從資料流擷取資訊，以及尋找模式、趨勢和關聯性。 根據資料的內容，您可以接著執行應用程式工作。 例如，您可以引發警示、開始進行自動化工作流程、將資訊提供給 Power BI 等報表工具，或是儲存資料，以便之後進行調查。 

範例：

* 由金融服務公司所提供的個人化即時股票交易分析和警示。
* 透過檢查交易資料即時偵測詐騙行動。 
* 資料與身分識別保護服務。
* 分析實體物件 (物聯網或 IoT) 中裝載的感應器和傳動器所產生的資料。
* Web 點選流分析。
* 客戶關係管理 (CRM) 應用程式，例如當客戶體驗的表現在某段時間內變差時發出警示。

## <a name="how-does-stream-analytics-work"></a>串流分析如何運作？

此圖說明串流分析的流程，您將了解如何擷取、分析，並將資料傳送出去進行簡報或執行動作。 

![串流分析流程](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

串流分析會從串流資料的來源開始。 透過 Azure 事件中樞或 IoT 中樞，您可以將資料從裝置擷取至 Azure。 也可以從 Azure Blob 儲存體等資料存放區提取資料。 

若要檢查資料流，您可以建立指定資料來源的串流分析作業。 該作業也會指定「轉換」&mdash;功能如何尋找資料、模式或關聯性。 針對這項工作，串流分析支援類似 SQL 的查詢語言，可讓您篩選、排序、彙總和聯結一段時間內的串流資料。

最後，該作業會指定輸出以傳送轉換的資料。 這可讓您控制要執行什麼工作來回應分析的資訊。 例如，您可以執行以下動作來回應分析：

* 傳送命令以變更裝置的設定。 
* 將資料傳送至由處理程序監視的佇列，處理程序會根據找到的內容採取動作。 
* 將資料傳送至 Power BI 儀表板以產生報表。
* 將資料傳送至儲存體，例如 Data Lake Store、SQL Server 資料庫或 Azure Blob 或表格儲存體等。

執行作業時，您可以監視作業和調整其每秒處理的事件數。 您也可以取得作業的產生診斷記錄，以便進行移難排解。

## <a name="key-capabilities-and-benefits"></a>主要功能和優點

串流分析的設計訴求是方便使用、具靈活性、適用於任何作業大小，以及經濟實惠。

### <a name="connectivity-to-many-inputs-and-outputs"></a>可與許多輸入和輸出連線

串流分析會直接連線到 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)進行串流擷取，並連線到 [Azure Blob 儲存體服務](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts)以取得歷史資料。 如果您從事件中樞取得資料，可以結合串流分析與其他資料來源和處理引擎。

作業輸入也可以包含參考資料 (靜態或變更緩慢的資料)。 您可以將串流資料加入此參考資料，並使用與進行資料庫查詢相同的方式執行查閱作業。

以許多方向路由傳送串流分析作業輸出。 您可以寫入至儲存體，例如 Azure 儲存體 Blob 或表格、Azure SQL DB、Azure Data Lake Store 或 Azure Cosmos DB。 資料可以在這些儲存體中透過 Azure HDInsight 進行批次分析。 您可以將輸出傳送至另一個服務，以便另一個處理程序使用，例如事件中樞、Azure 服務匯流排主題或佇列。 您可以將輸出傳送至 Power BI 以視覺效果呈現。

### <a name="ease-of-use"></a>容易使用

若要定義轉換，可以使用簡單及敘述性的[串流分析查詢語言](https://msdn.microsoft.com/library/azure/dn834998.aspx)，讓您無須程式設計即可建立複雜的分析。 查詢語言會採用串流資料作為輸入。 接著您可以篩選和排序資料、彙總值、執行計算、加入資料 (加入至資料流或參考資料)，並使用地理空間函式。 您可以在入口網站中編輯查詢 (使用 IntelliSense 和語法檢查)，然後使用從即時資料流中擷取的範例資料測試查詢。

### <a name="extensible-query-language"></a>可延伸的查詢語言

您可以透過定義和叫用其他函式來延伸查詢語言的功能。 您可以在 Azure 機器學習服務中定義函式呼叫，以充分運用 Azure 機器學習解決方案。 若要執行作為串流分析查詢一部分的複雜計算，也可以整合 JavaScript 使用者定義函式 (UDFs)。

### <a name="scalability"></a>延展性

串流分析每秒可處理 1 GB 的傳入資料。 與 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)和 [Azure IoT 中樞](https://azure.microsoft.com/services/iot-hub/)整合，可讓作業每秒擷取數百萬來自連線裝置、點擊資料流及記錄檔等項目的事件。 透過事件中樞的分割功能，您可以將計算分割為邏輯步驟，而每個步驟都可以再進一步地分割來增加延展性。

### <a name="low-cost"></a>低成本

作為雲端服務，串流分析已進行最佳化，讓您以低成本執行分析作業。 您可根據串流單位使用量和系統處理的資料量執行隨用隨付。 使用量衍生自所處理的事件量，以及佈建在叢集內，用來處理串流分析工作的計算能力。

### <a name="reliability-quick-recovery-and-repeatability"></a>可靠性、快速修復和可重複性

作為雲端中受管理的服務，串流分析可協助防止資料遺失，並提供業務續航力。 如果發生失敗，該服務會提供內建的修復功能。 此服務具備內部維護狀態的能力，因此會提供可重複的結果，確保能封存事件並在日後重新套用處理，且一律取得相同的結果。 這項功能可讓您及時回頭檢視，並在執行根本原因分析和假設狀況分析等案例時調查計算方式。

## <a name="next-steps"></a>後續步驟

* 先從[嘗試從 IoT 裝置執行匯入和查詢](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md)開始。
* 建置[端對端串流分析解決方案](stream-analytics-real-time-fraud-detection.md)，透過檢查電話中繼資料以找出詐騙電話。
* 了解用於串流分析且類似 SQL 的查詢語言，以及[視窗函式](stream-analytics-window-functions.md)等獨特概念。
* 了解如何[調整串流分析工作](stream-analytics-scale-jobs.md)。 
* 了解如何[整合串流分析和 Azure 機器學習](stream-analytics-machine-learning-integration-tutorial.md)。
* 在 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)中尋找串流分析問題的答案。


