---
title: "使用適用於 Visual Studio 的 Azure 串流分析工具 | Microsoft Docs"
description: "開始使用適用於 Visual Studio 的 Azure 串流分析工具"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sujie
ms.openlocfilehash: 8e3f1ae6739896dfd1329561dbcede38a6069546
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>使用適用於 Visual Studio 的 Azure 串流分析工具
適用於 Visual Studio 的 Azure 串流分析工具現已正式推出。 這些工具為「串流分析」的使用者提供更豐富的疑難排解體驗，而且能撰寫複雜查詢，甚至在本機撰寫查詢。 您也能將串流分析工作匯出到 Visual Studio 專案中。

## <a name="introduction"></a>簡介
在本教學課程中，您將學習如何使用適用於 Visual Studio 的串流分析工具來建立、撰寫、在本機測試、管理及偵錯 Azure 串流分析作業。 

完成本教學課程之後，您將能夠：

* 熟悉適用於 Visual Studio 的串流分析工具。
* 設定及部署串流分析工作。
* 使用本機範例資料於本機測試作業。
* 使用監視功能進行問題疑難排解。
* 將現有作業匯出到專案。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 完成[利用串流分析來建置 IoT 解決方案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)中直至「建立串流分析作業」的步驟。 
* 安裝 Visual Studio 2017、Visual Studio 2015 或 Visual Studio 2013 Update 4。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 
* 依照[安裝指示](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)，安裝 Visual Studio 適用的串流分析工具。

## <a name="create-a-stream-analytics-project"></a>建立串流分析專案
在 Visual Studio 中，選取 [檔案] > [新增專案]。 在左側的範本清單中，選取 [串流分析]，然後選取 [Azure 串流分析應用程式]。
在頁面底部輸入專案 [名稱]、[位置] 和 [方案名稱]，就像您其他的專案一樣。

