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
ms.openlocfilehash: b0304265dd43986a59515e8d68630a76d0364748
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>使用適用於 Visual Studio 的 Azure 串流分析工具
適用於 Visual Studio 的 Azure 串流分析工具現已正式推出。 這些工具為「串流分析」的使用者提供更豐富的疑難排解體驗，而且能撰寫複雜查詢，甚至在本機撰寫查詢。 您還能將串流分析工作匯出到 Visual Studio 專案中。

## <a name="introduction"></a>簡介
在本教學課程中，您將學習如何使用適用於 Visual Studio 的 Azure 串流分析工具來建立、撰寫、本機測試、管理及偵錯 Azure 串流分析工作。 

完成本教學課程之後，您將能夠：
* 熟悉適用於 Visual Studio 的 Azure 串流分析工具。
* 設定及部署串流分析工作。
* 使用本機範例資料於本機測試作業。
* 使用監視功能進行問題疑難排解。
* 將現有作業匯出到專案。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：
* 完成[利用串流分析教學課程來建置 IoT 解決方案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)的**建立串流分析工作**之前的步驟。 
* 安裝 Visual Studio 2017、Visual Studio 2015、Visual Studio 2013 Update 4。 支援 Enterprise (Ultimate/Premium)、Professional、Community 版本；不支援 Express 版本。 
* 依照[安裝指示](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)，安裝 Visual Studio 適用的串流分析工具。

## <a name="create-a-stream-analytics-project"></a>建立串流分析專案
在 Visual Studio 中，按一下 [檔案] 功能表，並選取 [新增專案]。 從左側的範本清單選擇 串流分析，然後按一下Azure 串流分析應用程式。
在底部輸入 [專案名稱]、[位置] 和 [方案名稱]，就像您其他的專案一樣。

