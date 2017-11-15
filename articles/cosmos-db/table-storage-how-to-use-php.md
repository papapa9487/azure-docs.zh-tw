---
title: "如何使用 PHP 中的資料表儲存體 | Microsoft Docs"
description: "了解如何使用 PHP 的資料表服務來建立和刪除資料表，以及插入、刪除和查詢資料表。"
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 5cfdfc814d1dfc15149d7b100ba1f6b68e90bb36
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-from-php"></a>如何從 PHP 使用 Azure 資料表儲存體
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>概觀
本指南說明如何使用 Azure 資料表服務執行一般案例。 這些範例均是以 PHP 撰寫，並使用 [Azure SDK for PHP][download]。 所涵蓋的案例包括「建立和刪除資料表」以及「在資料表中插入、刪除及查詢實體」 。 如需有關 Azure 資料表服務的詳細資訊，請參閱 [後續步驟](#next-steps) 一節。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式
若要建立一個存取 Azure 資料表服務的 PHP 應用程式，唯一的要求就是從您的程式碼內參考 Azure SDK for PHP 中的類別。 您可以使用任何開發工具來建立應用程式 (包括 [記事本])。

在本指南中，您將使用可從 PHP 應用程式內本機呼叫的資料表服務功能，或可從 Azure Web 角色、背景工作角色或網站內執行的程式碼中呼叫的資料表服務功能。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端程式庫
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>設定讓您的應用程式存取資料表服務
若要使用 Azure 資料表服務 API，您必須：

1. 參考使用 [require_once][require_once] 陳述式的自動載入器檔案，以及
2. 參考任何您可能使用的類別。

下列範例顯示如何納入自動載入器檔案及參考 **ServicesBuilder** 類別。

> [!NOTE]
> 本文中的範例假設您已透過 Composer 安裝 PHP Client Libraries for Azure。 如果您是以手動方式安裝程式庫，則必須參考 <code>WindowsAzure.php</code> 自動載入器檔案。
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下列各範例中，一律會顯示 `require_once` 陳述式，但只會參考要執行之範例所需的類別。

## <a name="set-up-an-azure-storage-connection"></a>設定 Azure 儲存體連接
若要具現化 Azure 資料表服務用戶端，您必須先具備一個有效的連接字串。 資料表服務的連接字串格式為：

用於存取即時服務：

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

用於存取模擬器儲存體：

```php
UseDevelopmentStorage=true
```

若要建立任何 Azure 服務用戶端，您必須使用 **ServicesBuilder** 類別。 您可以：

* 直接將連接字串傳遞給它，或
* 使用 **CloudConfigurationManager (CCM)** 到多種外部來源檢查連接字串：
  * 預設已支援一種外部來源，即環境變數
  * 您可以擴充 **ConnectionStringSource** 類別以加入新來源

在本文的各範例中，將會直接傳遞連接字串。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>建立資料表
**TableRestProxy** 物件可讓您以 **createTable** 方法建立資料表。 建立資料表時，您可以設定資料表服務逾時值。 (如需有關表格服務逾時值的詳細資訊，請參閱[設定表格服務作業的逾時值][table-service-timeouts]。)

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

如需有關資料表名稱限制的資訊，請參閱[了解表格服務資料模型][table-data-model]。

## <a name="add-an-entity-to-a-table"></a>將實體加入至資料表
若要將實體新增至資料表，請建立一個新的 **Entity** 物件，然後將它傳遞給 **TableRestProxy->insertEntity**。 請注意，建立實體時，您必須指定 `PartitionKey` 和 `RowKey`。 這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。 系統使用 `PartitionKey` 自動將資料表的實體散發在許多儲存體節點上。 具有相同 `PartitionKey` 的實體會儲存在相同節點上。 (對儲存在同一節點上的多個實體執行作業，會比對儲存在不同節點上的實體執行作業有更佳的執行效果。)`RowKey` 是實體在分割內的唯一識別碼。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

如需有關資料表屬性和類型的資訊，請參閱[了解表格服務資料模型][table-data-model]。

**TableRestProxy** 類別提供兩種插入實體的替代方法：**insertOrMergeEntity** 和 **insertOrReplaceEntity**。 若要使用這些方法，請建立一個新的 **Entity** ，然後將它當做參數傳遞給其中一個方法。 只要實體不存在，每個方法都會插入實體。 如果實體已經存在，**insertOrMergeEntity** 會在屬性已經存在時更新屬性值，並在屬性不存在時新增屬性，而 **insertOrReplaceEntity** 則是會完全取代現有的實體。 下列範例示範如何使用 **insertOrMergeEntity**。 如果 `PartitionKey` 為「tasksSeattle」且 `RowKey` 為「1」的實體尚未存在，便會將之插入。 不過，如果先前已經插入 insertOrMergeEntity (如上述範例所示)，方法便會更新 `DueDate` 屬性並新增 `Status` 屬性。 `Description` 和 `Location` 屬性也會更新，但是所使用的值實際上會讓它們保持不變。 如果如範例中所示並未新增後面兩個屬性，但這兩個屬性存在於目標實體上，它們現有的值就會保持不變。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>擷取單一實體
**TableRestProxy-&gt;getEntity`RowKey` 方法可讓您透過查詢其** 和 `PartitionKey` 來擷取單一實體。 在以下範例中，會將分割區索引鍵 `tasksSeattle` 和資料列索引鍵 `1` 傳遞給 **getEntity** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>擷取資料分割中的所有實體
實體查詢使用篩選條件建構而成 (如需詳細資訊，請參閱[查詢資料表和實體][filters])。 若要擷取資料分割中的所有實體，請使用 "PartitionKey eq *partition_name*" 篩選條件。 下列範例示範如何透過將篩選條件傳遞給 **queryEntities** 方法來擷取 `tasksSeattle` 分割中的所有實體。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>擷取資料分割中實體的子集
前面範例中所使用的相同模式可用來擷取資料分割中的任何實體子集。 您所擷取的實體子集將取決於您使用的篩選條件 (如需詳細資訊，請參閱[查詢資料表和實體][filters])。下列範例示範如何使用篩選條件擷取位於特定 `Location` 且 `DueDate` 在指定日期之前的所有實體。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>擷取實體屬性的子集
查詢可以擷取實體屬性的子集。 這項稱為「投射」 的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。 若要指定要擷取的屬性，請將屬性的名稱傳遞給 **Query->addSelectField** 方法。 您可以呼叫此方法許多次以新增其他屬性。 執行 **TableRestProxy->queryEntities** 之後，傳回的實體將只具有選取的屬性。 (如果您想要傳回資料表實體的子集，請使用篩選條件，如上面的查詢所示。)

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>更新實體
若要更新現有的實體，可以對實體使用 **Entity->setProperty** 和 **Entity->addProperty** 方法，然後呼叫 **TableRestProxy->updateEntity**。 下列範例會擷取一個實體、修改一個屬性、移除另一個屬性，以及新增一個屬性。 請注意，移除屬性的方式是將它的值設定成 **null**。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>刪除實體
若要刪除實體，請將資料表名稱以及實體的 `PartitionKey` 和 `RowKey` 傳遞給 **TableRestProxy->deleteEntity** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

請注意，針對並行檢查，您可以使用 **DeleteEntityOptions->setEtag** 方法為要刪除的實體設定 Etag，並將 **DeleteEntityOptions** 物件傳遞給 **deleteEntity** 做為第四個參數。

## <a name="batch-table-operations"></a>批次資料表作業
**TableRestProxy->batch** 方法可讓您以單一要求執行多項作業。 這裡的模式涉及將作業新增至 **BatchRequest** 物件，然後將 **BatchRequest** 物件傳遞給 **TableRestProxy->batch** 方法。 若要將作業新增至 **BatchRequest** 物件，您可以呼叫下列任一方法許多次：

* **addInsertEntity** (新增 insertEntity 作業)
* **addUpdateEntity** (新增 updateEntity 作業)
* **addMergeEntity** (新增 mergeEntity 作業)
* **addInsertOrReplaceEntity** (新增 insertOrReplaceEntity 作業)
* **addInsertOrMergeEntity** (新增 insertOrMergeEntity 作業)
* **addDeleteEntity** (新增 deleteEntity 作業)

下列範例示範如何以單一要求執行 **insertEntity** 和 **deleteEntity** 作業：

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

如需有關批次處理資料表作業的詳細資訊，請參閱[執行實體群組交易][entity-group-transactions]。

## <a name="delete-a-table"></a>刪除資料表
最後，若要刪除資料表，請將資料表名稱傳遞給 **TableRestProxy->deleteTable** 方法。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>後續步驟
了解 Azure 資料表服務的基礎概念之後，請依循下列連結以深入了解更複雜的儲存體工作。

* [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。

* [PHP 開發人員中心](/develop/php/)。

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
