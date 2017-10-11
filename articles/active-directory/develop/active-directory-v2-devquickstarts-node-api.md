---
title: "使用 Node.js 保護 Azure Active Directory v2.0 Web API 安全 | Microsoft Docs"
description: "了解如何建置 .Node.js Web API 應用程式，以接受來自個人 Microsoft 帳戶及公司或學校帳戶的權杖。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 94e945a52b9df7c495de1611baa08083357670c9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>使用 Node.js 保護 Web API 安全
> [!NOTE]
> 並非所有的 Azure Active Directory 案例和功能都可以和 v2.0 端點搭配使用。 若要判斷您應該使用 v2.0 端點或 v1.0 端點，請參閱 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

當您使用 Azure Active Directory (Azure AD) v2.0 端點時，您可以使用 [OAuth 2.0](active-directory-v2-protocols.md) 存取權杖來保護您的 Web API。 如果使用者同時有個人 Microsoft 帳戶及公司或學校帳戶，只要透過 OAuth 2.0 存取權杖，就能安全地存取您的 Web API。

*Passport* 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 架構或 resitify Web 應用程式。 在 Passport 中，一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他選項進行驗證。 我們已為 Azure AD 開發一個策略。 在本文中，我們會向您說明如何安裝模組，然後新增 Azure AD `passport-azure-ad` 外掛程式。

