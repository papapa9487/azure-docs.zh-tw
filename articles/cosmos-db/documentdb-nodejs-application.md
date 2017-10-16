---
title: "建置適用於 Azure Cosmos DB 的 Node.js Web 應用程式 | Microsoft Docs"
description: "這個 Node.js 教學課程會探索如何使用 Microsoft Azure Cosmos DB，從 Azure 網站上裝載的 Node.js Express Web 應用程式來儲存和存取資料。"
keywords: "應用程式開發, 資料庫教學課程, 了解 node.js, node.js 教學課程"
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/14/2017
ms.author: mimig
ms.openlocfilehash: 32d4b4a7db134975158e53fc964c24b52b14a1e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="_Toc395783175"></a>使用 Azure Cosmos DB 來建置 Node.js Web 應用程式
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

本 Node.js 教學課程說明如何使用 Azure Cosmos DB 和 DocumentDB API，來儲存和存取 Azure 網站上所託管的 Node.js Express 應用程式資料。 您會建置一個可供建立、擷取及完成工作的簡單網頁型工作管理應用程式 (待辦事項應用程式)。 在 Azure Cosmos DB 中，這些工作會儲存為 JSON 文件。 本教學課程會逐步引導您建立和部署應用程式，並說明每個程式碼片段中的狀況。

![本 Node.js 教學課程所建立的「我的待辦事項清單」應用程式螢幕擷取畫面](./media/documentdb-nodejs-application/cosmos-db-node-js-mytodo.png)

是否沒有時間完成本教學課程，只是想要取得完整的解決方案？ 沒有問題，您可以從 [GitHub][GitHub] 取得完整的範例解決方案。 如需有關如何執行應用程式的指示，只需閱讀 [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) 檔案即可。

## <a name="_Toc395783176"></a>必要條件
> [!TIP]
> 本 Node.js 教學課程假設您先前已有些許使用 Node.js 和 Azure 網站的經驗。
> 
> 

