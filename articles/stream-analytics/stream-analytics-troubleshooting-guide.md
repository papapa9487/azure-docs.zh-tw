---
title: "Azure 串流分析的疑難排解指南 | Microsoft Docs"
description: "如何對您的串流分析作業進行疑難排解"
keywords: "疑難排解指南"
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
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: dcff312e4a282b15e76ea32aadb1981a496a2446
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Azure 串流分析的疑難排解指南

第一眼看到 Azure 串流分析的疑難排解作業時，您可能會覺得這是複雜的工作。 在與許多使用者合作後，我們建立了本指南來協助您簡化程序，並移除輸入、輸出、查詢和函式的任何猜測工作。

## <a name="troubleshoot-your-stream-analytics-job"></a>對您的串流分析作業進行疑難排解

為取得串流分析作業的最佳疑難排解結果，請使用下列指導方針︰

1.  測試連線：
    - 針對各個輸入和輸出使用 [測試連線] 按鈕，驗證輸入及輸出的連線能力。

2.  檢查您的輸入資料：
    - 若要驗證輸入資料是否正流入事件中樞，請使用[服務匯流排總管](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)與 Azure 事件中樞連線 (若有使用事件中樞輸入)。  
    - 針對各個輸入使用 [**範例資料**](stream-analytics-sample-data-input.md) 按鈕，並下載輸入範例資料。
    - 檢查範例資料以了解資料形式︰結構描述和[資料類型](https://msdn.microsoft.com/library/azure/dn835065.aspx)。

3.  測試查詢︰
    - 在 [查詢] 索引標籤中，使用 [測試] 按鈕來測試查詢並使用已下載的範例資料來[測試查詢](stream-analytics-test-query.md)。 檢查是否有任何錯誤並嘗試修正。
    - 如果您使用 [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx)，請確定事件有大於[作業開始時間](stream-analytics-out-of-order-and-late-events.md)的時間戳記。

4.  對應用程式進行偵錯：
    - 從簡單的 select 陳述式到需多個步驟且複雜的彙總，以漸進方式重新建置查詢。 若要逐步建置查詢邏輯，請使用 [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) 子句。
    - 使用 [SELECT INTO](stream-analytics-select-into.md) 對查詢步驟進行偵錯。

5.  排除常見的錯誤，例如︰
    - 查詢中的 [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) 子句篩選出所有事件，造成無法產生任何輸出作業。
    - 當您使用視窗函式時，請等候完整的視窗運作時間，以查看查詢的輸出。
    - 事件的時間戳記早於作業開始時間，因此事件遭到刪除。

6.  使用事件順序︰
    - 若前述步驟皆運作正常，請移至 [設定] 刀鋒視窗，並選取[**事件排序**](stream-analytics-out-of-order-and-late-events.md)。 請確定此原則已設定為適用於您的情況。 如果您使用 [測試] 按鈕測試查詢，則不會套用原則。 此結果是在瀏覽器中進行測試與在生產環境中執行作業之間的一個差異。

7.  使用計量進行偵錯：
    - 若已經過預定的持續時間 (以查詢為基礎)，而您沒有收到輸出，請嘗試下列方法︰
        - 請查看 [監視] 索引標籤上的[**監視計量**](stream-analytics-monitoring.md)。因為是彙總值，計量會延遲幾分鐘顯示。
            - 若輸入事件 > 0，則作業可以讀取輸入資料。 如果輸入事件不是 > 0，然後︰
                - 若要查看資料來源是否為有效資料，請使用[服務匯流排總管](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)進行檢查。 此項檢查適用於使用事件中樞作為輸入的作業。
                - 請檢查資料序列化格式及資料編碼是否正確。
                - 如果該作業使用事件中樞，請檢查訊息本文是否為 *Null*。
            - 若資料轉換錯誤 > 0 且持續增加中，則可能符合下列情況：
                - 作業可能無法還原序列化事件。
                - 事件結構描述可能與查詢中事件的定義或預期結構描述不相符。
                - 事件中某些欄位的資料類型可能不符合預期類型。
            - 如果執行階段錯誤 > 0，則表示作業可以接收資料，但在處理查詢時會產生錯誤。
                - 若要找出錯誤，請前往[稽核記錄](../azure-resource-manager/resource-group-audit.md)，並篩選*失敗*狀態。
            - 如果 InputEvents > 0 且 OutputEvents = 0，則表示符合下列其中一個情況︰
                - 查詢處理產生了零個輸出事件。
                - 事件或其欄位可能格式錯誤，因此在查詢處理後導致零個輸出。
                - 由於連線或驗證問題使然，作業無法推送資料至輸出[接收](stream-analytics-select-into.md)。
        - 在所有前述錯誤案例中，作業記錄訊息說明了其他詳細資料 (包括目前所發生的事)，僅有查詢邏輯篩選掉所有事件的案例除外。 如果多事件處理產生錯誤，串流分析會在 10 分鐘內將同類型的前三個錯誤記錄到作業記錄中。 接著會隱藏其他完全相同的錯誤並顯示錯誤訊息，內容為「錯誤發生次數太頻繁，因此隱藏這些錯誤」。

8. 使用稽核和診斷記錄進行偵錯：
    - 使用[稽核記錄](../azure-resource-manager/resource-group-audit.md)，並透過篩選找出錯誤並進行偵錯。
    - 使用[作業診斷記錄](stream-analytics-job-diagnostic-logs.md)找出錯誤並進行偵錯。

9. 檢查輸出︰
    - 當作業狀態為「執行」時，依據查詢中所設定的持續時間而定，您可以在接收資料來源中看到輸出。
    - 如果您無法看到要傳送至特定輸出類型的輸出，請將其重新導向至較不複雜的類型，例如 Azure Blob。 透過使用儲存體總管，您可以查看是否可以看到輸出。 此外，請確定輸出上是否有節流限制阻擋了資料接收。

10. 搭配使用資料流分析與作業圖表計量︰
    - 若要分析資料流程與識別問題，請使用[作業圖表與計量](stream-analytics-job-diagram-with-metrics.md)。

11. 建立支援案例：
    - 最後，如果所有解決方案均失敗，請使用包含您作業的訂用帳戶識別碼來建立 Microsoft 支援服務案例。

## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

