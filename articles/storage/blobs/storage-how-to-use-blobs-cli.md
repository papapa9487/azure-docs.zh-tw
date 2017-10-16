---
title: "在 Azure Blob 儲存體 (物件儲存體) 上使用 Azure CLI 執行作業 | Microsoft Docs"
description: "了解如何上傳和下載 Azure Blob 儲存體中的 Blob，以及建構共用存取簽章 (SAS) 來管理儲存體帳戶中 Blob 的存取。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: marsma
ms.openlocfilehash: c37fc0b701b668ab6bb9213a487ec8baa33fe663
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>使用 Azure CLI 執行 Blob 儲存體作業

Azure Blob 儲存體是一項儲存大量非結構化物件資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 本教學指導涵蓋 Azure Blob 儲存體中的基本作業，例如上傳、下載和刪除 Blob。 您會了解如何：

> [!div class="checklist"]
> * 建立容器
> * 將檔案 (Blob) 上傳至容器
> * 列出容器中的 Blob
> * 從容器下載 Blob
> * 在儲存體帳戶之間複製 Blob
> * 刪除 Blob
> * 顯示和修改 Blob 屬性和中繼資料
> * 使用共用存取簽章 (SAS) 管理安全性

本教學課程需要 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>建立容器

容器類似於電腦上的目錄，可讓您將 Blob 群組組織在容器中，就像您將檔案組織在目錄中一樣。 儲存體帳戶可以具有任意數目的容器。 您可以在容器中儲存最多 500 TB 的 Blob 資料，這是儲存體帳戶中的資料量上限。