![建立新專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

[方案總管] 中會產生專案 **Toll**。

![方案總管中的付費專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>選擇正確的訂用帳戶
1. 在 [檢視] 功能表上，選取 Visual Studio 中的 [伺服器總管]。

2. 使用您的 Azure 帳戶登入。 

## <a name="define-input-sources"></a>定義輸入來源
1. 在 [方案總管] 中，展開 [輸入] 節點，並將 **Input.json** 重新命名為 **EntryStream.json**。 按兩下 **EntryStream.json**。

2. 在 [輸入別名] 中，輸入 **EntryStream**。 請注意，輸入別名是在查詢指令碼中使用。

3. 在 [來源類型] 中，選取 [資料流]。

4. 在 [來源] 中，選取 [事件中樞]。

5. 在 [服務匯流排命名空間] 中，選取下拉式清單中的 [TollData] 選項。

6. 在 [事件中樞名稱] 中，選取 [entry]。

7. 在 [事件中樞原則名稱] 中，選取 [RootManageSharedAccessKey] (預設值)。

8. 在 [事件序列化格式] 中選取 [Json]，及針對 [編碼] 選取 [UTF8]。
   
   您的設定看起來會像這樣：
   
   ![輸入設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. 在頁面底部，選取 [儲存] 以完成精靈。 您現在可以新增另一個輸入來源以建立輸出資料流。 以滑鼠右鍵按一下 [輸入] 節點，然後選取 [新增項目]。
   
   ![新增項目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. 在快顯視窗中，選取 [串流分析輸入]，然後將 [名稱] 變更為 **ExitStream.json**。 選取 [新增] 。
   
    ![新增項目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. 在專案中按兩下 **ExitStream.json**，並按照填入欄位進入資料流的步驟操作。 務必在 [事件中樞名稱] 中輸入 **exit**，如下列螢幕擷取畫面所示：
   
    ![ExitStream 設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   您已經定義了兩個輸入資料流。
   
   ![兩個輸入資料流](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   接下來，為包含車輛登記資料的 Blob 檔案新增參考資料輸入。
   
12. 以滑鼠右鍵按一下專案中的 [輸入] 節點，然後依照資料流輸入的相同步驟操作。 在 [來源類型] 中選取 [參考資料]，並針對 [輸入別名] 輸入 **Registration**。
   
    ![註冊設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. 選取**儲存體**帳戶，其包含具有 **TollData** 的選項。 容器名稱是 **TollData**，而 [路徑模式] 是 **registration.json**。 這個檔案名稱會區分大小寫，且應該是小寫。

14. 選取 [儲存] 完成精靈。

現在，所有輸入都已定義。

## <a name="define-output"></a>定義輸出
1. 在 [方案總管] 中，展開 [輸入] 節點，然後按兩下 [Output.json]。

2. 在 [輸出別名] 中，輸入 **output**。 在 [接收] 中，選取 [SQL Database]。

3. 在 [資料庫] 名稱中，輸入 **TollDataDB**。

4. 在 [使用者名稱] 中，輸入 **tolladmin**。 在 [密碼] 中，輸入 **123toll!**。 在 [資料表] 中，輸入 **TollDataRefJoin**。

5. 選取 [ **儲存**]。

   ![輸出設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>串流分析查詢
本教學課程會嘗試回答幾個與付費資料相關的商務問題。 我們已建構可在串流分析中用來提供相關答案的查詢。 在開始第一個串流分析作業之前，讓我們先來探索一個簡單的情節和查詢語法。

### <a name="introduction-to-stream-analytics-query-language"></a>串流分析查詢語言簡介
假設您需要計算進入某個收費亭的車輛數目。 由於這是連續的事件串流，因此您必須定義時段。 我們來將問題修改為「每三分鐘有多少車輛進入收費亭？」 此措施通常稱為輪轉計數。

讓我們查看可回答這個問題的串流分析查詢：

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

如您所見，串流分析會使用類似 SQL 的查詢語言。 它新增幾個擴充來指定和時間有關的查詢層面。

如需詳細資料，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時段](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

您已經將第一個串流分析查詢編寫完成，現在該利用位於下列路徑中您 TollApp 資料夾中的範例資料檔案來測試查詢了：

**..\TollApp\TollApp\Data**

這個資料夾包含下列檔案：

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>問題：進入某個收費亭的車輛數目
在專案中，按兩下 [Script.asaql]，就會在編輯器中開啟指令碼。 將上一節中的指令碼貼到編輯器中。 查詢編輯器支援 IntelliSense、語法著色和錯誤標記。

![查詢編輯器](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>在本機測試串流分析查詢
您可以先編譯查詢以查看是否有任何語法錯誤。

1. 若要使用範例資料驗證此查詢，您可以使用本機範例資料，方法是以滑鼠右鍵按一下輸入並選取 [新增本機輸入]。
   
   ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. 在快顯視窗中，從本機路徑選取範例資料。 選取 [ **儲存**]。
   
   ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   系統會自動在您的輸入資料夾新增名為 **local_EntryStream.json** 的檔案。
   
   ![本機輸入資料夾檔案清單](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. 在查詢編輯器中選取 [在本機執行]。 或者，您可以按 F5。
   
   ![在本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   您可以從主控台輸出中尋找輸出路徑。 按任何鍵以開啟結果資料夾。
   
   ![本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. 檢查本機資料夾中的結果。
   
   ![本機資料夾結果](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>範例輸入
您也可以將輸入來源的輸入資料取樣到本機檔案。 以滑鼠右鍵按一下輸入設定檔，然後選取 [範例資料]。 

![範例資料](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

請注意，目前只能取樣事件中樞或 IoT 中樞。 不支援其他輸入來源。 在快顯對話方塊中，填入用來儲存範例資料的本機路徑。 選取 [範例]。

![範例資料設定](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
您可以在 [輸出] 視窗中查看進度。 

![範例資料輸出](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>將串流分析查詢提交至 Azure
1. 在 [查詢編輯器] 中，選取指令碼編輯器中的 [提交至 Azure]。

   ![提交至 Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. 選取 [建立新的 Azure 串流分析作業]。 在 [工作名稱] 中，輸入 **TollApp**。 在下拉式清單中選擇正確的**訂用帳戶**。 選取 [提交]。

   ![提交作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>啟動工作
現在已建立作業，作業檢視會自動開啟。 
1. 選取綠色箭頭按鈕以啟動作業。

   ![啟動作業按鈕](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 選擇預設設定，再選取 [啟動]。
 
   ![啟動作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   您可以看到作業狀態變成 [執行中]，且有輸入/輸出事件。

   ![工作摘要和計量](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中查看結果
1. 開啟 Visual Studio 伺服器總管，然後用滑鼠右鍵按一下 [TollDataRefJoin]  資料表。

2. 選取 [顯示資料表資料] 可查看作業的輸出。
   
   ![顯示資料表資料](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>檢視作業計量
[作業計量] 會顯示一些基本的作業統計資料。 

![作業計量](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>在伺服器總管中列出作業
在 [伺服器總管] 中，選取 [串流分析作業]，然後選取 [重新整理]。 您的作業會出現在 [串流分析作業] 下。

![工作清單](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>開啟作業檢視
展開作業節點並按兩下 [作業檢視] 節點來開啟作業檢視。

![作業檢視](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>將現有作業匯出到專案
有兩種方式可以將現有的作業匯出到專案。
* 在 [伺服器總管] 的 [資料流分析工作] 節點下，以滑鼠右鍵按一下作業節點。 選取 [匯出至新的串流分析專案]。
   
   ![匯出至新的串流分析專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   產生的專案會在 [方案總管] 中顯示。
   
    ![方案總管作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* 在作業檢視中，選取 [產生專案]。
   
   ![產生專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>已知問題與限制
 
* 如果查詢具有 geo-spatial 函式，則不能執行本機測試。
* 編輯器不支援新增或變更 JavaScript UDF。
* 本機測試不支援將輸出儲存為 JSON 格式。 
* 不支援 Power BI 輸出和 ADLS 輸出。



## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