![建立 Azure 串流分析專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

您會看到 [方案總管] 中產生一個 **Toll** 專案。

![建立 Azure 串流分析專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>選擇正確的訂用帳戶
1. 在 Visual Studio 中從 [檢視] 功能表開啟 [伺服器總管]。
2. 登入您的 Azure 帳戶。 

## <a name="define-input-sources"></a>定義輸入來源
1.  在 [方案總管中]，展開 [輸入] 節點，並將 **Input.json** 重新命名為 **EntryStream.json**。 按兩下 **EntryStream.json**。
2.  您的 [輸入別名] 現在應該是 **EntryStream**。 請注意，輸入別名將用於查詢指令碼中。 
3.  [來源類型] 是 [資料流]。
4.  [來源] 是 [事件中樞]。
5.  [服務匯流排命名空間] 應該是下拉式清單中的 **tollData**。
6.  [事件中樞名稱] 應該設定為 [進入]。
7.  [事件中樞原則名稱] 是**RootManageSharedAccessKey** (預設值)。
8.  選取 [JSON] 做為 [事件序列化格式]，並選取 [UTF8] 做為 [編碼] 格式。
   
   您的設定看起來會像這樣：
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9.  請按一下頁面底部的 [儲存] 來完成精靈。 您現在可以新增另一個輸入來源以建立輸出資料流。 以滑鼠右鍵按一下 [輸入] 節點，再按一下 [新增項目]。
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. 在快顯視窗中，選取 [Azure 串流分析輸入]，然後將名稱變更為 **ExitStream.json**。 按一下 [新增] 。
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. 在專案中按兩下 **ExitStream.json** 並按照填入進入資料流的步驟操作。 請務必輸入 [事件中樞名稱] 的值，如以下螢幕擷取畫面所示：
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   您已經定義了兩個輸入資料流。
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   接下來，您將為包含車輛登記資料的 Blob 檔案新增參考資料輸入。
   
12. 以滑鼠右鍵按一下專中的 [輸入] 節點，然後依照資料流輸入的相同程序，但選取 [參考資料] 而不是 [資料流]，且 [輸入別名] 是 [註冊]。
   
   ![定義輸入來源](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. 選取包含 **tolldata** 的儲存體帳戶。 容器名稱應該是 **tolldata**，[路徑格式] 應該是 **registration.json**。 這個檔案名稱會區分大小寫，且應該是小寫。
14. 按一下 [儲存] 完成精靈。

現在，所有輸入都已經定義了。

## <a name="define-output"></a>定義輸出
1.  在 [方案總管] 中，展開 [輸入] 節點，然後按兩下 **Output.json**。
2.  將 [輸出別名] 設定為 [輸出]，然後將 [接受] 設定為 [SQL 資料庫]。
2.  輸入資料庫名稱：**TollDataDB**。
3.  在 [使用者名稱] 欄位中輸入 **tolladmin**，在 [密碼] 欄位中輸入 **123toll!** ，在 [資料表] 欄位中輸入 **TollDataRefJoin**。
4.  按一下 [儲存] 。

![定義輸出](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="azure-stream-analytics-query"></a>Azure 串流分析查詢
本教學課程會嘗試回答和幾個通行費資料相關的業務問題，並建構可在 Azure 串流分析中使用的串流分析查詢來提供相關的答案。
在您開始第一個串流分析工作之前，讓我們先來探索簡單的案例和查詢語法。

### <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure 串流分析查詢語言簡介
假設您需要計算進入某個收費亭的車輛數目。 由於這是連續的事件串流，因此您必須定義一個「時段」。 我們來將問題修改為「每三分鐘有多少車輛進入收費亭」。 這通常稱為輪轉計數。

我們來看看回答這個問題的 Azure 串流分析查詢：

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

如您所見，Azure 串流分析會使用類似 SQL 的查詢語言，並新增幾個擴充功能來指定和時間有關的查詢層面。

如需詳細資料，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時段](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

您已經將第一個 Azure 串流分析查詢編寫完成，現在該利用位於下列路徑中您 TollApp 資料夾中的範例資料檔案來測試查詢了：

**..\TollApp\TollApp\Data**

此資料夾包含下列檔案：•   Entry.json •   Exit.json •   Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>問題：進入某個收費亭的車輛數目
在專案中，按兩下 Script.asaql 來在編輯器中開啟該指令碼，然後在編輯器中貼上上一節中的指令碼。 查詢編輯器支援 IntelliSense、語法著色和錯誤標記。

![Edit query](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="testing-azure-stream-analytics-queries-locally"></a>在本機測試 Azure 串流分析查詢

1. 您可以先編譯查詢以查看是否有任何語法錯誤。 [TBD]
2. 若要使用範例資料驗證此查詢，您可以使用本機範例資料，方法是以滑鼠右鍵按一下 [輸入] 並從內容功能表選取 [新增本機輸入]。
   
   ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
   在快顯視窗中，從本機路徑選取範例資料。 按一下 [儲存] 。
   
   ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   系統會自動在您的輸入資料夾新增名為 **local_EntryStream.json** 的檔案。
   
   ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. 按一下查詢編輯器中的 [在本機執行]。 或者，您可以按 F5。
   
   ![本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   您可以在主控台輸出找到輸出路徑，然後按一下任意鍵來開啟結果資料夾。
   
   ![本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. 在本機資料夾查看結果。
   
   ![本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>範例輸入
您也可以將輸入來源的輸入資料取樣到本機檔案。 以滑鼠右鍵按一下輸入設定檔，然後選取 [範例資料]。 

![範例資料](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

請注意，您只能對事件中樞或 IoT 中樞取樣。 不支援其他輸入來源。  在快顯視窗中，填入儲存取樣資料的路徑。 按一下 [取樣]。

![範例資料](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
您可以在 [輸出] 視窗中查看進度。 

![範例資料](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-azure-stream-analytics-query-to-azure"></a>送出 Azure 串流分析查詢到 Azure
在 [查詢編輯器] 中，按一下 [從指令碼編輯器提交至 Azure]。

![提交作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
選擇 [建立新的 Azure 串流分析工作]。 輸入 [作業名稱]，如下所示。 選擇正確的訂用帳戶。 按一下 [提交]。

![提交作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>啟動工作
作業已經建立，並會自動開啟作業檢視。 按一下「綠色」的按鈕來啟動作業。

![開始工作](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
選擇預設設定並按一下 [啟動]。
 
![開始工作](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

您可以看到作業狀態變成 [執行中]，且有輸入/輸出事件。

![開始工作](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中查看結果
1. 開啟 Visual Studio 伺服器總管，然後用滑鼠右鍵按一下 [TollDataRefJoin]  資料表。
2. 按一下 [顯示資料表資料]  可查看您工作的輸出。
   
   ![伺服器總管中 [顯示資料表資料] 的選取項目](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>檢視作業計量
您可以在 [作業計量] 找到一些基本的作業統計資料。 

![作業計量](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>在伺服器總管中列出作業
在 [伺服器總管] 中按一下 [串流分析工作]，再按一下 [重新整理]。 您應該可以看到作業顯示在 [串流分析工作] 底下。

![列出工作](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-job-view"></a>開啟作業檢視
展開作業節點並按兩下 [作業檢視] 節點來開啟作業檢視。

![作業檢視](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>將現有作業匯出到專案
有兩種方式可以將現有的作業匯出到專案。
1. 在 [伺服器總管] 中，以滑鼠右鍵按一下 [串流分析工作] 節點下的作業節點。 按一下內容功能表中的 [匯出到新的串流分析專案]。
   
   ![匯出作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   產生的專案會在 [方案總管] 中。
   
   ![匯出作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
2. 在作業檢視中，按一下 [產生專案]。
   
   ![匯出作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>已知問題與限制
 
1. 如果查詢中沒有 Geo-Spatial 函數，則不能執行本機測試。 
2. 編輯器不支援新增或變更 JavaScript UDF。
3. 本機測試不支援將輸出儲存為 JSON 格式。 
4. 不支援 Power BI 輸出和 ADLS 輸出。



## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-get-started.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