使用 [az storage container create](/cli/azure/storage/container#create) 命令，建立用於儲存 Blob 的容器。

```azurecli-interactive
az storage container create --name mystoragecontainer
```

容器名稱必須以字母或數字開頭，並且只能包含字母、數字和連字號 (-) 字元。 如需 Blob 和容器的其他命名規則，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

## <a name="enable-public-read-access-for-a-container"></a>啟用容器的公用讀取存取

新建立的容器預設為專用。 亦即，若沒有[共用存取簽章](#create-a-shared-access-signature-sas)或儲存體帳戶的存取金鑰，沒有人可以存取容器。 使用 Azure CLI，您可以將容器權限設為三個層級的其中一個來修改此行為：

| | |
|---|---|
| `--public-access off` | (預設) 沒有公用讀取存取 |
| `--public-access blob` | 僅限 Blob 的公用讀取存取 |
| `--public-access container` | Blob 的公用讀取存取可以列出容器中的 Blob |

將公用存取設為 `blob` 或 `container` 時，您可對網際網路上的任何人啟用唯讀存取。 例如，如果您想要在網站上顯示儲存為 Blob 的影像，您需要啟用公用讀取存取。 如果想要啟用讀取/寫入存取，您必須改用[共用存取簽章 (SAS)](#create-a-shared-access-signature-sas)。

使用 [az storage container set-permission](/cli/azure/storage/container#create) 命令，對您的容器啟用公用讀取存取。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>將 Blob 上傳至容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 區塊 blob 是最常用的 Blob 類型，儲存在 Azure 儲存體中。 必須將資料新增到現有的 Blob，無需修改其現有內容 (例如，用於記錄) 時，就會使用附加 Blob。 分頁 Blob 支援 IaaS 虛擬機器的 VHD 檔案。

在此範例中，我們將 Blob 上傳到使用 [az storage blob upload](/cli/azure/storage/blob#upload) 命令最後一個步驟所建立的容器。

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

如果 Blob 不存在，此作業會建立 Blob，若已存在，則會加以覆寫。 上傳數個檔案，讓您可在下一個步驟中列出 Blob 時，看到多個項目。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob#list) 命令列出容器中的 Blob。

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

範例輸出：

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>下載 Blob

使用 [az storage blob download](/cli/azure/storage/blob#download) 命令，下載您在上一個步驟上傳的 Blob。

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>在儲存體帳戶之間複製 Blob

您可以在儲存體帳戶內或在不同儲存體帳戶和區域之間，以非同步方式複製 Blob。

下列範例示範如何從一個儲存體帳戶複製 Blob 到另一個儲存體帳戶。 我們會先在來源儲存體帳戶中建立容器，指定對其 Blob 的公用讀取存取權。 接著，我們會將檔案上傳至容器，最後，將 Blob 從該容器複製到目的地儲存體帳戶中的容器。

在此範例中，目的地容器必須已存在於目的地儲存體帳戶中，複製作業才能成功。 此外，在 `--source-uri` 引數中指定的來源 Blob 必須包含共用存取簽章 (SAS) 權杖，或者可公開存取，如此範例所示。

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>刪除 Blob

使用 [az storage blob delete](/cli/azure/storage/blob#delete) 命令，刪除容器中的 Blob。

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>顯示和修改 Blob 屬性和中繼資料

每個 Blob 都有數個服務定義的屬性，您可以使用 [az storage blob show](/cli/azure/storage/blob#show) 命令來顯示它們，包括其名稱、類型、長度和其他項目。 您也可以使用 [az storage blob metadata update](/cli/azure/storage/blob/metadata#update) 命令，搭配您自己的屬性及其值設定 Blob。

在此範例中，我們首先顯示 Blob 的服務定義屬性，然後更新具有兩個我們自己的中繼資料屬性的 Blob。 最後，我們會使用 [az storage blob metadata show](/cli/azure/storage/blob/metadata#show) 命令，顯示 Blob 的中繼資料屬性及其值。

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>建立共用存取簽章 (SAS)

若要在無須提供儲存體帳戶存取金鑰的情況下，將限制存取授與儲存體帳戶中的物件，則可使用共用存取簽章 (SAS) 達成此目標。 若要產生 URI，允許存取私用資源，您可以建立具有所需權限和有效性範圍 （其有效期間）的 SAS 權杖，並將其當作查詢字串附加至資源的 URL，因此形成其完整的 SAS URI。 然後，具有此 SAS URI (資源的 URL 加上 SAS 權杖) 的任何人都可在 SAS 權杖的有效性範圍內存取它。 例如，您可以想要將私用 Blob 的讀取存取開放兩分鐘，讓某人可以檢視它。

在下列步驟中，您將停用容器的公用存取、測試僅限私用存取，然後產生並測試 SAS URI。

### <a name="disable-container-public-access"></a>停用容器公用存取

首先，將容器的存取層級設為 `off`。 這指定，若沒有共用存取簽章或儲存體帳戶存取金鑰，無法存取容器或其工具。

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>驗證私用存取

若要驗證沒有該容器中 Blob 的公用讀取存取，請使用 [az storage blob url](/cli/azure/storage/blob#url) 命令，取得其中一個 Blob 的 URL。

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

瀏覽至私用瀏覽器視窗中 Blob 的 URL。 這時會呈現 `ResourceNotFound` 錯誤，因為 Blob 是私用的，而且您並未包含共用存取簽章。

### <a name="create-a-sas-uri"></a>建立 SAS URI

現在我們將建立允許存取 Blob 的 SAS URI。 在下列範例中，我們首先使用 [az storage blob url](/cli/azure/storage/blob#url)，在一個變數中填入 Blob 的 URL，然後在另一個變數中填入使用 [az storage blob generate-sas](/cli/azure/storage/blob#generate-sas) 命令所產生的 SAS 權杖。 最後，串連這兩個 (由 `?` 查詢字串分隔符號分隔)，以輸出 Blob 的完整 SAS URI。

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>測試 SAS URI

在私用瀏覽器視窗中，瀏覽至您在上面程式碼片段中使用 `echo` 命令所顯示的完整 SAS URI。 此時，未呈現錯誤，因此您可以檢視或下載 Blob。

等久一點讓 URL 過期 (此範例中是兩分鐘)，然後瀏覽至另一個私用瀏覽器視窗中的 URL。 您現在會看到 `AuthenticationFailed` 錯誤，因為 SAS 權杖已過期。

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包括您已建立的儲存體帳戶，以及已在本教學指導中上傳的任何 Blob)，請使用 [az group delete](/cli/azure/group#delete) 命令刪除資源群組。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解使用 Azure 儲存體的基本概念：

> [!div class="checklist"]
> * 建立容器
> * 將檔案 (Blob) 上傳至容器
> * 列出容器中的 Blob
> * 從容器下載 Blob
> * 在儲存體帳戶之間複製 Blob
> * 刪除 Blob
> * 顯示和修改 Blob 屬性和中繼資料
> * 使用共用存取簽章 (SAS) 管理安全性

下列資源提供有關使用 Azure CLI，以及使用儲存體帳戶中資源的其他資訊。

* Azure CLI
  * [利用 Azure CLI 2.0 登入](/cli/azure/authenticate-azure-cli) - 了解使用 CLI 進行驗證的不同方法，包括透過[服務主體](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal)進行的非互動式登入，用於執行無人看管的 Azure CLI 指令碼。
  * [Azure CLI 2.0 命令參考](/cli/azure/)
* Microsoft Azure 儲存體總管
  * [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
