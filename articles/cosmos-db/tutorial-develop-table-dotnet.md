---
title: "Azure Cosmos DB：使用 .NET 搭配資料表 API 進行開發 | Microsoft Docs"
description: "了解如何使用 .NET 搭配 Azure Cosmos DB 的「資料表 API」進行開發"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: arramac
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 52cb5f2569b6c3a5301752b1e8bfb6cea13ff7f6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB：使用 .NET 搭配資料表 API 進行開發

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。

本教學課程涵蓋下列工作： 

> [!div class="checklist"] 
> * 建立 Azure Cosmos DB 帳戶 
> * 在 app.config 檔案中啟用功能 
> * 使用[資料表 API](table-introduction.md) (預覽) 來建立資料表
> * 將實體新增到資料表 
> * 插入一批實體 
> * 擷取單一實體 
> * 使用自動次要索引來查詢實體 
> * 取代實體 
> * 刪除實體 
> * 刪除資料表
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料表 

Azure Cosmos DB 針對需要無結構描述設計之索引鍵-值存放區的應用程式，提供[資料表 API](table-introduction.md) (預覽)。 [Azure 資料表儲存體](../storage/common/storage-introduction.md) SDK 和 REST API 可用來與 Azure Cosmos DB 搭配運作。 您可以使用 Azure Cosmos DB 來建立具有高輸送量需求的資料表。 Azure Cosmos DB 支援輸送量最佳化資料表 (非正式的名稱為「進階資料表」)，目前是公開預覽版。 

針對具有高儲存體和較低輸送量需求的資料表，您可以繼續使用 Azure 資料表儲存體。 Azure Cosmos DB 在未來的更新中將導入對儲存體最佳化資料表的支援，而現有和新的 Azure 資料表儲存體帳戶將會以無縫接軌的方式升級至 Azure Cosmos DB。

如果您目前使用 Azure 資料表儲存體，便可獲得「進階資料表」預覽版的下列優點：

