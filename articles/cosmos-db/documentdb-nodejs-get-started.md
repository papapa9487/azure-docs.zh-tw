---
title: "適用於 Azure Cosmos DB 的 DocumentDB API 之 Node.js 教學課程 | Microsoft Docs"
description: "使用 DocumentDB API 所建立的 Cosmos DB 之 Node.js 教學課程。"
keywords: "node.js 教學課程，節點資料庫"
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: article
ms.date: 08/14/2017
ms.author: anhoh
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 02e98aadc6a001c7275266d89a196a57bb366b3c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="nodejs-tutorial-use-the-documentdb-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Node.js 教學課程：使用 Azure Cosmos DB 中的 DocumentDB API 以建立 Node.js 主控台應用程式
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js for MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

歡迎使用 Azure Cosmos DB Node.js SDK 的 Node.js 教學課程！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 Azure Cosmos DB 資源。

本文將討論：

* 建立及連線至 Azure Cosmos DB 帳戶
* 設定您的應用程式
* 建立節點資料庫
* 建立集合
* 建立 JSON 文件
* 查詢集合
* 取代文件
* 刪除文件
* 刪除節點資料庫

沒有時間嗎？ 別擔心！ 您可以在 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started)上找到完整的方案。 請參閱 [取得完整的解決方案](#GetSolution) ，以取得簡要指示。

完成 Node.js 教學課程之後，請使用此頁面頂端和底部的投票按鈕來提供意見。 如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

讓我們開始吧！

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Node.js 教學課程的必要條件
請確定您具有下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費 Azure 試用](https://azure.microsoft.com/pricing/free-trial/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v0.10.29 版或更高版本。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步驟 1：建立 Azure Cosmos DB 帳戶
讓我們來建立 Azure Cosmos DB 帳戶。 如果您已經擁有想要使用的帳戶，就可以直接跳到[設定您的 Node.js 應用程式](#SetupNode)。 如果您是使用「Azure Cosmos DB 模擬器」，請依照 [Azure Cosmos DB 模擬器](local-emulator.md)的步驟來設定模擬器，然後直接跳到[設定您的 Node.js 應用程式](#SetupNode)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>步驟 2：設定您的 Node.js 應用程式
1. 開啟您偏好的終端機。
2. 找出您想儲存 Node.js 應用程式的資料夾或目錄位置。
3. 使用下列命令，建立兩個空白的 JavaScript 檔案：
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X：
     * ```touch app.js```
     * ```touch config.js```
4. 透過 npm 安裝 documentdb 模組。 使用下列命令：
   * ```npm install documentdb --save```

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。

## <a id="Config"></a>步驟 3：設定您的應用程式設定
在您慣用的文字編輯器中開啟 ```config.js```。

然後，複製並貼上以下的程式碼片段，以及將屬性 ```config.endpoint``` 和 ```config.primaryKey``` 設定為您的 Azure Cosmos DB 端點 uri 和主要金鑰。 您可以在 [Azure 入口網站](https://portal.azure.com)找到這些設定。

![node.js 教學課程 - 顯示 Azure Cosmos DB 帳戶的 Azure 入口網站螢幕擷取畫面，內含反白顯示的 [主動式] 集線器、[Azure Cosmos DB 帳戶] 刀鋒視窗上反白顯示的 [金鑰] 按鈕、[金鑰] 刀鋒視窗上反白顯示的 [URI]、[主要金鑰] 和 [次要金鑰] 值 - 節點資料庫][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

在您設定 ```config.endpoint``` 和 ```config.primaryKey``` 屬性的位置下面，複製 ```database id```、```collection id``` 和 ```JSON documents``` 並貼到您的 ```config``` 物件。 如果您已經有想要儲存於資料庫中的資料，就可以使用 Azure Cosmos DB 的 [資料移轉工具](import-data.md)，而不用新增文件定義。

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


資料庫、集合和文件定義將作為您 Azure Cosmos DB 的 ```database id```、```collection id``` 和文件的資料。

最後，匯出您的 ```config``` 物件，如此就可以在 ```app.js``` 檔案中參考它。

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>步驟 4：連線至 Azure Cosmos DB 帳戶
在文字編輯器中開啟您的空白 ```app.js``` 檔案。 複製並貼上以下的程式碼，以匯入 ```documentdb``` 模組和新建的 ```config``` 模組。

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

複製並貼上以下的程式碼，以使用先前儲存的 ```config.endpoint``` 和 ```config.primaryKey``` 來建立新的 DocumentClient。

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

您現在具有程式碼，可將 Azure Cosmos DB 用戶端初始化，讓我們看看如何使用 Azure Cosmos DB 資源。

## <a name="step-5-create-a-node-database"></a>步驟 5：建立節點資料庫
複製並貼上以下的程式碼，以設定 [找不到] 的HTTP 狀態、資料庫 url 和集合 url。 這些 url 可讓 Azure Cosmos DB 用戶端尋找正確的資料庫和集合。

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

可以使用 **DocumentClient** 類別的 [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式來建立[資料庫](documentdb-resources.md#databases)。 資料庫是分割給多個集合之文件儲存體的邏輯容器。

複製並貼上 **getDatabase** 函式，以 ```config``` 物件中指定的 ```id```，在 app.js 檔案中加入建立新資料庫的函式。 此函數會檢查具有相同 ```FamilyRegistry``` 識別碼的資料庫不存在。 如果存在，我們會傳回該資料庫，而不會建立新資料庫。

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

複製並貼上以下的程式碼，以設定 **getDatabase** 函式來加入協助程式函式 **exit**，該函式會列印結束訊息和呼叫 **getDatabase** 函式。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功建立 Azure Cosmos DB 資料庫。

## <a id="CreateColl"></a>步驟 6：建立集合
> [!WARNING]
> **createCollection** 會建立具有定價含意的新集合。 如需詳細資訊，請造訪 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。
> 
> 

可以使用 **DocumentClient** 類別的 [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式建立[集合](documentdb-resources.md#collections)。 集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。

將 **getCollection** 函式複製並貼到 app.js 檔案中的 **getDatabase** 函式之下，以 ```config``` 物件中指定的 ```id``` 建立新的集合。 同樣地，我們會檢查確定具有相同 ```FamilyCollection``` 識別碼的集合不存在。 如果存在，我們會傳回該集合，而不會建立新集合。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

將以下的程式碼複製並貼到對 **getDatabase** 的呼叫之下，以執行 **getCollection** 函式。

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功建立 Azure Cosmos DB 集合。

## <a id="CreateDoc"></a>步驟 7：建立文件
可以使用 **DocumentClient** 類別的 [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 函式來建立[文件](documentdb-resources.md#documents)。 文件會是使用者定義的 (任意) JSON 內容。 您現在可以將文件插入 Azure Cosmos DB。

將 **getFamilyDocument** 函式複製並貼到 **getCollection** 函式之下，以便建立包含 ```config``` 物件中儲存之 JSON 資料文件。 同樣地，我們會檢查以確定具有相同識別碼的文件不存在。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

將以下的程式碼複製並貼到對 **getCollection** 的呼叫之下，以執行 **getFamilyDocument** 函式。

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功建立 Azure Cosmos DB 文件。

![node.js 教學課程 - 說明帳戶、資料庫、集合和文件之間階層式關聯性的圖表 - 節點資料庫](./media/documentdb-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>步驟 8︰查詢 Azure Cosmos DB 資源
Azure Cosmos DB 支援針對儲存於每個集合的 JSON 文件進行[豐富查詢](documentdb-sql-query.md)。 下列範例程式碼示範您可以針對集合中之文件執行的查詢。

將 **queryCollection** 函式複製並貼到 app.js 檔案中的 **getFamilyDocument** 函式之下。 Azure Cosmos DB 支援類 SQL 查詢，如下所示。 如需建立複雜查詢的詳細資訊，請參閱[查詢遊樂場](https://www.documentdb.com/sql/demo)和[查詢文件](documentdb-sql-query.md)。

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


下圖說明如何針對您所建立的集合呼叫 Azure Cosmos DB SQL 查詢語法。

![node.js 教學課程 - 說明查詢範圍和意義的圖表 - 節點資料庫](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

因為 Azure Cosmos DB 查詢已侷限於單一集合，所以查詢中的 [FROM](documentdb-sql-query.md#FromClause) 關鍵字是選擇性的。 因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。 依預設，Azure Cosmos DB 會推斷該 Families、root 或您選擇的變數名稱來參考目前的集合。

將以下的程式碼複製並貼到對 **getFamilyDocument** 的呼叫之下，以執行 **queryCollection** 函式。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功查詢 Azure Cosmos DB 文件。

## <a id="ReplaceDocument"></a>步驟 9：取代文件
Azure Cosmos DB 支援取代 JSON 文件。

將 **replaceFamilyDocument** 函式複製並貼到 app.js 檔案中的 **queryCollection** 函式之下。

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

將以下的程式碼複製並貼到對 **queryCollection** 的呼叫之下，以執行 **replaceDocument** 函式。 此外，再次將此程式碼加入至 **queryCollection** 呼叫，確認已成功變更文件。

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功取代 Azure Cosmos DB 文件。

## <a id="DeleteDocument"></a>步驟 10︰刪除文件
Azure Cosmos DB 支援刪除 JSON 文件。

將 **deleteFamilyDocument** 函式複製並貼到 **replaceFamilyDocument** 函式之下。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

將以下的程式碼複製並貼到對 **queryCollection** 的呼叫之下，以執行 **deleteDocument** 函式。

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

恭喜！ 您已成功將 Azure Cosmos DB 文件刪除。

## <a id="DeleteDatabase"></a>步驟 11：刪除節點資料庫
刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。

將 **cleanup** 函式複製並貼到 **deleteFamilyDocument** 函式之下，以移除資料庫和所有子系資源。

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

將以下的程式碼複製並貼到對 **deleteFamilyDocument** 的呼叫之下，以執行 **cleanup** 函式。

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>步驟 12：一起執行您的 Node.js 應用程式！
總之，呼叫您的函數的順序應如下所示︰

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

在終端機中，找到您的 ```app.js``` 檔案並執行命令：```node app.js```

您應該可以看到入門應用程式的輸出。 輸出應該會與下列範例文字相符。

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

恭喜！ 您已完成 Node.js 教學課程，並擁有您的第一個 Azure Cosmos DB 主控台應用程式！

## <a id="GetSolution"></a>取得完整的 Node.js 教學課程方案
如果您沒有時間完成本教學課程中的步驟，或只想要下載程式碼，您可以從 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) 取得程式碼。

若要執行包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

* [Azure Cosmos DB 帳戶][create-account]。
* 您可以在 GitHub 上找到 [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) 方案。

透過 npm 安裝 **documentdb** 模組。 使用下列命令：

* ```npm install documentdb --save```

接下來，將 ```config.js``` 檔案中的 config.endpoint 和 config.primaryKey 的值更新為如 [步驟 3：設定您的應用程式組態](#Config)中所述。 

然後在終端機，找到您的 ```app.js``` 檔案並執行命令：```node app.js```。

建置就這麼容易，繼續努力！ 

## <a name="next-steps"></a>後續步驟
* 需要更複雜的 Node.js 範例嗎？ 請參閱[使用 Azure Cosmos DB 來建置 Node.js Web 應用程式](documentdb-nodejs-application.md)。
* 了解如何[監視 Azure Cosmos DB 帳戶](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中，針對範例資料集執行查詢。
* 如需深入了解程式設計模型，請參閱 [Azure Cosmos DB 文件頁面](https://azure.microsoft.com/documentation/services/documentdb/)中的＜開發＞一節。

[create-account]: create-documentdb-dotnet.md#create-account
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