## <a name="download"></a>下載
本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs)。 若要依照教學課程執行，您可以[下載應用程式基本架構的 .zip 檔](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

您也可以在本教學課程結束時取得完整的應用程式。

## <a name="1-register-an-app"></a>1：註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立新的應用程式，或遵循[這些詳細步驟](active-directory-v2-app-registration.md)來註冊應用程式。 請確定您已執行下列動作：

* 複製指派給您的應用程式的「應用程式識別碼」。 您在本教學課程中將需要用到它。
* 為您的應用程式新增 **行動** 平台。
* 複製入口網站的「重新導向 URI」  。 您必須使用預設 URI 值 `urn:ietf:wg:oauth:2.0:oob`。

## <a name="2-install-nodejs"></a>2：安裝 Node.js
若要使用本教學課程的範例，您必須[安裝 Node.js](http://nodejs.org)。

## <a name="3-install-mongodb"></a>3︰安裝 MongoDB
若要成功使用此範例，您必須[安裝 MongoDB](http://www.mongodb.org)。 在此範例中，您將會使用 MongoDB，讓 REST API 得以在不同伺服器執行個體之間持續使用。

> [!NOTE]
> 在本文中，我們假設您使用 MongoDB 的預設安裝和伺服器端點︰mongodb://localhost。
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4：在您的 Web API 中安裝 restify 模組
我們會使用 Resitfy 來建置 REST API。 Restify 是衍生自 Express 的最小且具彈性的 Node.js 應用程式架構。 Restify 提供一組強大的功能，可讓您以 Connect 為基礎來建置 REST API。

### <a name="install-restify"></a>安裝 restify
1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

    如果 **azuread** 目錄不存在，請予以建立：

    `mkdir azuread`

2.  安裝 restify：

    `npm install restify`

    這個命令的輸出如下所示：

    ```
    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>您有收到錯誤訊息嗎？
在某些作業系統上，當您使用 `npm` 命令時，您可能會看到此訊息︰`Error: EPERM, chmod '/usr/local/bin/..'`。 如果您嘗試要求以系統管理員身分執行帳戶，則會發生錯誤。 若發生此這個情況，請使用 `sudo` 命令，以更高的權限層級執行 `npm`。

#### <a name="did-you-get-an-error-related-to-dtrace"></a>您有收到 DTrace 的相關錯誤嗎？
當您安裝 restify 時，您可能會看到此訊息︰

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify 有強大的機制可使用 DTrace 追蹤 REST 呼叫。 不過，在許多作業系統上無法使用 DTrace。 您可以放心地忽略此錯誤訊息。


## <a name="5-install-passportjs-in-your-web-api"></a>5：在您的 Web API 中安裝 Passport.js
1.  在命令提示字元中，將目錄切換至 **azuread**。

2.  安裝 Passport.js：

    `npm install passport`

    這個命令的輸出如下所示：

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6：將 passport-azure-ad 新增至您的 Web API
接下來，使用 passport-azuread 新增 OAuth 策略。 `passport-azuread` 是一套將 Azure AD 連線至 Passport 的策略。 在這個 Rest API 範例中，我們會對持有人權杖使用此策略。

> [!NOTE]
> 雖然 OAuth2.0 提供可發行任何已知權杖類型的架構，但只有某些權杖類型經常使用。 持有人權杖通常用於保護端點。 持有人權杖是 OAuth 2.0 中最普遍發行的權杖類型。 許多 OAuth 2.0 實作會假設持有者權杖是唯一發行的權杖類型。
> 
> 

1.  在命令提示字元中，將目錄切換至 **azuread**。

    `cd azuread`

2.  安裝 Passport.js `passport-azure-ad` 模組︰

    `npm install passport-azure-ad`

    這個命令的輸出如下所示：

    ```
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7：將 MongoDB 模組新增至您的 Web API
在此範例中，我們將使用 MongoDB 作為資料存放區。 

1.  安裝 Mongoose (常用的外掛程式) 來管理模型和結構描述： 

    `npm install mongoose`

2.  安裝 MongoDB 的資料庫驅動程式 (也稱為 MongoDB)：

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8：安裝其他模組
安裝其餘所需的模組。

1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  輸入下列命令。 此命令會在您的 node_modules 目錄中安裝下列模組：

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9：為您的相依性建立 Server.js 檔案
您的 Web API 伺服器的大部分功能都在 Server.js 檔案中。 將您的大部分程式碼新增至此檔案。 基於生產目的，您可以將功能分解成較小的檔案，例如用於個別的路由和控制器。 在本文中，我們會針對此目的使用 Server.js。

1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  使用您選擇的編輯器，建立 Server.js 檔案。 將下列資訊新增至此檔案：

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  儲存檔案。 我們很快會用到此檔案。

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10：建立可儲存 Azure AD 設定的設定檔
這個程式碼檔會將設定參數從您的 Azure AD 入口網站傳遞到 Passport.js。 在本文開頭，您已在將 Web API 新增至入口網站時建立這些設定值。 在您複製程式碼之後，我們將會說明這些參數應該指定的值。

1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  在編輯器中，建立 Config.js 檔案。 加入下列資訊：

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>必要值

*   **IdentityMetadata**：`passport-azure-ad` 會在此處尋找適用於識別提供者 (IDP) 的設定資料，以及用來驗證 JSON Web 權杖 (JWT) 的金鑰。 如果您使用 Azure AD，則可能不需要變更此值。

*   **audience**：來自入口網站的重新導向 URI。

> [!NOTE]
> 請經常變換金鑰。 請確定您一律從 "openid_keys" URL 中提取，而且應用程式可以存取網際網路。
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11：將設定新增至 Server.js 檔案
您的應用程式需要從您剛才建立的組態檔中讀取這些值。 在應用程式中將 .config 檔案新增為必要資源。 將全域變數設定成 Config.js 中的變數。

1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  在編輯器中開啟 Server.js。 加入下列資訊：

    ```Javascript
    var config = require('./config');
    ```

3.  將新的區段新增至 Server.js：

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12：使用 Moongoose 新增 MongoDB 模型和結構描述資訊
接著，在 REST API 服務中連接這三個檔案。

在本文中，我們會使用 MongoDB 來儲存工作。 我們在「步驟 4」中討論到這一點。

在您於步驟 11 建立的 Config.js 檔案中，您的資料庫稱為 *tasklist*。 這是您在 mongoose_auth_local 連線 URL 結尾指定的資訊。 您不需要在 MongoDB 中事先建立此資料庫。 第一次執行您的伺服器應用程式時會建立資料庫 (假設資料庫尚未存在)。

您已經告訴伺服器要使用哪個 MongoDB 資料庫。 接下來，您需要撰寫一些額外的程式碼，為您的伺服器工作建立模型和結構描述。

### <a name="the-model"></a>模型
結構描述模型非常基本。 需要的話，您可以展開它。 

結構描述模型有下列值︰

*   **NAME**。 指派至工作的人員。 這是**字串**值。
*   **TASK**。 工作的名稱。 這是**字串**值。
*   **DATE**。 工作到期的日期。 這是**日期時間**值。
*   **COMPLETED**。 是否已完成工作。 這是**布林**值。

### <a name="create-the-schema-in-the-code"></a>在程式碼中建立結構描述
1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  在編輯器中開啟 Server.js。 在設定項目下方，新增下列資訊：

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

此程式碼會連線至 MongoDB 伺服器。 它也會傳回結構描述物件。

#### <a name="using-the-schema-create-your-model-in-the-code"></a>在程式碼中使用這個結構描述建立模型
在上述程式碼下方，新增下列程式碼：

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

您可以從程式碼看出，您需要先建立結構描述。 接下來，您可以建立模型物件。 當您定義**路由**時，您可以使用模型物件來儲存整個程式碼的資料。

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13：新增工作 REST API 伺服器的路由
既然您已經有可以使用的資料庫模型，請新增要用於 REST API 伺服器的路由。

### <a name="about-routes-in-restify"></a>關於 restify 中的路由
Restify 中的路由與您使用 Express 堆疊時，運作方式完全相同。 您可以使用您預期用戶端應用程式呼叫的 URI 來定義路由。 通常，您會在個別的檔案中定義您的路由。 在本教學課程中，我們會將路由放在 Server.js 中。 在生產用途上，建議您將這些路由納入您自己的檔案中。

Restify 路由的典型模式是：

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


這是最基本層級的模式。 Resitfy (及 Express) 提供更深入的功能，例如能夠定義應用程式類型，以及不同端點之間的複雜路由。

#### <a name="add-default-routes-to-your-server"></a>將預設路由加入伺服器
新增基本 CRUD 路由：**建立**、**擷取**、**更新**和**刪除**。

1.  在命令提示字元中，將目錄切換至 **azuread**：

    `cd azuread`

2.  在編輯器中開啟 Server.js。 在您稍早輸入的資料庫項目底下，新增下列資訊：

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();
    _task.save(function(err) {
    if (err) {
    req.log.warn(err, 'createTask: unable to save');
    next(err);
    } else {
    res.send(201, _task);
    }
    });
    return next();
    }
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
    }
    if (!owner) {
    log.warn(err, "You did not pass an owner when listing tasks.");
    } else {
    res.json(data);
    }
    });
    return next();
    }
    ```

### <a name="add-error-handling-for-the-routes"></a>新增路由的錯誤處理
新增一些錯誤處理，以便向用戶端通知您遇到的問題。

在您已撰寫的程式碼下方，新增下列程式碼︰

```Javascript
///--- Errors for communicating something more information back to the client.
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="14-create-your-server"></a>14：建立伺服器
最後一件事是新增您的伺服器執行個體。 伺服器執行個體會管理您的呼叫。

