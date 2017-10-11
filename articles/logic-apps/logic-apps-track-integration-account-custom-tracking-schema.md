---
title: "適用於 B2B 監視的自訂追蹤結構描述 - Azure Logic Apps | Microsoft Docs"
description: "建立自訂追蹤結構描述以監視來自 Azure 整合帳戶中交易的 B2B 訊息。"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b71a4938dde2a71f1ce29403af7aa9101358d64c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>啟用追蹤來監視您的完整工作流程，端對端
有內建的追蹤可供您啟用您不同部分的企業對企業工作流程，例如追蹤 AS2 或 X12 訊息。 當您建立包含邏輯應用程式、BizTalk Server、SQL Server 或任何其他層級的工作流程時，您可以啟用自訂追蹤，記錄從您工作流程開始到結尾的事件。 

本主題提供您可以在邏輯應用程式外部層級中使用的自訂程式碼。 

## <a name="custom-tracking-schema"></a>自訂追蹤結構描述
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| 屬性 | 類型 | 說明 |
| --- | --- | --- |
| sourceType |   | 執行來源的類型。 允許的值為 **Microsoft.Logic/workflows** 和 **custom**。 (必要) |
| 來源 |   | 如果來源類型是 **Microsoft.Logic/workflows**，則來源資訊必須遵循此結構描述。 如果來源類型為 **custom**，則結構描述為 JToken。 (必要) |
| systemId | String | 邏輯應用程式系統識別碼。 (必要) |
| runId | String | 邏輯應用程式執行識別碼。 (必要) |
| operationName | String | 作業 (例如動作或觸發程序) 的名稱。 (必要) |
| repeatItemScopeName | String | 如果動作在 `foreach`/`until` 迴圈內，重複項目名稱。 (必要) |
| repeatItemIndex | Integer | 動作是否在 `foreach`/`until` 迴圈內。 指出重複的項目索引。 (必要) |
| trackingId | String | 追蹤識別碼，使訊息相互關聯。 (選用) |
| correlationId | String | 相互關連識別碼，使訊息相互關聯。 (選用) |
| clientRequestId | String | 用戶端可以填入此值，使訊息相互關聯。 (選用) |
| eventLevel |   | 事件的層級。 (必要) |
| eventTime |   | 事件的時間，以 UTC 格式 YYYY-MM-DDTHH:MM:SS.00000Z。 (必要) |
| recordType |   | 追蹤記錄的類型。 允許的值為 **custom**。 (必要) |
| record |   | 自訂記錄類型。 允許的格式為 JToken。 (必要) |

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述
如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰
* [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>後續步驟
* [深入了解監視 B2B 訊息](logic-apps-monitor-b2b-message.md)。   
* 了解[在 Operations Management Suite 入口網站中追蹤 B2B 訊息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。
* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)。
