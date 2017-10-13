---
title: "在使用 PowerShell 的 Azure Blob 儲存體 (物件儲存體) 上執行作業 | Microsoft Docs"
description: "教學課程 - 在使用 PowerShell 的 Azure Blob 儲存體 (物件儲存體) 上執行作業"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 執行 Azure Blob 儲存體作業

Azure Blob 儲存體是一項儲存大量非結構化物件資料的服務 (例如文字或二進位資料)，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 本文件涵蓋 Azure Blob 儲存體中的基本作業，例如上傳、下載和刪除 Blob。 您會了解如何：

> [!div class="checklist"]
> * 建立容器 
> * 上傳 Blob
> * 列出容器中的 Blob 
> * 下載 Blob
> * 複製 Blob
> * 刪除 Blob
> * 檢視和設定 Blob 的中繼資料和屬性
> * 使用共用存取簽章管理安全性

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 容器類似於電腦上的目錄，可讓您將 Blob 群組組織在容器中，就像您在電腦上將檔案組織在資料夾中一樣。 一個儲存體帳戶的容器數目沒有限制，只受限於儲存體帳戶中佔用的空間數量 (最多 500TB)。 

當您建立容器時，您可以設定存取層級，以協助定義誰可以存取該容器中的 Blob。 例如，容器可以是私用容器 (存取層級 = `Off`)，這表示除非有共用存取簽章或儲存體帳戶的存取金鑰，否則沒有人可以存取容器。 如果建立容器時未指定存取層級，則預設為私用。

您可能希望可公開存取容器中的影像。 例如，如果想儲存要在網站上顯示的影像，您需要將影像設為公用。 在此情況下，您可以將容器存取層級設為 `blob`，任何人只要有 URL 指向該容器中 blob，都可以存取該 Blob。

若要建立容器，請設定容器名稱，然後建立容器，並將權限設為 'blob'。 容器名稱必須以字母或數字開頭，並且只能包含字母、數字和連字號 (-) 字元。 如需 Blob 和容器的其他命名規則，請參閱[命名和參考容器、Blob 及中繼資料](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

使用 [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) 建立新的容器。 將存取層級設為公用。 此範例中的容器名稱是 *howtoblob*。

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。  用來備份 IaaS VM 的 VHD 檔案是分頁 Blob。 附加 Blob 用於記錄，例如當您想要寫入檔案，並繼續新增更多資訊時。 儲存在 Blob 儲存體中的大部分檔案都是區塊 Blob。 

若要將檔案上傳至區塊 Blob，請取得容器參考，然後取得該容器中區塊 Blob 的參考。 取得 Blob 參考之後，即可使用 [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) 將資料上傳至 blob。 如果 Blob 不存在，此作業會建立 Blob，如已存在，則予以覆寫。

以下顯示如何將 Blob 上傳至容器。 首先，設定變數來指向本機電腦上檔案所在的目錄，再變數表示要上傳的檔案名稱。 當您想要重複執行相同的作業時，這很有用。 請上傳多個檔案，在列出容器中的 Blob 時，就會看到多個項目。

下列範例會將本機磁碟上 D:\\_TestImages 中的 Image001.jpg 和 Image002.png，載入至您剛建立的容器。

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

上傳其他檔案。 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

請上傳您所需的檔案數量，再繼續進行。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) 並選取要顯示的 Blob 名稱，以取得容器中的 Blob 清單。

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>下載 Blob

將 blob 下載到本機磁碟。 首先，設定變數指向 Blob 下載的本機目的地資料夾。 然後，針對要下載每個 Blob，設定名稱並呼叫 [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) 以下載 Blob。

此範例會將 Blob 複製到本機磁碟上的 D:\\_TestImages\Downloads。 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>複製 Blob

複製 Blob 時有幾個注意事項。 您可能只是以新名稱將 Blob 複製到相同位置。 您可能將 Blob 複製到不同的儲存體帳戶。 您可能將大型 Blob (例如 VHD 檔案) 複製到不同的儲存體帳戶。 這些都會以不同方式完成。

### <a name="simple-blob-copy"></a>簡單的 Blob 複製

您可以將容器中的其一個 Blob 複製成新的 Blob，以建立複本。 這個範例會將 Blob 以不同的名稱複製到相同的容器中，但您也可以輕易地建立另一個容器並將 Blob 複製到那個容器中。 這個範例示範如何使用 [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) 來複製 Blob。 您必須指定來源及目的地 Blob 名稱和容器名稱。

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>將 Blob 複製到不同的儲存體帳戶 

您可以將 Blob 複製到不同的儲存體帳戶。 例如，將其中一個 VM 的後端 VHD 檔案複製到不同的儲存體帳戶，以備份此檔案。 

設定第二個儲存體帳戶、擷取內容、在該儲存體帳戶中設定容器，然後執行複製。 這部分指令碼與上述指令碼幾乎完全相同，差別只在於使用第二個儲存體帳戶，而不是第一個儲存體帳戶。

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>以非同步方式複製非常大的 Blob