Restify (及 Express) 有深入的自訂功能可搭配 REST API 伺服器使用。 在本教學課程中，我們會使用最基本的設定。

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15：新增路由 (目前不含驗證)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16︰執行伺服器
您最好在新增驗證之前先測試伺服器。

測試伺服器最簡單方式是在命令提示字元中使用 curl。 若要這樣做，您只需要利用簡單的公用程式將輸出剖析為 JSON。 

1.  安裝我們將在下列範例中使用的 JSON 工具︰

    `$npm install -g jsontool`

    這會全域安裝 JSON 工具。

2.  請確定您的 MongoDB 執行個體正在執行：

    `$sudo mongod`

3.  將目錄切換至 **azuread**，然後執行 curl：

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  新增工作︰

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    回應應為：

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Brandon 的工作清單︰

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果所有這些命令都執行無誤，您就可以將 OAuth 新增至 REST API 伺服器。

「您現在已具有 REST API 伺服器和 MongoDB！」

## <a name="17-add-authentication-to-your-rest-api-server"></a>17：將驗證新增至 REST API 伺服器
現在您有一個執行中的 REST API，請設定它來搭配 Azure AD 使用。

在命令提示字元中，將目錄切換至 **azuread**：

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>使用 passport-azure-ad 隨附的 oidcbearerstrategy
到目前為止，您已經建置典型的 REST TODO 伺服器，且其中不含任何授權種類。 現在，新增驗證。

