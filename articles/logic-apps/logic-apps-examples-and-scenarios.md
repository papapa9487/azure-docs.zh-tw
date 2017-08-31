---
title: "範例和常見案例 - Azure Logic Apps | Microsoft Docs"
description: "透過範例、案例和教學課程深入了解 Logic Apps"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/9/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 50df1e3db239a6aa34ac91bfbd582625c5b0041b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="examples-and-common-scenarios-for-azure-logic-apps"></a>Azure Logic Apps 的範例和常見案例

為了協助您深入了解 Azure Logic Apps 中的眾多模式和功能，以下提供了常見的範例與案例。

## <a name="key-scenarios-for-logic-apps"></a>邏輯應用程式的重要案例

Azure Logic Apps 針對不同的服務提供了彈性的協調流程和整合功能。 Logic Apps 服務是「無伺服器」服務，因此您不必擔心調整或執行個體；您需要做的就只是定義工作流程 (觸發程序和動作)。 基礎平台可處理調整、可用性和效能。 只要是需要協調多個動作的案例 (尤其是跨多個系統時)，都是 Azure Logic Apps 的絕佳使用案例。 以下有一些模式和範例。

## <a name="respond-to-triggers-and-extend-actions"></a>回應觸發程序並延伸動作

每個邏輯應用程式都會從觸發程序來開始。 例如，您的工作流程可以從排程事件、手動叫用或外部系統的事件 (例如「當檔案新增至 FTP 伺服器時」觸發程序) 來開始。 Azure Logic Apps 目前支援超過 100 個立即可用的連接器，範圍從內部部署 SAP 到 Microsoft 辨識服務。 對於可能沒有已發佈連接器的系統和服務，您也可以延伸邏輯應用程式。

* [建立自訂觸發程序或動作](../logic-apps/logic-apps-create-api-app.md)
* [為工作流程執行設定長時間執行的動作](../logic-apps/logic-apps-create-api-app.md)
* [使用 Webhook 回應外部事件和動作](../logic-apps/logic-apps-create-api-app.md)
* [呼叫、觸發或巢狀處理具有 HTTP 要求同步回應的工作流程](../logic-apps/logic-apps-http-endpoint.md)
* [教學課程︰回應 Twilio SMS Webhook 並傳送文字回應](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)
* [教學課程︰使用 Logic Apps 和 Power BI 在幾分鐘內建置具有 AI 技術的社交儀表板](http://aka.ms/logicappsdemo)

## <a name="error-handling-logging-and-control-flow-capabilities"></a>錯誤處理、記錄和控制流程功能

邏輯應用程式包含豐富的功能，可用於處理進階控制流程，例如條件、開關、迴圈和範圍。 若要確保解決方案的彈性，您也可以在工作流程中實作錯誤和例外狀況處理。 針對工作流程執行狀態的通知和診斷記錄，Azure Logic Apps 也提供了監視和警示。

* [使用 switch 陳述式來執行不同動作](../logic-apps/logic-apps-switch-case.md)
* [在邏輯應用程式中使用迴圈和批次處理陣列和集合中的項目](../logic-apps/logic-apps-loops-and-scopes.md)
* [在工作流程中撰寫錯誤和例外狀況處理](../logic-apps/logic-apps-exception-handling.md)
* [開啟現有 Logic Apps 的監視、記錄和警示](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [建立 Logic Apps 時開啟監視和診斷記錄](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)
* [使用案例︰醫療保健公司如何使用邏輯應用程式的例外狀況處理來處理 HL7 FHIR 工作流程](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="deploy-and-manage-logic-apps"></a>部署和管理邏輯應用程式

您可以完全透過 Visual Studio、Visual Studio Team Services 或其他任何原始檔控制和自動化建置工具，來開發及部署邏輯應用程式。 為了支援將工作流程與相依連線部署在資源範本中的功能，邏輯應用程式使用 Azure 資源部署範本。 Visual Studio 工具會自動產生這些範本，您可以將其簽入原始檔控制以便控制版本。

* [建立自動部署範本](../logic-apps/logic-apps-create-deploy-template.md)
* [在 Visual Studio 中建置和部署 Logic Apps](../logic-apps/logic-apps-deploy-from-vs.md)
* [監視邏輯應用程式的健康狀態](../logic-apps/logic-apps-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>執行內的內容類型、轉換 (Conversion) 及轉換 (Transformation)

您可以使用 Azure Logic Apps [工作流程定義語言](http://aka.ms/logicappsdocs)中的許多函數，來存取、轉換 (Convert) 及轉換 (Transform) 多種內容類型。 例如，您可以使用 `@json()` 和 `@xml()` 工作流程運算式，在字串、JSON 和 XML 之間進行轉換 (Convert)。 Logic Apps 引擎會保留內容類型，以支援在服務之間以不失真的方式進行內容傳輸的功能。

* [處理非 JSON 內容類型](../logic-apps/logic-apps-content-type.md)，例如 `application/xml`、`application/octet-stream` 和 `multipart/formdata`
* [工作流程運算式在邏輯應用程式中的運作方式](../logic-apps/logic-apps-author-definitions.md)
* [參考：Azure Logic Apps 工作流程定義語言](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>其他整合和功能

邏輯應用程式也提供與許多服務 (例如 Azure Functions、Azure API 管理、Azure App Service) 和自訂 HTTP 端點 (例如 REST 和 SOAP) 的整合。

* [使用 Azure 無伺服器建立即時社交儀表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [從邏輯應用程式呼叫 Azure Functions](../logic-apps/logic-apps-azure-functions.md)
* [案例：使用 Azure Functions 觸發邏輯應用程式](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [部落格︰從邏輯應用程式呼叫 SOAP 端點](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端對端案例

* [白皮書：使用 Azure 服務 (例如 Logic Apps) 進行企業整合端對端案例管理](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="next-steps"></a>後續步驟

- [處理邏輯應用程式中的錯誤和例外狀況](../logic-apps/logic-apps-exception-handling.md)
- [使用工作流程定義語言撰寫工作流程定義](../logic-apps/logic-apps-author-definitions.md)
- [提交有關我們該如何改善 Azure Logic Apps 的評論、問題、意見或建議](https://feedback.azure.com/forums/287593-logic-apps)
