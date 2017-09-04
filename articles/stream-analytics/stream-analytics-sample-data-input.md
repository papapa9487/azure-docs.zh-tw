---
title: "對 Azure 串流分析中的輸入進行取樣 | Microsoft Docs"
description: "針對串流分析作業進行移難排解時精準找出問題。"
keywords: "對輸入進行疑難排解、輸入取樣"
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
ms.openlocfilehash: db9686fad7153d63fc659869b93821200e08397c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-stream-analytics-input-stream-sampling"></a>Azure 資料流分析輸入串流的取樣

藉由使用 Azure 串流分析，您可以對來自檔案的輸入事件進行取樣並在入口網站測試查詢，且無須開始或停止作業。

## <a name="testing-your-query"></a>測試查詢

在串流分析作業的詳細資料窗格中，按一下 [查詢] 下的查詢名稱，可開啟 [查詢編輯器] 刀鋒視窗。 (在我們的範例中，因為尚未建立任何查詢，請按一下[< >] 預留位置。)

![串流分析查詢編輯器](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

如同先前版本，可建立查詢且內容豐富的編輯器刀鋒視窗隨即出現。 刀鋒視窗現在已更新為新的左側窗格，其中會顯示由查詢所使用及為此作業定義的輸入與輸出。

![串流分析查詢編輯器的輸入與輸出清單](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

此外也會顯示尚未定義的其他一組輸入與輸出。 這是來自您開始使用的查詢範本。 當您編輯查詢時，這組輸入與輸出就會變更或甚至消失。 您目前可以放心地忽略這組輸入與輸出。

若要測試樣本輸入資料，以滑鼠右鍵按一下任何輸入，然後選取 [從檔案上傳樣本資料]。

![串流分析查詢編輯器從檔案上傳樣本資料的命令](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

上傳完成後，按一下 [測試] 以根據您提供的樣本資料測試此查詢。

![串流分析查詢編輯器測試按鈕](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

如果您想要儲存測試輸出以供稍後使用，顯示您查詢輸出的瀏覽器中會包含下載結果的連結。 您現在可以輕鬆地反覆修改查詢並反覆測試查詢來查看輸出的變更狀況。

![串流分析查詢編輯器樣本輸出](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

在上述的圖中已加入第二個輸出，稱為 **HighAvgTempOutput**。

當您在查詢中使用多個輸出時，您可以分別查看每個輸出的結果，並輕鬆地在兩者之間切換。

當您滿意結果之後，您可以儲存您的查詢、啟動您的作業，以及坐下來觀看神奇的串流分析。

## <a name="get-help"></a>取得說明

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

