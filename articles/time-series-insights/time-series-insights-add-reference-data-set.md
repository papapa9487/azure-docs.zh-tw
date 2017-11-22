---
title: "如何將參考資料集新增至 Azure 時間序列深入解析環境 | Microsoft Docs"
description: "本文說明如何將參考資料集新增至 Azure 時間序列深入解析環境。 將參考資料聯結至您的來源資料很實用，可以增加資料值。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 入口網站建立時間序列深入解析環境的參考資料集

本文說明如何將參考資料集新增至 Azure 時間序列深入解析環境。 將參考資料聯結至您的來源資料很實用，可以增加資料值。

參考資料集是許多項目的集合，這些項目是從事件來源的事件擴展而來的。 Time Series Insights 輸入引擎會將事件來源的事件和參考資料集中的項目聯結在一起。 然後此增強的事件可用於查詢。 這項聯結是以參考資料集中定義的索引鍵為基礎。

## <a name="add-a-reference-data-set"></a>新增參考資料集

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 找出您的現有時間序列深入解析環境。 按一下 Azure 入口網站左側功能表中的 [所有資源]。 選取 Time Series Insights 環境。

3. 在 [環境拓撲] 標題下，選取 [參考資料集]。

    ![建立 Time Series Insights 參考資料集](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. 選取 [+新增] 來新增參考資料集。

5. 指定唯一的 [參考資料集名稱]。

    ![建立 Time Series Insights 參考資料集 - 詳細資料](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. 在空白欄位中指定 [索引鍵名稱]，然後選取其 [類型]。 此名稱和類型用來從事件來源的事件中選取正確的屬性，以便聯結至參考資料。 

   例如，如果提供的索引鍵名稱為 **DeviceId**，而類型為 **String**，則時間序列深入解析輸入引擎會在要查詢和聯結的每個輸入事件中尋找名稱為 **DeviceId** 和類型為 **String** 的屬性。 您可以提供一個以上的索引鍵與事件聯結。 索引鍵名稱的對應會區分大小寫。

7. 選取 [ **建立**]。

## <a name="next-steps"></a>後續步驟
* 以程式設計的方式[管理參考資料](time-series-insights-manage-reference-data-csharp.md)。
* 如需完整的 API 參考，請參閱＜[參考資料 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api)＞文件。
