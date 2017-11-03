---
title: "使用共用快照集 (預覽) | Microsoft Docs"
description: "Azure 共用快照集是在某個時間點拍攝的 Azure 檔案共用唯讀版本，是備份共用的一個方法。"
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>使用共用快照集 (預覽)
共用快照集 (預覽) 是在某個時間點拍攝的 Azure 檔案共用唯讀版本。 建立共用快照集之後，即可加以讀取、複製或刪除，但不能修改。 共用快照集提供在某個時間點備份共用的方法。 

在本文中，您將了解如何建立、管理和刪除共用快照集。 如需詳細資訊，請參閱[共用快照集概觀](storage-snapshots-files.md)或[快照集常見問題集](storage-files-faq.md)。

## <a name="create-a-share-snapshot"></a>建立共用快照集

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 或任何 Azure 儲存體 SDK 來建立共用快照集。 下列小節說明如何使用入口網站、CLI 和 PowerShell 來建立共用快照集。 

您可以在檔案共用使用中時，拍攝其共用快照集。 不過，共用快照集只會在發出共用快照集命令時，擷取已經寫入至檔案共用的資料。 這可能會排除任何應用程式或作業系統已經快取的任何資料。

### <a name="create-a-share-snapshot-by-using-the-portal"></a>使用入口網站來建立共用快照集  
如需建立時間點共用快照集，請前往入口網站中的檔案共用，然後選取 [建立快照集] 按鈕。

>   ![入口網站中的快照集功能表](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 建立共用快照集
您可以使用 `az storage share snapshot` 命令來建立共用快照集：

```azurecli-interactive
az storage share snapshot -n <share name>
```

範例輸出︰
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>使用 PowerShell 建立共用快照集
您可以使用 `$share.Snapshot()` 命令來建立共用快照集：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>執行常見的共用快照集作業

您可以使用 Windows 中的 [舊版] 索引標籤，透過 REST、用戶端程式庫、PowerShell 和入口網站，列舉與您檔案共用相關聯的共用快照集。 在掛接檔案共用之後，您可以使用 Windows 中的 [舊版] 索引標籤來檢視所有舊版的檔案。 

下列小節說明如何使用 Azure 入口網站、Windows 和 Azure CLI 2.0 列出、瀏覽，以及還原共用快照集。

### <a name="share-snapshot-operations-in-the-portal"></a>在入口網站中共用快照集作業

您可以在入口網站中查看檔案共用的所有共用快照集，並瀏覽共用快照集以檢視其內容。

#### <a name="view-a-share-snapshot"></a>檢視共用快照集
在檔案共用的 [快照集] 底下，選取 [檢視快照集]。

![入口網站中的「檢視快照集」命令](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>列出並瀏覽至共用快照集內容
檢視共用快照集的清單，然後選取所需時間戳記，即可直接瀏覽一個快照集的內容。

![在時間戳記清單中選取的快照集](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

您也可以選取 [列出快照集] 檢視上的 [連線] 按鈕，以取得 `net use` 命令，以及特定共用快照集的目錄路徑。 接著，您可以直接瀏覽至該快照集。


![將 [命令] 方塊與窗格連線](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>下載或從共用快照集還原
分別使用 [下載] 或 [還原] 按鈕，可從入口網站下載，或從快照集還原檔案。

![[下載] 及 [還原] 按鈕](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Windows 中的共用快照集作業
當您已經拍攝檔案共用的共用快照集時，可以從 Windows 上掛接的檔案共用檢視舊版的共用、目錄或特定檔案。 例如，以下是您可以使用 [舊版] 功能，在 Windows 中檢視和還原舊版目錄的方式。

> [!Note]  
> 您可以在共用層級和檔案層級上執行相同的作業。 只有包含該目錄或檔案變更的版本才會顯示在清單中。 如果特定目錄或檔案尚未在兩個共用快照集之間變更，共用快照集就會顯示在共用層級的舊版清單中，但不會顯示在目錄或檔案的舊版清單中。

#### <a name="mount-a-file-share"></a>掛接檔案共用
首先，使用 `net use` 命令掛接檔案共用。

#### <a name="open-a-mounted-share-in-file-explorer"></a>在 [檔案總管] 中開啟掛接的共用
移至 [檔案總管]，並尋找掛接的共用。

![在 [檔案總管] 中掛接的共用](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>列出舊版
瀏覽至需要還原的項目或父項目。 按兩下以移至所需的目錄。 按一下滑鼠右鍵，然後從功能表中選取 [屬性]。

![以滑鼠右鍵按一下功能表以取得選取的目錄](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

選取 [舊版]，以查看此目錄之共用快照集的清單。 視網路速度和目錄中的共用快照集數目而定，清單可能需要幾秒鐘的時間才能載入。

![[舊版] 索引標籤](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

您可以選取 [開啟]，開啟特定的快照集。 

![已開啟快照集](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>從舊版還原
選取 [還原] 可在共用快照集建立時，將整個目錄的內容以遞迴方式複製到原始位置。
 ![警告訊息中的 [還原] 按鈕](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 中的共用快照集作業
您可以使用 Azure CLI 2.0 執行作業，例如，列出共用快照集、瀏覽共用快照集內容、從共用快照集還原或下載檔案，或刪除共用快照集。

#### <a name="list-share-snapshots"></a>列出共用快照集

您可以使用 [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 搭配 `--include-snapshots`，列出特定共用的快照集：

```azurecli-interactive 
az storage share list --include-snapshots
```

此命令會為您提供共用快照集的清單，以及其所有相關聯的屬性。 以下是輸出的範例：

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>瀏覽至共用快照集
您可以使用 [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 瀏覽至特定的共用快照集並檢視其內容。 指定共用名稱，以及您需要瀏覽的時間戳記，如下列範例所示：

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

在輸出中，您會看到共用快照集的內容與該共用快照集建立時的共用內容相同：

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>從共用快照集還原

您可以從共用快照集複製或下載檔案，藉此還原檔案。 如下列範例所示，使用 `az storage file download` 命令：

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

在輸出中，您會看到已下載檔案的內容及其屬性與該共用快照集建立時的內容和屬性相同：

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-a-share-snapshot"></a>刪除共用快照集

您可以使用 Azure 入口網站、PowerShell、CLI、REST API 或任何儲存體 SDK，將共用快照集刪除。 下列小節說明如何使用 Azure 入口網站、CLI 和 PowerShell 將共用快照集刪除。

您可以瀏覽至共用快照集，並使用任何比較工具來檢視兩個快照集之間的差異。 接著，您可以決定需要刪除哪一個共用快照集。 

您無法刪除包含共用快照集的共用。 您必須先將其所有共用快照集刪除後，才能將共用刪除。

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>使用入口網站將共用快照集刪除  
您可以在入口網站中移至 [檔案共用] 刀鋒視窗，並使用 [刪除] 按鈕，以刪除一個或多個共用快照集。

>   ![刪除按鈕](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>使用 Azure CLI 2.0 刪除共用快照集
您可以使用 `[az storage share delete]` 命令來刪除共用快照集。 在下列範例中，使用 `--snapshot '2017-10-04T23:28:35.0000000Z' ` 參數的共用快照集時間戳記：

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

範例輸出︰
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>使用 PowerShell 刪除共用快照集
您可以使用 `Remove-AzureStorageShare -Share` 命令來刪除共用快照集：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>後續步驟
* [快照集概觀](storage-snapshots-files.md)
* [快照集常見問題集](storage-files-faq.md)
