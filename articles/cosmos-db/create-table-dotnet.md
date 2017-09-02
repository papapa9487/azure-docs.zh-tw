---
title: "使用資料表 API 來建置 Azure Cosmos DB .NET 應用程式 | Microsoft Docs"
description: "使用 .NET 來開始使用 Azure Cosmos DB 的資料表 API"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 29e7eebda5177d6e852ef04ad82d9d38a8d30ed8
ms.contentlocale: zh-tw
ms.lasthandoff: 06/23/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB：使用資料表 API 來建置 .NET 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何建立 Azure Cosmos DB 帳戶，並使用 Azure 入口網站在該帳戶內建立資料表。 接著，您要撰寫程式碼，將實體插入、更新和刪除，然後從 NuGet 使用新的 [Windows Azure 儲存體進階資料表](https://aka.ms/premiumtablenuget) (預覽版) 套件來執行一些查詢。 此文件庫具有與公用 [Windows Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的類別和方法簽章，但它也可以使用[資料表 API](table-introduction.md) (預覽版) 來連線到 Azure Cosmos DB 帳戶。 

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>新增資料表

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>新增範例資料

您現在可以使用資料總管 (預覽) 將資料新增至您的新資料表。

1. 在資料總管中，展開 **sample-table**，按一下 [實體]，然後按一下 [新增實體]。

   ![在 Azure 入口網站的 [資料總管] 中建立新實體](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. 現在，將資料新增至 PartitionKey 值方塊和 RowKey 值方塊，然後按一下 [新增實體]。

   ![為新的實體設定磁碟分割索引鍵和資料列索引鍵](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    您現在可以在資料表中新增更多實體、編輯實體，或在資料總管中查詢資料。 資料總管也可供您調整輸送量，以及對資料表新增預存程序、使用者定義函式和觸發程序。

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製「資料表」應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 然後在 Visual Studio 中開啟方案檔案。 

## <a name="review-the-code"></a>檢閱程式碼

讓我們快速檢閱應用程式中所發生的事情。 請開啟 Program.cs 檔案，您會發現這些程式碼行建立 Azure Cosmos DB 資源。 

* 已初始化 CloudTableClient。

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* 如果資料表不存在，將會建立資料表。

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* 已建立新的資料表容器。 您會注意到，這段程式碼與一般的 Azure 資料表儲存體 SDK 非常類似。 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>更新您的連接字串

我們現在會更新連接字串資訊，讓您的應用程式可與 Azure Cosmos DB 通訊。 

1. 在 Visual Studio 中，開啟 app.config 檔案。 

2. 在 [Azure 入口網站](http://portal.azure.com/)的 Azure Cosmos DB 左側導覽功能表中，按一下 [連接字串]。 然後在新的窗格中，按一下連接字串的複製按鈕。 

    ![檢視及複製 [連接字串] 窗格中的端點和帳戶金鑰](./media/create-table-dotnet/keys.png)

3. 將值貼到 app.config 檔案中，作為 PremiumStorageConnectionString 的值。 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    您可以讓 StandardStorageConnectionString 保持原狀。

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 在 Visual Studio 中，於 [方案總管] 中的 [PremiumTableGetStarted] 專案上按一下滑鼠右鍵，然後按一下 [管理 NuGet 套件]。 

2. 在 NuGet [瀏覽] 方塊中，輸入 *WindowsAzure.Storage-PremiumTable*。

3. 請選取 [包括發行前版本] 方塊。 

4. 從結果中，安裝 **WindowsAzure.Storage-PremiumTable** 程式庫。 這會安裝預覽版的 Azure Cosmos DB 資料表 API 套件以及所有相依性。 請注意，這個 NuGet 套件不同於 Azure 資料表儲存體所使用的 Windows Azure 儲存體套件。 

5. 按 CTRL + F5 來執行應用程式。

    主控台視窗會顯示將要在資料表中新增、擷取、查詢、取代和刪除的資料。 當指令碼完成時，請任意鍵以關閉主控台視窗。 
    
    ![快速入門的主控台輸出](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. 如果您想要在 [資料總管] 中查看新實體，只要將 program.cs 中的行 188-208 註解化，使其不會遭到刪除，然後再次執行範例。 

    您現在可以回到 [資料總管]，按一下 [重新整理]，展開 [人員] 資料表並按一下 [實體]，然後使用此新資料。 

    ![在資料總管中新增實體](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用資料總管來建立資料表，以及如何執行應用程式。  現在，您可以使用資料表 API 來查詢您的資料。  

> [!div class="nextstepaction"]
> [使用資料表 API 來進行查詢](tutorial-query-table.md)


