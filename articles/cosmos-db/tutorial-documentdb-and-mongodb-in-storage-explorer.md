---
title: "在 Azure 儲存體總管中管理 Azure Cosmos DB"
description: "學習如何在 Azure 儲存體總管中管理 Azure Cosmos DB。"
Keywords: "Azure Cosmos DB, Azure 儲存體總管, DocumentDB, MongoDB, DocumentDB"
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: 2cd3656156b77c71be85a1a18567232f4466fc68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>在 Azure 儲存體總管 (預覽版本) 中管理 Azure Cosmos DB

在 Azure 儲存體總管中使用 Azure Cosmos DB 可讓使用者管理 Azure Cosmos DB 實體、操縱資料、更新預存程序及觸發程序，以及其他 Azure 實體 (例如儲存體 Blob 及佇列)。 現在您可以使用同一個工具在同一處管理您不同的 Azure 實體。 目前，Azure 儲存體總管支援 SQL (DocumentDB) 和 MongoDB 帳戶。

在本文中，您將會了解如何使用儲存體總管來管理 Azure Cosmos DB。


## <a name="prerequisites"></a>必要條件

- SQL (DocumentDB) 或 MongoDB 資料庫的 Azure Cosmos DB 帳戶。 若您還沒有帳戶，您可以根據[Azure Cosmos DB：使用 .NET 及 Azure 入口網站建置 DocumentDB API Web 應用程式](create-documentdb-dotnet.md)中的描述，在 Azure 入口網站中建立帳戶。
- 安裝最新版本的 Azure 儲存體總管。 您可以使用下列連結進行安裝：[Linux](https://go.microsoft.com/fwlink/?linkid=858559)，[Mac](https://go.microsoft.com/fwlink/?linkid=858561)，[Windows](https://go.microsoft.com/fwlink/?linkid=858562)。

## <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

1. 在安裝 **Azure 儲存體總管**之後，按一下左邊的**外掛程式**圖示，如下圖所示。
       
   ![外掛程式圖示](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. 選取 新增 Azure 帳戶，然後按一下登入。

   ![連線到 Azure 訂用帳戶](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. 在 [Azure 登入] 對話方塊中，選取 [登入]，然後輸入您的 Azure 認證。

    ![Sign in](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. 從清單中選取您的訂用帳戶，然後按一下套用。

    ![套用](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    [總管] 窗格會更新，並顯示選取之訂閱中的帳戶。

    ![帳戶清單](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    您已成功將您的 **Azure Cosmos DB 帳戶**連線到您的 Azure 訂用帳戶。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>使用連接字串連線到 Azure Cosmos DB

另外一種連線到 Azure Cosmos DB 的方式為使用連接字串。 使用下列步驟來使用連接字串進行連線。

1. 在左邊的樹狀目錄中尋找 [Local and Attached] \(本機與已連結)，以滑鼠右鍵按一下 [Azure Cosmos DB 帳戶]，選擇 [連線到 Azure Cosmos DB...]

    ![使用連接字串連線到 Azure Cosmos DB](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. 選擇適用於您帳戶類型的**預設體驗**，其可能是 **DocumentDB** 或 **MongoDB**，將您的**連接字串**貼上，然後按一下確定 來連線到 Azure Cosmos DB 帳戶。 如需擷取連接字串的資訊，請參閱[取得連接字串](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string)。

    ![連接字串](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 資源管理

您可以透過執行下列作業來管理 Azure Cosmos DB 帳戶：
* 在 Azure 入口網站中開啟帳戶
* 將資源新增至快速存取清單
* 搜尋和重新整理資源
* 建立和刪除資料庫
* 建立和刪除集合
* 建立、編輯、刪除和篩選文件
* 管理預存程序、觸發程序和使用者定義函式

### <a name="quick-access-tasks"></a>快速存取工作

以滑鼠右鍵按一下 [總管] 窗格中的訂用帳戶，您可以執行許多快速動作工作：

* 以滑鼠右鍵按一下 Azure Cosmos DB 帳戶或資料庫，您可以選擇 [在入口網站中開啟] 並使用瀏覽器在 Azure 入口網站上管理資源。

     ![在入口網站中開啟](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* 您也可以將 Azure Cosmos DB 帳戶、資料庫及集合新增至 [快速存取]。
* [Search from Here] \(從這裡搜尋) 可在選取的路徑下使用關鍵字搜尋。

    ![從這裡搜尋](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>資料庫與集合管理
#### <a name="create-a-database"></a>建立資料庫 
以滑鼠右鍵按一下 Azure Cosmos DB 帳戶，選擇 [建立資料庫]，輸入資料庫名稱，然後按  **ENTER 鍵**以完成。

![建立資料庫](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>刪除資料庫
以滑鼠右鍵按一下資料庫，按一下 [刪除資料庫]，然後在快顯視窗中按一下 [是]。 資料庫節點隨即會刪除，而 Azure Cosmos DB 帳戶會自動重新整理。

![刪除 database1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![刪除 database2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>建立集合
以滑鼠右鍵按一下您的資料庫，選擇 [建立集合]，然後提供下列資訊 (例如**集合識別碼****儲存體容量**等)。按一下 [確定] 以完成。 如需分割區索引鍵設定的資訊，請參閱[設計資料分割](partition-data.md#designing-for-partitioning)。

若在建立集合時使用了分割區索引鍵，則一旦建立過程完成後，該分割區索引鍵的值便不能在集合上進行變更。

![建立 collection1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![建立 collection2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>刪除集合
以滑鼠右鍵按一下集合，按一下 [刪除集合]，然後在快顯視窗中按一下 [是]。 

集合節點隨即刪除，且資料庫會自動重新整理。  

![刪除集合](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>文件管理

#### <a name="create-and-modify-documents"></a>建立及修改文件
若要建立新文件，請在左邊視窗中開啟 文件，按一下 新增文件，在右邊窗格中編輯內容，然後按一下儲存。 您可以也更新現有的文件，然後按一下儲存。 按一下 [捨棄] 以捨棄變更。

![文件](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>刪除文件
按一下 [刪除] 按鈕來刪除選取的文件。
#### <a name="query-for-documents"></a>查詢文件
輸入 [SQL 查詢](documentdb-sql-query.md)，然後按一下套用 來編輯文件篩選。

![Filter](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理預存程序、觸發程序和 UDF
* 若要建立預存程序，請在左邊的樹狀目錄中，以滑鼠右鍵按一下 預存程序，選擇 建立預存程序，在左邊輸入名稱，在右邊的視窗中輸入預存程序指令碼，然後按一下建立。 
* 您也可以透過按兩下現有的預存程序，進行更新，然後按一下更新 以儲存，或按一下 捨棄 以取消變更，來編輯現有的預存程序。

![預存程序](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* 針對**觸發程序**及 **UDF** 的操作與**預存程序**的操作雷同。

## <a name="demo"></a>示範
* 請觀看下列影片以了解如何在 Azure 儲存體總管中使用 Azure Cosmos DB：[在 Azure 儲存體總管中使用 Azure Cosmos DB](https://go.microsoft.com/fwlink/?linkid=858710)。

## <a name="next-steps"></a>後續步驟

現在，您已經將 Azure 儲存體總管連線到您的 Azure Cosmos DB 帳戶，您可以在[開始使用儲存體總管 (預覽版本) ](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)中深入了解儲存體總管，並連線至更多服務。

