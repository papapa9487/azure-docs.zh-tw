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
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: f2a92e45b8a759d2d8193ac188efdcfc694a3e6d
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="logic-apps-pricing-model"></a>Logic Apps 的定價模式
您可以使用 Azure Logic Apps，在雲端建立和執行可調整規模的自動化整合工作流程。 以下是關於 Logic Apps 計費和定價運作方式的詳細資料。
## <a name="consumption-pricing-model"></a>取用量定價模式
透過新建立的邏輯應用程式，您只需支付您使用的項目。 新的邏輯應用程式會使用取用量方案和定價模型，這表示由邏輯應用程式執行個體所執行的所有執行都會納入計量。
### <a name="what-are-action-executions"></a>什麼是動作執行？
邏輯應用程式定義中的每個步驟都是一個動作，包括觸發程序、控制流程步驟、呼叫內建動作，以及呼叫連接器。
### <a name="triggers"></a>觸發程序
觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序具有數種會影響邏輯應用程式計量方式的不同行為。
* **輪詢觸發程序**：此觸發程序會持續檢查端點，直到它取得的訊息符合用來建立邏輯應用程式執行個體以啟動工作流程的準則。 您可以透過邏輯應用程式設計工具，在觸發程序中設定輪詢間隔。 每個輪詢要求都會以一個執行來計算，即使未建立任何邏輯應用程式執行個體時也一樣。
* **Webhook 觸發程序**：此觸發程序會等候用戶端將要求傳送到特定端點。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。
* **週期性觸發程序**：此觸發程序會根據您在觸發程序中設定的週期間隔來建立邏輯應用程式執行個體。 例如，您可以設定一個週期性觸發程序，每隔三天執行一次或基於更複雜的排程來執行。

您可以在邏輯應用程式的 [概觀] 窗格中，於 [觸發程序歷程記錄] 區段下方找到觸發程序執行。

### <a name="actions"></a>動作
內建動作 (例如，呼叫 HTTP、Azure Functions 或 API 管理的動作，以及控制流程步驟) 會以原生動作來計量，而且具有其各自的類型。 呼叫[連接器](https://docs.microsoft.com/connectors) \(英文\) 的動作具有 "ApiConnection" 類型。 連接器會歸類為標準或企業連接器，並以其各自的[價格][pricing]來計量。
所有成功和失敗的執行動作都會納入計算，並以動作執行來計量。 不過，因為條件不符而略過的動作，或因為邏輯應用程式在完成之前就終止而未執行的動作，均不會當成動作執行來計算。 停用的邏輯應用程式無法將新的執行個體具現化，因此，停用它們時，就不會針對它們進行收費。

> [!NOTE]
> 停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

在迴圈內部執行的動作會針對迴圈中的每個週期來計算。 例如，在 "for each" 迴圈中處理 10 個項目之清單的單一動作會以下列方式來計算：將清單項目數 (10) 乘以迴圈中的動作數 (1)，再加上用來啟動迴圈的一。 因此，針對此範例的計算是 (10 * 1) + 1，結果就是 11 個動作執行。

### <a name="integration-account-usage"></a>整合帳戶使用量
以取用量為根據的使用量包括一個[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探勘、開發和測試 Logic Apps 的 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 處理](logic-apps-enterprise-integration-xml.md)功能，而不需額外成本。 您可以在每個區域中擁有這其中一個整合帳戶，最多可儲存 10 個合約和 25 張地圖。 您可以擁有且上傳不限數量的夥伴、結構描述和憑證。

Logic Apps 也會搭配支援的 Logic Apps SLA 來提供基本和標準整合帳戶。 當您只想使用訊息處理，或者做為與較大商務實體有交易夥伴關係的小型企業夥伴時，就可以使用基本整合帳戶。 標準整合帳戶支援更複雜的 B2B 關係，並增加您可以管理的實體數目。 如需詳細資訊，請參閱 [Azure 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="pricing"></a>價格
如需詳細資訊，請參閱 [Logic Apps 價格](https://azure.microsoft.com/pricing/details/logic-apps)。

## <a name="next-steps"></a>後續步驟
* [Logic Apps 概觀][whatis]
* [建立第一個邏輯應用程式][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