如果您要複製好幾 GB 的超大型 Blob，請利用非同步複製，先啟動複製作業，之後再回來檢查檢查狀態，直到完成為止。 這樣就不會在執行複製作業時動彈不得。

如果是在儲存體帳戶內複製，則複製速度非常快。 如果是在相同區域中的兩個儲存體帳戶之間複製，則複製也相當快。 不過，如果來源和目的地位於不同區域，則可能需要數小時才能完成複製，視檔案的距離和大小而定。 

此指令碼使用上一個複製範例中定義的相同變數。 差別在於這次是擷取複製狀態，而且每隔 10 秒重複查詢，直到完成為止。 您可能需要將非常大的 Blob 上傳至儲存體帳戶，才能看到這經過多次反覆才完成。

使用 [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) 查詢複製狀態。 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>刪除 Blob

若要從儲存體帳戶中移除 Blob，請使用 [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob)。 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>讀取和寫入 Blob 的屬性及中繼資料

若要針對傳回的 **IListBlobItem** 存取一組屬性與方法，您必須先將它轉換成 **CloudBlockBlob**、**CloudPageBlob** 或 **CloudBlobDirectory** 物件。 如果不清楚類型，可使用類型檢查來決定要將其轉換至何種類型。 下列程式碼示範如何使用 [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) 並將結果轉換成 CloudBlockBlob 物件，以擷取並輸出先前上傳的其中一個 Blob 的輸出及屬性。

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

呼叫 FetchAttributes 填入系統屬性，然後檢視這些屬性。 某些屬性為可寫入，您可以變更其值。 這個範例示範如何變更內容類型，又稱為 MIME 類型。

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

每個 Blob 本身都有可設定的中繼資料。 例如，您可能會儲存上傳 Blob 的使用者名稱，或第一次上傳的日期/時間。 中繼資料由成對「索引鍵/值」組成。 可使用 PowerShell 來修改，如下所示。

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>管理 Blob 安全性 

根據預設，Azure 儲存體會限制只有帳戶擁有者 (擁有儲存體帳戶存取金鑰) 才能存取資料，以保持資料安全。 當您需要共用儲存體帳戶中的 Blob 資料時，請注意不可危及您帳戶存取金鑰的安全性。 若要這樣做，您可以使用共用存取簽章 URL，這是資產的 URL，其中包含查詢參數和安全性權杖，可將特定的權限層級開放一段特定時間。 例如，您可以將私用 Blob 的讀取存取開放 5 分鐘供人檢視。 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>將容器和其 Blob 的存取層級設為私用

首先，將容器的存取層級設為 `Off`，以指定沒有共用存取簽章或帳戶金鑰就無法存取。 使用 [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl)。

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>測試私用存取

若要確認您無法存取該容器中的 Blob，請建構其中一個 Blob 的 URL 但不使用共用存取簽章，然後嘗試檢視 Blob。 URL 的格式如下 (使用 HTTPS 通訊協定)：

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

以下顯示如何建立 Blob URL。

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

複製 Blob URL 並貼到私密瀏覽器視窗中 -- 將會顯示授權錯誤，因為 Blob 是私用，而且您未加進共用存取簽章。 

### <a name="create-the-sas-uri"></a>建立 SAS URI

建立 SAS URI -- 這是 Blob 的連結，其中包含組成 SAS 的查詢參數和安全性權杖。 將此 URI 貼到私密瀏覽視窗中 -- 將會顯示影像。 

首先，建立存取的開始日期/時間和到期日期/時間。 此範例使用 2 分鐘的時間。 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

使用 [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken)建立 SAS URI。 您需要容器名稱、Blob 名稱、儲存體帳戶內容、要授與的權限 (本例中是讀取、寫入和刪除)，以及存取的開始和結束時間。 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

複製 SAS URI 並貼到私密瀏覽器視窗中，將會顯示影像。

等久一點讓 URL 過期 (此範例中是 2 分鐘)，然後將 URL 貼到另一個 InPrivate 瀏覽器視窗中。 這次會發生授權錯誤，而且不會顯示圖片，因為 SAS URI 已過期。

## <a name="clean-up-resources"></a>清除資源

移除您已建立的所有資產。 若要這樣做，您可以移除資源群組，這也會刪除群組中包含的所有資源。 本例中會移除所有儲存體帳戶和資源群組本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解基本的 Blob 儲存體管理，像是如何：

> [!div class="checklist"]
> * 建立容器 
> * 上傳 Blob
> * 列出容器中的 Blob 
> * 下載 Blob
> * 複製 Blob
> * 刪除 Blob
> * 讀取和寫入 Blob 的中繼資料及屬性
> * 使用共用存取簽章管理安全性

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell 儲存體 Cmdlet
* [儲存體 PowerShell Cmdlet](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存體總管
* [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。