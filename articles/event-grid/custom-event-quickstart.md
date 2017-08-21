---
title: "Azure Event Grid 的自訂事件 | Microsoft Docs"
description: "使用 Azure Event Grid 來發佈主題，以及訂閱該事件。"
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 08/15/2017
ms.topic: hero-article
ms.service: event-grid
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 5bfe08c249d01f5e01cd4cd82f609201da7eccbc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---

# <a name="create-and-route-custom-events-with-azure-event-grid"></a>使用 Azure Event Grid 建立和路由傳送自訂事件

Azure Event Grid 是一項雲端事件服務。 在本文中，您可使用 Azure CLI 建立自訂主題、訂閱主題，以及觸發事件來檢視結果。 一般而言，您可將事件傳送至可回應事件的端點，例如 Webhook 或 Azure Function。 不過，若要簡化這篇文章，您可將事件傳送至只會收集訊息的 URL。 使用名為 [RequestBin](https://requestb.in/) 的開放原始碼、第三方工具來建立此 URL。

當您完成時，您會看到事件資料已傳送至端點。

![事件資料](./media/custom-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行最新版的 Azure CLI (2.0.14 或更新版本)。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Event Grid 為 Azure 資源，必須放入 Azure 資源群組中。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 

下列範例會在 westus2 位置建立名為 gridResourceGroup 的資源群組。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>建立自訂主題

主題會提供您張貼事件之使用者定義的端點。 下列範例可在您的資源群組中建立主題。 以主題的唯一名稱取代 `<topic_name>`。 主題名稱必須是唯一的，因為它由 DNS 項目表示。 在預覽版本中，Event Grid 支援 **westus2** 和 **westcentralus** 位置。

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-a-message-endpoint"></a>建立訊息端點

訂閱主題之前，讓我們建立事件訊息的端點。 讓我們建立可收集訊息的端點，以便檢視訊息，而不需撰寫程式碼來回應事件。 RequestBin 是一個開放原始碼的第三方工具，可讓您建立端點，以及檢視傳送給它的要求。 移至 [RequestBin](https://requestb.in/)，然後按一下 [建立 RequestBin]。  複製 bin URL，因為您在訂閱主題時需要用到它。

## <a name="subscribe-to-a-topic"></a>訂閱主題

您可訂閱主題，告知 Event Grid 您想要追蹤的事件。 下列範例可訂閱您所建立的主題，從 RequestBin 傳遞 URL 作為事件通知的端點。 以您訂用帳戶的唯一名稱取代 `<event_subscription_name>`，並以上一節中的值取代 `<URL_from_RequestBin>`。 藉由在訂閱時指定端點，以便 Event Grid 將事件路由傳送至該端點。 對於 `<topic_name>`，使用您稍早建立的值。 

```azurecli-interactive
az eventgrid topic event-subscription create --name <event_subscription_name> \
  --endpoint <URL_from_RequestBin> \
  -g gridResourceGroup \
  --topic-name <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>將事件傳送至主題

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 首先，讓我們取得主題的 URL 和金鑰。 再次，將您的主題名稱用於 `<topic_name>`。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

為了簡化這篇文章，我們已設定要傳送至主題的範例事件資料。 一般而言，應用程式或 Azure 服務就會傳送事件資料。 下列範例可取得事件資料：

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
如果您打算繼續使用此事件，請勿清除在此本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>後續步驟

您現在知道如何建立主題和事件訂閱，深入了解 Event Grid 可協助您：

- [關於 Event Grid](overview.md)
- [使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md)
