---
title: "Azure 快速入門 - 使用 Azure CLI 在 Azure Blob 儲存體之間傳送物件 | Microsoft Docs"
description: "快速了解使用 Azure CLI 在 Azure Blob 儲存體之間傳送物件"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: tamram
ms.openlocfilehash: a300294c83cb206e6211985c736e3ff01bb1ab43
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>使用 Azure CLI 在 Azure Blob 儲存體之間傳送物件

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 此快速入門詳細說明使用 Azure CLI 在 Azure Blob 儲存體之間上傳和下載資料。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 容器可讓您組織 Blob 群組，就像在電腦的目錄中組織檔案一樣。

使用 [az storage container create](/cli/azure/storage/container#create) 命令，建立用於儲存 Blob 的容器。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>上傳 Blob

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 儲存在 Blob 儲存體中的大部分檔案是以區塊 Blob 儲存。 當資料必須新增到現有的 Blob，但不修改其現有的內容 (例如用於記錄) 時，則使用附加 Blob。 分頁 Blob 則是用來支援 IaaS 虛擬機器的 VHD 檔案。

在此範例中，我們將 Blob 上傳到使用 [az storage blob upload](/cli/azure/storage/blob#upload) 命令最後一個步驟所建立的容器。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

如果 Blob 不存在，此作業會予以建立，若已存在，則予以覆寫。 請上傳您所需的檔案數量，再繼續進行。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob#list) 命令列出容器中的 Blob。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>下載 Blob

使用 [az storage blob download](/cli/azure/storage/blob#download) 命令下載稍早之前上傳的 Blob。

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 進行資料傳輸

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 公用程式是適用於 Azure 儲存體的高效能可編寫指令碼資料轉送的另一個選項。 您可以使用 AzCopy 在 Blob、檔案和表格儲存體之間傳送資料。

這裡提供一個快速範例，利用 AzCopy 命令將名稱為 *myfile.txt* 的檔案上傳至 *mystoragecontainer* 容器。

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包括在本快速入門中所建立的儲存體帳戶)，請使用 [az group delete](/cli/azure/group#delete) 命令刪除資源群組。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何在本機磁碟和 Azure Blob 儲存體中的容器之間傳輸檔案。 若要深入了解在 Azure 儲存體中使用 Blob，請繼續進行使用 Azure Blob 儲存體的教學課程。

> [!div class="nextstepaction"]
> [作法：使用 Azure CLI 的 Blob 儲存體作業](storage-how-to-use-blobs-cli.md)
