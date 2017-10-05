---
title: "透過 OMS 監視邏輯應用程式執行並取得深入解析 - Azure Logic Apps | Microsoft Docs"
description: "透過 Log Analytics 和 Operations Management Suite (OMS) 監視邏輯應用程式執行，來取得深入解析和更豐富的偵錯詳細資料，以便進行疑難排解和診斷"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.translationtype: HT
ms.sourcegitcommit: 890acae2aebf7684e567b9b49377ca7b6da95245
ms.openlocfilehash: 5afe01e713516277abc3c2dfcccfc6d36cfc70d4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="monitor-and-get-insights-about-logic-app-runs-with-operations-management-suite-oms-and-log-analytics"></a>透過 Operations Management Suite (OMS) 和 Log Analytics 監視邏輯應用程式執行並取得深入解析

如需監視和更豐富的偵錯資訊，您可以在建立邏輯應用程式時，同時開啟 Log Analytics。 Log Analytics 會透過 Operations Management Suite (OMS) 入口網站，為邏輯應用程式執行提供診斷記錄和監視功能。 當您將 Logic Apps 管理解決方案新增至 OMS 時，您會取得邏輯應用程式執行的彙總狀態，以及狀態、執行時間、重新提交狀態和相互關聯識別碼等特定詳細資料。

本主題說明如何開啟 Log Analytics 或在 OMS 中安裝 Logic Apps 管理解決方案，讓您能夠檢視邏輯應用程式執行的執行階段事件和資料。

 > [!TIP]
 > 若要監視現有的 Logic Apps，請遵循下列步驟以[開啟診斷記錄，並將邏輯應用程式執行階段資料傳送至 OMS](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

## <a name="requirements"></a>需求

開始之前，您需要有 OMS 工作區。 了解[如何建立 OMS 工作區](../log-analytics/log-analytics-get-started.md)。 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>建立 Logic Apps 時開啟診斷記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，建立一個邏輯應用程式。 選擇 [新增] > [企業整合] > [邏輯應用程式] > [建立]。

   ![建立邏輯應用程式](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. 在 [建立邏輯應用程式] 頁面上，執行下列工作：

   1. 為您的邏輯應用程式命名並選取您的 Azure 訂用帳戶。 
   2. 建立或選取一個 Azure 資源群組。
   3. 將 [Log Analytics] 設定為 [開啟]。 
   選取您要傳送邏輯應用程式執行相關資料的 OMS 工作區。 
   4. 當您準備就緒時，選擇 [釘選到儀表板] > [建立]。

      ![建立邏輯應用程式](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      完成此步驟之後，Azure 會建立您的邏輯應用程式，此應用程式現在會與您的 OMS 工作區建立關聯。 
      此外，此步驟也會在您的 OMS 工作區中自動安裝 Logic Apps 管理解決方案。

3. 若要在 OMS 中檢視您的邏輯應用程式執行，請[繼續進行這些步驟](#view-logic-app-runs-oms)。

## <a name="install-the-logic-apps-management-solution-in-oms"></a>在 OMS 中安裝 Logic Apps 管理解決方案

如果您在建立邏輯應用程式時已開啟 Log Analytics，請略過此步驟。 您已在 OMS 中安裝 Logic Apps 管理解決方案。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選擇 [更多服務]。 搜尋 "log analytics" 作為您的篩選條件，然後選擇 [Log Analytics]，如下所示：

   ![選擇 [Log Analytics]](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. 在 [Log Analytics] 下，尋找並選取 OMS 工作區。 

   ![選取 OMS 工作區](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. 在 [管理] 下，選擇 [OMS 入口網站]。

   ![選擇 [OMS 入口網站]](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. 在您的 OMS 首頁上，如果出現升級橫幅，請選擇此橫幅，即可先升級您的 OMS 工作區。 然後選擇 [方案庫]。

   ![選擇 [方案庫]](media/logic-apps-monitor-your-logic-apps-oms/solutions-gallery.png)

5. 在 [所有解決方案] 下，找到並選擇 [Logic Apps Management] \(Logic Apps 管理) 解決方案的磚。

   ![選擇 [Logic Apps Management] \(Logic Apps 管理)](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

6. 若要在您的 OMS 工作區中安裝解決方案，請選擇 [新增]。

   ![針對 [Logic Apps Management] \(Logic Apps 管理) 選擇 [新增]](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-oms-workspace"></a>在 OMS 工作區中檢視邏輯應用程式執行

1. 若要檢視邏輯應用程式執行的計數和狀態，請移至 OMS 工作區的概觀頁面。 檢閱 [Logic Apps Management] \(Logic Apps 管理) 磚中的詳細資料。

   ![顯示邏輯應用程式執行計數和狀態的概觀磚](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   > [!Note]
   > 如果出現此升級橫幅而非 [Logic Apps 管理] 圖格，請選擇此橫幅，即可先升級您的 OMS 工作區。
  
   > ![升級「OMS 工作區」](media/logic-apps-monitor-your-logic-apps-oms/oms-upgrade-banner.png)

2. 若要檢視含有邏輯應用程式執行之更多詳細資料的摘要，請選擇 [Logic Apps Management] \(Logic Apps 管理) 磚。

   在這裡，您的邏輯應用程式執行會依名稱或執行狀態分組。

   ![邏輯應用程式執行的狀態摘要](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. 若要檢視特定邏輯應用程式或狀態的執行，請選取邏輯應用程式或狀態的資料列。

   以下範例顯示特定邏輯應用程式的所有執行：

   ![檢視邏輯應用程式或狀態的執行](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   > [!NOTE]
   > [重新提交] 資料行顯示為 [是]，表示執行是重新提交執行的結果。

4. 若要篩選這些結果，您可以執行用戶端和伺服器端篩選。

   * 用戶端篩選：針對每個資料行，選擇您想要的篩選。 
   這裡有一些範例：

     ![資料行篩選範例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * 伺服器端篩選：若要選擇特定時間範圍或限制出現的執行次數，請使用頁面頂端的範圍控制項。 
   根據預設，一次只能出現 1,000 筆記錄。 
   
     ![變更時間範圍](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. 若要檢視特定執行的所有動作及其詳細資料，請選取資料列，這會開啟 [記錄搜尋] 頁面。 

   * 若要以資料表檢視這項資訊，請選擇 [資料表]。
   * 若要變更查詢，您可以編輯搜尋列中的查詢字串。 
   若要獲得更佳的體驗，請選擇 [進階分析]。

     ![檢視邏輯應用程式執行的動作和詳細資料](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     在這裡的 [Azure Log Analytics] 頁面上，您可以更新查詢並檢視資料表的結果。 
     此查詢使用 [Kusto 查詢語言](https://docs.loganalytics.io/docs/Language-Reference)，如果您想要檢視不同的結果，可予以編輯。 

     ![Azure Log Analytics - 查詢檢視](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>後續步驟

* [監視 B2B 訊息](../logic-apps/logic-apps-monitor-b2b-message.md)

