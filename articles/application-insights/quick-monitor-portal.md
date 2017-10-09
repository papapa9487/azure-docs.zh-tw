---
title: "使用 Azure Application Insights 監視 ASP.NET Web 應用程式 | Microsoft Docs"
description: "提供指示說明如何快速設定 ASP.NET Web 應用程式，以透過 Application Insights 來監視"
services: application-insights
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/14/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 5c7e4f876165752824dab2ab8d0c7ecd44864710
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---

# <a name="start-monitoring-your-aspnet-web-application"></a>開始監視 ASP.NET Web 應用程式

利用 Azure Application Insights，您可以輕鬆監視 Web 應用程式的可用性、效能和使用情形。  還可讓您快速識別並診斷應用程式的錯誤，不必等使用者回報。  使用從 Application Insights 收集有關您的應用程式效能和效率的相關資訊，您可以進行謹慎的選擇來維護和改善您的應用程式。

本快速入門示範如何將 Application Insights 新增至現有的 ASP.NET Web 應用程式，並開始分析即時統計資料，而這只是您可用來分析應用程式的多種方法的其中一個。 如果您沒有 ASP.NET Web 應用程式，您可以遵循[建立 ASP.NET Web 應用程式快速入門](../app-service/app-service-web-get-started-dotnet.md)來建立一個。

## <a name="prerequisites"></a>必要條件
若要完成本快速入門：

- 使用下列工作負載安裝 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 開發
    - Azure 開發


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="enable-application-insights"></a>啟用 Application Insights

1. 在 Visual Studio 2017 中開啟您的專案。
2. 從 [專案] 功能表選取 [設定 Application Insights]。 Visual Studio 會將 Application Insights SDK 新增至您的應用程式。
3. 按一下 [免費開始]，選取您慣用的計費方案，然後按一下 [註冊]。

    ![將 Application Insights 新增至 Visual Studio](./media/quick-monitor-portal/add-application-insights.png)

4. 從 [偵錯] 功能表選取 [開始偵錯]或按 F5 鍵來執行您的應用程式。

## <a name="confirm-app-configuration"></a>確認應用程式設定

Application Insights 會為您的應用程式收集遙測資料，不論其執行所在位置為何。 請使用下列步驟來開始檢視此資料。

1. 按一下 [專案] -> [Application Insights] -> [搜尋偵錯工作階段遙測] 來開啟 Application Insights。  您會從您目前的工作階段查看遙測。<BR><br>![Visual Studio 中的遙測](./media/quick-monitor-portal/telemetry-in-vs.png)

2. 按一下清單中的第一個要求 (此範例中的取得首頁/索引) 來查看要求詳細資料。 請注意，狀態碼和回應時間會隨著有關要求的其他重要資訊併入。<br><br>![Visual Studio 中的回應詳細資料](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中開始監視

您現在可以在 Azure 入口網站中開啟 Application Insights 以檢視有關執行中應用程式的各種詳細資料。

1. 在方案總管中以滑鼠右鍵按一下 [連線的服務 Application Insights] 資料夾，然後按一下 [開啟 Application Insights 入口網站]。  您可以看到有關您的應用程式的某些資訊和各種選項。

    ![應用程式對應](media/quick-monitor-portal/001.png)

2. 按一下 [應用程式對應]，來取得應用程式元件之間相依性關聯性的視覺化配置。  每個元件會顯示負載、效能、失敗和警示等 KPI。

    ![應用程式對應](media/quick-monitor-portal/application-map.png)

3. 按一下其中一個應用程式元件的 [應用程式對應]![](media/quick-monitor-portal/app-analytics-icon.png) 上的 [應用程式分析] 圖示。  這樣會開啟 **Application Insights Analytics**，它提供一種豐富查詢語言，可用於分析 Application Insights 收集的所有資料。  此案例中會為您產生查詢，可將要求計數以圖表呈現。  您可以撰寫自己的查詢來分析其他資料。

    ![Analytics](media/quick-monitor-portal/analytics.png)

4. 返回 [概觀] 頁面，然後按一下 [即時資料流]。  這會顯示有關您的應用程式的即時統計資料，因為它在執行中。  這包含連入要求數量、這些要求的持續時間及發生的任何失敗之類的資訊。  您也可以檢查重要效能計量，例如處理器和記憶體。

    ![即時資料流](media/quick-monitor-portal/live-stream.png)

如果準備好要在 Azure 中裝載您的應用程式，您現在可以將它發佈。 請遵循[建立 ASP.NET Web 應用程式快速入門](../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy)中所述的步驟。

## <a name="next-steps"></a>後續步驟
在這個快速入門中，您已啟用您的應用程式，供 Azure Application Insights 進行監視。  繼續進行教學課程，以了解如何使用它來監視統計資料和偵測應用程式中的問題。

> [!div class="nextstepaction"]
> [Azure Application Insights 教學課程](app-insights-tutorial-runtime-exceptions.md)
