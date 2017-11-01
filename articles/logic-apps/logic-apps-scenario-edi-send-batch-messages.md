---
title: "以群組或集合批次處理 EDI 訊息 - Azure Logic Apps | Microsoft Docs"
description: "傳送 EDI 訊息以便在邏輯應用程式中批次處理"
keywords: "批次, 批次程序, 批次編碼"
author: divswa
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 837cb0d9595da5b5bd4f01fb4576f75e98ab8912
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>批次傳送 X12 訊息給交易夥伴

在企業對企業 (B2B) 案例中，合作夥伴通常是以群組或批次方式交換訊息。 若要以群組或批次傳送訊息給交易夥伴，您可以建立一個含有多個項目的批次，然後使用 X12 批次動作將這些項目當作一個批次來處理。


像其他訊息一樣，X12 訊息的批次處理也使用批次觸發程序和動作。 同樣地，就 X12 而言，批次也會先經過一個「X12 編碼」步驟，然後才傳送給合作夥伴或任何其他目的地。 如需有關批次觸發程序和動作的詳細資訊，請參閱[批次處理訊息](logic-apps-batch-process-send-receive-messages.md)。

本主題將說明如何透過執行下列工作，以批次方式處理 X12 訊息：
* [建立一個會接收項目並建立批次的邏輯應用程式](#receiver)。 這個「接收者」邏輯應用程式會執行下列動作：
 
   * 指定批次名稱，以及以批次形式發行項目之前要符合的發行準則。

   * 使用指定的 X12 協議或合作夥伴身分識別來處理或編碼批次中的項目。

* [建立邏輯應用程式，將項目傳送至批次](#sender)。 這個「傳送者」邏輯應用程式會指定要將項目傳送到哪個位置來進行處理，這個位置必須是現有的接收者邏輯應用程式。


## <a name="prerequisites"></a>必要條件

若要遵循此範例，您需要這些項目：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 已經定義並與 Azure 訂用帳戶相關聯的[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)

* 至少要有兩個您已在整合帳戶中定義的[合作夥伴](logic-apps-enterprise-integration-partners.md)。 請確定每個合作夥伴在合作夥伴的屬性中都使用 X12 (標準運輸公司法典) 辨識符號作為企業身分識別。

* 已經在整合帳戶中定義的 [X12 合約](logic-apps-enterprise-integration-x12.md)

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>建立一個會接收 X12 訊息並建立批次的邏輯應用程式

您必須先使用**批次**觸發程序來建立「接收者」邏輯應用程式，才能將訊息傳送給批次。 這樣一來，當您建立寄件者邏輯應用程式時，可以選取這個接收者邏輯應用程式。 針對接收者，您需指定批次名稱、發行準則、X12 協議，以及其他設定。 


1. 在 [Azure 入口網站](https://portal.azure.com)中，使用下列名稱來建立一個邏輯應用程式："BatchX12Messages"。

2. 在 Logic Apps 設計工具中，新增**次**觸發程序，它會啟動您的邏輯應用程式工作流程。 在搜尋方塊中，輸入 "batch" 作為篩選條件。 選取此觸發程序：**批次 – 批次訊息**

   ![新增批次觸發程序](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. 提供批次的名稱，並指定釋放批次的準則，例如：

   * **批次名稱**：用來識別批次，在本例中為 "TestBatch"。

   * **發行準則**：批次發行準則，此準則可以訊息計數、排程作為基礎或同時以這兩者作為基礎。
   
     ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **訊息計數**：在釋放進行處理之前要保留為一個批次的訊息數目，在本例中為 "5"。

     ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **排程**：用於進行處理的批次發行排程，在此範例中為「每隔 10 分鐘」。

     ![提供批次觸發程序詳細資料](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. 新增另一個動作，此動作會將已分組或分批的訊息編碼，然後建立 X12 批次處理訊息。 

   a. 選擇 [+ 新步驟] > [新增動作]。

   b. 在搜尋方塊中，輸入 "X12 batch" 作為篩選條件，然後為 **X12 - 批次編碼**選取一個動作。 與「X12 編碼」連接器類似，批次編碼動作也有多種變化。 您可以選取其中任何一個。

   ![選取「X12 批次編碼」動作](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. 設定您剛才新增之動作的屬性。

   * 在 [X12 協議名稱] 方塊中，從下拉式清單中選取協議。 如果您的清單是空的，請確定您已與整合帳戶建立連線。

   * 在 [BatchName] 方塊中，從動態內容清單中選取 [批次名稱] 欄位。
   
   * 在 [PartitionName] 方塊中，從動態內容清單中選取 [分割區名稱] 欄位。

   * 在 [項目] 方塊中，從動態內容清單中選取 [批次處理的項目]。

   ![「批次編碼」動作詳細資料](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. 針對測試用途，請新增一個將批次處理訊息傳送給[要求 Bin 服務](https://requestbin.fullcontact.com/) \(英文\) 的 HTTP 動作。 

   1. 在搜尋方塊中輸入 "HTTP" 作為篩選條件。 選取此動作：**HTTP - HTTP**
    
      ![選取 HTTP 動作](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. 從 [方法] 清單中，選取 [POST]。 針對 [Uri] 方塊，為您的要求 Bin 產生一個 URI，然後輸入該 URI。 在 [本文] 方塊中，當動態清單開啟時，選取 [依合約名稱的批次編碼] 區段底下的 [本文] 欄位。 如果您沒有看到 [本文]，請選擇 [依合約名稱的批次編碼]旁邊的 [查看更多]。

      ![提供 HTTP 動作詳細資料](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  既然您已建立接收者邏輯應用程式，現在便可儲存您的邏輯應用程式。

    ![儲存您的邏輯應用程式](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 資料分割有 5,000 則訊息或 80 MB 的限制。 如果符合其中一個條件，即可能釋出批次 (即使不符合使用者定義的條件)。

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>建立一個會將 X12 訊息傳送給批次的邏輯應用程式

現在建立一或多個將項目傳送給接收者邏輯應用程式所定義之批次的邏輯應用程式。 對於傳送者，您指定接收者邏輯應用程式和批次名稱、訊息內容，和任何其他設定。 您可以選擇性地提供唯一的資料分割索引鍵，將批次分成子集來收集具有該索引鍵的項目。

傳送者邏輯應用程式需要知道要將項目傳送到何處，但接收者邏輯應用程式則不需知道任何關於傳送者的資訊。


1. 使用下列名稱來建立另一個邏輯應用程式："X12MessageSender"。 將以下觸發程序新增到您的邏輯應用程式中：**要求 / 回應 - 要求** 
   
   ![新增「要求」觸發程序](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. 新增新的步驟，將訊息傳送至批次。

   1. 選擇 [+ 新步驟] > [新增動作]。

   2. 在搜尋方塊中，輸入 "batch" 作為篩選條件。 

3. 選取此動作：**將訊息傳送給批次 – 選擇包含批次觸發程序的 Logic Apps 工作流程**

   ![選取 [將訊息傳送給批次]](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. 現在，選取您先前建立的 "BatchX12Messages" 邏輯應用程式，它現在會顯示為動作。

   ![選取「批次接收者」邏輯應用程式](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > 此清單也會顯示有批次觸發程序的任何其他邏輯應用程式。

5. 設定批次屬性。

   * **批次名稱**：接收者邏輯應用程式所定義的批次名稱，在本例中為 "TestBatch"，並會在執行階段進行驗證。

     > [!IMPORTANT]
     > 請確定您不要變更批次名稱，它必須符合接收者邏輯應用程式所指定的批次名稱。
     > 變更批次名稱會導致傳送者邏輯應用程式失敗。

   * **訊息內容**：您想要傳送給批次的訊息內容
   
   ![設定批次屬性](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. 儲存您的邏輯應用程式。 傳送者邏輯應用程式現在看起來應該類似這個範例：

   ![儲存您的傳送者邏輯應用程式](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>儲存邏輯應用程式

若要測試您的批次處理解決方案，請從 [Postman](https://www.getpostman.com/postman) 或類似的工具，將 X12 訊息張貼到您的傳送者邏輯應用程式。 不久之後，您的要求 Bin 中應該就會開始收到 X12 訊息 (以 5 個項目一批或每隔 10 分鐘的方式)，這些訊息全都具有相同的分割區索引鍵。

## <a name="next-steps"></a>後續步驟

* [以批次方式處理訊息](logic-apps-batch-process-send-receive-messages.md) 
* [在 Visual Studio 中使用 Azure Logic Apps 和 Functions 建置邏輯應用程式](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [適用於邏輯應用程式的例外狀況處理與記錄錯誤](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
