---
title: "Azure 串流分析的事件順序和延遲處理 | Microsoft Docs"
description: "深入了解串流分析如何處理資料串流中順序錯亂或延遲的事件。"
keywords: "順序錯亂、延遲、事件"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Azure 串流分析事件的順序考量

## <a name="understand-arrival-time-and-application-time"></a>了解抵達時間與應用時間。

在事件的時態性資料流中，每個事件都有指派的時間戳記。 Azure 串流分析會使用抵達時間或應用時間，將時間戳記指派給每個事件。 System.Timestamp 資料行有指派給事件的時間戳記。 事件到達來源時，系統會為輸入來源指派抵達時間。 抵達時間是事件中樞輸入的 EventEnqueuedTime 以及 Blob 輸入的 [Blob 上次修改時間](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)。 產生事件時會指派應用時間，且其為承載的一部分。 若要按照應用時間處理事件，請在選取查詢中使用 "Timestamp by" 子句。 如果 "Timestamp by" 子句不存在，將按照抵達時間處理事件。 您可以使用事件中樞的 EventEnqueuedTime 屬性，並使用 Blob 輸入的 BlobLastModified 屬性來存取抵達時間。 Azure 串流分析會依時間戳記順序產生輸出，並提供處理順序錯亂資料的一些設定。


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Azure 串流分析對多個資料流的處理

在少數情況下，Azure 串流分析工作會結合來自多個時間軸的事件，這些情況包括：

* 從多個分割區產生輸出。 沒有明確 "Partition by PartitionId" 的查詢必須結合來自所有分割區的事件。
* 兩個以上不同輸入來源的聯集。
* 聯結輸入來源。

在結合多個時間軸的案例中，Azure 串流分析只會在結合的所有來源都至少位於時間 *t1* 之後，才會針對時間戳記 *t1* 產生輸出。
例如，如果查詢從包含兩個分割區 (其中一個分割區 *P1* 在時間 *t1* 之前含有事件，而另一個分割區 *P2* 在時間 *t1 + x* 之前含有事件) 的*事件中樞*分割區進行讀取，則輸出會在時間 *t1* 之前產生。
不過，如果有明確的 *"Partition by PartitionId"* 子句，則兩個分割區會單獨進行。
[延遲傳入容錯] 設定是用來處理某些分割區中資料不存在的問題。

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>設定 [延遲傳入容錯] 和 [容錯順序不正確]
未依照順序的輸入串流為以下兩者其中之一：
* 已分類 (並因此**延遲**)。
* 已由系統調整 (根據使用者指定的原則)。

依**應用時間**處理時，串流分析可容許延遲和順序錯亂的事件。 下列設定位於 Azure 入口網站的 [事件順序] 選項中： 