首先，指明您要使用 Passport。 將這段程式碼放在緊鄰您先前的伺服器設定後面：

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> 撰寫 API 時，最好一律將資料連結到使用者無法證明其在權杖中是唯一的項目。 當此伺服器儲存 TODO 項目時，它會根據權杖 (透過 token.sub 呼叫) 中的使用者訂用帳戶識別碼來儲存它們。 您需要將 token.sub 放在 “owner” 欄位中。 這可確保只有該使用者可以存取使用者的 TODO。 沒有其他人可以存取先前輸入的 TODO。 “owner” 完全不會公開在 API 中。 外部使用者可以要求其他使用者的 TODO (即使通過驗證)。
> 
> 

接下來，我們將使用隨附於 `passport-azure-ad` 的 Open ID Connect Bearer 策略。 將這段程式碼放在您稍早貼上的程式碼後面：

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport 會針對其所有策略 (Twitter、Facebook 等) 使用類似的模式。 所有策略寫入器均遵守此模式。 將使用權杖和 `done` 作為參數的 `function()` 傳遞給策略。 策略會在完成所有工作之後傳回。 請儲存使用者並隱藏權杖，這樣一來，您就不必再次要求它。

> [!IMPORTANT]
> 上述程式碼會將可通過驗證的所有使用者帶往您的伺服器。 這就是所謂的自動註冊。 在生產伺服器中，您應該會想要讓所有人都必須先完成您選擇的註冊過程，才能進入您的伺服器。 這是您通常會在消費者應用程式中看到的模式。 應用程式可能會允許您使用 Facebook 進行註冊，但之後會要求您輸入其他資訊。 如果您沒有針對本教學課程使用命令列程式，可以從傳回的權杖物件中擷取電子郵件。 然後，您可能會要求使用者輸入其他資訊。 由於這是測試伺服器，您會將使用者直接加入記憶體中的資料庫。
> 
> 

### <a name="protect-endpoints"></a>保護端點
您可以透過想要使用的通訊協定指定 **passport.authenticate()** 呼叫，以保護端點。

您可以在伺服器程式碼中編輯路由，以利用更進階的選項︰

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18︰再次執行伺服器應用程式
再次使用 curl，檢查您是否以 OAuth 2.0 保護您的端點。 請在您針對這個端點執行任何用戶端 SDK 之前這樣做。 傳回的標頭應該會讓您知道驗證是否正常運作。

1.  請確定您的 MongoDB 執行個體正在執行：

    `$sudo mongod`

2.  切換至 **azuread** 目錄，然後使用 curl：

    `$ cd azuread`

    `$ node server.js`

3.  嘗試基本的 POST 方法：

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

401 回應會指出 Passport 層正嘗試重新導向至已授權的端點，這正是您想要的結果。

「您現在擁有一項使用 OAuth 2.0 的 REST API 服務！」

在未使用 OAuth 2.0 相容用戶端的情況下，您已經儘可能地使用此伺服器的所有功能。 在這方面，您必須檢閱其他教學課程。

## <a name="next-steps"></a>後續步驟
做為參考，我們以 [.zip 檔案](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)的形式提供已完成的範例 (不含您的設定值)。 您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

現在，您可以進入更進階的主題。 您可以嘗試[使用 v2.0 端點保護 Node.js Web 應用程式](active-directory-v2-devquickstarts-node-web.md)。

以下是一些其他資源：

* [Azure AD v2.0 開發人員指南](active-directory-appmodel-v2-overview.md)
* [Stack Overflow "azure-active-directory" 標籤 (英文)](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新
我們建議您註冊，即可在發生安全性事件時收到通知。 請在 [Microsoft 技術安全性通知](https://technet.microsoft.com/security/dd252948)頁面上，訂閱資訊安全摘要報告警示。

