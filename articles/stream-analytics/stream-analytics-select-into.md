---
title: "使用 SELECT INTO 對 Azure 串流分析查詢進行偵錯 | Microsoft Docs"
description: "在串流分析中使用 SELECT INTO 陳述式對進行中的查詢進行資料取樣"
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 6ffa756eef0cfa44d7dd397e43afbf054ac2df7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="debug-queries-by-using-select-into-statements"></a>使用 SELECT INTO 陳述式對查詢進行偵錯

在即時的資料處理中，了解資料在查詢進行中所呈現的樣子將會相當有幫助。 由於 Azure 串流分析作業的輸入或步驟可以讀取多次，因此您可以寫入額外的 SELECT INTO 陳述式。 這種方式會將中繼資料輸出到儲存體，讓您可以檢查資料的正確性，就像您對程式進行偵錯時*監看變數*的動作一樣。

## <a name="use-select-into-to-check-the-data-stream"></a>使用 SELECT INTO 檢查資料串流

下列 Azure 串流分析作業中的查詢範例有一個串流輸入、兩個的參考資料輸入和要傳送至 Azure 資料表儲存體的輸出。 查詢會聯結事件中樞的資料和兩個參考 Blob 以取得名稱和類別資訊︰

![SELECT INTO 查詢範例](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

請注意，雖然作業正在執行，但不會在輸出中產生任何事件。 在 [監視]圖格上 (如此處所示)，您可以看到輸入正在產生資料，但您無法知道哪一個**聯結**步驟會造成所有資料遭到捨棄。

![監視圖格](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
在此情況下，您可以新增一些額外的 SELECT INTO 陳述式以「記錄」中繼「聯結」結果及從輸入中讀取的資料。

在此範例中，我們新增了兩個新的「暫存輸出」。 這些暫存輸出可以是您喜歡的任何接收體。 在這裡我們使用 Azure 儲存體作為範例︰

![新增額外的 SELECT INTO 陳述式](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

然後您可以重新撰寫查詢，如下所示︰

![重新撰寫 SELECT INTO 查詢](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

現在請再次啟動作業，並讓它執行幾分鐘的時間。 然後使用 Visual Studio Cloud Explorer 查詢 temp1 和 temp2 以產生下列表格︰

**temp1 資料表**
![SELECT INTO temp1 資料表](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 資料表**
![SELECT INTO temp2 資料表](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

如您所見，temp1 和 temp2 都有資料，且 temp2 的名稱資料欄已正確地填入。 不過，因為輸出中仍沒有資料，可能有些地方出了問題︰

![SELECT INTO output1 資料表沒有資料](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

藉由資料取樣，您幾乎可以確定問題出自第二個「聯結」。 您可以從 Blob 下載參考資料，以及查看下列項目︰

![SELECT INTO 參考資料表](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

如您所見，此參考資料中的 GUID 格式與 temp2 中的 [from] 資料欄格式不同。 這就是為什麼資料並未如預期般送達 output1。

您可以修正資料格式並上傳至參考 Blob，然後再試一次︰

![SELECT INTO 暫存資料表](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

此時，輸出中的資料已如預期般進行格式化並填入。

![SELECT INTO 的最終資料表](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

