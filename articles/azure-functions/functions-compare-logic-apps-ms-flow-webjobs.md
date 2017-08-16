---
title: "在 Flow、Logic Apps、Functions 和 WebJobs 之間做選擇 | Microsoft Docs"
description: "比較和對照 Microsoft 的雲端整合服務，並決定您應該使用哪一項服務。"
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow, 流程, logic apps, azure functions, 函數, azure webjobs, webjobs, 事件處理, 動態計算, 無伺服器架構"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2017
ms.author: chrande; glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 256bc04684ba337fe8a403d977079c96b63ca61d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>在 Flow、Logic Apps、Functions 和 WebJobs 之間做選擇
本文會比較和對照 Microsoft Cloud 中的下列服務，這些服務全都可以解決商務程序的整合問題和自動化︰

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service-web/web-sites-create-web-jobs.md)

這些服務和不同系統「結合」在一起時全都會變得很有用。 它們全都可以定義輸入、動作、條件和輸出。 您可以在排程或觸發程序上執行上述各項服務。 不過，每項服務都會新增一組唯一值，比較這些服務不是「哪一項服務最好？」的問題， 而是「哪一項服務最適合這種情況？」的問題。 這些服務的組合通常是快速建置可調整的全功能整合方案的最佳方式。

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow 與 邏輯應用程式
我們可以將 Microsoft Flow 和 Azure Logic Apps 放在一起討論，因為兩者都是「組態優先」  的整合服務，因此能夠輕鬆地建置處理程序和工作流程，並與各種 SaaS 和企業應用程式整合。 

* Flow 是以 Logic Apps 為基礎所建置
* 它們擁有相同的工作流程設計工具
* [連接器](../connectors/apis-list.md) 也可在另一項服務中運作

Flow 可讓任何辦公室工作人員有能力執行簡單的整合 (例如取得重要電子郵件的簡訊)，而不必透過開發人員或 IT。 另一方面，Logic Apps 則可以實現需要企業級 DevOps 和安全性作法的進階或關鍵任務整合 (例如 B2B 處理程序)。 一般來說，商務工作流程會隨著時間而趨於複雜。 因此，一開始您可以先從流程著手，然後再視需要將它轉換為邏輯應用程式。

下表可協助您判斷最適合所給定整合的是 Flow 還是 Logic Apps。