在依照本文中的指示進行之前，您應先確定備妥下列項目：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] v0.10.29 版或更高版本。
* [Express 產生器](http://www.expressjs.com/starter/generator.html) (您可以透過 `npm install express-generator -g` 進行安裝)
* [Git][Git]。

## <a name="_Toc395637761"></a>步驟 1：建立 Azure Cosmos DB 資料庫帳戶
我們將從建立 Azure Cosmos DB 帳戶開始著手。 如果您已經擁有帳戶，或如果您正在使用 Azure Cosmos DB 模擬器來進行本教學課程，可以跳到[步驟 2：建立新的 Node.js 應用程式](#_Toc395783178)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>步驟 2 - 建立新的 Node.js 應用程式
現在，我們來了解如何使用 [Express](http://expressjs.com/) 架構來建立基本的 Hello World Node.js 專案。

1. 開啟您喜好的終端機，例如 Node.js 命令提示字元。
2. 瀏覽至您想要在其中儲存新應用程式的目錄。
3. 使用 Express 產生器來產生名為 **todo**的新應用程式。
   
        express todo
4. 開啟您的新 **todo** 目錄並安裝相依性。
   
        cd todo
        npm install
5. 執行新的應用程式。
   
        npm start
6. 您可以檢視新的應用程式，請導覽瀏覽器至 [http://localhost:3000/](http://localhost:3000)。
   
    ![了解 Node.js - Hello World 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/documentdb-nodejs-application/cosmos-db-node-js-express.png)

    然後，若要停止應用程式，請在終端機視窗中按 CTRL+C，然後按一下 **y** 以終止批次作業。

## <a name="_Toc395783179"></a>步驟 3：安裝其他模組
**package.json** 檔案是建立在專案根目錄中的其中一個檔案。 這個檔案包含 Node.js 應用程式所需的其他模組清單。 之後，當您將此應用程式部署至 Azure 網站時，此檔案可用來決定 Azure 上需要安裝哪些模組才能支援您的應用程式。 在本教學課程中，我們還需要再安裝兩個封裝。

1. 返回終端機，透過 npm 安裝 **async** 模組。
   
        npm install async --save
2. 透過 npm 安裝 **documentdb** 模組。 這是 Azure Cosmos DB 發揮所有強大功能的模組。
   
        npm install documentdb --save
3. 快速檢查應用程式的 **package.json** 檔案應該會顯示其他模組。 這個檔案會告訴 Azure 在執行您的應用程式時要下載及安裝的封裝。 它看起來應該類似下面的範例。
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    這會讓 Node (之後則是 Azure) 知道您的應用程式需要仰賴這些額外模組。

## <a name="_Toc395783180"></a>步驟 4：在節點應用程式中使用 Azure Cosmos DB 服務
前面的內容在講述所有初始設定和組態，現在讓我們來了解本教學課程的真正目的，也就是使用 Azure Cosmos DB 撰寫一些程式碼。

### <a name="create-the-model"></a>建立模型
1. 在專案目錄中，請在與 package.json 檔案相同的目錄中建立名為 **models** 的新目錄。
2. 在 **models** 目錄中，建立名為 **taskDao.js** 的新檔案。 此檔案將包含應用程式所建立工作的模型。
3. 在同一個 **models** 目錄中，建立另一個名為 **docdbUtils.js** 的新檔案。 這個檔案會包含一些實用、可重複使用，適用於整個應用程式的程式碼。 
4. 將下列程式碼複製到 **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };               
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
5. 儲存並關閉 **docdbUtils.js** 檔案。
6. 在 **taskDao.js** 檔案的開頭加入下列程式碼，以參考我們之前建立的 **DocumentDBClient** 和 **docdbUtils.js**：
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. 接下來，要加入程式碼以定義和匯出 Task 物件。 這會負責初始化我們的 Task 物件，並設定我們即將使用的資料庫和文件集合。
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. 接下來，新增下列程式碼以定義 Task 物件上的其他方法，可用來與 Azure Cosmos DB 中存放的資料進行互動。
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. 儲存並關閉 **taskDao.js** 檔案。 

### <a name="create-the-controller"></a>建立控制器
1. 在專案的 **routes** 目錄中，建立名為 **tasklist.js** 的新檔案。 
2. 在 **tasklist.js**中加入以下程式碼。 這會載入供 **tasklist.js**使用的 DocumentDBClient 和 async 模組。 這也會定義 **TaskList** 函式，系統會傳遞我們稍早定義的 **Task** 物件執行個體給它：
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. 繼續在 **tasklist.js** 檔案中新增用來 **showTasks (顯示工作)、addTask (新增工作)** 和 **completeTasks (完成工作)** 的方法：
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. 儲存並關閉 **tasklist.js** 檔案。

### <a name="add-configjs"></a>新增 config.js
1. 在您的專案目錄中，建立名為 **config.js**的新檔案。
2. 將下列程式碼新增至 **config.js**。 這會定義組態設定和我們的應用程式所需的值。
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. 在 **config.js** 檔案中，使用 [Microsoft Azure 入口網站](https://portal.azure.com)上 Azure Cosmos DB 帳戶的 [金鑰] 刀鋒視窗中找到的值，更新 HOST 和 AUTH_KEY 的值。
4. 儲存並關閉 **config.js** 檔案。

### <a name="modify-appjs"></a>修改 app.js
1. 在專案目錄中，開啟 **app.js** 檔案。 這是稍早建立 Express Web 應用程式時所建立的檔案。
2. 將下列程式碼新增至 **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. 此程式碼會定義要使用的組態檔，並繼續讀出此檔案中的值到我們即將使用的變數。
4. 將 **app.js** 檔案中的下列兩行取代為：
   
        app.use('/', index);
        app.use('/users', users); 
   
      下列程式碼片段：
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. 這幾行會定義 **TaskDao** 物件的新執行個體，內含與 Azure Cosmos DB 的新連線 (使用從 **config.js** 中讀取的值)，將 Task 物件初始化，然後將表單動作繫結至 **TaskList** 控制站上的方法。 
6. 最後，儲存並關閉 **app.js** 檔案，我們就差不多快完成了。

## <a name="_Toc395783181"></a>步驟 5：建置使用者介面
現在，讓我們將注意力轉到建置使用者介面，以便使用者可以實際與我們的應用程式互動。 我們建立的 Express 應用程式使用 **Jade** 做為檢視引擎。 如需 Jade 的詳細資訊，請參閱 [http://jade-lang.com/](http://jade-lang.com/)。

1. **views** 目錄中的 **layout.jade** 檔是用來作為其他 **.jade** 檔案的全域範本。 在此步驟中，您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap)，這個工具組能夠方便設計美觀的網站。 
2. 開啟在 **views** 資料夾中找到的 **layout.jade** 檔案，並將其中的內容取代為下列內容：

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    這段程式碼實際上會指示 **Jade** 引擎轉譯出我們應用程式的部分 HTML，並建立稱為 **content** 的**區塊**，讓我們能在其中提供內容頁面的配置。

    儲存並關閉此 **layout.jade** 檔案。

3. 現在，開啟 **index.jade** 檔案 (應用程式即將使用的檢視)，並將檔案中的內容取代為下列內容；
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

這個程式碼會擴充配置，並為我們在前面的 **layout.jade** 檔案中看到的 **content** 預留位置提供內容。
   
在此配置中，我們建立了兩個 HTML 表單。

第一個表單包含資料的表格，以及可讓我們透過張貼到控制器的 **/completetask** 方法來更新項目的按鈕。
    
第二個表單包含兩個輸入欄位，以及可讓我們透過張貼到控制器的 **/addtask** 方法來建立項目的按鈕。

這應該就是要讓應用程式開始運作所需的所有程式碼。

## <a name="_Toc395783181"></a>步驟 6：在本機執行您的應用程式
1. 若要在本機電腦上測試應用程式，請在終端機中執行 `npm start` 以啟動應用程式，然後重新整理 [http://localhost:3000](http://localhost:3000) 瀏覽器頁面。 此頁面現在看起來應該類似以下影像：
   
    ![[我的待辦事項清單] 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/documentdb-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > 如果您收到有關 layout.jade 檔案或 index.jade 檔案縮排的錯誤，請確定這兩個檔案的前兩行靠左對齊 (沒有空格)。 如果前兩行之前有空格，請將空格移除，儲存這兩個檔案，然後重新整理瀏覽器視窗。 

2. 使用 項目、項目名稱 和 類別 欄位來輸入新工作，然後按一下新增項目。 便會使用這些屬性在 Azure Cosmos DB 中建立文件。 
3. 系統應該會更新此頁面，以在 [待辦事項] 清單中顯示新建立的項目。
   
    ![[待辦事項] 清單中包含一個新項目的應用程式螢幕擷取畫面](./media/documentdb-nodejs-application/cosmos-db-node-js-added-task.png)
4. 若要完成工作，您只需勾選 已完成 資料行中的核取方塊，然後按一下更新工作 。 這會更新您已建立的文件。

5. 若要停止應用程式，請在終端機視窗中按 CTRL+C，然後按一下 **Y** 以終止批次作業。

## <a name="_Toc395783182"></a>步驟 7：將應用程式開發專案部署至 Azure 網站
1. 如果您還沒有這麼做，請為您的 Azure 網站提供一個 Git 儲存機制。 您可以在 [Azure App Service 的本機 Git 部署](../app-service/app-service-deploy-local-git.md) 主題中找到有關如何執行這項操作的指示。
2. 新增您的 Azure 網站做為 Git 遠端。
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. 透過推送到遠端進行部署。
   
        git push azure master
4. 幾秒後，Git 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

    恭喜！ 您剛剛已使用 Azure Cosmos DB 建置您的第一個 Node.js Express Web 應用程式，並將它發佈至 Azure 網站。

    如果您想要下載或參考本教學課程的完整參考應用程式，可以從 [GitHub][GitHub] 進行下載。

## <a name="_Toc395637775"></a>接續步驟

* 需要使用 Azure Cosmos DB 來執行規模和效能測試嗎？ 請參閱 [Azure Cosmos DB 的效能和規模測試](performance-testing.md)
* 了解如何[監視 Azure Cosmos DB 帳戶](monitor-accounts.md)。
* 在 [Query Playground](https://www.documentdb.com/sql/demo)中，針對範例資料集執行查詢。
* 探索 [Azure Cosmos DB 文件](https://docs.microsoft.com/azure/documentdb/)。

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

