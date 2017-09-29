---
title: "將 Azure Blob 儲存體事件路由傳送至自訂 Web 端點 (預覽) | Microsoft Docs"
description: "使用 Azure Event Grid 以訂閱 Blob 儲存體事件。"
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: article
ms.service: storage
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: a68d5c4ee8ad69cd888765a96566a7ca6c13cff3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---

# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>將 Blob 儲存體事件路由至自訂的 Web 端點 (預覽)

Azure Event Grid 是一項雲端事件服務。 在本文中，您可使用 Azure CLI 訂閱 Blob 儲存體事件，以及觸發事件來檢視結果。 

> [!IMPORTANT]
> 您必須註冊，Blob 儲存體事件預覽才能完成本教學課程。  在[這裡](storage-blob-event-overview.md#join-the-preview)深入了解預覽方案。

一般而言，您可將事件傳送至可回應事件的端點，例如 Webhook 或 Azure Function。 為了簡化本文中所示的範例，我們將事件傳送至只會收集訊息的 URL。 使用名為 [RequestBin](https://requestb.in/) 的開放原始碼、第三方工具來建立此 URL。

> [!NOTE]
> **RequestBin** 是一個開放原始碼工具，不適用於高輸送量的使用方式。 在此使用工具單純用於示範。 如果您一次推送多個事件，則可能看不到工具中的所有事件。

當您完成本文所述的步驟時，您會看到事件資料已傳送至端點。

![事件資料](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行最新版的 Azure CLI (2.0.14 或更新版本)。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Event Grid 為 Azure 資源，必須放入 Azure 資源群組中。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。 

下列範例會在 *westcentralus* 位置建立名為 `<resource_group_name>` 的資源群組。  以資源群組的唯一名稱取代 `<resource_group_name>`。

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>建立 Blob 儲存體帳戶

若要使用 Azure 儲存體，您需要儲存體帳戶。  Blob 儲存體事件目前只可用於 Blob 儲存體帳戶。

Blob 儲存體帳戶是特殊的儲存體帳戶，可將非結構化資料儲存為 Azure 儲存體中的 Blob (物件)。 Blob 儲存體帳戶類似於現有的一般用途儲存體帳戶，可共用所有強大的持續性、可用性、延展性以及您現今使用的效能功能，包括區塊 Blob 和附加 Blob 的 100% API 一致性。 對於只需要封鎖或附加 Blob 儲存體的應用程式，我們建議使用 Blob 儲存體帳戶。

> [!NOTE]
> 對於預覽版本，Blob 儲存體事件僅適用於 **westcentralus** 位置的儲存體帳戶。

以儲存體帳戶的唯一名稱取代 `<storage_account_name>`，並以您稍早建立的資源群組取代 `<resource_group_name>`。

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>建立訊息端點

從 Blob 儲存體帳戶訂閱事件之前，讓我們建立事件訊息的端點。 我們將建立可收集訊息的端點，以便檢視訊息，而不需撰寫程式碼來回應事件。 RequestBin 是一個開放原始碼的第三方工具，可讓您建立端點，以及檢視傳送給它的要求。 移至 [RequestBin](https://requestb.in/)，然後按一下 [建立 RequestBin]。  複製 bin URL，因為您在訂閱主題時需要用到它。

## <a name="subscribe-to-your-blob-storage-account"></a>訂閱您的 Blob 儲存體帳戶

您可訂閱主題，告知 Event Grid 您想要追蹤的事件。下列範例可訂閱您所建立的 Blob 儲存體帳戶，並從 RequestBin 傳遞 URL 作為事件通知的端點。 以事件訂用帳戶的唯一名稱取代 `<event_subscription_name>`，並以上一節中的值取代 `<URL_from_RequestBin>`。 藉由在訂閱時指定端點，以便 Event Grid 將事件路由傳送至該端點。 對於 `<resource_group_name>` 和 `<storage_account_name>`，使用您稍早建立的值。 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
```

## <a name="trigger-an-event-from-blob-storage"></a>從 Blob 儲存體觸發事件

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 首先，我們要設定儲存體帳戶的名稱和金鑰，然後建立容器，接著建立並上傳檔案。 同樣地，使用您稍早建立之 `<storage_account_name>` 和 `<resource_group_name>` 的值。

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 瀏覽至您稍早建立的 RequestBin URL。 或者，按一下已開啟 RequestBin 瀏覽器中的重新整理。 您會看到剛傳送的事件。 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  }
}]

```

## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此儲存體帳戶和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

以您在上面建立的資源群組取代 `<resource_group_name>`。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>後續步驟

您現在知道如何建立主題和事件訂用帳戶，請深入了解 Blob 儲存體事件以及 Event Grid 如何協助您：

- [回應 Blob 儲存體事件](storage-blob-event-overview.md)
- [關於 Event Grid](../../event-grid/overview.md)

