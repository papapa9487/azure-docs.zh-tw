---
title: "透過 Azure Functions 的適用於 Azure Logic Apps 的自訂程式碼 | Microsoft Docs"
description: "使用 Azure Functions 建立並執行適用於 Azure Logic Apps 的自訂程式碼"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 18442c87b049200fac5ed41cc7034ba7a848b8d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>透過 Azure Functions 新增並執行適用於 Logic Apps 的自訂程式碼

若要在邏輯應用程式中執行 C# 或 node.js 的自訂程式碼片段，您可以透過 Azure Functions 建立自訂函式。 
[Azure Functions](../azure-functions/functions-overview.md) 提供 Microsoft Azure 中無伺服器運算的功能，並有助於執行下列工作：

* 邏輯應用程式中欄位的進階格式設定或計算
* 在工作流程中執行計算。
* 利用 C# 或 node.js 中支援的函式來擴充邏輯應用程式功能

## <a name="create-custom-functions-for-your-logic-apps"></a>建立邏輯應用程式的自訂函式

建議您在 Azure Functions 入口網站中，從**一般 Webhook - 節點**或**一般 Webhook - C#** 範本建立新的函式。 結果會建立自動填入以接受來自邏輯應用程式之 `application/json` 的範本。 系統即會自動偵測您從這些範本建立的函式，並顯示於邏輯應用程式設計工具的 [我的區域中的 Azure Functions] 下方。

在 Azure 入口網站中函式的 [整合] 窗格上，您的範本應該會顯示將 [模式] 設為 [Webhook]，以及將 [Webhook 類型] 設為 [一般 JSON]。 

Webhook 函數會接受要求，並透過 `data` 變數將它傳入方法。 您可以使用點標記法 (例如 `data.function-name`) 來存取承載的屬性。 例如，將日期時間值轉換為日期字串的簡單 JavaScript 函數看起來如以下範例︰

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>從 Logic Apps 呼叫 Azure Functions

若要列出您訂用帳戶中的容器，並選取您想要呼叫的函式，可在邏輯應用程式設計工具中，按一下 [動作] 功能表，然後從 [我的區域中的 Azure Functions] 中選取。

選取函式之後，系統會要求您指定輸入承載物件。 這個物件是邏輯應用程式要傳送到函式的訊息，且必須是 JSON 物件。 例如，如果我想要傳入 Salesforce 觸發程序的**上次修改**日期，函式承載可能看起來如下範例：

![上次修改日期][1]

## <a name="trigger-logic-apps-from-a-function"></a>從函數觸發 Logic Apps

您可以從函式內部觸發邏輯應用程式。 請參閱[做為可呼叫端點的邏輯應用程式](logic-apps-http-endpoint.md)。 建立含有手動觸發程序的邏輯應用程式，然後從您的函式內，產生 HTTP POST 到手動觸發程序 URL，其中包含您想要傳送到邏輯應用程式的承載。

### <a name="create-a-function-from-logic-app-designer"></a>從邏輯應用程式設計工具建立函式

您也可以從設計工具中建立 node.js webhook 函式。 首先，選取 [我的區域中的 Azure Functions]  ，然後選擇適用於您的函數的容器。 如果您還沒有容器，就必須從 [Azure Functions 入口網站](https://functions.azure.com/signin)建立一個。 然後選取 建立新的) 。  

若要根據您想要計算的資料來產生範本，請指定您打算傳入函數的內容物件。 這個物件必須是 JSON 物件。 例如，如果您從 FTP 動作傳入檔案內容，內容承載可能看起來如下範例：

![內容承載][2]

> [!NOTE]
> 因為此物件無法轉換為字串，所以會直接將內容新增至 JSON 承載。 不過，如果物件不是 JSON 權杖 (也就是字串或 JSON 物件/陣列)，即會發生錯誤。 若要將物件轉換為字串，請加上引號，如本文的第一個圖例所示。
> 

設計工具接著會產生您可以內嵌建立的函數範本。 變數會根據您想要傳入函數的內容預先建立。

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
