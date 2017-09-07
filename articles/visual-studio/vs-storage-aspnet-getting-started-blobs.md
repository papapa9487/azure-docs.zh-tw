---
title: "開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET) | Microsoft Docs"
description: "在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何於 Visual Studio 的 ASP.NET專案中開始使用 Azure Blob 儲存體"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraig
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b4390fe96d8e4c25ff05c5514e0a08d1392b6641
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體是可將非結構化的資料儲存在雲端作為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為物件儲存體。

本教學課程說明如何使用 Azure Blob 儲存體撰寫一些常見案例的 ASP.NET 程式碼。 案例包括建立 blob 容器，以及上傳、列出、下載和刪除 blob。

##<a name="prerequisites"></a>必要條件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>建立 MVC 控制器 

1. 在 [方案總管] 中，用滑鼠右鍵按一下 [控制器]，然後從內容功能表中選取 [新增] > [控制器]。

    ![將控制器新增至 ASP.NET MVC 應用程式](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. 在 [Add Scaffold] 對話方塊中，按一下 [MVC 5 Controller - Empty]然後選取 [新增]。

    ![指定 MVC 控制器類型](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. 在 [新增控制器] 對話方塊中，將控制器命名為 BlobsController，然後選取 [新增]。

    ![命名 MVC 控制器](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. 將下列 using 指示詞新增至 `BlobsController.cs` 檔案：

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>建立 Blob 容器

Blob 容器是 blob 和資料夾的巢狀階層。 下列步驟說明如何建立 blob 容器：

> [!NOTE]
> 
> 本章節中的程式碼假設您已完成＜[設定開發環境](#set-up-the-development-environment)＞章節中的步驟。 

1. 開啟 `BlobsController.cs` 檔案。

1. 新增名為 **CreateBlobContainer** 的方法，其會傳回 **ActionResult**。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. 在 **CreateBlobContainer** 方法，取得 **CloudStorageAccount**物件，其代表您的儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得儲存體連接字串和儲存體帳戶資訊。 (將 &lt;storage-account-name> 變更為您正在存取的 Azure 儲存體帳戶名稱。)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 取得 **CloudBlobClient** 物件，代表 Blob 服務用戶端。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 取得 **CloudBlobContainer** 物件，代表所需 blob 容器名稱的參考。 **CloudBlobClient.GetContainerReference** 方法不會進行對 blob 儲存體的要求。 無論 blob 容器是否存在都會傳回參考。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 呼叫 **CloudBlobContainer.CreateIfNotExists** 方法來建立容器 (如果尚不存在)。 如果容器不存在且已成功建立，則 **CloudBlobContainer.CreateIfNotExists** 方法會傳回 **true**。 否則，會傳回 **false**。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. 使用 blob 容器的名稱更新 **ViewBag**。

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. 在 [方案總管] 中，展開 [檢視] 資料夾、用滑鼠右鍵按一下 [blob]，然後從內容功能表中選取 [新增] > [已連接的服務]。

1. 在 [新增檢視] 對話方塊中，針對檢視名稱輸入 **CreateBlobContainer**，然後選取 [新增]。

1. 開啟 `CreateBlobContainer.cshtml` 並加以修改，以便其如下列程式碼片段所示：

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. 在 [方案總管] 中，展開 **檢視-> 共用** 資料夾，然後開啟 `_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. 執行應用程式，並選取 **Create Blob Container** 來查看類似下列螢幕擷取畫面的結果︰
  
    ![建立 Blob 容器](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    如前所述，僅當容器不存在且已建立時，**CloudBlobContainer.CreateIfNotExists** 方法才會傳回 **true**。 因此，如果您在容器已存在時執行應用程式，此方法會傳回 **false**。 若要多次執行應用程式，您必須先刪除容器後，才能再次執行應用程式。 可以透過 **CloudBlobContainer.Delete** 方法完成刪除容器。 您也可以使用 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)或 [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)來刪除容器。  

## <a name="upload-a-blob-into-a-blob-container"></a>將 Blob 上傳至 blob 容器

一旦您[建立 blob 容器](#create-a-blob-container)後，可以將檔案上傳至該容器。 本節將逐步指示將本機檔案上傳至 blob 容器。 步驟假設您已建立名為 *test-blob-container* 的 blob 容器。 

> [!NOTE]
> 
> 本章節中的程式碼假設您已完成＜[設定開發環境](#set-up-the-development-environment)＞章節中的步驟。 

1. 開啟 `BlobsController.cs` 檔案。

1. 新增名為 **UploadBlob** 的方法，其會傳回 **EmptyResult**。

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. 在 **UploadBlob** 方法內，取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊：(將 &lt;storage-account-name> 變更為您要存取之 Azure 儲存體帳戶的名稱。)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 取得 **CloudBlobClient** 物件，代表 Blob 服務用戶端。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 取得 **CloudBlobContainer** 物件，代表 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 如稍早所述，Azure 儲存體支援不同的 blob 類型。 若要擷取分頁 blob 的參考，請呼叫 **CloudBlobContainer.GetPageBlobReference** 方法。 若要擷取區塊 Blob 的參考，請呼叫 **CloudBlobContainer.GetBlockBlobReference** 方法。 通常，建議使用區塊 Blob 的類型。 (將 <blob-name>* 變更為您在上傳後要提供給 blob 的名稱。)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. 擁有 Blob 參照後，即可藉由呼叫物件的 **UploadFromStream** 方法，將任何資料流上傳至其中。 如果 Blob 不存在，**UploadFromStream**方法會予以建立，若已存在，則予以覆寫。 (將 *&lt;file-to-upload>* 變更為想要上傳檔案的完整路徑。)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. 在 [方案總管] 中，展開 [檢視] 資料夾、用滑鼠右鍵按一下 [blob]，然後從內容功能表中選取 [新增] > [已連接的服務]。

1. 在 [方案總管] 中，展開 **檢視-> 共用** 資料夾，然後開啟 `_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取 [上傳 blob]。  
  
本章節 - [列出 blob 容器中的 blob](#list-the-blobs-in-a-blob-container) - 說明如何列出 blob 容器中的 blob。    

## <a name="list-the-blobs-in-a-blob-container"></a>列出 blob 容器中的 blob

下一節將說明如何列出 blob 容器中的 blob。 範例程式碼參考在區段中建立的 *test-blob-container*，[建立 blob 容器](#create-a-blob-container)。

> [!NOTE]
> 
> 本章節中的程式碼假設您已完成＜[設定開發環境](#set-up-the-development-environment)＞章節中的步驟。 

1. 開啟 `BlobsController.cs` 檔案。

1. 新增名為 **ListBlobs** 的方法，其會傳回 **ActionResult**。

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. 在 **ListBlobs** 方法內，取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊：(將 &lt;storage-account-name> 變更為您要存取之 Azure 儲存體帳戶的名稱。)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 取得 **CloudBlobClient** 物件，代表 Blob 服務用戶端。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 取得 **CloudBlobContainer** 物件，代表 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 若要列出 blob 容器中的 Blob，請使用 **CloudBlobContainer.ListBlobs**方法。 **CloudBlobContainer.ListBlobs** 會傳回您轉換成 **CloudBlockBlob****CloudPageBlob** 或 **CloudBlobDirectory** 物件的 **IListBlobItem** 物件。 下列程式碼片段列舉 blob 容器中的所有 blob。 每個 blob 會根據其類型轉型為適當的物件，且其名稱 (或是在 **CloudBlobDirectory** 中案例中之 URI) 會新增至清單。

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    除了 blob，blob 容器可以包含目錄。 讓我們假設您有名為 *test-blob-container* 的 blob 容器與下列階層︰

        foo.png
        dir1/bar.png
        dir2/baz.png

    使用上述的程式碼範例，**blob** 字串清單包含類似下列的值︰

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    如您所見，此清單只包含最上層實體；而不包含巢狀的 (*bar.png* 和 *baz.png*)。 若要列出 blob 容器內的所有實體，您必須呼叫 **CloudBlobContainer.ListBlobs** 方法，並針對 **useFlatBlobListing** 參數傳入 **true**。    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    將 **useFlatBlobListing** 參數設為 **true** 會傳回 blob 容器中所有實體的簡單列表，並產生下列結果︰

        foo.png
        dir1/bar.png
        dir2/baz.png

1. 在 [方案總管] 中，展開 [檢視] 資料夾、用滑鼠右鍵按一下 [blob]，然後從內容功能表中選取 [新增] > [已連接的服務]。

1. 在 [新增檢視] 對話方塊中，針對檢視名稱輸入 **ListBlobs**，然後選取 [新增]。

1. 開啟 `ListBlobs.cshtml` 並加以修改，以便其如下列程式碼片段所示：

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. 在 [方案總管] 中，展開 **檢視-> 共用** 資料夾，然後開啟 `_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. 執行應用程式，並選取 **List blobs** 來查看類似下列螢幕擷取畫面的結果︰
  
    ![blob 列表](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>下載 Blob

本章節說明如何下載 blob，將它保存到本機儲存體或讀取到字串的內容。 範例程式碼參考在區段中建立的 *test-blob-container*，[建立 blob 容器](#create-a-blob-container)。

1. 開啟 `BlobsController.cs` 檔案。

1. 新增名為 **DownloadBlob** 的方法，其會傳回 **ActionResult**。

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. 在 **DownloadBlob** 方法內，取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊：(將 &lt;storage-account-name> 變更為您要存取之 Azure 儲存體帳戶的名稱。)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 取得 **CloudBlobClient** 物件，代表 Blob 服務用戶端。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 取得 **CloudBlobContainer** 物件，代表 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 藉由呼叫 **CloudBlobContainer.GetBlockBlobReference** 或 **CloudBlobContainer.GetPageBlobReference** 方法取得 blob 參考物件。 (將 &lt;blob-name> 變更為您要下載的 blob 名稱。)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. 若要下載 blob，請使用 **CloudBlockBlob.DownloadToStream** 或 **CloudPageBlob.DownloadToStream** 方法，視 blob 類型而定。 下列程式碼片段使用 **CloudBlockBlob.DownloadToStream** 方法將 Blob 內容傳送給資料流物件，可將該物件永久儲存成本機檔案：(將 &lt;local-file-name> 變更為代表您要下載 blob 之處的完整檔案名稱。) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. 在 [方案總管] 中，展開 **檢視-> 共用** 資料夾，然後開啟 `_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取 [下載 blob] 下載 blob。 在 **CloudBlobContainer.GetBlockBlobReference** 方法呼叫中指定的 blob 會下載至您在 **File.OpenWrite** 方法呼叫中指定的位置。 

## <a name="delete-blobs"></a>刪除 Blob

下列步驟說明如何刪除 blob：

> [!NOTE]
> 
> 本章節中的程式碼假設您已完成＜[設定開發環境](#set-up-the-development-environment)＞章節中的步驟。 

1. 開啟 `BlobsController.cs` 檔案。

1. 新增名為 **DeleteBlob** 的方法，其會傳回 **ActionResult**。

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。 使用下列程式碼從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊：(將 &lt;storage-account-name> 變更為您要存取之 Azure 儲存體帳戶的名稱。)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 取得 **CloudBlobClient** 物件，代表 Blob 服務用戶端。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 取得 **CloudBlobContainer** 物件，代表 blob 容器名稱的參考。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 藉由呼叫 **CloudBlobContainer.GetBlockBlobReference** 或 **CloudBlobContainer.GetPageBlobReference** 方法取得 blob 參考物件。 (將 &lt;blob-name> 變更為您要刪除的 blob 名稱。)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. 在 [方案總管] 中，展開 **檢視-> 共用** 資料夾，然後開啟 `_Layout.cshtml`。

1. 在最後一個 **Html.ActionLink** 之後，新增下列 **Html.ActionLink**：

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. 執行應用程式，並選取 [刪除 blob] 以刪除在 **CloudBlobContainer.GetBlockBlobReference** 方法呼叫中指定的 blob。 

## <a name="next-steps"></a>後續步驟
如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。

  * [開始使用 Azure 資料表儲存體和 Visual Studio 已連線的服務 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [開始使用 Azure 佇列儲存體和 Visual Studio 已連線的服務 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)

