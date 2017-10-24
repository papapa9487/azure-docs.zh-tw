---
title: "如何使用 Azure 檔案共用快照集 (預覽) | Microsoft Docs"
description: "使用 Azure 檔案共用快照集。 Azure 檔案共用快照集是在某個時間點拍攝的 Azure 檔案共用唯讀版本。 一旦建立共用快照集之後，即可加以讀取、複製或刪除，但不能修改。 共用快照集提供在某個時間點備份共用的方法。"
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
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>使用 Azure 檔案共用快照集 (預覽)
Azure 檔案共用快照集 (預覽) 是在某個時間點拍攝的 Azure 檔案共用唯讀版本。 一旦建立共用快照集之後，即可加以讀取、複製或刪除，但不能修改。 共用快照集提供在某個時間點備份共用的方法。 在本文中，我們將了解如何建立、管理和刪除 Azure 檔案共用快照集。 若要深入了解共用快照集，請參閱[共用快照集概觀](storage-snapshots-files.md)或[快照集常見問題集](storage-files-faq.md)

## <a name="create-azure-files-share-snapshots"></a>建立 Azure 檔案共用快照集

您可以使用入口網站、Powershell、CLI、REST 或任何儲存體 SDK，建立共用快照集。 下列幾節將會告訴您如何使用入口網站、CLI 和 Powershell 建立共用快照集。 

您可以在檔案共用使用中時，拍攝其共用快照集。 不過，共用快照集只會在發出共用快照集命令時，擷取已經寫入至 Azure 檔案共用的資料。 這可能會排除任何應用程式或作業系統已經快取的任何資料。

### <a name="create-share-snapshot-using-portal"></a>使用入口網站建立共用快照集  
您可以直接瀏覽至入口網站中的檔案共用，然後選取 `Create a Snapshot` 按鈕來建立時間點共用快照集。

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>使用 CLI 2.0 建立共用快照集
您可以使用 `az storage share snapshot` 命令來建立共用快照集：

```azurecli-interactive
az storage share snapshot -n <share name>
```

範例輸出
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

### <a name="create-share-snapshot-using-powershell"></a>使用 Powershell 建立共用快照集
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
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>列出共用快照集、瀏覽共用快照集內容，然後從快照集還原

您可以使用 Windows 中的「舊版」整合，透過 REST、用戶端程式庫、PowerShell 和入口網站，列舉與您檔案共用相關聯的共用快照集。 一旦掛接 Azure 檔案共用之後，您可以使用 SMB「舊版」整合，檢視所有舊版的檔案。 一旦掛接 Azure 檔案共用之後，您可以使用 SMB「舊版」整合，檢視所有舊版的目錄。 在下列幾節中，您將了解如何使用 Azure 入口網站、Windows 和 Azure CLI 2.0 列出、瀏覽，以及還原共用快照集。

### <a name="share-snapshot-operations-in-portal"></a>在入口網站中共用快照集作業

您可以在入口網站查看檔案共用的所有共用快照集，並瀏覽共用快照集以檢視其內容

#### <a name="view-share-snapshot"></a>檢視共用快照集
在檔案共用的 [快照集] 底下，選取 [檢視快照集]

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>列出並瀏覽共用快照集內容
檢視共用快照集的清單，然後選取所需時間戳記中的共用快照集，即可直接瀏覽其內容。

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

您也可以選取 [列出快照集] 檢視上的 [連接] 按鈕，以取得 `net use` 命令，以及您可以直接瀏覽之特定共用快照集的目錄路徑。


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>下載共用快照集或從其中還原
從入口網站內下載所需的檔案，或從快照集中還原。

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Windows 中的檔案共用快照集作業
當您已經拍攝檔案共用的共用快照集時，可以從 Windows 上掛接的 Azure 檔案共用檢視舊版的共用、目錄或特定檔案。 例如，以下是您可以使用 [舊版] 功能，在 Windows 中檢視和還原舊版特定目錄的方式：

> [!Note]  
> 在共用層級以及檔案層級可以完成的作業相同。 只有包含該目錄或檔案變更的版本才會顯示在清單中。 如果特定目錄或檔案尚未在兩個共用快照集之間變更，共用快照集只會顯示共用層級的舊版清單中，但不會顯示在目錄或檔案的舊版清單中。

#### <a name="mount-file-share"></a>掛接檔案共用
首先，使用 net use 命令掛接檔案共用。

#### <a name="open-mounted-share-in-explorer"></a>在 [檔案總管] 中開啟掛接的共用
移至 [檔案總管]，並尋找掛接的共用。

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>列出舊版
 瀏覽至需要還原的項目或父項目。 按兩下以瀏覽至所需的目錄。 按一下滑鼠右鍵，然後從功能表中選取 [屬性]

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

選取 [舊版]，以查看此目錄之共用快照集的清單。 視網路速度和共用快照集的數目而定，清單可能需要幾秒鐘的時間才能載入。

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

您可以選取 [開啟]，瀏覽特定的快照集 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>從舊版還原
**還原**可在共用快照集建立時，將整個目錄的內容以遞迴方式複製到原始位置。
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Azure CLI 2.0 中的檔案共用快照集作業
您可以使用 Azure CI 2.0 執行相同的作業，例如，列出共用快照集、瀏覽共用快照集內容、從共用快照集還原或下載檔案，或刪除共用快照集。

#### <a name="list-share-snapshots"></a>列出共用快照集

您可以使用 [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 搭配 `--include-snapshots`，列出特定共用的共用快照集

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>範例輸出
此命令將為您提供共用快照集的清單，以及其所有相關聯的屬性。

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

#### <a name="browse-share-snapshots"></a>瀏覽共用快照集
您也可以使用 [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) 瀏覽至特定的共用快照集，以檢視其內容。 您必須指定我們想要瀏覽至 `--snapshot '2017-10-04T19:45:18.0000000Z'` 的共用名稱 `--share-name` 與時間戳記

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>範例輸出

您將會看到共用快照集的內容與該共用快照集建立時的共用內容相同。

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
#### <a name="restore-from-share-snapshots"></a>從共用快照集還原

您可以使用 `az storage file download` 命令，從共用快照集複製或下載檔案，藉此還原檔案

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>範例輸出

您會看到已下載檔案的內容及其屬性與該共用快照集建立時的內容和屬性相同。

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

## <a name="delete-azure-files-share-snapshot"></a>刪除 Azure 檔案共用快照集

您可以使用 Azure 入口網站、PowerShell、CLI、REST API 或任何儲存體 SDK，刪除檔案共用快照集。 下列幾節將會告訴您如何使用 Azure 入口網站、CLI 和 Powershell 刪除共用快照集。

您可以瀏覽共用快照集，並使用任何比較工具檢視兩個共用快照集之間的差異，以決定您想要刪除的共用快照集。 

您無法刪除包含共用快照集的共用。 您必須先刪除其所有共用快照集，才能刪除共用。

### <a name="delete-share-snapshot-using-portal"></a>使用入口網站刪除共用快照集  
您可以瀏覽至入口網站中的檔案共用刀鋒視窗，並選取 `delete` 按鈕，以刪除一個或多個共用快照集。

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>使用 Azure CLI 2.0 刪除共用快照集
您可以使用 `[az storage share delete]` 命令，並提供具有共用快照集時間戳記的 `--snapshot '2017-10-04T23:28:35.0000000Z' ` 參數來刪除共用快照集：

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

範例輸出
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>使用 PowerShell 刪除共用快照集
您可以使用 `Remove-AzureStorageShare -Share` 命令來建立共用快照集：

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
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