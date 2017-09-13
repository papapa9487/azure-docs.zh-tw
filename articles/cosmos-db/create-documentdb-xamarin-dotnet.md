---
title: "Azure CosmosDB︰使用 Xamarin 和 Facebook 驗證建置 Web 應用程式 | Microsoft Docs"
description: "提供可用來連線及查詢 Azure Cosmos DB 的 .NET 程式碼範例"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: c8e2e688a7a8b99272c304df37ceff0f9ec52b19
ms.contentlocale: zh-tw
ms.lasthandoff: 05/31/2017

---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure CosmosDB︰使用 .NET、Xamarin 和 Facebook 驗證建置 Web 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本快速入門會示範如何使用 Azure 入口網站建立 Azure Cosmos DB 帳戶、文件資料庫和集合。 您會接著建置和部署以 [DocumentDB .NET API](documentdb-sdk-dotnet.md)、[Xamarin](https://www.xamarin.com/) 和 Azure Cosmos DB 授權引擎為基礎的待辦事項清單 Web 應用程式。 待辦事項清單 Web 應用程式會實作每個使用者資料模式，可讓使用者使用 Facebook 驗證登入及管理自己的待辦事項。

## <a name="prerequisites"></a>必要條件

如果尚未安裝 Visual Studio 2017，您可以下載並使用**免費的** [Visual Studio 2017 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>新增集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>複製範例應用程式

現在，我們將從 Github 複製 DocumentDB API 應用程式、設定連接字串，然後執行它。 您會看到，以程式設計方式來處理資料有多麼的容易。 

1. 開啟 Git 終端機視窗 (例如 Git Bash)，然後使用 `cd` 來切換到工作目錄。  

2. 執行下列命令來複製範例存放庫。 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. 然後從 Visual Studio 中的 samples/xamarin/UserItems/xamarin.forms 資料夾開啟 DocumentDBTodo.sln 檔案。 

## <a name="review-the-code"></a>檢閱程式碼

Xamarin 資料夾中的程式碼包含︰

* Xamarin 應用程式。 應用程式會將使用者的待辦事項儲存在名為 UserItems 的分割集合中。
* 資源權杖訊息代理程式 API。 簡單 ASP.NET Web API，可為已登入應用程式的使用者代理 Azure Cosmos DB 資源權杖。 資源權杖是短期的存取權杖，可供應用程式存取權已登入使用者的資料。

下圖說明驗證和資料流程。

* UserItems 集合是使用資料分割索引鍵 '/userid' 建立。 指定集合的資料分割索引鍵，可讓 Azure Cosmos DB 隨著使用者數和項目數成長無限擴充。
* Xamarin 應用程式可讓使用者以 Facebook 認證登入。
* Xamarin 應用程式會使用 Facebook 存取權杖來驗證 ResourceTokenBroker API
* 資源權杖訊息代理程式 API 會使用 App Service 驗證功能來驗證要求，並要求具有所有文件讀取/寫入權限的 Azure Cosmos DB 資源權杖共用已驗證使用者的資料分割索引鍵。
* 資源權杖訊息代理程式會將資源權杖傳回給用戶端應用程式。
* 應用程式會使用資源權杖來存取使用者的待辦事項。

![具有範例資料的待辦事項應用程式](./media/create-documentdb-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>更新您的連接字串

現在，返回 Azure 入口網站以取得連接字串資訊，並將它複製到應用程式中。

1. 在 [Azure 入口網站](http://portal.azure.com/)中，於您 Azure Cosmos DB 帳戶的左側瀏覽區中，按一下 [金鑰]，然後按一下 [讀寫金鑰]。 在下一個步驟中，您將使用畫面右側的複製按鈕，將 URI 和主要金鑰複製到 Web.config 檔案。

    ![在 Azure 入口網站的 [金鑰] 刀鋒視窗中檢視並複製存取金鑰](./media/create-documentdb-xamarin-dotnet/keys.png)

2. 在 Visual Studio 2017 中，開啟 azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker 資料夾中的 Web.config 檔案。 

3. 從入口網站複製您的 URI 值 (使用 [複製] 按鈕)，並使它成為 Web.config 中的 accountUrl 值。 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. 然後，從入口網站複製您的主要金鑰值，並使它成為 Web.congif 中的 accountKey 值。 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

您現已更新應用程式，使其具有與 Azure Cosmos DB 通訊所需的所有資訊。 

## <a name="build-and-deploy-the-web-app"></a>建置並部署 Web 應用程式

1. 在 Azure 入口網站中，建立 App Service 網站以裝載資源權杖訊息代理程式 API。
2. 在 Azure 入口網站中，開啟資源權杖訊息代理程式 API 網站的 [應用程式設定] 刀鋒視窗。 填寫下列應用程式設定︰

    * accountUrl - Azure Cosmos DB 帳戶的 [金鑰] 索引標籤中的 Azure Cosmos DB 帳戶 URL。
    * accountKey - Azure Cosmos DB 帳戶的 [金鑰] 索引標籤中的 Azure Cosmos DB 帳戶主要金鑰。
    * 已建立之資料庫和集合的 databaseId 和 collectionId

3. 將 ResourceTokenBroker 方案發佈至您建立的網站。

4. 開啟 Xamarin 專案，並瀏覽至 TodoItemManager.cs。 填寫 accountURL、collectionId、databaseId 及 resourceTokenBrokerURL 的值，作為資源權杖訊息代理程式網站的基底 https url。

5. 完成[如何設定 App Service 應用程式以使用 Facebook 登入](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md)教學課程，以設定 Facebook 驗證及設定 ResourceTokenBroker 網站。

    執行 Xamarin 應用程式。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 入口網站中檢閱 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本快速入門所建立的所有資源： 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您剛建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 Azure Cosmos DB 帳戶、如何使用 [資料總管] 建立集合，以及如何建置和部署 Xamarin 應用程式。 您現在可以將其他資料匯入到 Cosmos DB 帳戶。 

> [!div class="nextstepaction"]
> [將資料匯入到 Azure Cosmos DB](import-data.md)

