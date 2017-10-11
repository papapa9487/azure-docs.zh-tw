---
title: "案例 - 使用 Azure 無伺服器建立客戶深入解析儀表板 | Microsoft Docs"
description: "舉例說明如何使用 Azure Logic Apps 與 Azure Functions 來建置儀表板，以管理客戶的意見反應、社交資料和其他項目。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>使用 Azure Logic Apps 與 Azure Functions 來建立即時的客戶深入解析儀表板

Azure 無伺服器工具提供了強大的功能，可在雲端中快速建置及裝載應用程式，而不必考慮基礎結構。  在此案例中，我們將建立會對客戶的意見反應而觸發的儀表板、使用機器學習服務分析意見反應，並為深入解析發佈來源，例如 Power BI 或 Azure Data Lake。

## <a name="overview-of-the-scenario-and-tools-used"></a>所用案例和工具的概觀

為了實作此解決方案，我們會在 Azure 中利用無伺服器應用程式的兩個重要元件︰[Azure Functions](https://azure.microsoft.com/services/functions/) 和 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)。

Logic Apps 是雲端中的無伺服器工作流程引擎。  它可跨無伺服器元件提供協調流程，而且也會連線到超過 100 個服務和 API。  在此案例中，我們會建立針對客戶的意見反映而觸發的邏輯應用程式。  某些連接器有助於回應客戶的意見反應，這些連接器包括 Outlook.com、Office 365、Survey Monkey、Twitter 和[來自 Web 表單](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/)的 HTTP 要求。  針對下面的工作流程，我們會監視 Twitter 上的主題標籤。

函式可在雲端中提供無伺服器計算。  在此案例中，我們會使用 Azure Functions 來根據一系列的預先定義關鍵字，為客戶所發的推文加上旗標。

整個解決方案可以[建置在 Visual Studio 中](logic-apps-deploy-from-vs.md)，也可[部署為資源範本的一部分](logic-apps-create-deploy-template.md)。  另外，[Channel 9 上](http://aka.ms/logicappsdemo)還有影片來逐步解說此案例。

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>建置會針對客戶資料觸發的邏輯應用程式

在 Visual Studio 或 Azure 入口網站中[建立邏輯應用程式](logic-apps-create-a-logic-app.md)之後︰

1. 針對來自 Twitter 的**新推文**新增觸發程序
2. 設定觸發程序來接聽推文上的關鍵字或主題標籤。

   > [!NOTE]
   > 觸發程序的循環屬性會決定邏輯應用程式檢查輪詢式觸發程序上是否有新項目的頻率

   ![Twitter 觸發程序的範例][1]

此應用程式現在會針對所有新推文引發。  然後，我們可以取得該推文資料，並了解其中所表達的更多情緒。  為此，我們使用 [Azure 辨識服務](https://azure.microsoft.com/services/cognitive-services/)來偵測文字的情緒。

1. 按一下 [新步驟]
1. 選取或搜尋 [文字分析] 連接器
1. 選取 [偵測情緒] 作業
1. 如果出現提示，請提供適用於文字分析服務的有效辨識服務金鑰
1. 新增 [推文文字] 作為要分析的文字。

現在，我們已有推文資料和有關推文的深入解析，因此可能會有其他許多相關的連接器︰
* Power BI - 新增資料列到串流資料集︰在 Power BI 儀表板上檢視即時推文。
* Azure Data Lake - 附加檔案︰將客戶資料新增至要納入分析作業的 Azure Data Lake 資料集。
* SQL - 新增資料列︰將資料儲存在資料庫中，以供日後擷取。
* Slack - 傳送訊息︰針對需要有所行動的負面意見反應在 Slack 通道上發出警示。

Azure Function 也可用來根據資料執行更多的自訂計算。

## <a name="enriching-the-data-with-an-azure-function"></a>使用 Azure Function 來豐富資料

在建立函式之前，我們必須先在 Azure 訂用帳戶中擁有函式應用程式。  您可以[在這裡找到](../azure-functions/functions-create-first-azure-function-azure-portal.md)有關在入口網站中建立 Azure Function 的詳細資料

函式若要能夠直接從邏輯應用程式中呼叫，就必須有 HTTP 繫結觸發程序。  我們建議使用 **HttpTrigger** 範本。

在此案例中，Azure Function 的要求本文會是推文文字。  在函式程式碼中，只需定義關於推文文字是包含關鍵字還是片語的邏輯。  函式本身可視案例所需而保持簡單或複雜。

在函式結束時，只需對邏輯應用程式傳回含有一些資料的回應。  這可能是簡單的布林值 (例如 `containsKeyword`) 或複雜的物件。

![已設定的 Azure Function 步驟][2]

> [!TIP]
> 在存取邏輯應用程式函式中的複雜回應時，請使用剖析 JSON 動作。

函式儲存後，就能新增至先前建立的邏輯應用程式中。  在邏輯應用程式中：

1. 按一下以新增 [新步驟]
1. 選取 [Azure Functions] 連接器
1. 選取要選擇現有函式，並瀏覽至所建立的函式
1. 為**要求本文**傳入**推文文字**

## <a name="running-and-monitoring-the-solution"></a>執行和監控解決方案

在 Logic Apps 中撰寫無伺服器協調流程的優點之一是其擁有豐富的偵錯和監控功能。  任何執行 (目前或過去的) 皆可從 Visual Studio 內、Azure 入口網站或透過 REST API 和 SDK 來檢視。

若要測試邏輯應用程式，其中一個最簡單的方法是使用設計工具中的 [執行] 按鈕。  按一下 [執行] 就會繼續每隔 5 秒輪詢觸發程序一次直到偵測到事件，並在執行進行時提供即時檢視。

先前執行的歷程記錄可在 Azure 入口網站中的 [概觀] 刀鋒視窗上檢視，或使用 Visual Studio Cloud Explorer 來檢視。

## <a name="creating-a-deployment-template-for-automated-deployments"></a>建立部署範本來進行自動化部署

解決方案開發成功後，就能透過 Azure 部署範本加以擷取並部署到世界上的任何 Azure 區域。  這不僅適用於修改參數以獲得此工作流程的不同版本，也適用於在建置和發行管線中整合此解決方案。  關於建立部署範本的詳細資料可以[在此文章中](logic-apps-create-deploy-template.md)找到。

Azure Functions 也可納入部署範本中，因此，您可以將整個解決方案與所有相依性當作單一範本來管理。  函式部署範本的範例可在 [Azure 快速入門範本儲存機制](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic)中找到。

## <a name="next-steps"></a>後續步驟

* [查看 Azure Logic Apps 的其他範例和案例](logic-apps-examples-and-scenarios.md)
* [觀看關於完整建立此解決方案的影片逐步解說](http://aka.ms/logicappsdemo)
* [了解如何在邏輯應用程式內處理和攔截例外狀況](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png