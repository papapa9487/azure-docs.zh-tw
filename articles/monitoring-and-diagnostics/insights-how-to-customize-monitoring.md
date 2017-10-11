---
title: "Microsoft Azure 中的計量概觀 | Microsoft Docs"
description: "了解如何在 Azure 自訂監視圖表。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.openlocfilehash: 3f9ebb0f5737714dd685f0dcc1ff4b1c0c89528f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure 中的計量概觀
所有的 Azure 服務都會追蹤重要計量，可讓您監視服務的健全狀況、效能、可用性和使用情況。 您可以在 Azure 入口網站中檢視這些計量，而且您也可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) 或 [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) 以程式設計方式存取完整的計量集合。

在某些服務中，您可能需要開啟診斷，才能查看任何計量。 在其他服務中 (例如虛擬機器)，您將會取得基本的計量集合，但需要啟用完整的高頻率計量集合。 若要深入了解，請參閱 [啟用監視和診斷](insights-how-to-use-diagnostics.md) 。

## <a name="using-monitoring-charts"></a>使用監視圖表
您可以製作任何選擇時間期間上的計量圖表。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [ **瀏覽**]，然後按一下您有興趣監視的資源。
2. [ **監視** ] 區段中含有每個 Azure 資源最重要的計量。 例如，Web 應用程式具有**要求和錯誤**，而虛擬機器則具有 **CPU 百分比**和**磁碟讀取和寫入**：![監視透鏡](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. 按一下任何一個圖表，即會顯示 [ **計量** ] 刀鋒視窗。 在此刀鋒視窗上，除了一個圖形之外，還會有一個顯示這些計量彙總 (例如選擇時間範圍上的平均值、最小值和最大值) 的資料表。 下面是資源的警示規則。
    ![計量刀鋒視窗](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. 若要自訂出現的資料行，請按一下圖表上的 [編輯] 按鈕，或按一下 [計量] 刀鋒視窗上的 [編輯圖表] 命令。
5. 在 [編輯查詢] 刀鋒視窗上，您可以執行三個動作：
   
   * 變更時間範圍
   * 在長條圖與折線圖之間切換外觀
   * 選擇不同的計量 ![編輯查詢](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. 變更時間範圍十分容易，只要選取不同的範圍 (例如 [Past Hour]，再按一下刀鋒視窗底部的 [儲存] 即可。 您也可以選擇 [自訂]，這可讓您選擇過去 2 週的任何一段時間。 例如，您可以完整檢視這兩週，或僅檢視昨天的某 1 小時。 請在文字方塊中輸入不同的小時。
    ![自訂時間範圍](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. 在時間範圍下，您可以選擇任何要顯示在圖表上的度量數。
8. 當您按一下 [儲存] 時，系統便會儲存為該特定資源所做的變更。 例如，如果您有兩部虛擬機器，若變更其中一部虛擬機器的圖表，它將不會影響到另一部虛擬機器的圖表。

## <a name="creating-side-by-side-charts"></a>建立並排圖表
使用入口網站中的強大自訂功能，您可以新增任意數目的圖表。

1. 在刀鋒視窗頂端的 [...] 功能表中，按一下 [新增圖格]：  
    ![新增功能表](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 然後，您可以從右側螢幕上的 [資源庫] 中選取圖表：![資源庫](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 如果您沒有看到所需的計量，您可以隨時新增其中一個預設計量，並 **編輯** 圖表以顯示所需的計量。

## <a name="monitoring-usage-quotas"></a>監視使用量配額
大部分的計量會顯示某一段時間的趨勢，但是某些特定資料 (如使用量配額) 則會是包含臨界值的時間點資訊。

您也可以在刀鋒視窗上查看具有配額之資源的使用量配額：

![使用量](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

和計量一樣，您可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) 或 [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) 以程式設計方式存取完整的使用量配額集合。

## <a name="next-steps"></a>後續步驟
* [接收警示通知](insights-receive-alert-notifications.md) 。
* [啟用監視和診斷](insights-how-to-use-diagnostics.md) 來在您服務中收集詳細的高頻率計量。
* [自動調整執行個體計數](insights-how-to-scale.md) 以確保您的服務可用且可迅速回應。
* [監視應用程式效能](../application-insights/app-insights-azure-web-apps.md) 。
* 使用 [JavaScript 應用程式和網頁適用的 Application Insights](../application-insights/app-insights-web-track-usage.md) ，以取得有關造訪網頁瀏覽器的用戶端分析。
* [監視任何網頁的可用性和回應性](../application-insights/app-insights-monitor-web-app-availability.md) ，讓您可以找出您的頁面是否關閉。

