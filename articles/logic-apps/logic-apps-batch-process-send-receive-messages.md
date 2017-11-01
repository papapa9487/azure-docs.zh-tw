---
title: "以群組或集合批次處理訊息 - Azure Logic Apps | Microsoft Docs"
description: "傳送和接收訊息以便在邏輯應用程式中批次處理"
keywords: "批次, 批次程序"
author: jonfancey
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
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: e0b7292f25a145c699dbafaf4e31e3f9d072b957
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2017
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>傳送、接收，並在邏輯應用程式中批次處理訊息

若要以群組一起處理訊息，您可以傳送資料項目或訊息到「批次」中，然後以批次處理這些項目。 當您想要確保資料項目會以特定方式分組，並一起處理時，這個方法會很有用。 

您可以建立邏輯應用程式，使用**批次**觸發程序以批次接收項目。 然後您可以建立邏輯應用程式，使用**批次**動作將項目傳送到批次中。

本主題示範如何透過執行這些工作，建置批次解決方案： 

* [建立邏輯應用程式，以批次接收並收集項目](#batch-receiver)。 這個「批次接收者」邏輯應用程式，會指定批次名稱，以及在接收者邏輯應用程式釋出並處理項目之前，要符合的釋出準則。 

* [建立邏輯應用程式，將項目傳送至批次](#batch-sender)。 這個「批次傳送者」邏輯應用程式，會指定要將項目傳送到何處，這個位置必須是現有的批次接收者邏輯應用程式。 您也可以指定唯一索引鍵，例如客戶編號，以便將目標批次「分割」為以該索引鍵為基礎的子集。 這樣一來，所有具有該索引鍵的項目都會一起收集並處理。 

## <a name="requirements"></a>需求

若要遵循此範例，您需要這些項目：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* [如何建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)的基本知識 

* 電子郵件帳戶與任何 [Azure Logic Apps 所支援的電子郵件提供者](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>建立以批次接收訊息的邏輯應用程式

在可以傳送訊息至批次之前，您必須先使用**批次**觸發程序建立「批次接收者」邏輯應用程式。 這樣一來，當您建立寄件者邏輯應用程式時，可以選取這個接收者邏輯應用程式。 對於接收者，您可以指定批次名稱、釋放準則和其他設定。 

傳送者邏輯應用程式需要知道將項目傳送到何處，而接收者邏輯應用程式不需要知道任何關於傳送者的訊息。

1. 在 [Azure 入口網站](https://portal.azure.com)，以名稱 "BatchReceiver" 建立邏輯應用程式 

2. 在 Logic Apps 設計工具中，新增**次**觸發程序，它會啟動您的邏輯應用程式工作流程。 在搜尋方塊中，輸入 "batch" 作為篩選條件。 選取此觸發程序：**批次 – 批次訊息**

   ![新增批次觸發程序](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. 提供批次的名稱，並指定釋放批次的準則，例如：

   * **批次名稱**：用來識別批次，在本例中為 "TestBatch"。
   * **發行準則**：批次發行準則，此準則可以訊息計數、排程作為基礎或同時以這兩者作為基礎。
   
     ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **訊息計數**：在釋放進行處理之前要保留為一個批次的訊息數目，在本例中為 "5"。

     ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **排程**：用於進行處理的批次發行排程，在此範例中為「每隔 5 分鐘」。

     ![提供批次觸發程序詳細資料](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. 新增另一個動作，在批次觸發程序引發時傳送電子郵件。 每次批次有五個項目或過去 5 分鐘時，邏輯應用程式便會傳送電子郵件。

   1. 在批次觸發程序下方，選擇 [+ 新增步驟] > [新增動作]。

   2. 在搜尋方塊中，輸入 "email" 作為篩選條件。
   根據您的電子郵件提供者，選取電子郵件連接器。
   
      例如，如果您有公司或學校帳戶，請選取 Office 365 Outlook 連接器。 
      如果您有 Gmail 帳戶，請選取 Gmail 連接器。

   3. 為您的連接器選取此動作： **{*電子郵件提供者*} - 傳送電子郵件**

      例如：

      ![為您的電子郵件提供者選取「傳送電子郵件」動作](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. 如果您先前未建立電子郵件提供者的連線，請提供您的電子郵件認證，以便當系統提示您時進行驗證。 深入了解[驗證您的電子郵件認證](../logic-apps/logic-apps-create-a-logic-app.md)。

6. 設定您剛才新增之動作的屬性。

   * 在 [收件者] 方塊中，輸入收件者的電子郵件地址。 
   為了測試用途，您可以使用自己的電子郵件地址。

   * 在 [主旨] 方塊中，當 [動態內容] 清單出現時，請選取 [資料分割名稱] 欄位。

     ![從 [動態內容] 清單中，選取 [資料分割名稱]](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     在稍後的區段中，您可以指定唯一的資料分割索引鍵，將目標批次分成邏輯的集合，以便可以傳送訊息至該處。 
     每個集合都有一個由傳送者邏輯應用程式產生的唯一數字。 
     這項功能可讓您使用具有多個子集的單一批次，並用您提供的名稱定義每個子集。

   * 在 [內文] 方塊中，當 [動態內容] 清單出現時，請選取 [訊息識別碼] 欄位。

     ![對於 [內文]，選取 [訊息識別碼]](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     因為傳送電子郵件動作的輸入是陣列，設計工具會自動在**傳送電子郵件**動作前後新增 **For each** 迴圈。 
     這個迴圈會對批次中的每個項目執行內部動作。 
     所以，當批次觸發程序設定為五個項目時，每次引發觸發程序您便會收到五封電子郵件。

7.  既然您建立了批次接收者邏輯應用程式，請儲存您的邏輯應用程式。

    ![儲存您的邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > 資料分割有 5,000 則訊息或 80 MB 的限制。 如果符合其中一個條件，即可能釋出批次 (即使不符合使用者定義的條件)。


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>建立傳送訊息至批次的邏輯應用程式

現在建立一或多個將項目傳送給接收者邏輯應用程式所定義之批次的邏輯應用程式。 對於傳送者，您指定接收者邏輯應用程式和批次名稱、訊息內容，和任何其他設定。 您可以選擇性地提供唯一的資料分割索引鍵，將批次分成子集來收集具有該索引鍵的項目。

傳送者邏輯應用程式需要知道將項目傳送到何處，而接收者邏輯應用程式不需要知道任何關於傳送者的訊息。

1. 建立具有名稱 "BatchSender" 的另一個邏輯應用程式

   1. 在搜尋方塊中，輸入 "recurrence" 作為篩選條件。 
   選取此觸發程序：**排程 - 重複**

      ![新增 "Schedule-Recurrence" 觸發程序](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. 設定頻率和間隔，每分鐘執行傳送者邏輯應用程式。

      ![設定重複觸發程序的頻率和間隔](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. 新增新的步驟，將訊息傳送至批次。

   1. 在重複觸發程序下方，選擇 [+ 新增步驟] > [新增動作]。

   2. 在搜尋方塊中，輸入 "batch" 作為篩選條件。 

   3. 選取此動作：**將訊息傳送給批次 – 選擇包含批次觸發程序的 Logic Apps 工作流程**

      ![選取 [將訊息傳送給批次]](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. 現在，選取您先前建立的 "BatchReceiver" 邏輯應用程式，它現在會顯示為動作。

      ![選取「批次接收者」邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > 此清單也會顯示有批次觸發程序的任何其他邏輯應用程式。

3. 設定批次屬性。

   * **批次名稱**：接收者邏輯應用程式所定義的批次名稱，在本例中為 "TestBatch"，並會在執行階段進行驗證。

     > [!IMPORTANT]
     > 請確定您不要變更批次名稱，它必須符合接收者邏輯應用程式所指定的批次名稱。
     > 變更批次名稱會導致傳送者邏輯應用程式失敗。

   * **訊息內容**：您想要傳送的訊息內容。 
   針對此範例，請新增此運算式，它會將目前日期和時間插入到您傳送給批次的訊息內容：

     1. 當 [動態內容] 清單出現時，選擇 [運算式]。 
     2. 輸入運算式 **utcnow()**，然後選擇 [確定]。 

        ![在 [訊息內容] 中，選擇 [運算式]。 輸入"utcnow()"。](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. 現在設定批次的資料分割。 在 "BatchReceiver" 動作中，選擇 [顯示進階選項]。

   * **資料分割名稱**：要用於分割目標批次的選擇性唯一資料分割索引鍵。 針對此範例，請新增運算式，產生介於 1 到 5 的隨機數字。
   
     1. 當 [動態內容] 清單出現時，選擇 [運算式]。
     2. 輸入此運算式：**rand(1,6)**

        ![設定目標批次的資料分割](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        這個 **rand** 函式會產生一到五之間的數字。 
        因此您會將此批次分割成五個有編號的資料分割，此運算式會以動態方式設定。

   * **訊息識別碼**：選擇性的訊息識別碼，空白時是一個產生的 GUID。 
   針對此範例，請將這個方塊保留空白。

5. 儲存您的邏輯應用程式。 傳送者邏輯應用程式現在看起來應該類似這個範例：

   ![儲存您的傳送者邏輯應用程式](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>儲存邏輯應用程式

若要測試批次處理解決方案，請讓邏輯應用程式執行幾分鐘的時間。 很快，您會開始收到以五封為單位的電子郵件群組，全都具有相同的資料分割索引鍵。

BatchSender 邏輯應用程式會每分鐘執行、產生介於 1 到 5 的隨機數字，並使用這個產生的數字為訊息傳送目標批次的資料分割索引鍵。 每次批次有五個項目具有相同的資料分割索引鍵時，BatchReceiver 邏輯應用程式便會引發，並針對每個訊息傳送郵件。

> [!IMPORTANT]
> 完成測試時，請確定您停用 BatchSender 邏輯應用程式，以停止傳送訊息，並避免造成收件匣過多郵件。

## <a name="next-steps"></a>後續步驟

* [使用 JSON 建置於邏輯應用程式定義上](../logic-apps/logic-apps-author-definitions.md)
* [在 Visual Studio 中使用 Azure Logic Apps 和 Functions 建置邏輯應用程式](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [適用於邏輯應用程式的例外狀況處理與記錄錯誤](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
