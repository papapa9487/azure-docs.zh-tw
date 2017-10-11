---
title: "使用 Azure 事件中樞為 Azure Logic Apps 設定事件監視器 | Microsoft Docs"
description: "使用 Azure 事件中樞來監視資料流，為 Azure Logic Apps 接收事件和傳送事件"
services: logic-apps
keywords: "資料流、事件監視器、事件中樞"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>使用事件中樞連接器來監視、接收及傳送事件

若要設定事件監視器，以便邏輯應用程式偵測事件、接收事件和傳送事件，請從您的邏輯應用程式連線到 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)。 深入了解 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)。

## <a name="requirements"></a>需求

* 您在 Azure 中必須有[事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。 了解[如何建立事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)。 

* 若要在邏輯應用程式中使用[任何連接器](https://docs.microsoft.com/azure/connectors/apis-list)，您必須先建立邏輯應用程式。 了解[如何建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>檢查事件中樞命名空間權限並尋找連接字串

若要讓邏輯應用程式存取任何服務，您必須建立邏輯應用程式與服務之間的[*連線*](./connectors-overview.md)(如果您還尚未這麼做)。 此連線會授權邏輯應用程式存取資料。
若要讓邏輯應用程式存取事件中樞，您必須擁有事件中樞命名空間的 [管理] 權限和連接字串。

若要檢查您的權限並取得連接字串，請遵循下列步驟。

1.  登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。 

2.  移至您的事件中樞「命名空間」，而不是特定事件中樞。 在命名空間刀鋒視窗的 [設定] 之下，選擇 [共用存取原則]。 在 [宣告] 之下，確認您有該命名空間的 [管理] 權限。

    ![管理事件中樞命名空間的權限](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  若要複製事件中樞命名空間的連接字串，請選擇 [RootManageSharedAccessKey]。 選擇您的主索引鍵連接字串旁邊的 [複製] 按鈕。

    ![複製事件中樞命名空間連接字串](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 若要確認您的連接字串是否與您的事件中樞命名空間或特定事件中樞相關聯，請檢查連接字串中的 `EntityPath` 參數。 如果您發現這個參數，此連接字串適用於特定事件中樞「實體」，而不是使用您的邏輯應用程式的正確字串。

4.  將事件中樞觸發程序或動作新增至邏輯應用程式之後，當系統提示您輸入認證時，您可以連線至您的事件中樞命名空間。 指定您的連線名稱，輸入您複製的連接字串，然後選擇 [建立]。

    ![輸入事件中樞命名空間的連接字串](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    您在建立連線之後，連線名稱應該會出現在事件中樞觸發程序或動作中。 
    您現在可以在您的邏輯應用程式中繼續進行其他步驟。

    ![已建立事件中樞命名空間連線](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>在事件中樞收到新事件時啟動工作流程

[觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)是在邏輯應用程式中啟動工作流程的事件。 若要在新事件傳送至事件中樞時啟動工作流程，請遵循下列步驟來新增可偵測此事件的觸發程序。

1.  在 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")中，移至現有的邏輯應用程式，或建立空白的邏輯應用程式。

2.  在邏輯應用程式設計工具的搜尋方塊中，輸入 `event hubs` 作為您的篩選條件。 選取此觸發程序︰**當事件可用於事件中樞時**

    ![選取當事件中樞收到新事件時的觸發程序](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    如果您還沒有事件中樞命名空間的連線，系統會提示您立即建立此連線。 指定您的連線名稱，然後輸入事件中樞命名空間的連接字串。 
    如有必要，請了解[如何尋找您的連接字串](#permissions-connection-string)。

    ![輸入事件中樞命名空間的連接字串](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    在您建立連線之後，[當事件可用於事件中樞時] 觸發程序的設定隨即出現。

    ![當事件中樞收到新事件時的觸發程序設定](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  輸入或選取您想要監視的事件中樞名稱。 選取您想要檢查事件中樞的頻率和間隔。

    > [!TIP]
    > 若要選擇性地選取取用者群組以便讀取事件，請選擇 [顯示進階選項]。 

    ![指定事件中樞或取用者群組](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    您現在已設定觸發程序以啟動邏輯應用程式的工作流程。 
    您的邏輯應用程式會根據您所設定的排程，檢查指定的事件中樞。 
    如果您的應用程式在事件中樞找到新的事件，觸發程序就會在邏輯應用程式中執行其他動作或觸發程序。

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>將事件從邏輯應用程式傳送至事件中樞

[動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)是邏輯應用程式工作流程所執行的工作。 將觸發程序新增至邏輯應用程式之後，您可以新增一個動作，以對該觸發程序所產生的資料執行作業。 若要將事件從邏輯應用程式傳送至事件中樞，請遵循下列步驟。

1.  在邏輯應用程式設計工具中，於您的邏輯應用程式觸發程序之下，選擇 [新增步驟] > [新增動作]。

    ![選擇 [新增步驟]，然後選擇 [新增動作]](./media/connectors-create-api-azure-event-hubs/add-action.png)

    您現在可以尋找並選取要執行的動作。 
    雖然您可以選取任何動作，但在這個範例中，我們希望事件中樞動作傳送事件。

2.  在搜尋方塊中，輸入 `event hubs` 作為篩選條件。
選取此動作︰**傳送事件**

    ![選取「事件中樞 - 傳送事件」動作](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  輸入事件的必要詳細資料，例如要傳送事件的事件中樞名稱。 輸入有關事件的其他任何選擇性詳細資料，例如該事件的內容。

    > [!TIP]
    > 若要選擇性地指定要傳送事件的事件中樞資料分割，請選擇 [顯示進階選項]。 

    ![輸入事件中樞名稱和選擇性事件詳細資料](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  儲存您的變更。

    ![儲存您的邏輯應用程式](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    您現在已設定可從邏輯應用程式傳送事件的動作。 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/eventhubs/)的所有限制。 

## <a name="get-help"></a>取得說明

若要提出問題、回答問題以及查看其他 Azure Logic Apps 使用者的做法，請造訪 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

若要改善 Logic Apps 和連接器，請在 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)上票選或提交想法。

## <a name="next-steps"></a>後續步驟

*  [尋找 Azure Logic Apps 的其他連接器](./apis-list.md)