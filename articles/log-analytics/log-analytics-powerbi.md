---
title: "將 Azure Log Analytics 資料匯入至 Power BI | Microsoft Docs"
description: "Power BI 是 Microsoft 的雲端架構商務分析服務，能提供豐富的視覺效果和不同資料集的分析報告。  本文說明如何設定將 Log Analytics 資料匯入 Power BI，並將之設定為自動重新整理。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>將 Azure Log Analytics 資料匯入至 Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 是 Microsoft 的雲端架構商務分析服務，能提供豐富的視覺效果和不同資料集的分析報告。  您可以將 Log Analytics 記錄搜尋結果匯入 Power BI 資料集，以便運用其功能，例如可合併不同來源的資料，以及共用網路和行動裝置上的資料。

本文詳細說明如何將 Log Analytics 資料匯入 Power BI 並排定其進行自動重新整理。  [升級](#upgraded-workspace)和[舊版](#legacy-workspace)工作區包含不同的流程。

## <a name="upgraded-workspace"></a>已升級的工作區


若要將[升級 Log Analytics 工作區](log-analytics-log-search-upgrade.md)的資料匯入到 Power BI，您必須按照 Log Analytics 的記錄搜尋查詢在 Power BI 中建立資料集。  每次資料集重新整理時，就會執行查詢。  然後，您即可使用資料集中的資料建立 Power BI 報表。  若要在 Power BI 中建立資料集，您必須從 Log Analytics 將您的查詢匯出到 [Power Query (M) 語言](https://msdn.microsoft.com/library/mt807488.aspx)。  接著可以用它在 Power BI Desktop 中建立查詢，然後將它發佈至 Power BI 做為資料集。  以下將詳細說明此流程。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>匯出查詢
首先建立[記錄搜尋](log-analytics-log-search-new.md)，傳回您想要填入 Power BI 資料集的 Log Analytics 資料。  您接著可以將該查詢匯出到可以由 Power BI Desktop 使用的 [Power Query (M) 語言](https://msdn.microsoft.com/library/mt807488.aspx)。

1. 在 Log Analytics 中建立記錄搜尋，擷取您資料集的資料。
2. 如果您使用記錄搜尋入口網站，請按一下 **Power BI**。  如果您使用 Analytics 入口網站，請選取**匯出** > **Power BI 查詢 (M)**。  這兩個選項都會將查詢匯出到稱為 **PowerBIQuery.txt** 的文字檔。 

    ![匯出記錄搜尋](media/log-analytics-powerbi/export-logsearch.png) ![匯出記錄搜尋](media/log-analytics-powerbi/export-analytics.png)

3. 開啟該文字檔，然後複製其中的內容。

### <a name="import-query-into-power-bi-desktop"></a>將查詢匯入至 Power BI Desktop
Power BI Desktop 是桌面應用程式，可讓您建立可發行至 Power BI 的資料集和報表。  您也可以用它來建立查詢，使用從 Log Analytics 匯出的 Power Query 語言。 

1. 請安裝 [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (如果尚未安裝)，然後開啟該應用程式。
2. 選取**取得資料** > **空白查詢**開啟新查詢。  然後選取**進階編輯器**，並在查詢中貼上匯出的檔案內容。 按一下 [完成] 。

    ![Power BI Desktop 查詢](media/log-analytics-powerbi/desktop-new-query.png)

5. 執行查詢並顯示其結果。  可能會提示您提供連接到 Azure 的認證。  
6. 輸入查詢的描述性名稱。  預設值是 **Query1**。 按一下**關閉並套用**，將資料集新增至報表。

    ![Power BI Desktop 名稱](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>發行至 Power BI
您發行至 Power BI 時，會建立資料集和報表。  如果您在 Power BI Desktop 中建立報表，將連同您的資料一起發佈。  如果未建立，則將建立空白報表。  您可以在 Power BI 中修改該報表，也建立依照資料集建立新的報表。

8. 依照資料建立報表。  如果您不熟悉如何進行，請參閱 [Power BI Desktop 文件](https://docs.microsoft.com/power-bi/desktop-report-view)。  您準備將它傳送至 Power BI 時時，請按一下**發行**。  出現提示時，請在 Power BI 帳戶中選取目的地。  除非您有特定的目的地，否則請使用**我的工作區**。

    ![Power BI Desktop 發佈](media/log-analytics-powerbi/desktop-publish.png)

3. 發佈完成時，按一下**在 Power BI 中開啟**，以新的資料集開啟 Power BI。


### <a name="configure-scheduled-refresh"></a>設定排定的重新整理
在 Power BI 中建立的資料集將具有與您先前在 Power BI Desktop 看見的相同資料。  您需要定期重新整理資料集，以便再次執行查詢，並填入 Log Analytics 的最新資料。  

1. 按一下您上傳報表的工作區，並選取**資料集**功能表。 選取新的資料集旁邊的快顯功能表，並選取**設定**。 在**資料來源認證**下，應該會出現認證無效的訊息。  這是因為您尚未提供資料集重新整理其資料時使用的認證。  按一下**編輯認證**，並指定存取 Log Analytics 的認證。

    ![Power BI 排程](media/log-analytics-powerbi/powerbi-schedule.png)

5. 在**排定的重新整理**選項下，開啟**維持資料的最新狀態**。  您可以選擇變更**重新整理頻率**，以及執行重新整理的一個或多個特定時間。

    ![Power BI 重新整理](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>舊版工作區
您使用[舊版 Log Analytics 工作區](log-analytics-powerbi.md)設定 Power BI 時，會建立記錄查詢，以將結果匯出至 Power BI 中的對應資料集。  查詢和匯出會繼續自動依您定義的排程執行，讓資料集與 Log Analytics 收集的最新資料保持一致。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Power BI 排程
「Power BI 排程」  包含將一組資料從 OMS 儲存機制匯出至 Power BI 中對應資料集的記錄搜尋，還有定義這項搜尋執行頻率的排程，以維持資料集為最新狀態。

資料集中的欄位會符合記錄搜尋所傳回記錄的屬性。  如果搜尋傳回不同類型的記錄，則資料集將包含來自每個包含之記錄類型的所有屬性。  

### <a name="connecting-oms-workspace-to-power-bi"></a>將 OMS 工作區連接到 Power BI
您必須使用下列程序將 OMS 工作區連接到 Power BI 帳戶，才能從 Log Analytics 匯出至 Power BI。  

1. 在 OMS 主控台中，按一下 [設定]  圖格。
2. 選取 [帳戶] 。
3. 在 [工作區資訊] 區段中，按一下 [連接到 Power BI 帳戶]。
4. 輸入 Power BI 帳戶的認證。

### <a name="create-a-power-bi-schedule"></a>建立 Power BI 排程
使用下列程序為每個資料集建立 Power BI 排程。

1. 在 OMS 主控台中，按一下 [記錄檔搜尋]  圖格。
2. 輸入新的查詢或選取已儲存的搜尋，傳回您想要匯出至 **Power BI**的資料。  
3. 按一下頁面頂端的 [Power BI] 按鈕，開啟 [Power BI] 對話方塊。
4. 提供下列資料表中的資訊，並按一下 [儲存] 。

| 屬性 | 說明 |
|:--- |:--- |
| 名稱 |當您檢視 Power BI 排程清單時，用來識別排程的名稱。 |
| 已儲存的搜尋 |要執行的記錄搜尋。  您可以選取目前的查詢，或從下拉式清單方塊中選取現有的已儲存搜尋。 |
| 排程 |執行已儲存的搜尋，並匯出至 Power BI 資料集的頻率。  值必須介於 15 分鐘到 24 小時之間。 |
| 資料集名稱 |Power BI 中的資料集名稱。  如果不存在便會建立，如果存在則會更新。 |

### <a name="viewing-and-removing-power-bi-schedules"></a>檢視和移除 Power BI 排程
使用下列程序檢視現有 Power BI 排程的清單。

1. 在 OMS 主控台中，按一下 [設定]  圖格。
2. 選取 [Power BI] 。

除了排程的詳細資訊之外，還會顯示過去一週執行排程的次數，以及上次同步處理的狀態。  如果在同步處理時發生錯誤，您可以按一下連結以執行記錄的記錄搜尋，以及錯誤的詳細資訊。

您可以按一下 [移除資料行] 中的 **X** 來移除排程。  您可以選取 [關閉] 來停用排程。  若要修改排程，您必須移除它，然後再以新的設定重新建立。

![Power BI 排程](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>範例逐步解說
下一節逐步解說建立 Power BI 排程，並使用其資料集來建立簡單報表的範例。  在此範例中，一組電腦的所有效能資料都匯出至 Power BI，然後會建立折線圖以顯示處理器使用率。

#### <a name="create-log-search"></a>建立記錄搜尋
首先我們會建立記錄搜尋，尋找我們想要傳送至資料集的資料。  在此範例中，我們將使用一個查詢，傳回名稱開頭為 *srv*之電腦的所有效能資料。  

![Power BI 排程](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>建立 Power BI 搜尋
我們按一下 [Power BI]  按鈕以開啟 [Power BI] 對話方塊，並提供必要的資訊。  我們想要每小時執行一次此搜尋，並建立名為 *Contoso Perf*的資料集。  由於我們已經開啟會建立我們所要資料的搜尋，因此我們針對 [已儲存的搜尋] 保留預設值「使用目前的搜尋查詢」。

![Power BI 搜尋](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>驗證 Power BI 搜尋
為了驗證我們已正確建立排程，我們會在 OMS 儀表板的 [設定]  圖格中檢視 Power BI 搜尋的清單。  我們稍候幾分鐘的時間，並重新整理此檢視，直到它報告已執行同步處理。  您通常會排定資料集自動重新整理。

![Power BI 搜尋](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>在 Power BI 中驗證資料集
我們在 [powerbi.microsoft.com](http://powerbi.microsoft.com/) 登入我們的帳戶，並捲動至左窗格底部的 [資料集]。  我們可以看到已列出 *Contoso Perf* 資料集，表示我們的匯出已順利執行。

![Power BI 資料集](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>根據資料集建立報表
我們選取 **Contoso Perf** 資料集，然後按一下右邉 [欄位] 窗格中的 [結果]，檢視屬於此資料集的欄位。  為了建立折線圖，顯示每一部電腦的處理器使用率，我們執行下列動作。

1. 選取折線圖視覺效果。
2. 將 **ObjectName** 拖放到 [報表層級篩選] 並勾選 [處理器]。
3. 將 **CounterName** 拖放到 [報表層級篩選] 並勾選 [% 處理器時間]。
4. 將 **CounterValue** 拖放到 [值]。
5. 將 **Computer** 拖放到 [圖例]。
6. 將 **TimeGenerated** 拖放到 [軸]。

我們可以看到會顯示產生的折線圖，以及來自我們資料集的資料。

![Power BI 折線圖](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>儲存報表
我們在畫面頂端按一下 [儲存] 按鈕來儲存報表，並驗證它現在會列在左窗格中的 [報表] 區段中。

![Power BI 報表](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>後續步驟
* 深入了解 [記錄檔搜尋](log-analytics-log-searches.md) ，建置可以匯出至 Power BI 的查詢。
* 深入了解 [Power BI](http://powerbi.microsoft.com)，建置以 Log Analytics 匯出為基礎的視覺效果。
