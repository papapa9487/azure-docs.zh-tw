---
title: "Azure Application Insights 中的 Web 應用程式效能變更智慧型診斷 | Microsoft Docs"
description: "自動診斷您 Web 應用程式之效能遙測資料中的突然增加或段差情況。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: e0c8d712f03da0c5e47ea422b051c0b8734c6b21
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>診斷您應用程式遙測資料中的突發變更

*這項功能處於預覽狀態。*

只要按一下，即可診斷您 Web 應用程式的效能或使用情況上的突發變更！ 每當您在 [Application Insights](app-insights-overview.md) 的[分析](app-insights-analytics.md)中建立時間圖表時，都可以使用「智慧型診斷」功能。 每當您的結果趨勢有異常的變更 (例如突然增加或減少的情況) 時，「智慧型診斷」都會識別出可能解釋該變更原因之維度及相關值的模式。 這可協助您快速診斷問題。 

在此範例中，「智慧型診斷」已識別出與變更相關之屬性值的模式，並醒目提示具有該模式的結果與沒有該模式的結果之間的差異：

![範例分析診斷結果](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>診斷資料變更

1.  在 [分析] 中執行查詢，並將它轉譯成時間圖表。 
2.  按一下任何醒目提示的尖峰點 (如果有的話)。
 
    ![尖峰點](./media/app-insights-analytics-diagnostics/peak.png)

    診斷需要幾秒鐘的時間來探索模式。

3. [診斷結果] 索引標籤會顯示可能解釋您資料中斷原因的模式。

    ![結果](./media/app-insights-analytics-diagnostics/result.png)
 
    文字會顯示看似與該變動相互關聯的維度值。 在此範例中，它是與特定的要求和特定的瀏覽器版本關聯。

    另請注意，具有 True 和 False 篩選條件的兩個圖表元件。 False 元件顯示一個未變更的趨勢。 換句話說，如果我們將「診斷」已識別的問題維度組合排除，遙測結果中便沒有任何變更。 對照之下，該組合內的結果則在醒目提示的調查區域內顯示劇烈的變更。 這表示「診斷」已找到可解釋該變更原因的屬性組合。

4.  如果模式相當複雜，您將需要把滑鼠指標停在 [全部顯示] 上來查看維度。

    ![全部顯示](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  如果「診斷」找不到任何重要的模式來通知，就會顯示 [沒有結果] 頁面。 此時，您可以變更您的查詢。 例如，您可以將 [分析] 查詢中的時間範圍和量化縮小，以供日後分析及可能獲得較佳的結果。

在知道您網站的特定頁面於特定瀏覽器上會發生問題之後，您現在可以直接移至問題頁面並調查最近的變更。

## <a name="try-the-demo"></a>試用示範

[按一下這裡以查看範例資料的相關示範](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H)。

## <a name="how-it-works"></a>運作方式

「智慧型診斷」使用以 [DiffPatterns](app-insights-analytics-reference.md) 作業為基礎、不受監督的進階機器學習演算法。 它會尋找可能解釋資料變更原因的候選模式。 它會分析每個候選項目對計量的影響，並顯示與該變更最具相互關聯性的模式。

## <a name="no-diagnostic-points"></a>沒有任何診斷點？

只有在滿足下列條件的情況下，「智慧型診斷」才會運作：

 * 已開啟 [智慧型診斷] 設定。 請查看 [分析] 中的 [設定] 圖示底下。
 * 已選取 [分析] 設定中的 [智慧型診斷] 選項。 
 * 時間軸：圖表 X 軸的類型必須是 `datetime`。
 * 折線圖或區域圖：診斷只有在這些類型的圖表才能運作。 請在您的查詢結尾使用 `| render timechart` 或 `| render areachart`；或從下拉式清單選取器中選取 [折線圖] 或 [區域圖]。
 * 中斷：資料中必須有明顯的中斷。
 * 有足夠的點可供分析。
 * 查詢中的 summarize 子句不超過一個。
 * summarize 子句前面沒有任何包含名稱定義的 project 子句。

 
 ## <a name="related-articles"></a>相關文章

 * [Analytics 教學課程](app-insights-analytics-tour.md)
 * [智慧型偵測](app-insights-proactive-diagnostics.md)會自動向您發出效能問題警示。