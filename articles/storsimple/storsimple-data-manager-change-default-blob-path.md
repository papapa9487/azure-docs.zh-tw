---
title: "變更預設的 Blob 路徑 | Microsoft Docs"
description: "了解如何設定 Azure 函式來重新命名 Blob 檔案路徑 (私人預覽)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>變更預設的 Blob 路徑 (私人預覽)

此文章說明如何設定 Azure 函式來重新命名預設的 Blob 檔案路徑。 

## <a name="prerequisites"></a>必要條件

請確定您有已在資源群組內的混合式資料資源中正確設定的作業定義。

## <a name="create-an-azure-function"></a>建立 Azure 函式

若要建立 Azure 函式，請執行以下步驟：

1. 移至 [Azure 入口網站](http://portal.azure.com/)。

2. 在左窗格頂端按一下 [新增]。 

3. 在 [搜尋] 方塊中輸入**函式應用程式**，然後按 ENTER 鍵。

    ![在 [搜尋] 方塊中輸入「函式應用程式」](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. 在**結果**清單中按一下 [函式應用程式]。

    ![在結果清單中選取函式應用程式資源](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    [函式應用程式] 視窗隨即開啟。

5. 按一下 [建立] 。

    ![函式應用程式視窗的 [建立] 按鈕](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. 在 [函式應用程式] 組態刀鋒視窗中，執行下列動作︰

    a. 在 [應用程式名稱] 方塊中輸入應用程式名稱。
    
    b.這是另一個 C# 主控台應用程式。 在 [訂用帳戶] 方塊中輸入訂用帳戶名稱。

    c. 在**資源群組**下，按一下 [建立新項目]，然後輸入資源群組的名稱。

    d. 在 [主控方案] 方塊中輸入**取用方案**。

    e. 在 [位置]方塊中輸入位置。

    f. 在**儲存體帳戶**下選取現有的儲存體帳戶，或建立新的儲存體帳戶。 儲存體帳戶會在內部針對函式使用。

    ![輸入新的函式應用程式組態資料](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. 按一下 [建立] 。  
    函式應用程式隨即建立。

8. 在左窗格中，按一下 [更多服務]，然後執行下列動作︰
    
    a. 在 [篩選] 方塊中輸入**應用程式服務**。
    
    b. 按一下 [應用程式服務]。 

    ![在左側窗格中，選取 [更多服務]](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. 在應用程式服務的清單中，按一下函式應用程式的名稱。  
    函式應用程式頁面隨即開啟。

10. 在左窗格中，按一下 [新函式]，然後執行下列動作︰ 

    a. 在**語言**清單中，選取 **C#**。
    
    b.這是另一個 C# 主控台應用程式。 在範本圖格的陣列中，選取 **QueueTrigger CSharp**。

    c. 在 [為您的函式命名] 方塊中，輸入您函式的名稱。

    d. 在 [佇列名稱]方塊中，輸入您的資料轉換作業定義名稱。

    e. 在**儲存體帳戶連線**下，按一下 [新增]，然後選取對應至資料轉換作業的帳戶。  
        記下連線名稱。 稍後在 Azure 函式中需要此名稱。

       ![建立新的 C# 函式](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. 按一下 [建立] 。  
    [函式] 視窗隨即開啟。

11. 在 [函式]視窗中，執行 _.csx_ 檔案，並接著執行下列動作︰

    a. 貼上下列程式碼：

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b.這是另一個 C# 主控台應用程式。 使用您的儲存體帳戶連線，取代行 11 中的 **STORAGE_CONNECTIONNAME** (請參閱 8c)。

    c. 按一下左上角的 [儲存]。

    ![儲存函式](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 若要完成此函式，請透過執行以下動作再新增一個檔案︰

    a. 按一下 [檢視檔案]。

       ![[檢視檔案] 連結](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. 按一下 [新增]。
    
    c. 輸入 **project.json** 並按 Enter。
    
    d. 在 **project.json** 檔案中貼上下列程式碼：

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. 按一下 [儲存] 。

您已建立 Azure 函式。 每一次資料轉換作業產生新的 Blob，都會觸發此函式。

## <a name="next-steps"></a>後續步驟

[使用 StorSimple Data Manager UI 來轉換資料](storsimple-data-manager-ui.md)
