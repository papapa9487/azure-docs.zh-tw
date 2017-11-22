---
title: "Azure 快速入門 - 使用 Java 在 Azure Blob 儲存體之間傳送物件 | Microsoft Docs"
description: "快速了解使用 Java 在 Azure Blob 儲存體之間傳送物件"
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 2a825e59093b64ab56110f9a045b0325728b39b2
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>使用 Java 在 Azure Blob 儲存體之間傳送物件

在本快速入門中，您會了解如何使用 Java 在 Azure Blob 儲存體容器中上傳、下載及列出區塊 Blob。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* 安裝具有 Maven 整合的 IDE

* 或者，安裝並設定 Maven 從命令列運作

本教學課程使用具備「適用於 Java 開發人員的 Eclipse IDE」設定的 [Eclipse](http://www.eclipse.org/downloads/)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門使用的[應用程式範例](https://github.com/Azure-Samples/storage-blobs-java-quickstart)是基本的主控台應用程式。 

使用 [git](https://git-scm.com/) 將應用程式的複本下載至您的開發環境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

此命令會將存放庫複製到本機的 git 資料夾。 若要開啟專案，請啟動 Eclipse 並關閉歡迎畫面。 選取 [File] \(檔案\)，然後選取 [Open Projects from File System...] \(從檔案系統中開啟專案...\)。請確定已核取 [Detect and configure project natures] \(偵測並設定專案性質\)。 選取 [Directory] \(目錄\)，然後瀏覽至您儲存複製存放庫的位置，在其中選取 [javaBlobsQuickstart] 資料夾。 請確定 **javaBlobsQuickstarts** 專案顯示為 Eclipse 專案，然後選取 [Finish] \(完成\)。

當專案完成匯入之後，開啟 **AzureApp.java** (位於 **src/main/java** 內的 **blobQuickstart.blobAzureApp**)，並取代 `storageConnectionString` 字串內的 `accountname` 和 `accountkey`。 然後執行應用程式。
     

## <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串
    
在應用程式，您必須提供儲存體帳戶的連接字串。 開啟 **AzureApp.Java** 檔案。 找出 `storageConnectionString` 變數。 以您從 Azure 入口網站儲存的值取代連接字串中的 `AccountName` 和 `AccountKey` 值。 您的 `storageConnectionString` 看起來應該會像下面這樣：

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>執行範例

此範例會在您預設的目錄 (如果是 Windows 使用者，則是 [我的文件]) 中建立測試檔案、將它上傳至 Blob 儲存體、列出容器中的 Blob，然後以新名稱下載該檔案，以便比較新舊檔案。 

在 Eclipse 中按 **Ctrl+F11** 執行範例。

如果您想要在命令列使用 Maven 執行範例，請開啟殼層，然後導覽至複製目錄內的 **blobAzureApp**。 然後輸入 `mvn compile exec:java`。

如果您是在 Windows 上執行應用程式，以下是輸出的範例。

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 在繼續之前，請先檢查預設目錄 (如果是 Windows 使用者，則是 [我的文件]) 是否有這兩個檔案。 您可以開啟它們，並查看它們是否相同。 複製主控台視窗的 Blob URL，將它貼至瀏覽器以檢視 Blob 儲存體中的檔案內容。 當您按 Enter 鍵時，它會刪除儲存體容器和檔案。

您也可以使用 [Azure 儲存體總管](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 之類的工具來檢視 Blob 儲存體中的檔案。 Azure 儲存體總管是免費的跨平台工具，可讓您存取儲存體帳戶資訊。 

確認檔案之後，請按 Enter 鍵以完成示範並刪除測試檔案。 現在您已知道這個範例的功用，請開啟 **AzureApp.java** 檔案查看程式碼。 

## <a name="understand-the-sample-code"></a>了解範例程式碼

接下來，我們將透過範例程式碼來了解其運作方式。

### <a name="get-references-to-the-storage-objects"></a>取得儲存體物件的參考

第一件事是建立用來存取和管理 Blob 儲存體的物件參考。 這些物件是互為建置基礎，各自都為清單中的下一個物件所使用。

* 建立指向[儲存體帳戶](/java/api/com.microsoft.azure.management.storage._storage_account)之 **CloudStorageAccount** 物件的執行個體。

    **CloudStorageAccount** 物件是您儲存體帳戶的表示法，它可讓您以程式設計方式來設定和存取儲存體帳戶屬性。 使用 **CloudStorageAccount** 物件，您可以建立 **CloudBlobClient** 的執行個體，這是存取 Blob 服務的必要條件。

* 建立 **CloudBlobClient** 物件的執行個體，指向您儲存體帳戶中的 [Blob 服務](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client)。

    **CloudBlobClient** 能為您提供 Blob 服務的存取點，可讓您以程式設計方式來設定和存取 Blob 儲存體屬性。 使用 **CloudBlobClient**，您可以建立 **CloudBlobContainer** 物件的執行個體，這是建立容器的必要條件。

* 建立 **CloudBlobContainer** 物件的執行個體，代表您要存取的[容器](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container)。 容器是用來組織 Blob，就像在電腦上用資料夾組織檔案一樣。    

    只要您有 **CloudBlobContainer**，就可以建立 **CloudBlockBlob** 物件的執行個體，指向您感興趣的特定 [Blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) 並執行上傳、下載、複製等等作業。

> [!IMPORTANT]
> 容器名稱必須是小寫字母。 如需有關容器和 Blob 名稱的詳細資訊，請參閱[命名和參考容器、Blob 及中繼資料](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

### <a name="create-a-container"></a>建立容器 

在本節中，您要建立物件的執行個體、建立新的容器，然後設定容器上的權限，以便公開 Blob 並使用 URL 即可存取。 容器名為 **quickstartblobs**。 

因為我們需要在每次執行範例時建立新的容器，所以此範例會使用 **CreateIfNotExists**。 在整個應用程式都使用相同容器的生產環境中，最好只呼叫一次 **CreateIfNotExists**。 或者，您可以事先建立容器，就不需要在程式碼中建立。

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>將 Blob 上傳到容器

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 最常使用的是區塊 Blob，這也是本快速入門中所使用的。 

若要將檔案上傳至 Blob，請取得目標容器中的 Blob 參考。 取得 Blob 參考之後，即可使用 [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long) 將資料上傳至 Blob。 如果沒有 Blob，此作業會建立 Blob，如已存在，則予以覆寫。

範例程式碼會建立一個用於上傳和下載的本機檔案。 將要上傳的檔案儲存為 **source**，而 Blob 的名稱則為 **blob**。 下列範例會將檔案上傳到名為 **quickstartblobs** 的容器。

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

有數個[上傳方法](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob)可以搭配 Blob 儲存體使用。 例如，如果您有一個字串，便可以使用 UploadText 方法來取代 Upload 方法。 

區塊 Blob 可以是任何類型的文字或二進位檔案。 分頁 Blob 主要用於備份 IaaS VM 所用的 VHD 檔案。 附加 Blob 是用於記錄，例如當您想要寫入檔案，並繼續新增更多資訊時。 儲存在 Blob 儲存體中的大部分物件都是區塊 Blob。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

您可以使用 [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs) 來取得容器中的檔案清單。 下列程式碼會擷取 Blob 的清單，透過它們執行迴圈，顯示找到的 Blob URI。 您可以複製命令視窗中的 URI，將它貼入至瀏覽器來檢視檔案。

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>下載 Blob

使用 [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String) 將 Blob 下載到本機磁碟。

下列程式碼會下載上一節中上傳的 Blob，將 "_DOWNLOADED" 尾碼新增至 Blob 名稱，以便您在本機磁碟上看到這兩個檔案。 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>清除資源

如果不再需要本快速入門中上傳的 Blob，您可以使用 [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists) 刪除整個容器。 這也會刪除容器中的檔案。

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Java 在本機磁碟和 Azure Blob 儲存體之間傳送檔案。 若要深入了解 Blob 儲存體的用法，請繼續閱讀 Blob 儲存體操作說明。

> [!div class="nextstepaction"]
> [Blob 儲存體作業的使用說明](storage-java-how-to-use-blob-storage.md)

如需儲存體總管和 Blob 的詳細資訊，請參閱[使用儲存體總管管理 Azure Blob 儲存體資源](../../vs-azure-tools-storage-explorer-blobs.md)。
