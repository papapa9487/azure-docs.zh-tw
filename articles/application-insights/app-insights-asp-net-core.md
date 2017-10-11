---
title: "ASP.NET Core 的 Azure Application Insights | Microsoft Docs"
description: "監視 Web 應用程式的可用性、效能和使用方式。"
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: d86495eea467977f6c079de72e2b49a2a1da2b60
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core 的 Application Insights
[Application Insights](app-insights-overview.md) 可讓您監視 Web 應用程式的可用性、效能和使用情況。 當您取得有關應用程式在現實世界的效能和效率的意見反應時，您可以在每個開發生命週期中針對設計方向做出明智的抉擇。

![範例](./media/app-insights-asp-net-core/sample.png)

您需要 [Microsoft Azure](http://azure.com)的訂用帳戶。 使用 Microsoft 帳戶登入，可能是針對 Windows、XBox Live 或其他 Microsoft 雲端服務具備的帳戶。 您的小組可能已有 Azuare 組織訂用帳戶：請洽詢擁有者將您的 Microsoft 帳戶新增至其中。

## <a name="getting-started"></a>開始使用

* 在 Visual Studio [方案總管] 中以滑鼠右鍵按一下專案，然後選取 [設定 Application Insights]，或 [新增] > [Application Insights]。 [深入了解](app-insights-asp-net.md)。
* 如果您沒有看到這些功能表命令，請遵循[手動設定入門指南 (英文)](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)。 如果您的專案是使用 2017 年以前的 Visual Studio 版本建立，您可能需要執行這項操作。

## <a name="using-application-insights"></a>使用 Application Insights
登入 [Microsoft Azure 入口網站](https://portal.azure.com)，選取 [所有資源] 或 [Application Insights]，然後選取您建立的資源以監視應用程式。

在另一個瀏覽器視窗中，使用您的應用程式一段時間。 您會看到資料出現在 Application Insights 圖表。 (您可能需要按一下 [重新整理])。在您的開發過程只會有少量的資料，但是當您發行應用程式並有許多使用者時，這些圖表就會真正活躍起來。 

[概觀] 頁面會顯示關鍵效能圖表：伺服器回應時間、頁面載入時間和失敗的要求計數。 按一下任一圖表以查看更多的圖表和資料。

入口網站中的檢視分成三個主要類別：

* [計量瀏覽器](app-insights-metrics-explorer.md)顯示計量和計數 (如回應時間、失敗率或您使用 [API](app-insights-api-custom-events-metrics.md) 自行建立的計量) 的圖形與表格。 依屬性值篩選和分割資料，以進一步了解您的應用程式和其使用者。
* [搜尋總管](app-insights-diagnostic-search.md)列出個別事件，例如特定要求、例外狀況、記錄檔追蹤或您使用 [API](app-insights-api-custom-events-metrics.md) 自行建立的事件。 在事件中篩選和搜尋，並瀏覽相關事件以調查問題。
* [分析](app-insights-analytics.md) 可讓您在遙測上執行類似 SQL 的查詢，而且這是一個功能強大的分析與診斷工具。

## <a name="alerts"></a>Alerts
* 自動取得[主動診斷警示](app-insights-proactive-diagnostics.md)，告訴您相關的失敗率和其他度量異常的變更。
* 設定 [可用性測試](app-insights-monitor-web-app-availability.md) ，持續從全球各地的位置 測試您的網站，並在有任何測試失敗時立即取得電子郵件。
* 設定 [計量警示](app-insights-monitor-web-app-availability.md) ，以了解計量 (如回應時間或例外狀況率) 是否超出可接受的限制。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>開放原始碼
[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>後續步驟
* [將遙測加入至您的網頁](app-insights-javascript.md) 以監視頁面使用情況和效能。
* [監視相依性](app-insights-asp-net-dependencies.md) ，可查看 REST、SQL 或其他外部資源是否降低您的效能。
* [使用 API](app-insights-api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
* [可用性測試](app-insights-monitor-web-app-availability.md) 可持續從世界各地檢查您的應用程式。 

