---
title: "使用 MongoChef 連絡 Azure Cosmos DB | Microsoft Docs"
description: "了解如何使用 MongoChef 搭配 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶"
keywords: MongoChef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>使用 MongoChef 搭配 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶

若要連線到 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶，您必須︰

* 下載並安裝 [MongoChef](http://3t.io/mongochef)
* 具備您的 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶[連接字串](connect-mongodb-account.md)資訊

## <a name="create-the-connection-in-mongochef"></a>在 MongoChef 中建立連接
若要將 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶新增至 MongoChef 連線管理員，請執行下列步驟。

1. 使用[這裡](connect-mongodb-account.md)的指示來擷取 Azure Cosmos DB：適用於 MongoDB 的 API 的連線資訊。

    ![[連接字串] 刀鋒視窗的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. 按一下 連接 以開啟 連接管理員，然後按一下新增連線

    ![[MongoChef 連接管理員] 的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManager.png)
3. 在 [新增連線]視窗中，在 [伺服器] 索引標籤上輸入 Azure Cosmos DB：適用於 MongoDB 的 API 帳戶的主機 (FQDN) 和連接埠。

    ![[MongoChef 連接管理員伺服器] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. 在 [新增連線] 視窗中，請在 [驗證] 索引標籤上選擇驗證模式 [標準 (MONGODB-CR 或 SCARM-SHA-1)]，並輸入使用者名稱和密碼。  接受預設的驗證資料庫 (管理員)，或提供您自己的值。

    ![[MongoChef 連接管理員驗證] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. 在 [新增連接] 視窗的 [SSL] 索引標籤上，勾選 [使用 SSL 通訊協定連接] 核取方塊和 [接受自我簽署的 SSL 憑證] 選項按鈕。

    ![[MongoChef 連接管理員 SSL] 索引標籤的螢幕擷取畫面](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. 按一下 測試連接 按鈕以驗證連接資訊，按一下 確定 以返回 新增連線 視窗，然後按一下儲存。

    ![[MongoChef 測試連接] 視窗的螢幕擷取畫面](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>使用 MongoChef 以建立資料庫、集合和文件
若要使用 MongoChef 建立資料庫、集合和文件，請執行下列步驟。

1. 在 [連接管理員] 中，反白顯示連接，然後按一下 [連接]。

    ![[MongoChef 連接管理員] 的螢幕擷取畫面](./media/mongodb-mongochef/ConnectToAccount.png)
2. 以滑鼠右鍵按一下主機，然後選擇 [新增資料庫] 。  提供資料庫名稱，然後按一下 [確定] 。

    ![[MongoChef 新增資料庫] 選項的螢幕擷取畫面](./media/mongodb-mongochef/AddDatabase1.png)
3. 以滑鼠右鍵按一下資料庫，然後選擇 [新增集合] 。  提供集合名稱，然後按一下 [建立] 。

    ![[MongoChef 新增集合] 選項的螢幕擷取畫面](./media/mongodb-mongochef/AddCollection.png)
4. 按一下 [集合] 功能表項目，然後按一下 [新增文件]。

    ![[MongoChef 新增文件] 功能表項目的螢幕擷取畫面](./media/mongodb-mongochef/AddDocument1.png)
5. 在 新增文件 對話方塊中，貼上下列項目，然後按一下新增文件 。

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 新增其他文件，這次使用下列內容。

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. 執行範例查詢。 例如，搜尋姓氏 'Andersen' 的家族，並傳回父母和州欄位。

    ![Mongo Chef 查詢結果的螢幕擷取畫面](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>後續步驟
* 瀏覽 Azure Cosmos DB：適用於 MongoDB 的 API [範例](mongodb-samples.md)。
