---
title: "Azure 事件格線與 Azure 入口網站的自訂事件 | Microsoft Docs"
description: "使用 Azure 事件格線和 PowerShell 來發佈主題，以及訂閱該事件。"
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 0fe498b7b6dcf59bc5caef8ff5a40053e0498f85
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>使用 Azure 入口網站和事件格線建立和路由傳送自訂事件

Azure Event Grid 是一項雲端事件服務。 在本文中，您可使用 Azure 入口網站建立自訂主題、訂閱主題，以及觸發事件來檢視結果。 一般而言，您可將事件傳送至可回應事件的端點，例如 Webhook 或 Azure Function。 不過，若要簡化這篇文章，您可將事件傳送至只會收集訊息的 URL。 使用名為 [RequestBin](https://requestb.in/) 的開放原始碼、第三方工具來建立此 URL。

>[!NOTE]
>**RequestBin** 是一個開放原始碼工具，不適用於高輸送量的使用方式。 在此使用工具單純用於示範。 如果您一次推送多個事件，則可能看不到工具中的所有事件。

當您完成時，您會看到事件資料已傳送至端點。

![事件資料](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>建立資源群組

Event Grid 為 Azure 資源，必須放入 Azure 資源群組中。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

1. 在左側導覽中，選取 [資源群組]。 然後選取 [新增]。

   ![建立資源群組](./media/custom-event-quickstart-portal/create-resource-group.png)

1. 將資源群組名稱設定為 *gridResourceGroup*，將位置設定為 *westus2*。 選取 [ **建立**]。

   ![提供資源群組值](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>建立自訂主題

主題會提供您張貼事件之使用者定義的端點。 

1. 若要在資源群組中建立主題，請選取 [更多服務]，並搜尋事件格線。 從可用的選項選取 [事件格線主題]。

   ![建立事件格線主題](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. 選取 [新增] 。

   ![新增事件格線主題](./media/custom-event-quickstart-portal/add-topic.png)

1. 提供主題的名稱。 主題名稱必須是唯一的，因為它由 DNS 項目表示。 在預覽版本中，Event Grid 支援 **westus2** 和 **westcentralus** 位置。 選取您先前建立的資源群組。 選取 [ **建立**]。

   ![提供事件格線主題值](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. 建立主題之後，請選取 [重新整理] 以查看主題。

   ![查看事件格線主題](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>建立訊息端點

訂閱主題之前，讓我們建立事件訊息的端點。 讓我們建立可收集訊息的端點，以便檢視訊息，而不需撰寫程式碼來回應事件。 RequestBin 是一個開放原始碼的第三方工具，可讓您建立端點，以及檢視傳送給它的要求。 移至 [RequestBin](https://requestb.in/)，然後按一下 [建立 RequestBin]。  複製 bin URL，因為您在訂閱主題時需要用到它。

## <a name="subscribe-to-a-topic"></a>訂閱主題

您可訂閱主題，告知 Event Grid 您想要追蹤的事件。 

1. 若要建立事件格線訂用帳戶，再次選取 [更多服務]，並搜尋事件格線。 從可用的選項選取 [事件格線訂用帳戶]。

   ![建立事件格線訂用帳戶](./media/custom-event-quickstart-portal/create-subscription.png)

1. 選取 [+ 事件訂用帳戶]。

   ![新增事件格線訂用帳戶](./media/custom-event-quickstart-portal/add-subscription.png)

1. 提供事件訂用帳戶的唯一名稱。 針對主題類型，選取 [事件格線主題]。 針對執行個體，選取您建立的自訂主題。 提供 RequestBin 的 URL 作為事件通知的端點。 提供值完畢後，選取 [建立]。

   ![提供事件格線訂用帳戶值](./media/custom-event-quickstart-portal/provide-subscription-values.png)

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 為了簡化這篇文章，我們使用 Cloud Shell 將範例事件資料傳送至主題。 一般而言，應用程式或 Azure 服務就會傳送事件資料。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>將事件傳送至主題

首先，讓我們取得主題的 URL 和金鑰。 將您的主題名稱用於 `<topic_name>`。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

下列範例可取得事件資料：

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

如果您 `echo "$body"`，您可以看到完整事件。 JSON 的 `data` 元素是您的事件承載。 任何語式正確的 JSON 都可以進入這個欄位。 您也可以使用主體欄位進行進階路由傳送或篩選。

CURL 是可執行 HTTP 要求的公用程式。 在本文中，我們會使用 CURL 將事件傳送到我們的主題。 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 瀏覽至您稍早建立的 RequestBin URL。 或者，按一下已開啟 RequestBin 瀏覽器中的重新整理。 您會看到剛傳送的事件。

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用此事件，請勿清除在此本文中建立的資源。 如果您不打算繼續，刪除您在本文建立的資源。

選取資源群組，然後選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

您現在知道如何建立主題和事件訂閱，深入了解 Event Grid 可協助您：

- [關於 Event Grid](overview.md)
- [將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md)
