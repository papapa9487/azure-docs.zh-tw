---
title: "使用 Azure Cosmos DB 適用於 MongoDB 的 API 建置 Web 應用程式 | Microsoft Docs"
description: "使用適用於 MongoDB 的 API 建立線上資料庫 Web 應用程式的 Azure Cosmos DB 教學課程。"
keywords: "mongodb 範例"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: a0598d32b5bad240c0a5d77a6e19285115a9f6b0
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB：使用 .NET 來連線到 MongoDB 應用程式

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以快速建立及查詢文件、索引鍵/值及圖形資料庫，所有這些都受惠於位於 Azure Cosmos DB 核心的全域散發和水平調整功能。 

本教學課程示範如何使用 Azure 入口網站來建立 Azure Cosmos DB 帳戶，以及如何使用 [MongoDB API](mongodb-introduction.md) 來建立資料庫和集合以儲存資料。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶 
> * 更新您的連接字串
> * 在虛擬機器上建立 MongoDB 應用程式 


## <a name="create-a-database-account"></a>建立資料庫帳戶

我們將從在 Azure 入口網站中建立 Azure Cosmos DB 帳戶開始著手。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * 已經有 Azure Cosmos DB 帳戶？ 如果是，請直接跳到[設定您的 Visual Studio 方案](#SetupVS)
> * 您是否已有 Azure DocumentDB 帳戶？ 如果是，您的帳戶現在會是 Azure Cosmos DB 帳戶，且您可以直接跳到[設定您的 Visual Studio 方案](#SetupVS)。  
> * 如果您使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Visual Studio 方案](#SetupVS)。 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>更新您的連接字串

1. 在 Azure 入口網站的 [Azure Cosmos DB] 頁面中，選取適用於 MongoDB 帳戶的 API。 
2. 在帳戶刀鋒視窗的左列中，按一下 [快速入門]。 
3. 選擇您的平台 (*.NET 驅動程式*、*Node.js 驅動程式*、*MongoDB 殼層*、*Java 驅動程式*、*Python 驅動程式*)。 如果您沒有看到您的驅動程式或工具被列出，別擔心，我們會持續加入更多連線程式碼片段。 
4. 將程式碼片段複製和貼上您的 MongoDB 應用程式，如此就準備就緒。

## <a name="set-up-your-mongodb-app"></a>設定您的 MongoDB 應用程式

您可以使用[在 Azure 中建立一個連線到在虛擬機器上執行之 MongoDB 的 Web 應用程式](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md)教學課程，在進行最少修改的情況下，快速設定連線到適用於 MongoDB 帳戶之 API 的 MongoDB 應用程式 (在本機或發佈至 Azure Web 應用程式)。  

1. 請遵循教學課程，只有一項修改。  使用下列項目取代 Dal.cs 程式碼：

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. 從 Azure 入口網站中的 [金鑰] 頁面，依據您的帳戶設定，修改 Dal.cs 檔案中的下列變數：

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. 使用應用程式！

## <a name="clean-up-resources"></a>清除資源

如果您將不繼續使用此應用程式，請使用下列步驟，在 Azure 入口網站中刪除本教學課程所建立的所有資源。 

1. 從 Azure 入口網站的左側功能表中，按一下 [資源群組]，然後按一下您所建立資源的名稱。 
2. 在資源群組頁面上，按一下 [刪除]，在文字方塊中輸入要刪除之資源的名稱，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 建立 Azure Cosmos DB 帳戶 
> * 更新您的連接字串
> * 在虛擬機器上建立 MongoDB 應用程式

您現在可以繼續進行到下一個教學課程，以將 MongoDB 資料匯入到 Azure Cosmos DB。  

> [!div class="nextstepaction"]
> [將 MongoDB 資料匯入到 Azure Cosmos DB](mongodb-migrate.md)


