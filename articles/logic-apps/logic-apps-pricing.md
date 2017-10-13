---
title: "定價 & 計費 - Azure Logic Apps | Microsoft Docs"
description: "了解 Azure Logic Apps 的定價和計費方式。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.openlocfilehash: 63784c5e3af360b2f3f8cb330a9df8b27a85d859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-pricing-model"></a>Logic Apps 的定價模式
Azure Logic Apps 可讓您在雲端中執行整合工作流程和調整其規模。  以下是 Logic Apps 計費和定價方案的詳細資料。
## <a name="consumption-pricing"></a>取用定價
新建立的 Logic Apps 會使用取用方案。 使用 Logic Apps 的取用定價模式時，您只需針對使用的部分付費。  使用取用方案時，Logic Apps 不會執行節流。
在執行邏輯應用程式執行個體時所執行的所有動作都會計量。
### <a name="what-are-action-executions"></a>什麼是動作執行？
在邏輯應用程式定義中的每個步驟都是動作，包括觸發程序、控制流程步驟 (如條件、範圍、for each 迴圈、do until 迴圈、呼叫連接器和呼叫原生動作)。
觸發程序是設計用來在特定事件發生時，具現化新的邏輯應用程式執行個體的特殊動作。  觸發程序擁有許多會影響邏輯應用程式計量方式的不同行為。
* **輪詢觸發程序** – 此觸發程序會持續輪詢端點，直到它收到的訊息符合用來建立邏輯應用程式執行個體的準則。  在 Logic App Designer 中，可於觸發程序內設定輪詢間隔。  即使是未建立邏輯應用程式執行個體的輪詢要求也會計算為一個動作執行。
* **Webhook 觸發程序** – 此觸發程序會等候用戶端送來針對特定端點的要求。  每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 要求觸發程序和 HTTP Webhook 觸發程序皆為 Webhook 觸發程序。
* **循環觸發程序** – 此觸發程序會根據觸發程序中所設定的循環間隔建立邏輯應用程式執行個體。  例如，您可以將循環觸發程序設定為每三天執行一次，或甚至是每分鐘執行一次。

在 [Logic Apps 資源] 刀鋒視窗的 [觸發程序記錄] 部分可以看到觸發程序執行。

所有已執行的動作不論成功或失敗，都為計量為動作執行。  因為條件不符而略過的動作，或因為邏輯應用程式在完成之前就終止而未執行的動作，均不會計算為一個動作執行。

在迴圈內執行的動作每反覆運算一次就會計算一次。  例如，for each 迴圈內會針對 10 個項目所組成的清單逐一執行一遍的單一動作，將會計算為清單項目數 (10) 乘以迴圈中的動作數目 (1) 再加上起始迴圈時的一次執行，因此得出此範例共有 (10 * 1) + 1 = 11 個動作執行。
已停用的 Logic Apps 不能具現化新的執行個體，因此在停用時不會收費。  請注意，在停用邏輯應用程式之後，執行個體可能要先花點時間停止，然後才能完全停用。
### <a name="integration-account-usage"></a>整合帳戶用量
包含於根據耗用量的使用量中為[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)以供探勘、開發和測試用途，讓您無須額外成本即可使用 Logic Apps 的 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 處理](logic-apps-enterprise-integration-xml.md)功能。 您每個區域最多可以建立一個帳戶並存放最多 10 個合約和 25 個對應。 結構描述、憑證和協力廠商沒有限制，您可以視需要上載。

除了包含耗用量的整合帳戶，您也可以建立標準整合帳戶，沒有這些限制而具有標準 Logic Apps SLA。 如需詳細資訊，請參閱 [Azure 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="app-service-plans"></a>App Service 方案
先前建立參考 App Service 方案的 Logic Apps 會繼續如之前一樣運作。 根據選擇的計劃，超過規定的每日執行之後便會節流，但會使用動作執行計量表收取費用。
其訂用帳戶 (並沒有明確地與邏輯應用程式相關聯) 中有 App Service 方案之 EA 客戶會包含數量好處。  例如，如果您在 EA 訂用帳戶和相同訂用帳戶的邏輯應用程式中具有標準 App Service 方案，則不需負擔每日 10,000 個動作執行 (請參閱下表)。 

App Service 方案和其每日允許的動作執行次數︰
|  | 免費/共用/基本 | 標準 | 高級 |
| --- | --- | --- | --- |
| 每日的動作執行次數 |200 |10,000 |50,000 |
### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>從 App Service 方案定價轉換為取用定價
若要將具有相關聯 App Service 方案的邏輯應用程式變更為取用模式，請在邏輯應用程式定義中移除 App Service 方案的參考。  呼叫 PowerShell Cmdlet 即可完成此變更︰`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`
## <a name="pricing"></a>價格
如需定價詳細資料，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="next-steps"></a>後續步驟
* [Logic Apps 概觀][whatis]
* [建立第一個邏輯應用程式][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