- 具有多路連接及[自動和手動容錯移轉](regional-failover.md)的完備[全域散發](distribute-data-globally.md)
- 支援所有屬性 (次要索引) 之無從驗證結構描述的自動索引編製，以及快速查詢 
- 支援在任意數目的區域[獨立調整儲存體和輸送量](partition-data.md)
- 支援[每一資料表有專用輸送量](request-units.md) (可從每秒數百個要求調整到數百萬個要求)
- 支援[五個可調整的一致性層級](consistency-levels.md)，可根據您應用程式的需求，進行可用性、延遲及一致性的取捨
- 單一區域內可達 99.99% 的可用性，並且能夠新增更多區域來提高可用性，以及在一般可用性上達到[業界頂尖的全面性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
- 可與現有的 Azure 儲存體 .NET SDK 搭配運作，不需對您的應用程式進行任何程式碼變更

在預覽版期間，Azure Cosmos DB 會使用 .NET SDK 來支援「資料表 API」。 您可以從 NuGet 下載 [Azure 儲存體預覽 SDK](https://aka.ms/premiumtablenuget)，此 SDK 具有與 [Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用「資料表 API」來連線到 Azure Cosmos DB 帳戶。

若要深入了解複雜的 Azure 資料表儲存體工作，請參閱：

* [Azure Cosmos DB 簡介：資料表 API](table-introduction.md)
* 「資料表」服務參考文件，以取得關可用 API 的完整詳細資料：[適用於 .NET 的儲存體用戶端程式庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

### <a name="about-this-tutorial"></a>關於本教學課程
本教學課程的對象是熟悉 Azure 資料表儲存體 SDK 且想要使用 Azure Cosmos DB 所提供之進階功能的開發人員。 本教學課程是以[以 .NET 開始使用 Azure 表格儲存體](table-storage-how-to-use-dotnet.md)為基礎，並說明如何利用額外的功能，例如次要索引、佈建的輸送量及多路連接。 我們涵蓋了如何使用 Azure 入口網站來建立 Azure Cosmos DB 帳戶，然後建置和部署「資料表」應用程式。 此外，也逐步解說 .NET 範例，這些範例可用來建立和刪除資料表，以及插入、更新、刪除和查詢資料表資料。 

如果尚未安裝 Visual Studio 2017，您可以下載並使用「免費的」[Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

我們將從在 Azure 入口網站中建立 Azure Cosmos DB 帳戶開始著手。  

> [!TIP]
> * 已經有 Azure Cosmos DB 帳戶？ 如果是，請直接跳到[設定您的 Visual Studio 方案](#SetupVS)。
> * 您是否已有 Azure DocumentDB 帳戶？ 如果是，您的帳戶現在會是 Azure Cosmos DB 帳戶，且您可以直接跳到[設定您的 Visual Studio 方案](#SetupVS)。  
> * 如果您使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Visual Studio 方案](#SetupVS)。
<!---Loc Comment: Please, check link [Set up your Visual Studio solution] since it's not redirecting to any location.---> 
>
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製「資料表」應用程式、設定連接字串，然後執行它。

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. 然後在 Visual Studio 中開啟方案檔案。

## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將連接字串複製到 app.config 檔案。

2. 在 Visual Studio 中，開啟 app.config 檔案。 

3. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，然後將它設定為 app.config 中 account-key 的值。將先前建立的帳戶名稱用於 app.config 中的 account-name。
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> 若要使用此應用程式搭配標準的「Azure 資料表儲存體」，您必須變更 `app.config file`中的連接字串。 使用帳戶名稱作為資料表帳戶名稱，使用金鑰作為「Azure 儲存體」主要金鑰。 <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>建置並部署應用程式
1. 在 Visual Studio 中，於 [方案總管] 中的專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 

2. 在 NuGet [瀏覽] 方塊中，輸入 ***WindowsAzure.Storage-PremiumTable***。 請選取 [包括發行前版本]。

3. 從結果中，安裝 **WindowsAzure.Storage-PremiumTable** 並選擇預覽組建 `0.0.1-preview`。 此動作會安裝 Azure 資料表儲存體套件及所有相依項目。

4. 按 CTRL + F5 來執行應用程式。 

您現在可以返回 [資料總管]，以查看查詢、修改及使用此資料表資料。 

> [!NOTE]
> 若要使用此應用程式搭配「Azure Cosmos DB 模擬器」，只需變更 `app.config file` 中的連接字串即可。 請將下列值用於模擬器。 <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB 功能
Azure Cosmos DB 支援一些 Azure 資料表儲存體 API 中未提供的功能。 您可以透過下列 `appSettings` 組態值啟用新的功能。 我們並未在預覽版「Azure 儲存體 SDK」中導入任何新的簽章或多載。 這讓您既可連線到標準資料表也可連線到進階資料表，以及與其他「Azure 儲存體」服務 (例如 Blob 和佇列) 搭配運作。 


| 索引鍵 | 說明 |
| --- | --- |
| TableConnectionMode  | Azure Cosmos DB 支援兩種連線模式。 在 `Gateway` 模式下，一律是對 Azure Cosmos DB 閘道提出要求，閘道會將要求轉送到對應的資料分割區。 在 `Direct` 連線模式下，用戶端會擷取資料表與分割的對應，而提出要求時，則是直接對資料分割區提出。 我們建議使用預設的 `Direct`。  |
| TableConnectionProtocol | Azure Cosmos DB 支援兩種連線通訊協定 - `Https` 和 `Tcp`。 `Tcp` 是預設值，也是建議使用的通訊協定，因為它較精簡。 |
| TablePreferredLocations | 供讀取使用的慣用 (多路連接) 位置逗號分隔清單。 每個 Azure Cosmos DB 帳戶都可以與 1-30 個以上的區域建立關聯。 每個用戶端執行個體可以依慣用順序指定這些區域的子集，供低延遲讀取使用。 指定這些區域時，必須使用其[顯示名稱](https://msdn.microsoft.com/library/azure/gg441293.aspx)，例如 `West US`。 另請參閱[多路連接 API](tutorial-global-distribution-table.md)。
| TableConsistencyLevel | 您可以在五個定義完善的一致性層級之間做選擇，以在延遲、一致性及可用性之間做取捨：`Strong`、`Session`、`Bounded-Staleness`、`ConsistentPrefix` 及 `Eventual`。 預設值為 `Session`。 一致性層級的選擇會在多區域設定中造成明顯的效能差異。 如需詳細資料，請參閱[一致性層級](consistency-levels.md)。 |
| TableThroughput | 以每秒要求單位 (RU) 表示的資料表保留輸送量。 單一資料表可支援每秒數億個 RU。 請參閱[要求單位](request-units.md)。 預設值為 `400` |
| TableIndexingPolicy | 對資料表內所有資料行進行一致且自動的次要索引編製 | 符合索引編製原則規格的 JSON 字串。 若要了解如何變更索引編製原則以包含/排除特定資料行，請參閱[索引編製原則](indexing-policies.md)。 | 自動編製所有屬性 (字串的雜湊，以及數字的範圍) 的索引 |
| TableQueryMaxItemCount | 設定單一來回行程中每一資料表查詢所傳回的項目數上限。 預設值為 `-1`，這會讓 Azure Cosmos DB 在執行階段動態決定值。 |
| TableQueryEnableScan | 如果查詢無法針對任何篩選使用索引，則無論如何還是透過掃描執行它。 預設值為 `false`。|
| TableQueryMaxDegreeOfParallelism | 用於執行跨分割區查詢的平行處理程度。 `0` 為循序且不預先擷取，`1` 為循序並預先擷取，而值越高代表平行程度也越高。 預設值為 `-1`，這會讓 Azure Cosmos DB 在執行階段動態決定值。 |

若要變更預設值，請從 Visual Studio 中的 [方案總管] 開啟 `app.config` 檔案。 如下所示，加入 `<appSettings>` 元素的內容。 以您的儲存體帳戶名稱取代 `account-name`，並以您的帳戶存取金鑰取代 `account-key`。 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

讓我們快速檢閱應用程式中發生了什麼。 請開啟 `Program.cs` 檔案，您會發現這些程式碼行建立「資料表」資源。 

## <a name="create-the-table-client"></a>建立資料表用戶端
您需將 `CloudTableClient` 初始化以連線到資料表帳戶。

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
將此用戶端初始化時，會使用 `TableConnectionMode`、`TableConnectionProtocol`、`TableConsistencyLevel` 及 `TablePreferredLocations` 組態值 (如果在應用程式設定中指定)。
    
## <a name="create-a-table"></a>建立資料表
然後，您需使用 `CloudTable` 來建立資料表。 Azure Cosmos DB 中的「資料表」可以就儲存體和輸送量方面獨立調整，而資料分割則是由服務自動處理。 Azure Cosmos DB 同時支援固定大小和無限制的資料表。 如需詳細資料，請參閱 [Azure Cosmos DB 中的資料分割](partition-data.md)。 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

在資料表的建立方式方面有重大的差異。 Azure Cosmos DB 會保留輸送量，而不像 Azure 儲存體針對交易是採用以耗用量為基礎的模型。 保留模型有兩個主要的優點：

* 您的輸送量是專用/已保留的，因此當您的要求率正好在或低於所佈建的輸送量時，一律不會進行節流
* 保留模型[對輸送量大的工作負載來說較符合成本效益](key-value-store-cost.md)

您可以設定以每秒 RU (要求單位) 表示的 `TableThroughput` 設定，來設定預設輸送量。 

讀值為 1-KB 的實體會標準化為 1 RU，其他作業則會根據其 CPU、記憶體及 IOPS 耗用量來標準化為固定的 RU 值。 深入了解 [Azure Cosmos DB 中的要求單位](request-units.md)。

> [!NOTE]
> 雖然資料表儲存體 SDK 目前不支援修改輸送量，但是您可以使用 Azure 入口網站或 Azure CLI 來隨時立即變更輸送量。

接著，我們將逐步解說如何使用 Azure 資料表儲存體 SDK 來進行簡單的讀寫 (CRUD) 作業。 本教學課程會示範 Azure Cosmos DB 所提供之可預測的低個位數毫秒延遲和快速查詢。

## <a name="add-an-entity-to-a-table"></a>將實體新增到資料表
Azure 資料表儲存體中的實體會從 `TableEntity` 類別延伸，並且必須具有 `PartitionKey` 和 `RowKey` 屬性。 以下是一個客戶實體的範例定義。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

下列程式碼片段說明如何使用 Azure 儲存體 SDK 來插入實體。 Azure Cosmos DB 的設計目的是要為世界各地、不論任何規模都確保低延遲。

針對執行區域在與 Azure Cosmos DB 帳戶相同區域的應用程式，寫入在 P99 是 15 毫秒內完成，在 P50 則是大約 6 毫秒完成。 而這個持續時間說明了一個事實，就是寫入只有在同步複寫、永久認可且所有內容都已編製索引之後，才會認可回用戶端。

Azure Cosmos DB 的「資料表 API」為預覽版。 到正式運作時，P99 延遲保證會像其他 Azure Cosmos DB API 一樣由 SLA 作為後盾。 

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>插入一批實體
Azure 資料表儲存體支援批次作業 API，可讓您將更新、刪除及插入結合在同一個批次作業中。 Azure Cosmos DB 沒有像 Azure 資料表儲存體對批次 API 的一些限制。 例如，您可以在一個批次內執行多次讀取，可以在一個批次內對相同的實體執行多次寫入，而且沒有每一批次 100 個作業的限制。 

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>擷取單一實體
相同 Azure 區域內 Azure Cosmos DB 中的 擷取 (GET) 在 P99 是 10 毫秒內完成，在 P50 則是大約 1 毫秒完成。 您可以視所需的區域數量，將多個區域新增到您的帳戶來提供低延遲的讀取，並透過設定 `TablePreferredLocations` 將應用程式部署成從其本機區域 (多路連接) 讀取。 

您可以使用下列程式碼片段來擷取單一實體：

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> 若要了解多路連接 API，請參閱[使用多個區域進行開發](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>使用自動次要索引來查詢實體
您可以使用 `TableQuery` 類別來查詢資料表。 Azure Cosmos DB 具有寫入最佳化資料庫引擎，可自動為您資料表中的所有資料行編製索引。 Azure Cosmos DB 中的索引編製是不驗證結構描述的。 因此，即使資料列之間的結構描述不同，或結構描述隨著時間演變，仍然會自動編製索引。 由於 Azure Cosmos DB 支援自動次要索引，因此對任何屬性的查詢都可使用該索引並有效率地獲得服務。

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

在預覽版中，Azure Cosmos DB 支援的「資料表 API」查詢功能與 Azure 資料表儲存體相同。 Azure Cosmos DB 也支援排序、彙總、地理空間查詢、階層及各種不同的內建函式。 在未來的服務更新中，將會在資料表 API 中提供額外的功能。 如需這些功能的概觀，請參閱 [Azure Cosmos DB 查詢](documentdb-sql-query.md)。 

## <a name="replace-an-entity"></a>取代實體
若要更新實體，從資料表服務擷取實體、修改實體物件，然後將變更儲存回資料表服務。 下列程式碼會變更現有客戶的電話號碼。 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
同樣地，您也可以執行 `InsertOrMerge` 或 `Merge` 作業。  

## <a name="delete-an-entity"></a>刪除實體
使用更新實體時所展示的相同方法，輕鬆地在擷取實體後將其刪除。 下列程式碼會擷取並刪除客戶實體。

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>刪除資料表
最後，下列程式碼範例會從儲存體帳戶刪除資料表。 您可使用 Azure Cosmos DB 來立即刪除並重新建立資料表。

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>清除資源 

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本教學課程所建立的所有資源。   

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。  
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，我們涵蓋了如何開始使用 Azure Cosmos DB 搭配「資料表 API」，而您已經完成下列操作： 

> [!div class="checklist"] 
> * 建立 Azure Cosmos DB 帳戶 
> * 在 app.config 檔案中啟用功能 
> * 建立資料表 
> * 將實體新增到資料表 
> * 插入一批實體 
> * 擷取單一實體 
> * 使用自動次要索引來查詢實體 
> * 取代實體 
> * 刪除實體 
> * 刪除資料表  

您現在可以繼續進行到下一個教學課程，以進一步了解如何查詢資料表資料。 

> [!div class="nextstepaction"]
> [使用資料表 API 進行查詢](tutorial-query-table.md)

