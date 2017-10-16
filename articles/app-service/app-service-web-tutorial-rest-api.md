---
title: "Azure App Service 中的 Node.js API 應用程式 | Microsoft Docs"
description: "了解如何建立 Node.js RESTful API，並將其部署至 Azure App Service 中的 API 應用程式。"
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc
ms.openlocfilehash: f42917486e873782483f372eb6e747ab3080f820
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>建置 Node.js RESTful API 並將它部署至 Azure 中的 API 應用程式

本快速入門示範如何在 Azure 上使用 [Swagger](http://swagger.io/) 定義並將其部署在 Azure 上，建立以 Node.js [Express](http://expressjs.com/) 撰寫的 REST API。 您可使用命令列工具建立應用程式、使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 設定資源，以及使用 Git 部署應用程式。  當您完成後，您必須在 Azure 上執行工作範例 REST API。

## <a name="prerequisites"></a>必要條件

* [Git](https://git-scm.com/)
* [ 安裝 Node.js 和 NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="prepare-your-environment"></a>準備您的環境

1. 在終端機視窗中執行下列命令，將範例複製到本機電腦。

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. 變更為包含範例程式碼的目錄。

    ```bash
    cd app-service-api-node-contact-list
    ```

3. 在本機電腦上安裝 [Swaggerize](https://www.npmjs.com/package/swaggerize-express)。 Swaggerize 是一種工具，可從 Swagger 定義為您的 REST API 產生 Node.js 程式碼。

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>產生 Node.js 程式碼 

在教學課程的這一節當中，會建立 API 開發工作流程的模型，以供您在其中先建立 Swagger 中繼資料，再以此建立 (自動產生) API 伺服器程式碼的結構。 

將目錄變更為 change 資料夾，然後執行 `yo swaggerize`。 Swaggerize 可從 api.json 中的 Swagger 定義，為您的 API 建立 Node.js 專案。

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

當 Swaggerize 要求提供專案名稱時，請使用 ContactList。
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>自訂專案程式碼

1. 將 lib 資料夾複製到 `yo swaggerize` 所建立的 ContactList 資料夾，然後將目錄變更為 ContactList。

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. 安裝 `jsonpath` 和 `swaggerize-ui` NPM 模組。 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. 以下列程式碼取代 handlers/contacts.js 中的程式碼： 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    此程式碼會使用 lib/contactRepository.js 所提供的 lib/contacts.json 中儲存的 JSON 資料。 新的 contacts.js 程式碼會傳回存放庫中的所有連絡人作為 JSON 酬載。 

4. 以下列程式碼取代 **handlers/contacts/{id}.js** 檔案中的程式碼：

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    此程式碼可讓您使用路徑變數，只傳回具有指定之識別碼的連絡人。

5. 以下列程式碼取代 **server.js** 中的程式碼：

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    此程式碼會進行一些小變更，讓它能與 Azure App Service 搭配運作，並為您的 API 公開互動式 Web 介面。

### <a name="test-the-api-locally"></a>在本機測試 API

1. 啟動 Node.js 應用程式
    ```bash
    npm start
    ```
    
2. 瀏覽至 http://localhost:8000/contacts 以檢視整份連絡人清單的 JSON。
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. 瀏覽至 http://localhost:8000/contacts/2 以檢視 `id` 為 2 的連絡人。
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. 使用 Swagger Web 介面 (位於 http://localhost:8000/docs) 測試 API。
   
    ![Swagger Web 介面](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> 建立 API 應用程式

在本節中，您會使用 Azure CLI 2.0 建立資源，以在 Azure App Service 上裝載 API。 

1.  使用 [az login](/cli/azure/#login) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

    ```azurecli-interactive
    az login
    ```

2. 如果您有多個 Azure 訂用帳戶，請將預設訂用帳戶變更為所需的訂用帳戶。

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>使用 Git 部署 API

將認可從本機 Git 存放庫推送至 Azure App Service，以將您的程式碼部署到 API 應用程式。

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. 在 *ContactList* 目錄中初始化新的存放庫。 

    ```bash
    git init .
    ```

3. 排除 npm 在本教學課程的先前步驟中從 Git 建立的 *node_modules* 目錄。 在目前的目錄中建立新的 `.gitignore` 檔案，並在檔案中任意新的一行上新增下列文字。

    ```
    node_modules/
    ```
    使用 `git status` 確認忽略 `node_modules` 資料夾。

4. 認可對存放庫所做的變更。
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>在 Azure 中測試 API

1. 將瀏覽器開啟至 http://app_name.azurewebsites.net/contacts。 您會看到傳回的 JSON 與您稍早在本教學課程中於本機所做的要求相同。

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. 在瀏覽器中移至 `http://app_name.azurewebsites.net/docs` 端點，試試在 Azure 上執行的 Swagger UI。

    ![Swagger Ii](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    現在只要將認可推送至 Azure Git 存放庫，即可將範例 API 的更新部署至 Azure。

## <a name="clean-up"></a>清除

若要移除在本快速入門中建立的資源，請執行下列 Azure CLI 命令︰

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>後續步驟 
> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](app-service-web-tutorial-custom-domain.md)

