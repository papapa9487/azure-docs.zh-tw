---
title: "監視 Windows 傳統型應用程式的使用情況和效能"
description: "使用 HockeyApp 和 Application Insights 分析 Windows 傳統型應用程式的使用情况和效能。"
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: bwren
ms.openlocfilehash: 9d7e2a390adf10cbf5d88dd0084ce09136987309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>監視 Windows 傳統型應用程式的使用情況和效能


[Azure Application Insights](app-insights-overview.md) 和 [HockeyApp](https://hockeyapp.net) 可讓您監視所部署之應用程式的使用情況和效能。

> [!IMPORTANT]
> 我們建議使用 [HockeyApp](https://hockeyapp.net) 來發佈和監視傳統型應用程式和裝置應用程式。 使用 HockeyApp，您可以管理發佈、即時測試和使用者意見，以及監視使用情況和當機報告。 您也可以 [使用分析匯出及查詢遙測](app-insights-hockeyapp-bridge-app.md)。
> 
> 雖然遙測可以從傳統型應用程式傳送至 Application Insights，但這主要是用來進行偵錯和實驗性用途。
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>若要從 Windows 應用程式將遙測傳送至 Application Insights
1. 在 [Azure 入口網站](https://portal.azure.com)中，建立 [Application Insights 資源](app-insights-create-new-resource.md)。 針對應用程式類型，選擇 ASP.NET 應用程式。
2. 取得檢測金鑰的副本。 在您剛才建立的新資源之 [Essentials] 下拉式清單中尋找金鑰。 
3. 在 Visual Studio 中，編輯應用程式專案的 NuGet 封裝，並新增 Microsoft.ApplicationInsights.WindowsServer。 (或選擇 Microsoft.ApplicationInsights，如果您只想要單純的 API，而不需要標準遙測集合模組。)
4. 在程式碼中設定檢測金鑰︰
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`您的金鑰 `";` 
   
    或在 ApplicationInsights.config 中設定檢測金鑰 (如果您已安裝其中一個標準遙測封裝)︰
   
    `<InstrumentationKey>`*您的金鑰*`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，請確定其在方案總管中的屬性已設定為 [建置動作] = [內容]、[複製到輸出目錄] = [複製] 。
5. [使用 API](app-insights-api-custom-events-metrics.md) 傳送遙測。
6. 執行應用程式，並查看您在 Azure 入口網站中建立的資源內的遙測。

## <a name="telemetry"></a>程式碼範例
```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>後續步驟
* [建立儀表板](app-insights-dashboards.md)
* [診斷搜尋](app-insights-diagnostic-search.md)
* [探索度量](app-insights-metrics-explorer.md)
* [撰寫分析查詢](app-insights-analytics.md)

