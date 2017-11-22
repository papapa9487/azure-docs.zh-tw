---
title: "管理 Azure 內容傳遞網路中 Azure Blob 儲存體的期限 | Microsoft Docs"
description: "深入了解選項，以控制 Azure CDN 快取中的 Blob 存留時間。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 41b8f9d439184b91f8105e6bd136e48525632a85
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>管理 Azure 內容傳遞網路中 Azure Blob 儲存體的期限
> [!div class="op_single_selector"]
> * [Azure Web Apps/雲端服務、ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob 儲存體](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Azure 儲存體](../storage/common/storage-introduction.md)中的 [blob 服務](../storage/common/storage-introduction.md#blob-storage)是數個已與 Azure 內容傳遞網路 (CDN) 整合之 Azure 型來源的其中一個。 任何可公開存取的 Blob 內容均可在 Azure CDN 中加以快取，直到其存留時間 (TTL) 結束。 TTL 是由來自 Azure 儲存體之 HTTP 回應中的 [`Cache-Control` 標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)所決定。

> [!TIP]
> 您可以選擇不替 blob 設定 TTL。 在此情況下，Azure CDN 會自動套用預設為期 7 天的 TTL。
> 
> 針對 Azure CDN 如何加快對 blob 和其他檔案的存取速度，如需詳細資訊請參閱 [Azure 內容傳遞網路概觀](cdn-overview.md)。
> 
> 若要深入了解 Azure Blob 儲存體，請參閱 [Blob 儲存體簡介](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction)。
 

本教學課程會示範幾種可對 Azure 儲存體中的 Blob 設定 TTL 的方法。  

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) 是其中一種最快速、最強大的 Azure 服務管理方式。  請使用 `Get-AzureStorageBlob` Cmdlet 來取得 Blob 的參考，然後設定 `.ICloudBlob.Properties.CacheControl` 屬性。 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> 您也可以使用 PowerShell 來[管理 CDN 設定檔和端點](cdn-manage-powershell.md)。
> 
> 

## <a name="azure-storage-client-library-for-net"></a>適用於 .NET 的 Azure 儲存體用戶端程式庫
若要使用 .NET 設定 Blob 的 TTL，請使用[適用於 .NET 的 Azure 儲存體用戶端程式庫](../storage/blobs/storage-dotnet-how-to-use-blobs.md)，來設定 [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 屬性。

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> [適用於 .NET 的 Azure Blob 儲存體範例](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)中另外還提供了許多 .NET 程式碼範例。
> 
> 

## <a name="other-methods"></a>其他方法
* [Azure 命令列介面](../cli-install-nodejs.md)
  
    上傳 blob 時，使用 `-p` 參數設定 *cacheControl* 屬性。 本範例將 TTL 設定為一小時 (3600 秒)。
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [Azure 儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    在[放置 Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)、[放置區塊清單](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)或[設定 Blob 屬性](https://msdn.microsoft.com/library/azure/ee691966.aspx)要求中，明確設定 *x-ms-blob-cache-control* 屬性。
* 協力廠商儲存體管理工具
  
    某些協力廠商 Azure 儲存體管理工具可讓您對 Blob 設定「CacheControl」  屬性。 

## <a name="testing-the-cache-control-header"></a>測試 Cache-Control 標頭
您可以輕鬆地確認 Blob 的 TTL。  使用瀏覽器的[開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，測試 blob 是否包含 `Cache-Control` 的回應標頭。 您也可以使用 **wget**、[Postman](https://www.getpostman.com/) 或 [Fiddler](http://www.telerik.com/fiddler) 之類的工具來檢查回應標頭。

## <a name="next-steps"></a>後續步驟
* [深入了解 `Cache-Control` 標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [了解如何在 Azure CDN 中管理雲端服務內容的到期](cdn-manage-expiration-of-cloud-service-content.md)

