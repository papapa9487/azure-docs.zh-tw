---
title: "如何搭配 Ruby 使用 Azure 資料表儲存體 | Microsoft Docs"
description: "使用 Azure 表格儲存體 (NoSQL 資料存放區) 將結構化的資料儲存在雲端。"
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: b1ad9fc7b609209ca867e64226c3a0e393081408
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>如何搭配 Ruby 使用 Azure 資料表儲存體
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>概觀
本指南說明如何使用 Azure 資料表服務執行一般案例。 這些範例使用 Ruby API 撰寫。 所涵蓋的案例包括「建立和刪除資料表」、「在資料表中插入及查詢實體」。

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>建立 Ruby 應用程式
如需如何建立 Ruby 應用程式的指示，請參閱 [Azure VM 上的 Ruby on Rails Web 應用程式](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體
若要使用 Azure 儲存體，您必須下載並使用 Ruby Azure 套件，其中包含一組便利程式庫，能與儲存體 REST 服務通訊。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 來取得套件
1. 使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。
2. 在命令視窗中鍵入 **gem install azure** 以安裝 Gem 和相依性。

### <a name="import-the-package"></a>匯入套件
使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>設定 Azure 儲存體連接
azure 模組會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** 及**AZURE\_STORAGE\_ACCESS\_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。 如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::TableService** 之前指定帳戶資訊：

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

若要從 Azure 入口網站中的傳統或 Resource Manager 儲存體帳戶取得這些值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽到您要使用的儲存體帳戶。
3. 在右邊的 [設定] 刀鋒視窗中，按一下 [存取金鑰]。
4. [存取金鑰] 刀鋒視窗隨即顯示，您會看到存取金鑰 1 和存取金鑰 2。 您可以使用其中一個存取金鑰。
5. 按一下複製圖示以將金鑰複製到剪貼簿。

## <a name="create-a-table"></a>建立資料表
**Azure::TableService** 物件可讓您操作資料表及實體。 若要建立資料表，請使用 **create\_table()** 方法。 下列範例將建立資料表或列印錯誤訊息 (若有的話)。

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>將實體新增至資料表
若要新增實體，請先建立定義實體屬性的雜湊物件。 請注意，對每個實體都必須指定 **PartitionKey** 及 **RowKey**。 這些是實體的唯一識別碼，且其值的查詢速度比其他屬性快上許多。 Azure 儲存體會使用 **PartitionKey** ，自動將資料表的實體分散在許多儲存體節點上。 具有相同 **PartitionKey** 的實體會儲存在相同節點上。 **RowKey** 是實體在其所屬資料分割內的唯一識別碼。

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>更新實體
有多種方法可以用來更新現有的實體：

* **update\_entity()：**透過取代現有實體來進行更新。
* **merge\_entity()：**藉由將新的屬性值合併到現有實體來更新現有實體。
* **insert\_or\_merge\_entity()：**藉由取代來更新現有實體。 如果實體不存在，將會插入新的實體：
* **insert\_or\_replace\_entity()：**藉由將新的屬性值合併到現有實體來更新現有實體。 如果實體不存在，將會插入新的實體。

下列範例示範使用 **update\_entity()** 來更新實體：

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

使用 **update\_entity()** 及 **merge\_entity()** 時，如果要更新的實體不存在，則更新作業會失敗。 因此，如果您要儲存一個實體，而不管它是否已存在，您應該改用 **insert\_or\_replace\_entity()** 或 **insert\_or\_merge\_entity()**。

## <a name="work-with-groups-of-entities"></a>使用實體群組
有時候批次提交多個操作是有意義的，可以確保伺服器會進行不可部分完成的處理。 若要達到此目的，您首先必須建立 **Batch** 物件，然後在 **TableService** 上使用 **execute\_batch()** 方法。 下列範例示範在一個批次中，提交具備 RowKey 2 和 3 的兩個實體。 請注意，它僅適用於具備相同 PartitionKey 的實體。

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>查詢實體
若要查詢資料表中的實體，請使用 **get\_entity()** 方法，傳遞資料表名稱、**PartitionKey** 和 **RowKey**。

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>查詢實體集合
若要查詢資料表中的實體集合，請建立查詢雜湊物件並使用 **query\_entities()** 方法。 下列範例示範取得具備相同 **PartitionKey**的所有實體：

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> 如果單一查詢的結果集太大，以致於無法傳回，則會傳回接續權杖，供您用以擷取後續頁面。
>
>

## <a name="query-a-subset-of-entity-properties"></a>查詢實體屬性的子集
一項資料表查詢可以只擷取實體的少數屬性。 這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。 使用 select 子句並傳遞您要帶到用戶端的屬性名稱。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>刪除實體
若要刪除實體，請使用 **delete\_entity()** 方法。 您必須傳入資料表名稱，其中包含實體、實體的 PartitionKey 和 RowKey。

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>刪除資料表
若要刪除資料表，請使用 **delete\_table()** 方法並傳入要刪除的資料表名稱。

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>後續步驟

* [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
* GitHub 上的 [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制

