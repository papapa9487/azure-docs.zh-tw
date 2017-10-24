---
title: "新增條件和啟動工作流程 - Azure Logic Apps | Microsoft Docs"
description: "新增條件式邏輯、觸發程序、動作和參數，以控制 Azure Logic Apps 中工作流程的執行方式。"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-logic-apps-features"></a>使用 Logic Apps 功能

在[前面的主題](../logic-apps/logic-apps-create-a-logic-app.md)中，您已建立第一個邏輯應用程式。 若要控制邏輯應用程式的工作流程，您可以指定不同的路徑以供邏輯應用程式執行，以及指定如何處理陣列、集合和批次中的資料。 您可以將下列元素包含在邏輯應用程式的工作流程中：

* 條件和 [Switch 陳述式](../logic-apps/logic-apps-switch-case.md)可讓邏輯應用程式根據符合的特定條件來執行不同動作。

* [迴圈](../logic-apps/logic-apps-loops-and-scopes.md)可讓邏輯應用程式重複執行步驟。 例如，使用 **For_each** 迴圈時，可以對陣列重複動作。 或者使用 **Until** 迴圈，可以在符合條件之後重複動作。

* [Scope](../logic-apps/logic-apps-loops-and-scopes.md) 可讓您將一系列動作組成群組，例如用以進行例外狀況處理實作。

* 使用 **SplitOn** 命令時，[Debatching](../logic-apps/logic-apps-loops-and-scopes.md) 可讓邏輯應用程式啟動陣列中項目的個別工作流程。

本主題將介紹建置邏輯應用程式的其他概念：

* 用以編輯現有邏輯應用程式的程式碼檢視
* 啟動工作流程的選項

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>條件：只有在符合條件後才執行步驟

若要在資料符合特定準則時，才讓邏輯應用程式執行步驟，您可以新增條件，針對特定欄位或值比較工作流程中的資料。

例如，假設您的邏輯應用程式傳送過多的網站 RSS 摘要文章的電子郵件給您。 您可以新增條件，讓邏輯應用程式只有在新文章屬於特定類別時，才會傳送電子郵件。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，尋找並開啟邏輯應用程式。

2. 將條件新增至您想要的工作流程位置。 

   若要在邏輯應用程式工作流程中的現有步驟之間新增條件，將指標移動至您要新增條件的箭頭處。 
   選擇 **加號** \(**+**)， 然後選擇 **新增條件**。 例如：

   ![將條件新增至邏輯應用程式](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > 如果想要在目前的工作流程結尾處新增條件，請移至邏輯應用程式底部，然後選擇 **+ 新增步驟**。

3. 現在定義條件。 指定您想要評估的來源欄位、要執行的作業，以及目標值或欄位。 若要將現有欄位新增至條件，請從**新增動態內容清單**中選擇。

   例如：

   ![在基本模式中編輯條件](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   以下是完整的條件：

   ![完整條件](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > 若要在程式碼中定義條件，請選擇**在進階模式中編輯**。 例如：
   > 
   > ![在程式碼中編輯條件](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. 在 **IF YES** 和 **IF NO** 之下，以是否符合條件為基礎，新增要執行的步驟。

   例如：

   ![具 YES 和 NO 路徑的條件](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > 您可以將現有動作拖曳到 **IF YES** 和 **IF NO** 路徑。

5. 完成後，儲存邏輯應用程式。

現在您只有在文章符合條件時，才會收到電子郵件。

## <a name="repeat-actions-over-a-list-with-foreach"></a>使用 forEach 對清單重複執行動作

forEach 迴圈會指定要用來重複執行某動作的陣列。 如果它不是陣列，流程便會失敗。 舉例來說，如果 action1 會輸出訊息陣列，而您想要傳送每則訊息，則可以在動作的屬性中包含這個 forEach 陳述式︰`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>編輯邏輯應用程式的程式碼定義

儘管您擁有邏輯應用程式設計工具，您還是可以直接編輯定義邏輯應用程式的程式碼。

1. 在命令列中，選擇 [程式碼檢視]。

    隨即會開啟完整的編輯器，並顯示您所編輯的定義。

    ![程式碼檢視](media/logic-apps-use-logic-app-features/codeview.png)

    在文字編輯器中，您可以在相同的邏輯應用程式或邏輯應用程式之間複製並貼上任何數量的動作。 
    您也可以輕鬆地在定義中新增或移除整個區段，以及與其他人共用定義。

2. 若要儲存您的編輯，請選擇 [儲存]。

## <a name="parameters"></a>參數

某些 Logic Apps 功能只能在程式碼檢視中取得，例如參數。 參數可讓您輕鬆地在整個邏輯應用程式中重複使用值。 例如，如果您想要在數個動作中使用同一個電子郵件地址，您應將該電子郵件地址定義為參數。

參數很適合用來提取您很可能經常變更的值。 當您需要在不同環境中覆寫參數時，參數特別有用。 如需如何根據環境覆寫參數的詳細資訊，請參閱[撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)以及 [REST API 文件](https://docs.microsoft.com/rest/api/logic)。

這個範例示範如何更新現有的邏輯應用程式，讓您可以針對查詢字詞使用參數。

1. 在程式碼檢視中，尋找 `parameters : {}` 物件，並新增 `currentFeedUrl` 物件：

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. 移至 `When_a_feed-item_is_published` 動作、尋找 `queries` 區段，然後以 `"feedUrl": "#@{parameters('currentFeedUrl')}"` 取代查詢值 

    若要加入兩或多個字串，您也可以使用 `concat` 函式。 
    例如，`"@concat('#',parameters('currentFeedUrl'))"` 的運作方式與上述相同。

3.  完成之後，請選擇 [儲存]。 

    現在您可以透過 `currentFeedURL` 物件傳遞不同的 URL，藉以變更網站的 RSS 摘要。

深入了解[如何撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)。

## <a name="start-logic-app-workflows"></a>啟動邏輯應用程式工作流程

您有數個不同的選項可用來啟動您邏輯應用程式中定義的工作流程。 您一律可在 [Azure 入口網站]中隨選啟動工作流程。

### <a name="recurrence-triggers"></a>循環觸發程序

循環觸發程序會依照您指定的間隔執行。 當觸發程序具有條件式邏輯時，觸發程序會判斷工作流程是否需要執行。 觸發程序透過傳回 `200` 狀態碼，來指示工作流程應該執行。 當工作流程不需要執行時，觸發程序會傳回 `202` 狀態碼。

### <a name="callback-using-rest-apis"></a>使用 REST API 回呼

若要啟動工作流程，服務可以呼叫邏輯應用程式端點。 若要隨選啟動該種邏輯應用程式，請選擇命令列上的 [立即執行]。 請參閱[呼叫邏輯應用程式端點做為觸發程序來啟動工作流程](../logic-apps/logic-apps-http-endpoint.md)。 

<!-- Shared links -->
[Azure 入口網站]: https://portal.azure.com

## <a name="next-steps"></a>後續步驟

* [Switch 陳述式](../logic-apps/logic-apps-switch-case.md) 
* [迴圈、範圍和解除批次處理](../logic-apps/logic-apps-loops-and-scopes.md)
* [撰寫邏輯應用程式定義](../logic-apps/logic-apps-author-definitions.md)