---
title: "在系統與雲端服務之間建立第一個自動化工作流程 - Azure Logic Apps | Microsoft Docs"
description: "藉由建立及執行邏輯應用程式，自動執行系統整合和企業應用程式整合 (EAI) 案例的商務程序和工作流程"
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>透過 Azure 入口網站建立第一個自動執行工作流程和處理程序的邏輯應用程式

不需要撰寫程式碼，您可以利用[Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) 建置和執行自動化的工作流程，以整合系統和服務。 為了顯示如何輕鬆地利用工作流程將工作自動化，此教學課程建立一個基本邏輯應用程式，會檢查網站 RSS 摘要的新內容，並針對摘要中每個新的項目傳送電子郵件。 

![概觀 - 第一個邏輯應用程式範例](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立空白邏輯應用程式。
> * 新增觸發程序，在 RSS 摘要項目發佈時啟動邏輯應用程式。
> * 新增傳送電子郵件的動作，郵件中包含 RSS 摘要項目的相關詳細資料。
> * 執行並測試邏輯應用程式。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 來自 [Azure Logic Apps 所支援的任何電子郵件提供者](../connectors/apis-list.md)的電子郵件帳戶，用於傳送通知。 例如，您可以使用 Office 365 Outlook、Outlook.com、Gmail 或其他支援的提供者。 本教學課程是使用 Office 365 Outlook。

  > [!TIP]
  > 如果您有個人 [Microsoft 帳戶](https://account.microsoft.com/account)，您便有 Outlook.com 帳戶。 不然，如果您有 Azure 工作或學校帳戶，您便有 Office 365 Outlook 帳戶。

* 網站的 RSS 摘要連結。 這個範例會使用 [CNN.com 網站的頭條報導 RSS 摘要](http://rss.cnn.com/rss/cnn_topstories.rss)：`http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1.建立空白邏輯應用程式 

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。

2. 從 Azure 主要功能表中，選擇 [新增] > [企業整合] > [邏輯應用程式]。

   ![Azure 入口網站, 新增, 企業整合, 邏輯應用程式](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 在表中指定設定以建立應用程式邏輯。

   ![提供邏輯應用程式詳細資料](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **名稱** | *邏輯應用程式的名稱* | 提供唯一的邏輯應用程式名稱。 | 
   | **訂用帳戶** | *您的 Azure 訂用帳戶* | 選取您要使用的 Azure 訂用帳戶。 | 
   | **資源群組** | *您的 Azure 資源群組* | 建立或選取 Azure 資源群組，以協助您組織及管理相關的 Azure 資源。 | 
   | **位置** | *您的 Azure 區域* | 選取資料中心區域，用於部署邏輯應用程式。 | 
   |||| 

4. 當您準備就緒，選取 [釘選到儀表板]，然後選擇 [建立]。

   您現在已經為您的應用程式邏輯建立一項 Azure 資源。 
   在 Azure 部署您的邏輯應用程式之後，Logic Apps 設計工具會顯示常見模式的範本，以便您更快開始使用。

   > [!NOTE] 
   > 當您選取 [釘選到儀表板]，邏輯應用程式會在部署後出現於 Azure 儀表板，並在 Logic Apps 設計工具中自動開啟。 如果沒有，您可以手動尋找並開啟您的邏輯應用程式。

5. 現在，在 [範本] 之下，選擇 [空白邏輯應用程式]，以便從頭建置邏輯應用程式。

   ![選擇 [邏輯應用程式] 範本](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps 設計工具現在會顯示您可使用的[*連接器*](../connectors/apis-list.md)及其[*觸發程序*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，讓您用來啟動邏輯應用程式的工作流程。

   ![邏輯應用程式觸發程序](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2.新增啟動工作流程的觸發程序

每個邏輯應用程式皆必須以[*觸發程序*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)啟動。 當特定事件發生時，或新的資料符合您所設定的條件時，就會引發觸發程序。 Logic Apps 引擎接著會建立邏輯應用程式執行個體來執行工作流程。 每次觸發程序引發時，引擎就會建立另一個執行邏輯應用程式工作流程的個別執行個體。

1. 在搜尋方塊中，輸入 "rss" 作為篩選條件。 選取此觸發程序︰**RSS - 摘要項目發佈時** 

   ![選取觸發程序︰RSS - 摘要項目發佈時](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 提供您想要追蹤之網站的 RSS 摘要連結，例如 `http://rss.cnn.com/rss/cnn_topstories.rss`。 設定循環的間隔和頻率。 此範例中，將這些屬性設定為每天檢查摘要。 

   ![使用 RSS 摘要、頻率和間隔設定觸發程序](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. 儲存您目前的工作。 在設計工具的工具列上，選擇 [儲存]。
若要摺疊和隱藏觸發程序的詳細資料，選擇觸發程序的標題列。

   ![儲存您的邏輯應用程式](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   邏輯應用程式現在已上架，但是在您將動作新增至工作流程以前，不會執行任何動作，只會檢查 RSS 摘要的新項目。 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3.新增可回應觸發程序的動作

現在要新增動作，[*動作*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)是邏輯應用程式工作流程所執行的工作。 在此範例中，我們新增的動作會在 RSS 摘要中出現新項目時傳送電子郵件。

1. 在 Logic Apps 設計工具中，觸發程序之下，選擇 [+新增步驟]  >  [新增動作]。

   ![新增動作](./media/logic-apps-create-a-logic-app/add-new-action.png)

   設計工具會顯示[可用的連接器](../connectors/apis-list.md)，因此您可以選取要在觸發程序引發時執行的動作。

   ![從動作清單中選取](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 在搜尋方塊中，輸入「傳送電子郵件」作為篩選條件。 根據您的電子郵件提供者，尋找並選取相符的連接器。 然後針對您的連接器選取 [傳送電子郵件] 動作。 例如： 

   * 對於 Azure 公司或學校帳戶，選取 Office 365 Outlook 連接器。 
   * 對於個人 Microsoft 帳戶，選取 Outlook.com 連接器。 
   * 對於 Gmail 帳戶，選取 Gmail 連接器。 

   我們將使用 Office 365 Outlook 連接器繼續操作。 
   如果您使用不同的提供者，步驟維持不變，但您的 UI 可能會有所不同。 

   ![選取此動作：Office 365 Outlook - 傳送電子郵件](./media/logic-apps-create-a-logic-app/actions.png)

3. 當系統提示您輸入認證時，使用您電子郵件帳戶的使用者名稱和密碼登入。 

4. 提供表中指定的詳細資料，然後選擇您想要包含在郵件中的欄位。

   | 收件人 | 步驟 | 
   | -- | ----- | 
   | 選取適用於工作流程的欄位。 | 按一下編輯方塊以開啟 [動態內容] 清單，或選擇 [新增動態內容]。 | 
   | 檢視其他可用的欄位。 | 在 [動態內容] 清單中的每個區段選擇 [更多資訊]。  | 
   | 在編輯方塊中新增空白行。 | 按 Shift + Enter。 | 
   | 關閉 [動態內容] 清單。 | 再次選擇 [新增動態內容]。 | 
   ||| 

   ![選取要納入電子郵件中的資料](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | 設定 | 建議的值 | 說明 | 
   | ------- | --------------- | ----------- | 
   | **To** | *收件人電子郵件地址* | 輸入收件者的電子郵件地址。 為了測試用途，您可以使用自己的電子郵件地址。 | 
   | **主旨** | 新的 CNN 文章：**摘要標題** | 輸入電子郵件主旨的內容。 <p>在本教學課程中，輸入建議的文字，然後選取觸發程序的[摘要標題] 欄位，此欄位會顯示摘要項目標題。 | 
   | **內文** | 標題：**摘要標題** <p>發佈日期：**摘要主要連結** <p>連結：**主要摘要連結** | 輸入電子郵件內文的內容。 <p>在本教學課程中，輸入建議的文字，然後選取這些觸發程序欄位： <p>- **摘要標題**，會再次顯示摘要項目的標題 </br>- **摘要發佈於**，會顯示項目的發佈日期和時間 </br>- **主要摘要連結**，會顯示摘要項目的 URL | 
   |||| 

   > [!NOTE] 
   > 如果您選取存放陣列的欄位，設計工具會自動新增以參考該陣列的動作為主的 "For each" 迴圈。 如此一來，應用程式邏輯會在每個陣列項目上執行該動作。

5. 完成後，儲存變更。 在設計工具的工具列上，選擇 [儲存]。

   ![已完成的邏輯應用程式](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   若要立即測試應用程式邏輯，請繼續下一節。

## <a name="4-run-and-test-your-workflow"></a>4.執行並測試工作流程

1. 若要手動執行邏輯應用程式進行測試，在設計工具工具列上，選擇 [執行]。 或者，您可以讓邏輯應用程式根據您設定的排程，檢查指定的 RSS 摘要。

   ![執行邏輯應用程式](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   如果邏輯應用程式找到新的項目，則邏輯應用程式會傳送電子郵件，其中包含您選取的資料，例如：

   ![針對新 RSS 摘要項目傳送的電子郵件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   如果邏輯應用程式找不到任何新項目，如果邏輯應用程式會略過傳送電子郵件的動作，等候下一個間隔再檢查一次。 

2. 若要檢閱邏輯應用程式的執行和觸發歷程記錄，請在邏輯應用程式功能表上選擇 [概觀]。
若要檢視執行的更多詳細資料，請選擇執行的資料列。

   ![監視及檢視邏輯應用程式執行和觸發歷程記錄](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 如果找不到您預期的資料，請嘗試在工具列上選擇 [重新整理]。

   不論執行成功或失敗，[執行詳細資料] 檢視會顯示成功或失敗的步驟。 

   ![檢視邏輯應用程式執行的詳細資料](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   若要深入了解邏輯應用程式的狀態、執行歷程記錄、觸發歷程記錄，或要診斷邏輯應用程式，請參閱[針對邏輯應用程式進行疑難排解](../logic-apps/logic-apps-diagnosing-failures.md)。

3. 若要檢視每個步驟的輸入和輸出，請展開您需要檢閱的步驟。 此資訊可協助您診斷和偵錯應用程式邏輯中的問題。 例如：

   ![檢視步驟詳細資料](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   如需詳細資訊，請參閱[監視邏輯應用程式](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

恭喜，您已經建立並執行您的第一個基本邏輯應用程式。 此範例也顯示如何輕鬆地建立可自動執行整合系統和服務的工作流程程序 - 全都不需要程式碼。

> [!NOTE]
> 邏輯應用程式會繼續執行，直到您關閉應用程式為止。 若要暫時關閉應用程式，請繼續下一節。

## <a name="clean-up-resources"></a>清除資源

本教學課程使用的資源和執行的動作可能會產生 Azure 訂用帳戶費用。 當您完成本教學課程和測試時，請務必停用或刪除任何您不想產生費用的資源。

您可以停止執行邏輯應用程式，然後傳送電子郵件，而無須刪除應用程式。 在邏輯應用程式功能表上，選擇 [概觀]。 在工具列上，選擇 [刪除]。

![關閉應用程式邏輯](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>常見問題集

**問：**我還可利用邏輯應用程式做什麼？ </br>
**答：**您還可以執行其他工作，例如編輯、檢視 JSON 定義、檢閱活動記錄，或刪除邏輯應用程式。

若要尋找其他邏輯應用程式管理工作，請在邏輯應用程式功能表中檢閱這些命令︰

| Task | 步驟 | 
| ---- | ----- | 
| 檢視應用程式的狀態、執行和觸發程序歷程記錄以及一般資訊 | 選擇 [概觀]。 | 
| 編輯應用程式 | 選擇 [邏輯應用程式設計工具]。 | 
| 檢視應用程式的工作流程 JSON 定義 | 選擇 [邏輯應用程式程式碼檢視]。 | 
| 檢視在邏輯應用程式上執行的作業 | 選擇 [活動記錄]。 | 
| 檢視邏輯應用程式過去的版本 | 選擇 [版本]。 | 
| 暫時關閉應用程式 | 選擇 [概觀]，然後選擇工具列上的 [停用]。 | 
| 刪除應用程式 | 選擇 [概觀]，然後選擇工具列上的 [刪除]。 輸入邏輯應用程式的名稱，然後選擇 [刪除]。 | 
||| 

## <a name="get-support"></a>取得支援

* 如有關於 Azure Logic Apps 的問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 願意協助改善 Azure Logic Apps 和連接器嗎？ 請在 [Azure Logic Apps 使用者之聲網站](http://aka.ms/logicapps-wish)投票或提出意見。

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio 建立邏輯應用程式](../logic-apps/logic-apps-deploy-from-vs.md)
* [新增條件並執行工作流程](../logic-apps/logic-apps-use-logic-app-features.md)
*   [邏輯應用程式範本](../logic-apps/logic-apps-use-logic-app-templates.md)
* [從 Azure Resource Manager 範本建立邏輯應用程式](../logic-apps/logic-apps-arm-provision.md)
* [Logic Apps 使用量計量](../logic-apps/logic-apps-pricing.md) 
* [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)
