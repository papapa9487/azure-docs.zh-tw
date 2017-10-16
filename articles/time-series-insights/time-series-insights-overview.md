---
title: "Azure Time Series Insights 概觀 | Microsoft Docs"
description: "簡介 Azure Time Series Insights，這項新服務可用於時間序列資料分析和 IoT 解決方案"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>什麼是 Azure Time Series Insights

Azure Time Series Insights 是受管理的雲端服務，其具有儲存、分析和視覺效果元件，可讓您輕鬆地同時擷取、儲存、探索和分析數十億個事件。 Time Series Insights 可讓您以全域方式檢視資料，這可協助您探索隱藏的趨勢和異常狀況，並近乎即時地執行根本原因分析，進而快速驗證 IoT 解決方案並避免代價高昂的裝置停機。 Time Series Insights 會擷取事件代理人 (例如，IoT 中樞或事件中樞) 的時間序列資料、為資料編製索引，並根據可設定的保留原則來淘汰資料。 使用者可透過直覺式 UX 或 REST 查詢 API 來取用資料。

![Time Series Insight 概觀](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>主要案例

* 在短短幾分鐘內監視和驗證 IoT 解決方案。
* 大規模視覺化和分析 IoT 資料。
* 加速進行根本原因分析和異常偵測。
* 建立含有多個裝置、工廠和資料的全域檢視。

## <a name="capabilities-and-benefits"></a>功能和優點

* **容易上手**：Azure Time Series Insights 不必事先資料準備且速度無比快速。 在短短幾分鐘內連線至 Azure IoT 中樞或事件中樞內的數十億個事件。 連線後即可在幾秒內與感應器資料互動並加以視覺化，進而快速驗證您的 IoT 解決方案。 Time Series Insights 很容易使用；您不必撰寫任何程式碼就能與資料互動。  您不需要學習新的語言，因為 Time Series Insights 會為進階使用者提供細微且可輸入任意文字的查詢介面，並提供點選探索功能。

* **近乎即時的深入解析**：Time Series Insights 每天可以擷取數十億個感應器事件，其延遲時間只有一分鐘，因此您可以快速地回應變更。 Time Series Insights 協助您深入地了解感應器資料，它會協助您快速找出趨勢和異常狀況，進行複雜的根本原因分析，並避免耗費成本的停機時間。 藉由將即時和歷史的資料相互關聯，Time Series Insights 協助您了解其資料中隱藏的趨勢。

* **建置自訂解決方案**：將 Azure Time Series Insights 資料內嵌至現有應用程式，或使用 Time Series Insights REST API 建立新的自訂解決方案。 建立和共用個人化檢視，以便其他人瀏覽您所做的探索。

* **延展性**︰Time Series Insights 是為了大規模支援 IoT 而設計。 在預覽版中，它每天可以輸入 1 百萬到 1 億個事件，預設的保留時間長度是 31 天。 您可以用近乎即時的方式，來為即時資料流以及大量的歷史資料呈現視覺效果並對其進行分析。 往後，我們也會增加輸入和保留速率，以因應不斷發展的企業規模。

## <a name="time-series-insights-glossary"></a>Time Series Insights 詞彙

* **環境**︰環境是具有輸入和儲存容量的 Azure 資源。  客戶需透過 Azure 入口網站來佈建環境和其所需容量。
* **事件來源**：事件來源衍生自事件代理人，例如 Azure 事件中樞。  Time Series Insights 會直接連線到事件來源，並內嵌資料流，而不需撰寫任何程式碼。 目前，Time Series Insights 支援 Azure 事件中樞與 Azure IoT 中樞。
* **參考資料**︰Time Series Insights 可讓使用者聯結時間序列資料與參考資料。  參考資料可以包含關於裝置的中繼資料，也可以包含其他較不常變更的靜態資料。 Time Series Insights 可聯結參考資料與資料流，讓使用者以近乎即時的方式，來呈現此資料的視覺效果並對其進行分析。
