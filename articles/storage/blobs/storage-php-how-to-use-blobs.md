---
title: "如何使用 PHP 的 Blob 儲存體 (物件儲存體) | Microsoft Docs"
description: "使用 Azure Blob 儲存體 (物件儲存體) 在雲端中儲存非結構化資料。"
documentationcenter: php
services: storage
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 1af56b59-b3f0-4b46-8441-aab463ae088e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 9de2f7e81d75669267fe6448030c118d06b3f88a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-php"></a>如何使用 PHP 的 Blob 儲存體
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overview
Azure Blob 儲存體是可將非結構化的資料儲存在雲端作為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為物件儲存體。

本指南會示範如何使用 Azure Blob 服務執行一般案例。 這些範例均以 PHP 撰寫，並使用[適用於 PHP 的 Azure 儲存體用戶端程式庫][download]。 所涵蓋的案例包括**上傳**、**列出**、**下載**及**刪除** Blob。 如需 Blob 的詳細資訊，請參閱 [後續步驟](#next-steps) 一節。

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式
若要建立 PHP 應用程式並使其存取 Azure Blob 服務，唯一要求就是在您的程式碼中參考[適用於 PHP 的 Azure 儲存體用戶端程式庫][download]中的類別。 您可以使用任何開發工具來建立應用程式 (包括 [記事本])。

在本指南中，您會使用可從 PHP 應用程式內本機呼叫的 Blob 儲存體服務功能，或可在 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的服務功能。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端程式庫
[!INCLUDE [get-client-libraries](../../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>設定您的應用程式以存取 Blob 服務
若要使用 Azure Blob 服務 API，您必須：

1. 參考使用 [require_once] 陳述式的自動載入器檔案，以及
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動載入器檔案及參考 **ServicesBuilder** 類別。

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Common\ServicesBuilder;
```

在下列各範例中，一律會顯示 `require_once` 陳述式，但只會參考要執行之範例所需的類別。

## <a name="set-up-an-azure-storage-connection"></a>設定 Azure 儲存體連接
若要具現化 Azure Blob 服務用戶端，您必須具備有效的連接字串。 Blob 服務的連接字串格式為：

用於存取即時服務：

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

用於存取儲存體模擬器：

```php
UseDevelopmentStorage=true
```

若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。 您可以：

* 直接將連接字串傳遞給它，或
* 使用 Web 應用程式中的環境變數來儲存連接字串。 請參閱 [Azure Web 應用程式組態設定](../../app-service/web-sites-configure.md)文件來設定連接字串。

在本文的各範例中，將會直接傳遞連接字串。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);
```

## <a name="create-a-container"></a>建立容器
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

**BlobRestProxy** 物件可讓您使用 **createContainer** 方法建立 Blob 容器。 建立容器時，您可以在容器上設定選項，但這並非必要動作。 (下列範例顯示如何設定容器存取控制清單 (ACL) 和容器中繼資料。)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


// OPTIONAL: Set public access policy and metadata.
// Create container options object.
$createContainerOptions = new CreateContainerOptions();

// Set public access policy. Possible values are
// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
// CONTAINER_AND_BLOBS:
// Specifies full public read access for container and blob data.
// proxys can enumerate blobs within the container via anonymous
// request, but cannot enumerate containers within the storage account.
//
// BLOBS_ONLY:
// Specifies public read access for blobs. Blob data within this
// container can be read via anonymous request, but container data is not
// available. proxys cannot enumerate blobs within the container via
// anonymous request.
// If this value is not specified in the request, container data is
// private to the account owner.
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// Set container metadata.
$createContainerOptions->addMetaData("key1", "value1");
$createContainerOptions->addMetaData("key2", "value2");

try    {
    // Create container.
    $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

呼叫 **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** 可讓容器和 Blob 資料開放透過匿名要求來存取。 呼叫 **setPublicAccess(PublicAccessType::BLOBS_ONLY)** 可讓 Blob 資料開放透過匿名要求來存取。 如需容器 ACL 的詳細資訊，請參閱[設定容器 ACL (REST API)][container-acl]。

如需 Blob 服務錯誤碼的詳細資訊，請參閱 [Blob 服務錯誤碼][error-codes]。

## <a name="upload-a-blob-into-a-container"></a>將 Blob 上傳至容器
若要將檔案當作 Blob 上傳，請使用 **BlobRestProxy->createBlockBlob** 方法。 如果 Blob 不存在，此作業會予以建立，若已存在，則予以覆寫。 下列程式碼範例假設已建立容器，並使用 [fopen][fopen] 將檔案當作串流開啟。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

$content = fopen("c:\myfile.txt", "r");
$blob_name = "myblob";

try    {
    //Upload blob
    $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

請注意，前述範例會以串流方式上傳 Blob。 不過，也可以使用 [file\_get\_contents][file_get_contents] 之類的函式將 Blob 當作字串上傳。 若要使用前述範例執行這項操作，請將 `$content = fopen("c:\myfile.txt", "r");` 變更為 `$content = file_get_contents("c:\myfile.txt");`。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob
若要列出容器中的 Blob，請搭配使用 **BlobRestProxy->listBlobs** 方法與 **foreach** 迴圈，對結果進行迴圈。 下列程式碼會將容器中每個 Blob 的名稱作為輸出顯示，並將 URI 顯示於瀏覽器。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // List blobs.
    $blob_list = $blobRestProxy->listBlobs("mycontainer");
    $blobs = $blob_list->getBlobs();

    foreach($blobs as $blob)
    {
        echo $blob->getName().": ".$blob->getUrl()."<br />";
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="download-a-blob"></a>下載 Blob
若要下載 Blob，請呼叫 **BlobRestProxy->getBlob** 方法，然後在結果產生的 **GetBlobResult** 物件上呼叫 **getContentStream** 方法。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


try    {
    // Get blob.
    $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
    fpassthru($blob->getContentStream());
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

請注意，以上範例會以串流資源形式 (預設行為) 取得 Blob。 不過，您可以使用 [stream\_get\_contents][stream-get-contents] 函式將傳回的串流轉換成字串。

## <a name="delete-a-blob"></a>刪除 Blob
若要刪除 Blob，請將容器名稱和 Blob 名稱傳遞至 **BlobRestProxy->deleteBlob**。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // Delete blob.
    $blobRestProxy->deleteBlob("mycontainer", "myblob");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-a-blob-container"></a>刪除 Blob 容器
最後，若要刪除 Blob 容器，請將容器名稱傳遞至 **BlobRestProxy->deleteContainer**。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create blob REST proxy.
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

try    {
    // Delete container.
    $blobRestProxy->deleteContainer("mycontainer");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179439.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>後續步驟
了解 Azure Blob 服務的基礎概念之後，請參考下列連結以深入了解更複雜的儲存工作。

* 請瀏覽 [Azure 儲存體 PHP 用戶端程式庫的 API 參考](http://azure.github.io/azure-storage-php/)
* 請參閱[進階 Blob 範例](https://github.com/Azure/azure-storage-php/blob/master/samples/BlobSamples.php)。

如需詳細資訊，另請參閱 [PHP 開發人員中心](/develop/php/)。

[download]: https://github.com/Azure/azure-storage-php
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
