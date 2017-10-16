---
title: "使用 mongoimport 和 mongorestore 搭配適用於 MongoDB 的 Azure Cosmos DB API | Microsoft Docs"
description: "了解如何使用 mongoimport 和 mongorestore 將資料匯入適用於 MongoDB 的 API 帳戶"
keywords: mongoimport, mongorestore
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
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB：匯入 MongoDB 資料 

若要將資料從 MongoDB 移轉至 Azure Cosmos DB 帳戶以用於 MongoDB 所適用的 API，您必須︰

* 從 [MongoDB 下載中心](https://www.mongodb.com/download-center)下載 *mongoimport.exe* 或*mongorestore.exe*。
* 取得[適用於 MongoDB 的 API 連接字串](connect-mongodb-account.md)。

如果您從 MongoDB 匯入資料，而且想要將這些資料用於 Azure Cosmos DB，則必須使用[資料移轉工具](import-data.md)匯入資料。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 擷取連接字串
> * 使用 mongoimport 匯入 MongoDB 資料
> * 使用 mongorestore 匯入 MongoDB 資料

## <a name="prerequisites"></a>必要條件

* 增加輸送量︰資料移轉的時間長短取決於您為集合設定的輸送量。 針對較大資料移轉，請務必增加輸送量。 完成移轉之後，再降低輸送量以節省成本。 如需在 [Azure 入口網站](https://portal.azure.com)增加輸送量的詳細資訊，請參閱 [Azure Cosmos DB 中的效能等級和定價層](performance-levels.md)。

* 啟用 SSL：Azure Cosmos DB 有嚴格的安全性需求和標準。 與您的帳戶互動時，請務必啟用 SSL。 本文其他部分的程序包括如何針對 mongoimport 和 mongorestore 啟用 SSL。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>找到您的連接字串資訊 (主機、連接埠、使用者名稱、密碼)

1. 在 [Azure 入口網站](https://portal.azure.com)的左窗格中，按一下 [Azure Cosmos DB] 項目。
2. 在 [訂用帳戶] 窗格中，選取您的帳戶名稱。
3. 在 [連接字串] 刀鋒視窗中，按一下 [連接字串]。  
右窗格中有您成功連接到您的帳戶所需的所有資訊。

    ![[連接字串] 刀鋒視窗](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>使用 mongoimport 將資料匯入適用於 MongoDB 的 API

若要將資料匯入您的 Azure Cosmos DB 帳戶，請使用下列範本。 填寫專屬於您的帳戶的 [主機]、[使用者名稱]、[密碼] 值。  

範本：

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

範例：  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>使用 mongorestore 將資料匯入適用於 MongoDB 的 API

若要將資料還原至適用於 MongoDB 的 API 帳戶，請使用下列範本執行匯入。 填寫專屬於您的帳戶的 [主機]、[使用者名稱]、[密碼] 值。

範本：

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

範例：

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>成功移轉的指南

1. 預先建立並調整您的集合：
        
    * 根據預設，Azure Cosmos DB 會佈建含 1,000 個要求單位 (RU) 的新 MongoDB 集合。 使用 mongoimport、mongorestore 或 mongomirror 開始移轉之前，請從 [Azure 入口網站](https://portal.azure.com)或 MongoDB 驅動程式與工具預先建立您的所有集合。 如果您的集合大於 10 GB，請務必使用適當的分區金鑰建立[分區/分割集合](partition-data.md)。

    * 在 [Azure 入口網站](https://portal.azure.com)，僅針對移轉來增加集合的輸送量，單一分割區集合從 1,000 RU 起，分區集合則從 2,500 RU 起。 藉由較高的輸送量，您可以避免節流，並花費較少的時間進行移轉。 由於 Azure Cosmos DB 是以每小時計費，所以您可以在移轉之後立即減少輸送量以節省成本。

2. 估算單一文件消耗的 RU：

    a. 從 MongoDB 殼層連線到您的 Azure Cosmos DB MongoDB 資料庫。 您可以在[將 MongoDB 應用程式連接到 Azure Cosmos DB](connect-mongodb-account.md) 中找到指示。
    
    b.這是另一個 C# 主控台應用程式。 從 MongoDB 殼層使用其中一個範例文件來執行範例插入命令：
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. 執行 ```db.runCommand({getLastRequestStatistics: 1})```，您會收到如下的回應：
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. 記下要求費用。
    
3. 判斷從您的電腦到 Azure Cosmos DB 雲端服務的延遲：
    
    a. 從 MongoDB 殼層使用此命令，以啟用詳細資訊記錄：```setVerboseShell(true)```
    
    b.這是另一個 C# 主控台應用程式。 對資料庫執行簡單的查詢：```db.coll.find().limit(1)```。 您會收到如下的回應：

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. 移轉之前請先移除插入的文件，以確保不會有重複的文件。 您可以使用此命令來移除文件：```db.coll.remove({})```

5. 估算 *batchSize* 和 *numInsertionWorkers* 值：

    * *batchSize* 的算法是將總佈建的 RU 數，除以步驟 3 中寫入單一文件所花費的 RU 數。
    
    * 如果算出的 *batchSize* <= 24，請使用該數字做為您的 *batchSize* 值。
    
    * 如果算出的 *batchSize* > 24，則將 *batchSize* 的值設為 24。
    
    * *numInsertionWorkers* 的算法是使用此方程式：*numInsertionWorkers =  (佈建輸送量 * 延遲秒數) / (批次大小 * 單一寫入花費的 RU 數)*。
        
    |屬性|值|
    |--------|-----|
    |batchSize| 24 |
    |佈建的 RU | 10000 |
    |延遲 | 0.100 秒 |
    |寫入 1 個文件花費的 RU | 10 RU |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RU x 0.1 s) / (24 x 10 RU) = 4.1666*

6. 執行最後的移轉命令：

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>後續步驟

您可以繼續進行下一個教學課程，了解如何使用 Azure Cosmos DB 查詢 MongoDB 資料。 

> [!div class="nextstepaction"]
>[如何查詢 MongoDB 資料？](../cosmos-db/tutorial-query-mongodb.md)
