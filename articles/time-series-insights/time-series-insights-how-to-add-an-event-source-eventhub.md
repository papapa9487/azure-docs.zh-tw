---
title: "如何將事件中樞的事件來源新增至 Azure Time Series Insights 環境 | Microsoft Docs"
description: "本教學課程將說明如何將與事件中樞連線的事件來源新增至 Time Series Insights 環境"
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
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="how-to-add-an-event-hub-event-source"></a>如何新增事件中樞的事件來源

本教學課程將說明如何使用 Azure 入口網站，將從事件中樞讀取的事件來源新增至 Time Series Insights 環境。

## <a name="prerequisites"></a>必要條件

您已建立事件中樞，並正在對其寫入事件。 如需有關事件中樞的詳細資訊，請參閱 <https://azure.microsoft.com/services/event-hubs/>

> [取用者群組] 每個 Time Series Insights 事件必須有自身專屬的取用者群組，且不可與任何其他取用者共用。 如果多個讀取器從同一個取用者群組取用事件，則所有讀取器都可能會看到錯誤。 請注意，每一個事件中樞另外還有 20 個用戶群組的限制。 如需詳細資訊，請參閱 [事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

## <a name="choose-an-import-option"></a>選擇匯入選項

事件來源的設定可以手動輸入，或您可以從可用的事件中樞選取事件中樞。
在 [匯入選項] 選取器中，選擇以下其中一個選項：

* 手動提供事件中樞設定
* 從可用的訂用帳戶使用事件中樞

### <a name="select-an-available-event-hub"></a>選取可用的事件中樞

下表將說明 [新增事件來源] 索引標籤中的每個選項和選項描述，以便您選取可用的事件中樞作為事件來源：

| 屬性名稱 | 說明 |
| --- | --- |
| 事件來源名稱 | 事件來源的名稱。 此名稱在 Time Series Insights 中必須是唯一的。
| 來源 | 選擇 [事件中樞] 以建立事件中樞事件來源。
| 訂用帳戶識別碼 | 選取建立此事件中樞的訂用帳戶。
| 服務匯流排命名空間 | 選取包含事件中樞的服務匯流排命名空間。
| 事件中樞名稱 | 選取事件中樞的名稱。
| 事件中樞原則名稱 | 選取共用的存取原則，可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**讀取**權限。
| 事件中樞取用者群組 | 從事件中樞讀取資料的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。

### <a name="provide-event-hub-settings-manually"></a>手動提供事件中樞設定

下表將說明 [新增事件來源] 索引標籤中的每個屬性和屬性描述，以便您手動輸入設定：

| 屬性名稱 | 說明 |
| --- | --- |
| 事件來源名稱 | 事件來源的名稱。 此名稱在 Time Series Insights 中必須是唯一的。
| 來源 | 選擇 [事件中樞] 以建立事件中樞事件來源。
| 訂用帳戶識別碼 | 建立此事件中樞的訂用帳戶。
| 資源群組 | 建立此事件中樞的訂用帳戶。
| 服務匯流排命名空間 | 服務匯流排命名空間是一個容器，包含一組訊息實體。 建立新的事件中樞時，也會建立服務匯流排命名空間。
| 事件中樞名稱 | 事件中樞的名稱。 當您建立事件中心時，您也會指定特定名稱
| 事件中樞原則名稱 | 共用的存取原則，可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 事件來源的共用存取原則必須有**讀取**權限。
| 事件中樞原則金鑰 | 用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 在此輸入主要金鑰或次要金鑰。
| 事件中樞取用者群組 | 從事件中樞讀取資料的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。

## <a name="next-steps"></a>後續步驟

1. 將資料存取原則新增至您的環境[定義資料存取原則](time-series-insights-data-access.md)
1. 在[入口網站](https://insights.timeseries.azure.com)中存取您的環境
