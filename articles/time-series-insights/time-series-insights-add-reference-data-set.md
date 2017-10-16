---
title: "將參考資料集新增至 Azure Time Series Insights 環境 | Microsoft Docs"
description: "在本教學課程中，您會將參考資料集新增至 Time Series Insights 環境"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>使用 Ibiza 入口網站建立 Time Series Insights 環境的參考資料集

參考資料集是許多項目的集合，這些項目是從事件來源的事件擴展而來的。 Time Series Insights 輸入引擎會將事件來源的事件和參考資料集中的項目聯結在一起。 然後此增強的事件可用於查詢。 這項聯結是以參考資料集中定義的索引鍵為基礎。

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>將參考資料集新增至環境的步驟

1. 登入 [Ibiza 入口網站](https://portal.azure.com)。
2. 按一下 Ibiza 入口網站左側功能表中的 [所有資源]。
3. 選取 Time Series Insights 環境。

    ![建立 Time Series Insights 參考資料集](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. 選取 [參考資料集]，並按一下 [+ 新增]。

    ![建立 Time Series Insights 參考資料集 - 詳細資料](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. 指定參考資料集的名稱。
6. 指定索引鍵名稱和類型。 此名稱和類型會用來從事件來源的事件中選取正確屬性。 例如，如果提供的索引鍵名稱為 “DeviceId”，而類型為 “String”，則 Time Series Insights 輸入引擎會在輸入的事件中尋找名稱為 “DeviceId” 和類型為 “String” 的屬性。 您可以提供一個以上的索引鍵與事件聯結。 屬性名稱的對應會區分大小寫。

     ![建立 Time Series Insights 參考資料集 - 詳細資料](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. 按一下 [建立]。

## <a name="next-steps"></a>後續步驟

* 以程式設計的方式[管理參考資料](time-series-insights-manage-reference-data-csharp.md)。
* 如需完整的 API 參考，請參閱＜[參考資料 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api)＞文件。