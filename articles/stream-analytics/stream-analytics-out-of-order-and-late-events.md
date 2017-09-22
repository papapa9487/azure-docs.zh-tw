---
title: "Azure 串流分析的事件順序和延遲處理 | Microsoft Docs"
description: "深入了解 Azure 串流分析如何處理資料流中順序錯亂或延遲的事件"
keywords: "順序錯亂、延遲、事件"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure 串流分析事件的順序處理

在事件的時態性資料流中，每個事件都有指派的時間戳記。 Azure 串流分析會使用抵達時間或應用程式時間，將時間戳記指派給每個事件。 

**System.Timestamp** 資料行有指派給事件的時間戳記。 事件到達來源時，系統會為輸入來源指派抵達時間。 抵達時間是事件中樞輸入的 **EventEnqueuedTime** 和 Blob 輸入的 [Blob 上次修改時間](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)。 產生事件時會指派應用時間，且其為承載的一部分。 

若要按照應用時間處理事件，請在選取查詢中使用 TIMESTAMP BY 子句。 如果 TIMESTAMP BY 子句不存在，將按照抵達時間處理事件。 您可以使用事件中樞的 **EventEnqueuedTime** 屬性，並使用 Blob 輸入的 **BlobLastModified** 屬性來存取抵達時間。 

Azure 串流分析會依時間戳記順序產生輸出，並提供處理順序錯亂資料的一些設定。

![串流分析事件處理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

未依照順序的輸入串流為以下兩者其中之一：
* 已分類 (並因此*延遲*)。
* 已由系統調整 (根據使用者指定的原則)。

串流分析按照應用時間處理時，可容許延遲和順序錯亂事件。

**延遲抵達容許**

* 按照應用時間處理時，僅適用延遲抵達容許設定。 否則會予以忽略。
* 延遲抵達容許是抵達時間與應用時間之間的最大差異。 如果應用時間早於 *(抵達時間 - 延遲抵達時間)*，則會設定為 *(抵達時間 - 延遲抵達時間)*。
* 合併相同輸入資料流的多個分割區或多個輸入資料流時，延遲抵達容許是每個分割區等候新資料的時間上限。 

簡而言之，延遲抵達時間是事件產生與輸入來源收到事件之間的延遲上限。
首先會根據延遲抵達容許進行調整，再根據順序錯亂進行調整。 **System.Timestamp** 資料行有指派給事件的最後時間戳記。

**順序錯亂容許**

* 抵達順序錯亂但在「順序錯亂容許時間」內的事件，會透過*時間戳記重新排序*。 
* 抵達時間比容許範圍還晚的事件，會遭到*捨棄*或*調整*。
    * **調整**︰調整為看起來是在最低可接受時間抵達。 
    * **捨棄**︰放棄。

若要重新排序「順序錯亂容許時間」內收到的事件，查詢的輸出將*受到順序錯亂容許時間所延遲*。

**範例**

延遲抵達容許= 10 分鐘<br/>
順序錯亂容許 = 3 分鐘<br/>
按照應用時間處理<br/>

事件：

事件 1 _應用時間_ = 00:00:00，_抵達時間_ = 00:10:01，_System.Timestamp_ = 00:00:01，已調整，因為 (_抵達時間_ - _應用時間_) 大於延遲抵達容許。

事件 2 _應用時間_ = 00:00:01，_抵達時間_ = 00:10:01，_System.Timestamp_ = 00:00:01，未調整，因為應用時間在延遲抵達時間內。

事件 3 _應用時間_ = 00:10:00，_抵達時間_ = 00:10:02，_System.Timestamp_ = 00:10:00，未調整，因為應用時間在延遲抵達時間內。

事件 4 _應用時間_ = 00:09:00，_抵達時間_ = 00:10:03，_System.Timestamp_ = 00:09:00，已接受原始時間戳記，因為應用時間在順序錯亂容許內。

事件 5 _應用時間_ = 00:06:00，_抵達時間_ = 00:10:04，_System.Timestamp_ = 00:07:00，已調整，因為應用時間早於順序錯亂容許。



## <a name="get-help"></a>取得說明
如需其他協助，請參閱我們的 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

