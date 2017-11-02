---
title: "從 Application Insights 匯出至 Power BI | Microsoft Docs"
description: "Analytics 查詢可以在 Power BI 中顯示。"
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: fe708b14fac971d18d95fd1619907023ec35af89
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="feed-power-bi-from-application-insights"></a>從 Application Insights 提供 Power BI
[Power BI](http://www.powerbi.com/) 是一套商務分析工具，可協助您分析資料及分享見解。 每個裝置上都提供豐富的儀表板。 您可以結合許多來源的資料，包含來自 [Azure Application Insights](app-insights-overview.md) 的「分析」查詢。

有三種建議方法可將 Application Insights 資料匯出至 Power BI。 您可以單獨或一起使用這些方法。

* [**Power BI 配接器**](#power-pi-adapter) - 從您的應用程式設定完整的遙測儀表板。 儀表板中已預先定義一組圖表，但您可以從任何其他來源新增您自己的查詢。
* [**匯出 Analytics 查詢**](#export-analytics-queries) - 使用 Analytics 撰寫任何您想要的查詢，並將它匯出至 Power BI。 您可以將此查詢和任何其他資料一起放在儀表板上。
* [**連續匯出和串流分析**](app-insights-export-stream-analytics.md) - 這牽涉到更多設定工作。 如果您想要長期保留資料，此方法很有用。 否則，建議您使用其他方法。

## <a name="power-bi-adapter"></a>Power BI 配接器
這個方法會為您建立完整的遙測儀表板。 儀表板中已預先定義初始資料集，但您也可以在資料集中新增更多資料。

### <a name="get-the-adapter"></a>取得配接器
1. 登入 [Power BI](https://app.powerbi.com/)。
2. 依序開啟 [取得資料]、[服務] 和 [Application Insights]
   
    ![取自 Application Insights 資料來源](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. 提供 Application Insights 資源的詳細資料。
   
    ![取自 Application Insights 資料來源](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. 等候一兩分鐘來匯入資料。
   
    ![Power BI 配接器](./media/app-insights-export-power-bi/010.png)

您可以編輯儀表板，將 Application Insights 圖表與其他來源的圖表以及 Analytics 查詢相結合。 還有一個視覺效果資源庫，您可以從中取得更多圖表，且每個圖表都有您可以設定的參數。

初始匯入之後，儀表板和報告會持續每日更新。 您可以控制資料集上的重新整理排程。

## <a name="export-analytics-queries"></a>匯出 Analytics 查詢
此途徑可讓您撰寫您喜歡的任何 Analytics 查詢，然後將其匯出至 Power BI 儀表板  (您可以新增至配接器所建立的儀表板)。

### <a name="one-time-install-power-bi-desktop"></a>一次︰安裝 Power BI Desktop
若要匯入您的 Application Insights 查詢，您可以使用桌面版本的 Power BI。 但是，您接著可以將它發佈至 Web 或您的 Power BI 雲端工作區。 

安裝 [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)。

### <a name="export-an-analytics-query"></a>匯出 Analytics 查詢
1. [開啟 Analytics 並撰寫查詢](app-insights-analytics-tour.md)。
2. 測試並精簡查詢，直到您滿意結果。

   **請先確定查詢在「分析」中正確執行，再將它匯出。**
3. 在 [匯出] 功能表上，選擇 [Power BI (M)]。 儲存文字檔案。
   
    ![匯出 Power BI 查詢](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. 在 Power BI Desktop 中選取 [取得資料]、[空白查詢]，然後在查詢編輯器中，於 [檢視] 底下選取 [進階查詢編輯器]。

    將匯出的 M 語言指令碼貼到進階查詢編輯器。

    ![進階查詢編輯器](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. 您可能必須提供認證，才能讓 Power BI 得以存取 Azure。 使用「組織帳戶」來以 Microsoft 帳戶進行登入。
   
    ![提供 Azure 認證，讓 Power BI 能夠執行您的 Application Insights 查詢](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (如果您需要驗證認證，請使用「查詢編輯器」中的 [資料來源設定] 功能表命令。 指定用於 Azure 的認證時請小心，此認證有可能與您用於 Power BI 的認證不同)。
2. 選擇查詢的視覺效果，然後選取 x 軸、y 軸與分割維度的欄位。
   
    ![選取視覺效果](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. 將報表發佈至 Power BI 雲端工作區。 您可以從該處將同步版本內嵌至其他網頁。
   
    ![選取視覺效果](./media/app-insights-export-power-bi/publish-power-bi.png)
4. 不時手動重新整理報表，或在選項頁面上設定排定的重新整理。

## <a name="troubleshooting"></a>疑難排解

### <a name="401-or-403-unauthorized"></a>401 或 403 未經授權 
如果您的重新整理權杖尚未更新，可能會發生這種情況。 請嘗試下列步驟以確保您仍然具有存取權。 如果您沒有存取權且重新整理認證沒有作用，請開啟支援票證。

1. 登入 Azure 入口網站，並確定您可以存取資源
2. 嘗試重新整理儀表板的認證

### <a name="502-bad-gateway"></a>502 錯誤的閘道
這通常是傳回太多資料的分析查詢所導致。 您應該嘗試使用較小的時間範圍，或使用 [ago](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) 或 [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek)僅限函式[專案](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator)需要的欄位。

如果減少來自分析查詢的資料集不符合您的需求，您應該考慮使用 [API](https://dev.applicationinsights.io/documentation/overview) 以提取較大的資料集。 以下是如何將 M-Query 匯出轉換為使用 API 的指示。

1. 建立 [API 金鑰](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)
2. 更新您從分析匯出的 Power BI M 指令碼，方法是將 ARM URL 取代為 AI API (請參閱以下範例)
   * 將 **https://management.azure.com/subscriptions/...**
   * 取代為 **https://api.applicationinsights.io/beta/apps/...**
3. 最後，將認證更新為基本，並且使用您的 API 金鑰
  

**現有的指令碼**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**更新的指令碼**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>關於取樣
如果應用程式會傳送大量資料，調適性取樣功能或許會運作，並只傳送一定百分比的遙測。 如果您已經在 SDK 中或在擷取上手動設定取樣，也是如此。 [深入了解取樣。](app-insights-sampling.md)


## <a name="next-steps"></a>後續步驟
* [Power BI - 了解](http://www.powerbi.com/learning/)
* [Analytics 教學課程](app-insights-analytics-tour.md)

