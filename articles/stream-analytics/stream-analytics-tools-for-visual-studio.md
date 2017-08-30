---
title: "使用適用於 Visual Studio 的 Azure 串流分析工具 | Microsoft Docs"
description: "開始使用適用於 Visual Studio 的 Azure 串流分析工具"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: samacha
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 618c1055795a75e0ed71dacddba3e076f81f4946
ms.contentlocale: zh-tw
ms.lasthandoff: 07/04/2017

---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>使用適用於 Visual Studio 的 Azure 串流分析工具
## <a name="introduction"></a>簡介
在本教學課程中，您將學習如何使用適用於 Visual Studio 的 Azure 串流分析工具來建立、撰寫、在本機測試、管理及偵錯 Azure 串流分析作業。 

完成本教學課程之後，您將能夠：
* 熟悉適用於 Visual Studio 的串流分析工具。
* 設定及部署串流分析工作。
* 使用本機範例資料於本機測試作業。
* 使用監視功能進行問題疑難排解。
* 將現有作業匯出到專案。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：
* 完成[利用串流分析來建置 IoT 解決方案教學課程](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)中的「建立串流分析作業」之前的步驟。 
* 使用 Visual Studio 2015、Visual Studio 2013 Update 4 或 Visual Studio 2012。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 不支援 Visual Studio 2017。 
* 使用 Azure SDK for .NET 2.7.1 版或更新版本。 使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 來進行安裝。
* 安裝[適用於 Visual Studio 的串流分析工具](http://aka.ms/asatoolsvs)。

## <a name="create-a-stream-analytics-project"></a>建立串流分析專案
1. 在 Visual Studio 中，按一下 [檔案] 功能表，然後選取 [新增專案]。 

2. 在左側的範本清單中，選取 [串流分析]，然後按一下 [Azure 串流分析應用程式]。

3. 就像建立其他專案一樣，輸入專案 [名稱]、[位置] 和 [方案名稱]。

    ![[新增專案] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

    [方案總管] 中會產生 **Toll** 專案。

    ![[方案總管] 中產生的 Toll 專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>選擇正確的訂用帳戶
1. 在 Visual Studio 中，按一下 [檢視] 功能表，然後開啟 [伺服器總管]。

2. 使用 Azure 帳戶進行登入。 

## <a name="define-the-input-sources"></a>定義輸入來源
1.  在 [方案總管] 中，展開 [輸入] 節點，並將 **Input.json** 重新命名為 **EntryStream.json**。 按兩下 **EntryStream.json**。
2.  [輸入別名] 現在是 **EntryStream**。 查詢指令碼中會使用此輸入別名。 
3.  在 [來源類型] 中，選取 [資料流]。
4.  在 [來源] 中，選取 [事件中樞]。
5.  在 [服務匯流排命名空間] 中，選取 [TollData] 選項。
6.  在 [事件中樞名稱] 中，選取 [entry]。
7.  在 [事件中樞原則名稱] 中，選取 [RootManageSharedAccessKey] \(預設值)。
8.  在 [事件序列化格式] 中，選取 [Json]。 
9.  在 [編碼] 中，選取 [UTF8]。 您的設定應該如以下的螢幕擷取畫面：

    ![[輸入] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
 
10. 若要完成精靈，請按一下 [儲存]。 您現在可以新增另一個輸入來源以建立輸出資料流。 以滑鼠右鍵按一下 [輸入] 節點，然後選取 [新增項目]。

    ![新增項目](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
 
11. 在視窗中，選取 [Azure 串流分析輸入]，然後將 [名稱] 變更為 **ExitStream.json**。 按一下 [新增] 。

    ![[加入新項目] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
 
12. 在專案中按兩下 [ExitStream.json]，然後依照您在進入資料流中所做的相同步驟操作。 請務必輸入 **exit** 作為 [事件中樞名稱]，如下列螢幕擷取畫面所示：

    ![ExitStream 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)

    您現在已定義兩個輸入資料流：

    ![進入和離開的輸入資料流](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
 
    接下來，為包含車輛登記資料的 Blob 檔案新增參考資料輸入。

13. 以滑鼠右鍵按一下專案中的 [輸入] 節點，然後依照您在資料流輸入中所做的相同步驟操作。 在 [輸入別名] 中，輸入 **Registration**，然後在 [來源類型] 中，選取 [參考資料]。

    ![[註冊] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)

14. 在 [儲存體帳戶] 中，選取 [tolldata] 選項。 在 [容器] 中，選取 [tolldata]，然後在 [路徑模式] 中，輸入 **registration.json**。 這個檔案名稱會區分大小寫，且應該是小寫。
15. 若要完成精靈，請按一下 [儲存]。

現在，所有輸入都已定義。

## <a name="define-the-output"></a>定義輸出
1.  在 [方案總管] 中，展開 [輸入] 節點，然後按兩下 [Output.json]。

2.  在 [輸出別名] 中，輸入 **output**。 
3.  在 [接收] 中，選取 [SQL Database]。
4.  在 [資料庫] 中，選取 [TollDataDB]。
5.  在 [使用者名稱] 中，輸入 **tolladmin**。 
6.  在 [密碼] 中，輸入 **123toll!**。
7.  在 [資料表] 中，輸入 **TollDataRefJoin**。
8.  按一下 [儲存] 。

    ![[輸出] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="create-a-stream-analytics-query"></a>建立串流分析查詢
本教學課程會嘗試回答幾個與付費資料相關的商務問題。 也會建構串流分析查詢，可以在串流分析中用來提供相關的答案。
在開始第一個串流分析作業之前，讓我們先來探索一個簡單的情節和查詢語法。

### <a name="introduction-to-the-stream-analytics-query-language"></a>串流分析查詢語言簡介
假設您需要計算進入某個收費亭的車輛數目。 因為此範例是連續的事件資料流，您必須定義一段時間。 將問題修改為「每三分鐘有多少車輛進入收費亭？」 這樣的資料計數方式通常稱為輪轉計數。

查看可回答這個問題的串流分析查詢：

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

串流分析會使用類似 SQL 的查詢語言，並新增幾個擴充來指定和時間有關的查詢層面。

如需詳細資訊，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時間範圍](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

既然您已經撰寫第一個串流分析查詢，現在就開始測試。 使用下列路徑中位於 TollApp 資料夾的範例資料檔案：

..\TollApp\TollApp\Data

這個資料夾包含下列檔案：
*   Entry.json
*   Exit.json
*   registration.json

## <a name="count-the-number-of-vehicles-entering-a-toll-booth"></a>計算進入收費亭的車輛數目
在專案中，按兩下 [Script.asaql]，就會在 [查詢編輯器] 中開啟指令碼。 將上一節中的指令碼，複製並貼到編輯器中。 查詢編輯器支援 IntelliSense、語法著色和錯誤標記。

![查詢編輯器](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>在本機測試串流分析查詢

1. 若要編譯查詢來檢查語法是否錯誤，請以滑鼠右鍵按一下專案，然後選取 [建置]。 

2. 若要利用範例資料來驗證這個查詢，您可以使用本機範例資料。 以滑鼠右鍵按一下輸入，然後選取 [新增本機輸入]。

    ![新增本機輸入](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
 
3. 在快顯視窗中，從本機路徑選取範例資料。 按一下 [儲存] 。

    ![[新增本機輸入] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
 
    名為 **local_EntryStream.json** 的檔案會自動新增至您的輸入資料夾。

    ![新增至輸入資料夾的檔案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
 
4. 在 [查詢編輯器] 中，按一下 [在本機執行]。 或者，您可以按 F5 鍵。

    ![在本機執行](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)

    ![本機執行輸出](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)

    按下任何鍵，可在 Visual Studio 中的 [ASA 本機執行結果] 視窗中檢視輸出。 

    ![[ASA 本機執行結果] 視窗](./media/stream-analytics-tools-for-vs/local-testing-output.png)

5. 按一下 [開啟結果資料夾]，可同時以 CSV 和 JSON 格式來檢查輸出檔案。

    ![開啟結果資料夾輸出](./media/stream-analytics-tools-for-vs/local-testing-files.png)
 

### <a name="sample-the-input-data"></a>取樣輸入資料
您也可以將輸入來源的輸入資料取樣到本機檔案。 
1. 以滑鼠右鍵按一下輸入設定檔，然後選取 [範例資料]。 

   ![範例資料](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

    目前只能取樣事件中樞或 IoT 中樞。 不支援其他輸入來源。

2. 在快顯視窗中，輸入用來儲存範例資料的本機路徑。 按一下 [取樣]。

    ![[範例資料] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
    您可以在 [輸出] 視窗中查看進度。 

    ![[輸出] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>將串流分析查詢提交至 Azure
1. 在 [查詢編輯器] 中，按一下指令碼編輯器中的 [提交至 Azure]。

    ![提交至 Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. 選取 [建立新的 Azure 串流分析作業]。 輸入 [作業名稱]，然後選取正確的 [訂用帳戶]。 按一下 [提交] 。

    ![[提交作業] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-a-job"></a>啟動作業
現在已建立作業，作業檢視會自動開啟。 
1. 若要啟動作業，請按一下**綠色箭頭**按鈕。

    ![啟動作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. 選取預設設定，然後按一下 [啟動]。
 
    ![[啟動作業] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

    作業 [狀態] 會變為 [執行中]，也出現 [輸入事件] 和 [輸出事件]。

    ![作業摘要中的 [執行中] 狀態](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-the-results-in-visual-studio"></a>在 Visual Studio 中查看結果
1. 在 Visual Studio 中，開啟 [伺服器總管]，然後以滑鼠右鍵按一下 [TollDataRefJoin] 資料表。
2. 選取 [顯示資料表資料] 可查看作業的輸出。
   
    ![在伺服器總管中選取 [顯示資料表資料]](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)


### <a name="view-the-job-metrics"></a>檢視作業計量
您可以在 [作業計量] 找到一些基本的作業統計資料。 

![[作業計量] 視窗](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>在伺服器總管中列出作業
在 [伺服器總管] 中，按一下 [串流分析作業]，然後按一下 [重新整理]。 作業會出現在 [串流分析作業] 下。

![伺服器總管中列出的串流分析作業](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>開啟作業檢視
若要開啟作業檢視，請展開作業節點，然後按兩下 [作業檢視] 節點。

![[作業檢視] 節點](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>將現有作業匯出到專案
有兩種方式可以將現有的作業匯出到專案。

在 [伺服器總管] 中，以滑鼠右鍵按一下 [串流分析作業] 節點中的作業節點，然後選取 [匯出至新的串流分析專案]。

![匯出至新的串流分析專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)

[方案總管] 中會產生專案。

![[方案總管] 中產生的專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
 
您也可以使用作業檢視並按一下 [產生專案]。

![產生專案](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="known-issues-and-limitations"></a>已知問題與限制
 
- 不支援 Power BI 輸出和 Azure Data Lake Store 輸出。
- 編輯器不支援新增或變更 JavaScript 使用者定義函式。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure 串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

