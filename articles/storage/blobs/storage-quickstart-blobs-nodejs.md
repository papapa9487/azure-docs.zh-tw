---
title: "Azure 快速入門 - 使用 Node.js 在 Azure Blob 儲存體之間傳送物件 | Microsoft Docs"
description: "快速了解使用 Node.js 在 Azure Blob 儲存體之間傳送物件"
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: dd4d3abf082767c40760d020c0997b365452e769
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>使用 Node.js 在 Azure Blob 儲存體之間傳送物件

在本快速入門中，您會了解如何使用 Node.js 在 Azure Blob 儲存體容器中上傳、下載及列出區塊 Blob。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* 安裝 [Node.js](https://nodejs.org/en/)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門使用的[應用程式範例](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)是基本的主控台應用程式。 

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟應用程式，請尋找並開啟 storage-blobs-node-quickstart 資料夾，然後按兩下 index.js。

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串

在應用程式，您必須提供儲存體帳戶的連接字串。 開啟 `index.js` 檔案，尋找 `connectionString` 變數。 將連接字串的整個值取代為從 Azure 入口網站儲存的連接字串。 您的儲存體連接字串應該如下所示：

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>安裝必要的套件

在應用程式目錄中，執行 `npm install` 安裝 `package.json` 檔案中列出的任何必要封裝。

```javascript
npm install
```

## <a name="run-the-sample"></a>執行範例

此範例會在 [我的文件] 中建立測試檔案、將它上傳至 Blob 儲存體、列出容器中的 Blob，然後以新名稱下載該檔案，以便比較新舊檔案。

輸入 `node index.js` 執行範例。 下列輸出來自 Windows 系統。  如果使用 Linux，會出現適當檔案路徑的類似輸出。

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

在繼續之前，請檢查 MyDocuments 找出這兩個檔案。 您可以開啟它們，並查看它們是否相同。

您也可以使用 [Azure 儲存體總管](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 之類的工具來檢視 Blob 儲存體中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。

確認檔案之後，請按任一鍵以完成示範並刪除測試檔案。 現在您已知道這個範例的工作內容，請開啟 index.js 檔案中查看程式碼。 

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，我們將透過範例程式碼來了解其運作方式。

### <a name="get-references-to-the-storage-objects"></a>取得儲存體物件的參考

第一件事是建立用來存取和管理 Blob 儲存體的 `BlobService` 參考。 這些物件是互為建置基礎，各自都為清單中的下一個物件所使用。

* 建立 **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)** 物件的執行個體，指向您儲存體帳戶中的 Blob 服務。

* 建立新的容器，然後設定容器上的權限，以便公開 Blob 並使用 URL 即可存取。 容器以 **quickstartcontainer-** 為開頭。

因為我們需要在每次執行範例時建立新的容器，所以此範例會使用 [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)。 在整個應用程式都使用相同容器的生產環境中，最好只呼叫一次 CreateIfNotExists。 或者，您可以事先建立容器，就不需要在程式碼中建立。

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 最常使用區塊 Blob。 這些適用於儲存文字和二進位資料，因此本快速入門使用這些。

若要將檔案上傳至 Blob，可以使用 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) 方法。 如果沒有 Blob，此作業會建立 Blob，如已存在，則予以覆寫。

範例程式碼會建立用於上傳和下載的本機檔案，將要上傳的檔案以 **localFileToUpload** 中的 Blob 名稱儲存為 **localPath**。 下列範例會將檔案上傳到以 **quickstartcontainer-** 開頭的容器。

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

有數個上傳方法可以搭配 Blob 儲存體使用。 例如，如果有記憶體資料流，則您可以使用 [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) 方法，而非使用 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) 方法。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

接著，應用程式會使用 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) 取得容器中的檔案清單。 下列程式碼會擷取 Blob 的清單，透過它們執行迴圈，顯示找到的 Blob URI。 您可以複製命令視窗中的 URI，將它貼入至瀏覽器來檢視檔案。

如果容器中有 5,000 個或更少的 Blob，呼叫一次 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) 即可擷取所有的 Blob 名稱。 如果容器中有 5000 個以上的 Blob，服務會以 5000 為一組擷取清單，直到擷取所有的 Blob 名稱。 因此第一次呼叫此 API 時，它會傳回第一組的 5000 個 Blob 名稱和接續權杖。 第二次，您要提供權杖，服務會擷取下一組的 Blob 名稱，一直反復到接續權杖為 null 為止，這表示已擷取所有的 Blob 名稱。

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>下載 Blob

使用 [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile) 方法，將 Blob 下載至本機磁碟。

下列程式碼會下載上一節中上傳的 Blob，將 "_DOWNLOADED" 尾碼新增至 Blob 名稱，以便您在本機磁碟上看到這兩個檔案。 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>清除資源

如果不再需要本快速入門中上傳的 Blob，您可以使用 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 和 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists) 刪除整個容器。 如果不再需要所建立的檔案，請一併刪除。 您按下 Enter 結束應用程式時，應用程式會負責處理這個動作。

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Node.js 在本機磁碟和 Azure Blob 儲存體之間傳輸檔案。 若要深入了解 Blob 儲存體的用法，請繼續閱讀 Blob 儲存體操作說明。

> [!div class="nextstepaction"]
> [Blob 儲存體作業的使用說明](storage-nodejs-how-to-use-blob-storage.md)

如需儲存體總管和 Blob 的詳細資訊，請參閱[使用儲存體總管管理 Azure Blob 儲存體資源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