![串流分析事件處理](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**延遲抵達容許**
* 只有在按照應用時間處理時，才適用這個設定，否則會遭到忽略。
* 這是抵達時間與應用時間之間的最大差異。 如果應用時間早於 (抵達時間 - 延遲抵達時間)，則會設定為 (抵達時間 - 延遲抵達時間)
* 合併相同輸入資料流的多個分割區或多個輸入資料流時，延遲抵達容許是每個分割區等候新資料的時間上限。 

簡而言之，延遲抵達時間是事件產生與輸入來源收到事件之間的延遲上限。
首先會根據延遲抵達容許進行調整，再根據順序錯亂進行調整。 **System.Timestamp** 資料行將有指派給事件的最後時間戳記。

**順序錯亂容許**
* 抵達順序錯亂但在「順序錯亂容許時間」設定內的事件，會**透過時間戳記重新排序**。 
* 抵達時間比容許範圍還晚的事件，會遭到**捨棄或調整**。
    * **調整**︰調整為看起來是在最低可接受時間抵達。 
    * **捨棄**︰放棄。

若要重新排序「順序錯亂容許時間」內收到的事件，查詢的輸出將**受到順序錯亂容許時間所延遲**。

**範例**

* 延遲抵達容許 = 10 分鐘<br/>
* 順序錯亂容許 = 3 分鐘<br/>
* 按照應用時間處理<br/>
* 事件：
   * 事件 1 _應用時間_ = 00:00:00，_抵達時間_ = 00:10:01，_System.Timestamp_ = 00:00:01，已調整，因為 (_抵達時間_ - _應用時間_) 大於延遲抵達容許。
   * 事件 2 _應用時間_ = 00:00:01，_抵達時間_ = 00:10:01，_System.Timestamp_ = 00:00:01，未調整，因為應用時間在延遲抵達時間內。
   * 事件 3 _應用時間_ = 00:10:00，_抵達時間_ = 00:10:02，_System.Timestamp_ = 00:10:00，未調整，因為應用時間在延遲抵達時間內。
   * 事件 4 _應用時間_ = 00:09:00，_抵達時間_ = 00:10:03，_System.Timestamp_ = 00:09:00，已接受原始時間戳記，因為應用時間在順序錯亂容許內。
   * 事件 5 _應用時間_ = 00:06:00，_抵達時間_ = 00:10:04，_System.Timestamp_ = 00:07:00，已調整，因為應用時間早於順序錯亂容許。

## <a name="practical-considerations"></a>實際考量
如上所述，[延遲傳入容錯] 是應用時間與抵達時間之間的最大差異。
此外，依應用時間處理時，在套用 [容錯順序不正確] 設定之前，系統先對比已設定的 [延遲傳入容錯] 還要晚的事件進行調整。 因此，有效的順序不正確，是 [延遲傳入容錯] 和 [容錯順序不正確] 的最小值。

資料流內發生順序不正確事件的原因包括：
* 傳送者之間的時鐘誤差。
* 傳送者與輸入事件來源之間的變數延遲。

延遲傳入發生的原因包括：
* 傳送者在間隔之後針對稍後的間隔對事件進行批次及傳送。
* 傳送者傳送事件以及輸入來源接收到事件之間的延遲。

針對特定的作業設定 [延遲傳入容錯] 和 [容錯順序不正確] 時，應考慮正確性、延遲需求及上述因素。

以下是一些範例

### <a name="example-1"></a>範例 1： 
查詢中含有 "Partition by PartitionId" 子句且位於單一分割區內，系統使用同步傳送方法傳送事件。 同步傳送方法會封鎖，直到事件傳送出去為止。
在此情況下，順序不正確為零，因為在傳送下個事件之前，事件會搭配明確確認依序傳送。 延遲傳入是產生事件及傳送事件之間的延遲上限，以及傳送者與輸入來源之間的延遲上限

### <a name="example-2"></a>範例 2：
查詢中含有 "Partition by PartitionId" 子句且位於單一分割區內，系統使用非同步傳送方法傳送事件。 非同步傳送方法可以同時起始多個傳送，這可能會導致事件順序不正確。
在此情況下，順序不正確和延遲傳入都至少是產生事件及傳送事件之間的延遲上限，以及傳送者與輸入來源之間的延遲上限。

### <a name="example-3"></a>範例 3：
查詢中不含 "Partition by PartitionId"，且至少有兩個分割區。
設定與範例 2 相同。 不過，在其中一個分割區中缺少資料的情況下，可能會因額外的「延遲傳入容錯」時間而延遲輸出。

## <a name="to-summarize"></a>總結
* [延遲傳入容錯] 和 [容錯順序不正確] 時間應該根據正確性、延遲需求加以設定，同時也應該考慮事件的傳送方式。
* 建議將 [容錯順序不正確] 設定為小於 [延遲傳入容錯]。
* 結合多個時間軸時，在其中一個來源或分割區中缺少資料的情況下，可能會因額外的延遲傳入容錯時間而延遲輸出。

## <a name="get-help"></a>取得說明
如需其他協助，請參閱我們的 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
