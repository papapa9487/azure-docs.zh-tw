---
title: "如何為串流分析建立資料分析處理工作 | Microsoft Docs"
description: "為串流分析建立資料分析處理工作 | 學習路徑區段。"
keywords: "資料分析處理"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>如何為串流分析建立資料分析處理工作
Azure 串流分析中的最上層資源就是串流分析工作。  其包含一或多個輸入資料來源、一個表示資料轉換的查詢，以及一或多個用來寫入結果的輸出目標。 這些功能結合的結果，讓使用者能夠執行串流資料案例的資料分析處理工作。

若要開始使用串流分析，您必須先建立新的串流分析工作。  請注意，直到作業開始之前，這個動作不會以任何方式計費。

1. 登入線上 [Azure 傳統入口網站](http://manage.windowsazure.com)或 [Azure 入口網站](https://portal.azure.com/)。
2. 在入口網站中︰按一下 [新增]，根據您的入口網站按一下 [資料服務] 或 [資料分析]，然後按一下 [Azure 串流分析] 或 [串流分析]，再按一下 [快速建立]。
   
   ![資料分析處理工作精靈](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![建立資料分析處理工作](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. 為串流分析工作指定所需的組態。
   
   * 請在 [工作名稱]  方塊輸入名稱，以識別串流分析工作。 [工作名稱]  經驗證後，[工作名稱] 方塊中便會出現綠色的核取記號。 此 [工作名稱]  只能包含英數及 '-' 字元，且長度必須介於 3 到 63 個字元。
   * 請使用 Azure 入口網站的 [區域] 或 Azure 入口網站的 [位置]，來指定您要用來執行工作的地理位置。
   * 如果您使用的是 Azure 入口網站，請選取或建立儲存體帳戶來做為「區域監視儲存體帳戶」 。 此儲存體帳戶會用來儲存在此區域內執行之所有串流分析工作的監視資料。
   * 如果使用 Azure 入口網站，請指定新的或現有的 **資源群組** 來保存應用程式的相關資源。
4. 當新的串流分析工作選項設定完畢之後，請按一下 [建立串流分析工作] 。 建立串流分析工作可能需要數分鐘的時間。 若要檢查狀態，您可以監視 [通知中樞] 中的進度。
   
   ![資料分析處理作業通知中樞](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Azure 入口網站中資料分析處理作業的建立作業](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. 新作業會顯示 [已建立] 的狀態。 請注意，此時 [開始]  按鈕已停用。 請先設定作業輸入、查詢和輸出，才能開始作業。
   
   ![資料分析處理的作業狀態](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Azure 入口網站中資料分析處理的作業狀態](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


