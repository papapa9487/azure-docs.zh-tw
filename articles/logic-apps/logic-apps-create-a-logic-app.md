---
title: "在系統與雲端服務之間自動化工作流程 - Azure Logic Apps | Microsoft Docs"
description: "建立邏輯應用程式，自動化系統整合和企業應用程式整合 (EAI) 案例的工作流程"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "工作流程, 雲端應用程式, 雲端服務, 商務程序, 系統整合, 企業應用程式整合, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>自動化您的第一個工作流程是以邏輯應用程式處理資料

若要為組織更快速地整合系統和服務，您可以使用 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) 自動化工作流程和商務程序。 本快速入門會介紹如何藉由建立邏輯應用程式，輕鬆地建置及執行自動化工作流程。 範例應用程式會示範如何將檢查網站 RSS 摘要是否有新項目並傳送每個項目之電子郵件的工作流程自動化。

此範例邏輯應用程式會傳送電子郵件，如下列範例所示：

![針對新 RSS 摘要項目傳送的電子郵件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

以下是您建置的高階邏輯應用程式工作流程：

![概觀 - 邏輯應用程式範例](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

在此快速入門中，您可了解如何：

> [!div class="checklist"]
> * 建立空白邏輯應用程式。
> * 新增觸發程序以在新項目出現在 RSS 摘要時啟動工作流程。
> * 新增傳送電子郵件的動作，郵件中包含 RSS 摘要項目的相關詳細資料。
> * 執行邏輯應用程式工作流程。

如果您沒有 Azure 訂用帳戶，請在開始前[註冊免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* 來自 Azure Logic Apps 所支援的任何電子郵件提供者的電子郵件帳戶，用於傳送通知。 例如，您可以使用 Office 365 Outlook、Outlook.com 或 Gmail。 對於其他支援的電子郵件連接器，[檢閱連接器清單](https://docs.microsoft.com/connectors/)。 本快速入門使用 Office 365 Outlook。

  > [!TIP]
  > 如果您有個人 [Microsoft 帳戶](https://account.microsoft.com/account)，您便有 Outlook.com 帳戶。 不然，如果您有 Azure 工作或學校帳戶，您便有 Office 365 Outlook 帳戶。

* 網站的 RSS 摘要連結。 這個範例會使用 [Reuters 網站的頭條報導 RSS 摘要](http://feeds.reuters.com/reuters/topNews)：`http://feeds.reuters.com/reuters/topNews`

本快速入門不需要撰寫程式碼，但是 Logic Apps 支援使用程式碼的其他案例，例如，從使用 [Azure Functions](../azure-functions/functions-overview.md) 的邏輯應用程式執行您自己的程式碼。

## <a name="create-a-blank-logic-app"></a>建立空白邏輯應用程式 

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。 

2. 從 Azure 主要功能表中，選擇 [新增] > [企業整合] > [邏輯應用程式]。

   ![Azure 入口網站, 新增, 企業整合, 邏輯應用程式](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 使用此映像底下之資料表中的設定建立應用程式邏輯：

   ![提供邏輯應用程式詳細資料](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **名稱** | *邏輯應用程式的名稱* | 提供唯一的邏輯應用程式名稱。 | 
   | **訂用帳戶** | 您的 Azure 訂用帳戶名稱 | 選取您要使用的 Azure 訂用帳戶。 | 
   | **資源群組** | 您的 Azure 資源群組名稱 | 針對此邏輯應用程式建立 [Azure 資源群組](../azure-resource-manager/resource-group-overview.md)，並且組織與此應用程式相關聯的所有資源。 | 
   | **位置** | 您的 Azure 資料中心區域 | 選取資料中心區域，用於部署邏輯應用程式，例如，美國西部。 | 
   | **Log Analytics** | 關閉 | 開啟應用程式邏輯的診斷記錄，但是針對本快速入門保留 [關閉] 設定。 | 
   |||| 

4. 當您準備就緒，選取 [釘選到儀表板]。 這樣一來，邏輯應用程式就會在部署之後自動出現在 Azure 儀表板並且開啟。 選擇 [建立] 。

   > [!NOTE]
   > 如果您不想要釘選邏輯應用程式，您必須在部署之後以手動方式尋找邏輯應用程式並且開啟，以便繼續。

   Azure 部署您的邏輯應用程式之後，Logic Apps 設計工具隨即開啟，並顯示具有簡介影片的分頁。 
   您可以在影片底下找到一般邏輯應用程式模式的範本。 
   本快速入門會從頭建置邏輯應用程式。 

5. 捲動過簡介影片與一般觸發程序。 在 [範本] 底下，選擇 [空白邏輯應用程式]。

   ![選擇空白邏輯應用程式範本](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps 設計工具現在會顯示您可使用的連接器及其觸發程序，讓您用來啟動邏輯應用程式工作流程。

   ![邏輯應用程式觸發程序](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>新增觸發程序來偵測新的項目

每個邏輯應用程式工作流程都會以[觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)啟動。 當特定事件發生時，或新的資料符合您所設定的條件時，就會引發觸發程序。 每次引發觸發程序時，Logic Apps 引擎會建立邏輯應用程式執行個體，啟動並執行您的工作流程。

1. 在搜尋方塊中，輸入 "rss" 作為篩選條件。 選取此觸發程序︰**RSS - 摘要項目發佈時** 

   ![選取觸發程序︰RSS - 摘要項目發佈時](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 提供您想要監視的 RSS 摘要連結，例如 `http://feeds.reuters.com/reuters/topNews`。 設定重複的間隔和頻率。 這個範例會每隔五分鐘檢查摘要。

   ![使用 RSS 摘要、頻率和間隔設定觸發程序](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps 會建立與 RSS 摘要的連線。

   > [!TIP]
   > 若要簡化設計工具中的檢視，您可以摺疊和隱藏圖形的詳細資料 - 在圖形的標題列內按一下。

3. 儲存您的工作。 在設計工具的工具列上，選擇 [儲存]。 

   ![儲存您的邏輯應用程式](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   邏輯應用程式目前啟用中，但是不會執行檢查 RSS 摘要以外的任何其他作業。 因此，讓我們新增會在觸發程序引發時回應的動作。

## <a name="add-an-action-to-send-email"></a>新增動作來傳送電子郵件

現在您有觸發程序，新增[動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)以在 RSS 摘要中出現新項目時傳送電子郵件。 您的工作流程會在觸發程序引發之後執行此動作。

1. 在 Logic Apps 設計工具中，觸發程序之下，選擇 [+新增步驟]  >  [新增動作]。

   ![新增動作](./media/logic-apps-create-a-logic-app/add-new-action.png)

   設計工具會顯示邏輯應用程式在觸發程序引發時可以執行的動作。

   ![從動作清單中選取](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 在搜尋方塊中，輸入「傳送電子郵件」作為篩選條件。 尋找並選取您想要使用的電子郵件連接器。 然後針對您的連接器選取 [傳送電子郵件] 動作。 例如： 

   * 對於 Azure 工作或學校帳戶，選取 Office 365 Outlook。 
   * 對於個人 Microsoft 帳戶，選取 Outlook.com。 
   * 對於 Gmail 帳戶，選取 Gmail。 

   本快速入門使用 Office 365 Outlook。 
   如果您使用不同的電子郵件提供者，步驟維持不變，但您的 UI 可能會有所不同。 

   ![選取此動作：Office 365 Outlook - 傳送電子郵件](./media/logic-apps-create-a-logic-app/actions.png)

3. 當系統提示您輸入認證時，使用您電子郵件帳戶的使用者名稱和密碼登入。 

   Logic Apps 會建立與電子郵件帳戶的連線。

4. 現在，請指定您想要包含在電子郵件中的資料。 

   1. 在 [收件者] 方塊中，輸入收件者的電子郵件地址。 
   為了測試用途，您可以使用自己的電子郵件地址。

   2. 在 [主旨] 方塊中，輸入電子郵件主旨。 
   針對此範例中，輸入「新的 RSS 項目」，如下所示：

      ![輸入電子郵件主旨](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      當您在編輯方塊內按一下時，[新增動態內容清單] 隨即開啟，讓您可以選取要包含在動作中的可用資料欄位。 
      如果動態內容清單未開啟，請在個別編輯方塊底下，選擇 [新增動態內容]。

   3. 從 [新增動態內容] 清單中，選取 [摘要標題]，其中包括電子郵件中的項目標題。

      ![輸入電子郵件主旨](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      當您完成時，電子郵件主旨看起來如下列範例：

      ![新增的摘要標題](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > 如果您選取其中包含陣列的欄位，例如 **categories-item**，設計工具會自動新增以參考該欄位的動作為主的 "For each" 迴圈。 如此一來，應用程式邏輯可以在每個陣列項目上執行該動作。 
      > 
      > 若要移除迴圈，選擇迴圈標題列上的省略符號 (**...**)，然後選擇 [刪除]。

   4. 在 [內文] 方塊中，輸入電子郵件內文的內容。 
   針對此範例，輸入下列文字，然後選取這些欄位：

      ![新增電子郵件內文的內容](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | 欄位 | 說明 | 
      | ----- | ----------- | 
      | **摘要標題** | 顯示項目的標題。 | 
      | **摘要發佈時間** | 顯示摘要的發佈日期和時間。 | 
      | **主要摘要連結** | 顯示項目的 URL。 | 
      ||| 

      > [!TIP]
      > 若要在編輯方塊中新增空白的行，請按 Shift + Enter。 
      
5. 儲存您的工作。 在設計工具的工具列上，選擇 [儲存]。

   ![已完成的邏輯應用程式](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>執行邏輯應用程式工作流程

若要手動啟動邏輯應用程式，在設計工具工具列上，選擇 [執行]。 否則，您可以等待邏輯應用程式在您設定的排程執行。

![執行邏輯應用程式](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

如果 RSS 摘要有新的項目，邏輯應用程式會針對每個新的項目傳送電子郵件。 例如，以下是此邏輯應用程式傳送的範例 Outlook 電子郵件：

![針對新 RSS 摘要項目傳送的電子郵件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

如果摘要沒有新項目，邏輯應用程式會略過傳送電子郵件的步驟，並且等候下一個間隔再檢查一次。 

> [!TIP]
> 如果您沒有得到任何電子郵件，請檢查垃圾郵件資料夾。 或者，如果您不確定邏輯應用程式是否正確執行，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

恭喜，您已經建立並執行您的第一個邏輯應用程式。 本快速入門示範您可以多麼地輕鬆、快速建立自動化工作流程來整合系統和服務。

## <a name="clean-up-resources"></a>清除資源

邏輯應用程式會繼續執行，而且可能會在您的 Azure 訂用帳戶產生費用，直到您關閉或刪除您的應用程式。 此外，當您建立邏輯應用程式的連線時，這些連線會維持，即使您刪除邏輯應用程式之後也一樣。 

當您完成時，請務必停用或刪除您不想產生費用或不想要保留的任何資源。 若要刪除您為本快速入門建立的所有資源，請刪除您為此邏輯應用程式建立的 Azure 資源群組。 

### <a name="delete-resource-group"></a>刪除資源群組

如果您不想要保留與邏輯應用程式相關的任何項目，請刪除您為本快速入門建立的資源群組和所有相關資源。 深入了解[如何管理 Azure 資源群組](../azure-resource-manager/resource-group-portal.md#manage-resources)。

1. 在 Azure 功能表上，選擇 [資源群組]。

2. 選擇您想要刪除的資源群組。 在資源群組功能表上，選擇 [概觀] \(如果尚未選取)。 

3. 檢閱您想要刪除之群組中的所有資源。 當您準備好時，選擇資源群組工具列上的 [刪除資源群組]。

### <a name="turn-off-logic-app"></a>關閉邏輯應用程式

若要停止執行邏輯應用程式但是不刪除您的工作，請停用您的應用程式。 

在邏輯應用程式功能表上，選擇 [概觀]。 在工具列上，選擇 [刪除]。

  ![關閉應用程式邏輯](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 如果您沒有看到邏輯應用程式功能表，請嘗試返回 Azure 儀表板，然後重新開啟邏輯應用程式。

### <a name="delete-logic-app"></a>刪除邏輯應用程式

您可以只刪除邏輯應用程式，但是保留其他相關的所有資源，例如您建立的連線。

1. 在邏輯應用程式功能表上，選擇 [概觀]。 在工具列上，選擇 [刪除]。 

   ![刪除邏輯應用程式](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > 如果您沒有看到邏輯應用程式功能表，請嘗試返回 Azure 儀表板，然後重新開啟邏輯應用程式。

2. 確認您想要刪除邏輯應用程式，然後選擇 [刪除]。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從預先建立的範本來建立邏輯應用程式工作流程](../logic-apps/logic-apps-create-logic-apps-from-templates.md)