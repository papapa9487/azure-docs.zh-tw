---
title: "Azure Logic Apps 中不同動作的 Switch 陳述式 | Microsoft Docs"
description: "使用 Switch 陳述式，以根據運算式值來選擇要在邏輯應用程式中執行的不同動作"
services: logic-apps
keywords: "Switch 陳述式"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>使用 Switch 陳述式在邏輯應用程式中執行不同動作

當製作工作流程時，您通常需要根據物件或運算式的值採取不同的動作。 例如，您可能會根據 HTTP 要求的狀態碼或電子郵件中選取的選項，讓邏輯應用程式行為有所不同。

您可以使用 Switch 陳述式來實作這些案例。 邏輯應用程式可以評估權杖或運算式，並選擇具有相同值的案例來執行指定的動作。 只有一種情況應該符合 Switch 陳述式。

> [!TIP]
> 如同所有的程式設計語言，Switch 陳述式僅支援等號比較運算子。 如果您需要其他關係運算子 (例如，「大於」)，請使用條件陳述式。
> 若要確認確定性執行行為，案例必須包含唯一且靜態的值，而不是動態的權杖或運算式。

## <a name="prerequisites"></a>必要條件

- 有效的 Azure 訂用帳戶。 如果您沒有作用中 Azure 訂用帳戶，[請建立免費帳戶](https://azure.microsoft.com/free/)，或免費嘗試 [Logic Apps](https://tryappservice.azure.com/)。
- [邏輯應用程式基本知識](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>將 Switch 陳述式新增到工作流程

為了示範 Switch 陳述式的運作方式，此範例建立一個可監視上傳至 Dropbox 之檔案的邏輯應用程式。 上傳新檔案時，邏輯應用程式會將電子郵件傳送給核准者，讓他選擇是否要將那些檔案傳送到 SharePoint。 應用程式會使用 Switch 陳述式，以根據核准者所選取的值來執行不同的動作。

1. 建立邏輯應用程式，並選取觸發程序：**Dropbox - 建立檔案時**。

   ![使用 Dropbox - 建立檔案時觸發程序](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. 在觸發程序下方，新增此動作︰**Outlook.com - 傳送核准電子郵件**

   > [!TIP]
   > 邏輯應用程式也支援從 Office 365 Outlook 帳戶傳送核准電子郵件案例。

   - 如果您目前沒有連線，系統會提示您建立連線。
   - 填寫必要欄位。 例如，在 [收件者] 下方，指定用來傳送核准者電子郵件的電子郵件地址。
   - 在 [使用者選項] 下，輸入 `Approve, Reject`。

   ![設定連線](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. 新增 Switch 陳述式。

   - 選取 [+ 新步驟] > ...更多 > [新增 Switch 案例]。 
   - 現在，我們想要根據「傳送核准電子郵件」動作的 `SelectedOptions` 輸出來選取要執行的動作。 
   您可以在 [新增動態內容] 選取器中找到此欄位。
   - 核准者選取 `Approve` 時，使用「案例 1」進行處理。
     - 一經核准，使用 [**SharePoint Online - 建立檔案**動作](../connectors/connectors-create-api-sharepointonline.md)，將原始檔案複製到 SharePoint Online。
     - 在案例中新增另一個動作來通知使用者 SharePoint 上有可用的新檔案。
   - 當使用者選取 `Reject` 時，新增另一個案例來處理。
     - 如果已拒絕，傳送通知電子郵件通知其他核准者會拒絕檔案，且不需要任何進一步的動作。
   - `SelectedOptions` 只提供兩個選項，讓我們可以把[預設] 案例空白。

   ![Switch 陳述式](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Switch 陳述式需要至少一個預設案例以外的案例。

4. 在 Switch 陳述式之後，刪除上傳至 Dropbox 的原始檔案，方法是新增此動作：**Dropbox - 刪除檔案**

5. 儲存您的邏輯應用程式。 將檔案上傳至 Dropbox，以測試應用程式。 您應該很快就會收到核准電子郵件。 選取一個選項，並觀察行為。

   > [!TIP]
   > 請查看如何[監視邏輯應用程式](logic-apps-monitor-your-logic-apps.md)。

## <a name="understand-the-code-behind-switch-statements"></a>了解程式碼後置 Switch 陳述式

現在，您已使用 Switch 陳述式成功建立邏輯應用程式，讓我們看看 switch 陳述式後面的程式碼定義。

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"` 是 Switch 陳述式的名稱，您可以將它重新命名以增加可讀性。 
* `"type": "Switch"` 指出動作是 Switch 陳述式。 
* `"expression"` 是核准者在此範例中選取的選項，並且會針對稍後在定義中宣告的每個案例進行評估。 
* `"cases"` 可以包含任意數目的案例。 針對每個案例，`"Case *"` 是案例的預設名稱，您可以將它重新命名以增加可讀性。 
`"case"` 指定 Switch 運算式用於比較的案例標籤，而且必須為常數且唯一的值。 如果沒有符合 Switch 運算式的案例，則會執行 `"default"` 下方的動作。

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及查看其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Azure Logic Apps 和連接器，請在 [Azure Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

## <a name="next-steps"></a>後續步驟

- 了解如何[新增條件](logic-apps-use-logic-app-features.md)
- 了解[錯誤和例外狀況處理](logic-apps-exception-handling.md)
- 深入探討[工作流程語言功能](logic-apps-author-definitions.md)