|  | Flow | Logic Apps |
| --- | --- | --- |
| 觀眾 |辦公室工作人員、商務使用者 |IT 專家、開發人員 |
| 案例 |自助服務 |關鍵任務 |
| 設計工具 |瀏覽器內及行動裝置應用程式，僅限 UI |有瀏覽器內和 [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)、[程式碼檢視](../logic-apps/logic-apps-author-definitions.md)可用 |
| DevOps |特定、在生產環境中開發 |在 [Azure 資源管理](../logic-apps/logic-apps-arm-provision.md) |
| 管理員體驗 |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| 安全性 |標準做法︰[資料主權](https://wikipedia.org/wiki/Technological_Sovereignty)、敏感資料的[待用加密](https://wikipedia.org/wiki/Data_at_rest#Encryption)等。 |Azure 的安全性保證︰[Azure 安全性](https://www.microsoft.com/trustcenter/Security/AzureSecurity)、[資訊安全中心](https://azure.microsoft.com/services/security-center/)、[稽核記錄檔](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)等。 |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions 與 WebJobs
我們可以將 Azure Functions 和 Azure App Service WebJobs 放在一起討論，因為兩者都是針對開發人員所設計的「程式碼優先」  整合服務。 它們可讓您執行指令碼或一段程式碼以回應各種事件，例如[新的儲存體 Blob](functions-bindings-storage.md) 或 [WebHook 要求](functions-bindings-http-webhook.md)。 以下是其相似之處︰ 

* 兩者都是以 [Azure App Service](../app-service/app-service-value-prop-what-is.md) 為基礎所建置，並享有[原始檔控制](../app-service-web/app-service-continuous-deployment.md)、[驗證](../app-service/app-service-authentication-overview.md)和[監視](../app-service-web/web-sites-monitor.md)之類的功能。
* 兩者都是以開發人員為主的服務。
* 兩者皆支援標準的指令碼和程式設計語言。
* 兩者都有 NuGet 和 NPM 支援。

Functions 是 WebJobs 的自然進化，因為它採用有關 WebJobs 的最佳功能並加以改善。 其改善項目包括︰ 

* 簡化程式碼的開發、測試和執行，在瀏覽器中就可直接進行。
* 與其他 Azure 服務和第三方服務 (例如 [GitHub Webhook](https://developer.github.com/webhooks/creating/)) 內建整合。
* 按使用次數付費，不需要支付 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。
* 自動的 [動態調整](functions-scale.md)。
* 現有 App Service 客戶仍可在 App Service 方案上執行 (以利用使用量過低的資源)。
* 與 Logic Apps 整合。

下表摘要說明 Functions 和 WebJobs 之間的差異︰

|  | Functions | WebJobs |
| --- | --- | --- |
| 調整大小 |無組態調整 |隨著 App Service 方案調整 |
| 價格 |按使用次數付費或屬於 App Service 方案的一部分 |屬於 App Service 方案的一部分 |
| 執行類型 |觸發、排程 (依計時器觸發程序) |觸發、連續、排程 |
| 觸發程序事件 |[計時器](functions-bindings-timer.md)、[Azure Cosmos DB](functions-bindings-documentdb.md)、[Azure 事件中樞](functions-bindings-event-hubs.md)、[HTTP/WebHook (GitHub、Slack)](functions-bindings-http-webhook.md)、[Azure App Service Mobile Apps](functions-bindings-mobile-apps.md)、[Azure 通知中樞](functions-bindings-notification-hubs.md)、[Azure 服務匯流排](functions-bindings-service-bus.md)、[Azure 儲存體](functions-bindings-storage.md) |[Azure 儲存體](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)、[Azure 服務匯流排](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| 瀏覽器中開發 |支援 | 不支援 |
| 視窗指令碼 |實驗性 |支援 |
| PowerShell |實驗性 |支援 |
| C# |支援 |支援 |
| F# |支援 |不支援 |
| Bash |實驗性 |支援 |
| PHP |實驗性 |支援 |
| Python |實驗性 |支援 |
| JavaScript |支援 |支援 |

要使用 Functions 還是 WebJobs 最終取決於您已使用 App Service 做了什麼。 如果您有要為其執行程式碼片段的 App Service 應用程式，而且想要在相同的 DevOps 環境中一起管理，您應該使用 WebJobs。 如果您想要為其他 Azure 服務或甚至是第三方應用程式執行程式碼片段、如果您想要分開管理整合程式碼片段與 App Service 應用程式，或如果您想要從邏輯應用程式呼叫程式碼片段，您應該利用 Functions 中的所有改善項目。  

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow、Logic Apps 和 Functions 一起
如先前所述，哪一項服務最適合您取決於您的情況。 

* 若為簡單的商務最佳化，則使用 Flow。
* 如果您的整合案例對於 Flow 來說過於先進，或是您需要 DevOps 功能和安全性與法規遵循，則請使用 Logic Apps。
* 如果整合案例中的步驟需要高度自訂的轉換或專門的程式碼，則請撰寫函數應用程式，然後在邏輯應用程式中觸發函數做為動作。

您可以在流程中呼叫邏輯應用程式。 您也可以在邏輯應用程式中呼叫函數，也可以在函數中呼叫邏輯應用程式。 Flow、Logic Apps 和 Functions 之間的整合會隨時間持續改進。 您可以在某項服務中建置某物並用於其他服務。 因此，您對這三種技術所做的投資都是值得的。

## <a name="next-steps"></a>後續步驟
建立您的第一個流程、邏輯應用程式、函數應用程式或 WebJob 來開始使用每一項服務。 按一下下列任何連結︰

* [開始使用 Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)
* [使用 Visual Studio 部署 WebJob](../app-service-web/websites-dotnet-deploy-webjobs.md)

或者，透過下列連結取得有關這些整合服務的詳細資訊︰

* [利用 Azure Functions 和 Azure App Service 來進行整合案例 - 主講人：Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [整合變得簡單，主講人：Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps 即時網路廣播](http://aka.ms/logicappslive)
* [Microsoft Flow 常見問題集](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Azure WebJobs 文件資源](../app-service-web/websites-webjobs-resources.md)


