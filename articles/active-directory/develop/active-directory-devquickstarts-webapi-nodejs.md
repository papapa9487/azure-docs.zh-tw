---
title: "Azure AD Node.js 入門 | Microsoft Docs"
description: "如何建立可整合 Azure AD 以進行驗證的 Node.js REST Web API。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 4f58177f540c14172d7ece8b4bc8c8a2b9787f8f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-web-apis-for-nodejs"></a>開始使用適用於 Node.js 的 Web API
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 架構或 Restify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。 我們已為 Microsoft Azure Active Directory (Azure AD) 開發一項策略。 我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

若要這樣做，您需要：

1. 向 Azure AD 註冊應用程式。
2. 設定您的應用程式來使用 Passport 的 `passport-azure-ad` 外掛程式。
3. 設定用戶端應用程式呼叫待辦事項清單 Web API。

本教學課程的程式碼保留在 [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi)。

> [!NOTE]
> 本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊或管理設定檔。 而會著重在如何在使用者已通過驗證後呼叫 Web API。  我們建議您從[如何與 Azure Active Directory 整合文件](active-directory-how-to-integrate.md)著手，以了解 Azure Active Directory 的基本概念。
>
>

我們已在 MIT 授權底下的 GitHub 中發行這個執行範例的所有原始程式碼，因此您可以隨意複製 (或更棒的是您可以分散出去) 和提供意見反應及提取要求。

## <a name="about-nodejs-modules"></a>關於 Node.js 模組
我們會在本逐步解說中使用 Node.js 模組。 模組是指可載入的 JavaScript 封裝，可為您的應用程式提供特定功能。 您通常可以使用 NPM 安裝目錄中的 Node.js NPM 命令列工具來安裝模組。 但是，核心 Node.js 封裝中已包含一些模組 (例如 HTTP 模組)。

已安裝的模組會儲存在 Node.js 安裝目錄的根目錄下的 **node_modules** 目錄。 **node_modules** 目錄下的每個模組都會維護它自己的 **node_modules** 目錄，其中包含它所依賴的任何模組。 此外，每個必要模組都有**node_modules** 目錄。 這個遞迴目錄結構表示相依性鏈結。

此相依性鏈結結構會導致較高的應用程式使用量。 但它也會保證已符合所有相依性，而且用於開發的模組版本也會用於生產環境中。 這可讓實際執行的應用程式行為更容易預測，並防止可能會影響使用者的版本控制問題。

## <a name="step-1-register-an-azure-ad-tenant"></a>步驟 1：註冊 Azure AD 租用戶
若要使用這個範例，您需要 Azure Active Directory 租用戶。 如果您不確定什麼是租用戶或如何取得租用戶，請參閱[如何取得 Azure AD 租用戶](active-directory-howto-tenant.md)。

## <a name="step-2-create-an-application"></a>步驟 2：建立應用程式
接下來，在您的目錄中建立應用程式，以提供必要資訊給 Azure AD，讓它與應用程式安全地通訊。  在此案例中，因為用戶端應用程式和 Web API 會組成一個邏輯應用程式，所以由單一**應用程式識別碼**表示。  如果要建立應用程式，請遵循 [這些指示](active-directory-how-applications-are-added.md)。 如果您要建置企業營運應用程式，[這些額外的指示可能很實用](../active-directory-applications-guiding-developers-for-lob-applications.md)。

若要建立應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在頂端功能表上，選取您的帳戶。 然後，在 [目錄] 清單下，選擇您要註冊應用程式的 Active Directory 租用戶。

3. 在左側功能表中選取 [更多服務]，然後選取 [Azure Active Directory]。

4. 選取 [應用程式註冊]，然後選取 [新增]。

5. 遵照提示建立 **Web 應用程式和/或 Web API**。

      * 應用程式的 [名稱] 對使用者說明您的應用程式。

      * [ **登入 URL** ] 是指應用程式的基底 URL。  範例程式碼的預設 URL 是 `https://localhost:8080`。

6. 註冊之後，Azure AD 會指派唯一的應用程式識別碼給您的應用程式。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。

7. 從應用程式的 [設定]  ->  [屬性] 頁面，更新應用程式識別碼 URI。 [ **應用程式識別碼 URI** ] 是指應用程式的唯一識別碼。 慣例是使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`。

8. 從 [設定] 頁面建立應用程式的 [金鑰]，然後複製在某處。 稍後您將會用到此資訊。

## <a name="step-3-download-nodejs-for-your-platform"></a>步驟 3：下載您的平台適用的 Node.js
若要成功使用此範例，您必須具備已成功安裝的 Node.js。

從 [http://nodejs.org](http://nodejs.org)安裝 Node.js。

## <a name="step-4-install-mongodb-on-your-platform"></a>步驟 4：在您的平台上安裝 MongoDB
若要成功使用此範例，您必須具備已成功安裝的 MongoDB。 您可使用 MongoDB，讓 REST API 得以在不同伺服器執行個體之間持續使用。

從 [http://mongodb.org](http://www.mongodb.org)安裝 MongoDB。

> [!NOTE]
> 本逐步解說假設您會使用 MongoDB 的預設安裝和伺服器端點，在撰寫本文時為 mongodb://localhost。
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>步驟 5：在您的 Web API 中安裝 Restify 模組
我們會使用 Resitfy 來建置 REST API。 Restify 是衍生自 Express 的最小且具彈性的 Node.js 應用程式架構。 它有一組強大的功能，可在 Connect 之上建置 REST API。

### <a name="install-restify"></a>安裝 Restify
1. 從命令列將目錄變更至 **azuread** 目錄。 如果 **azuread** 目錄不存在，請予以建立。

        `cd azuread - or- mkdir azuread; cd azuread`

2. 輸入以下命令：

    `npm install restify`

    此命令會安裝 Restify。

#### <a name="did-you-get-an-error"></a>您有收到錯誤訊息嗎？
當您在某些作業系統上使用 NPM 時，可能會收到以下錯誤：**Error: EPERM, chmod '/usr/local/bin/..'** ，且建議您嘗試以管理員身分執行該帳戶。 若發生這個情況，使用 sudo 命令以更高的權限層級執行 NPM。

#### <a name="did-you-get-an-error-regarding-dtrace"></a>您有收到有關 DTRACE 的錯誤訊息嗎？
安裝 Restify 時，您可能會看到如下的錯誤：

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
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
Restify 提供強大機制來使用 DTrace 追蹤 REST 呼叫。 不過，許多作業系統沒有 DTrace。 您可以放心地忽略這些錯誤。

此命令的輸出應類似下列輸出：

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
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="step-6-install-passportjs-in-your-web-api"></a>步驟 6：在您的 Web API 中安裝 Passport.js
[Passport](http://passportjs.org/) 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 架構或 Restify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。

我們已為 Azure Active Directory 開發一個策略。 我們會安裝此模組，然後加入 Azure Active Directory 的策略外掛程式。

1. 從命令列將目錄變更至 **azuread** 目錄。

2. 若要安裝 passport.js，請輸入以下命令：

    `npm install passport`

    此命令的輸出應類似這樣：

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>步驟 7：將 Passport-Azure-AD 新增至您的 Web API
接下來，我們會使用 `passport-azure-ad` 來新增 OAuth 策略，這是一套將 Azure Active Directory 連線到 Passport 的策略。 在這個 Rest API 範例中，我們會對持有人權杖使用此策略。

> [!NOTE]
> 雖然 OAuth2 提供可發行任何已知權杖類型的架構，但只有某些權杖類型經常使用。 持有人權杖是最常用於保護端點的權杖。 這是 OAuth2 中最普遍發行的權杖類型。 許多實作假設持有人權杖是唯一發行的權杖類型。
>
>

從命令列將目錄變更至 **azuread** 目錄。

輸入下列命令以安裝 Passport.js `passport-azure-ad module`：

`npm install passport-azure-ad`

此命令的輸出應類似下列輸出：


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>步驟 8：將 MongoDB 模組新增至 Web API
我們使用 MongoDB 做為資料存放區。 基於這個理由，我們必須安裝廣泛使用的外掛程式 Mongoose 來管理模型與結構描述。 我們也必須安裝 MongoDB 的資料庫驅動程式 (同樣也稱為 MongoDB)。

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>步驟 9：安裝其他模組
接下來，我們會安裝其餘所需的模組。

1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

    `cd azuread`

2. 輸入下列命令，在您的 **node_modules** 目錄中安裝下列模組：

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>步驟 10：使用您的相依性建立 server.js
server.js 檔案會提供 Web API 伺服器的大部分功能。 我們會將大部分的程式碼新增至此檔案。 基於生產目的，我們建議您將功能重整成較小的檔案，例如單獨分開的路由和控制器。 在此示範中，我們會在這項功能中使用 server.js。

1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

    `cd azuread`

2. 在喜愛的編輯器中建立 `server.js` 檔案，然後新增下列資訊：

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. 儲存檔案。 我們稍後會再回到此檔案。

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>步驟 11：建立可儲存您的 Azure AD 設定的組態檔
這個程式碼檔會將設定參數從您的 Azure Active Directory 入口網站傳遞到 Passport.js。 您會在將 Web API 新增至入口網站 (本逐步解說的第一個部分) 時建立這些設定值。 在您複製程式碼之後，我們會說明要放入這些參數值的內容。

1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

    `cd azuread`

2. 在喜愛的編輯器中建立 `config.js` 檔案，然後新增下列資訊：

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. 儲存檔案。

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>步驟 12：將組態值新增至 server.js 檔案
我們必須從您跨應用程式建立的 .config 檔案中讀取這些值。 若要這樣做，我們會將 .config 檔案新增為我們的應用程式中的必要資源。 然後，我們會設定全域變數，以符合 config.js 文件中的變數。

1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

    `cd azuread`

2. 在喜愛的編輯器中開啟 `server.js` 檔案，然後新增下列資訊：

    ```Javascript
    var config = require('./config');
    ```
3. 然後，使用下列程式碼在 `server.js` 中加入新的區段：

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. 儲存檔案。

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>步驟 13：使用 Moongoose 新增 MongoDB 模型和結構描述資訊
現在，當我們將這三個檔案組合成 REST API 服務時，您便會開始看到所有準備工作的成效。

在此逐步解說中，我們使用 MongoDB 來儲存工作，如步驟 4 所述。

在步驟 11 中建立的 `config.js` 檔案中，我們會呼叫資料庫 **，因為那是我們放在 `tasklist`mogoose_auth_local** 連線 URL 結尾處的內容。 您不需要在 MongoDB 中事先建立此資料庫。 相反地，MongoDB 會在第一次執行我們的伺服器應用程式時為我們建立此資料庫 (假設此資料庫尚未存在)。

既然我們已經告訴伺服器想要使用哪個 MongoDB 資料庫，我們必須撰寫一些額外程式碼，以建立伺服器工作的模型和結構描述。

### <a name="discussion-of-the-model"></a>模型的討論
我們的結構描述模型很簡單。 您可視需要加以擴充。

名稱：指派給工作的人員名稱。 **字串**。

工作：工作本身。 **字串**。

日期：工作到期的日期。 **DATETIME**。

已完成：工作是否已完成。 **布林值**。

### <a name="creating-the-schema-in-the-code"></a>在程式碼中建立結構描述
1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

    `cd azuread`

2. 在喜愛的編輯器中開啟 `server.js` 檔案，然後在組態項目下方新增下列資訊：

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
您可以從程式碼得知，我們先建立我們的結構描述。 然後我們會建立模型物件，以在定義**路由**時用來儲存整個程式碼中的資料。

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>步驟 14：新增工作 REST API 伺服器的路由
既然我們已經擁有可以使用的資料庫模型，讓我們新增即將用於 REST API 伺服器的路由。

### <a name="about-routes-in-restify"></a>關於 Restify 中的路由
Restify 與 Express 堆疊中的路由運作方式完全相同。 您可以使用您預期用戶端應用程式呼叫的 URI 來定義路由。 通常，您會在個別的檔案中定義您的路由。 基於本文的目的，我們會將路由放在 server.js 檔案中。 在生產環境中，建議您將這些路由分散到其各自的檔案。

Restify 路由的典型模式如下：

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


這是最基本層級的模式。 Resitfy (及 Express) 可提供更深入的功能，例如定義應用程式類型和提供不同端點之間的複雜路由。 基於本文的目的，我們會讓這些路由保持簡單。

### <a name="add-default-routes-to-our-server"></a>將預設路由加入伺服器
我們現在會新增建立、擷取、更新和刪除的基本 CRUD 路由。

1. 從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)：

    `cd azuread`

2. 在喜愛的編輯器中開啟 `server.js` 檔案，然後在您先前製作的資料庫項目底下新增下列資訊：

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>在我們的 API 中新增錯誤處理
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>步驟 15：建立伺服器
我們已定義資料庫並已備妥路由。 最後一件事是新增伺服器執行個體，以管理我們的呼叫。

在 Restify (及 Express) 中，您可以為 REST API 伺服器進行多項自訂，但再重申一次，基於本文的目的，我們即將使用最基本的設定。

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>步驟 16：將路由新增至伺服器 (目前不需驗證)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>步驟 17：執行伺服器 (在新增 OAuth 支援之前)
新增驗證之前，請先測試您的伺服器。

測試伺服器的最簡單方式是在命令列中使用 curl。 在執行此動作之前，我們需要一個可讓我們將輸出剖析為 JSON 的公用程式。

1. 安裝下列 JSON 工具 (下列所有範例都會使用此工具)︰

    `$npm install -g jsontool`

    這會全域安裝 JSON 工具。 現在已完成此動作，讓我們開始使用伺服器：

2. 首先，確定您的 MongoDB 執行個體正在執行：

    `$sudo mongod`

3. 然後，變更目錄並啟動 curling：

    `$ cd azuread` `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
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

4. 接著，我們可以透過以下方式新增工作：

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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
    而且我們可以透過以下方式列出 Brandon 的工作：

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果都沒問題，我們便可以開始將 OAuth 新增至 REST API 伺服器。

您必須將 REST API 伺服器搭配 MongoDB 使用！

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>步驟 18：將驗證新增至 REST API 伺服器
既然我們已有執行中的 REST API，我們就可以讓它在 Azure AD 中發揮其價值。

從命令列將目錄變更至 **azuread** 資料夾 (如果您尚未在此目錄中)。

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>使用 passport-azure-ad 隨附的 OIDCBearerStrategy
到目前為止，我們已經建置典型的 REST TODO 伺服器，且其中不含任何授權種類。 這是我們將其結合在一起的起點。

1. 首先，需指出要使用 Passport。 在其他伺服器設定之後緊接著執行此動作：

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > 撰寫 API 時，我們建議您一律將資料連結到使用者無法欺騙之權杖中唯一的項目。 此伺服器儲存 TODO 項目時，會根據我們放在 [擁有者] 欄位的權杖 (透過 token.sub 呼叫) 中使用者的物件識別碼來儲存這些項目。 這可確保只有該使用者可以存取自己的 TODO。 不會在「擁有者」API 中公開，因此，外部使用者可以要求其他人的 TODO，即使它們已經過驗證也一樣。                    

2. 接下來，使用 `passport-azure-ad` 隨附的持有人策略。 目前查看一下此程式碼，我們很快就會說明其他部分。 將這段程式碼放在您上面所貼內容的後面：

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
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


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。 查看此策略，您會看見我們將它當成函式來傳遞，其有一個 token 和一個 done 做為參數。 策略完成所有工作之後會回到我們這邊。 當它完成之後，我們會儲存使用者並存放權杖，因此不需要再次要求它。

> [!IMPORTANT]
> 先前程式碼會讓所有使用者經歷伺服器的驗證。 這就是所謂的自動註冊。 在生產伺服器中，我們建議您要讓所有人都必須先經歷您所決定的註冊過程。 這通常是您在取用者應用程式中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。 如果這不是命令列程式，我們可以從傳回的權杖物件中擷取電子郵件，然後要求使用者填寫其他資訊。 由於這是測試伺服器，因此，我們直接將它們加入記憶體中的資料庫。
>
>

### <a name="protect-some-endpoints"></a>保護某些端點
透過您想要使用的通訊協定來指定 `passport.authenticate()` 呼叫，即可保護端點。

若要讓我們的伺服器程式碼執行更有趣的作業，請編輯路由。

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>步驟 19：再次執行應用程式伺服器，並確保它會拒絕您的要求
讓我們再次使用 `curl`，以查看我們現在是否有針對端點的 OAuth2 保護。 我們會在對此端點執行任何用戶端 SDK 之前，執行此動作。 傳回的標頭應該足以說明我們朝著正確的路徑前進。

1. 首先，確定您的 MongoDB 執行個體正在執行：

    `$sudo mongod`

2. 然後，變更目錄並啟動 curling。

      `$ cd azuread` `$ node server.js`

3. 嘗試基本 POST。

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

此時 401 是您期待的回應。 此回應指出 Passport 層正嘗試重新導向至已授權的端點，這正是您想要的結果。

## <a name="next-steps"></a>後續步驟
在未使用 OAuth2 相容用戶端的情況下，您已經儘可能地使用此伺服器的所有功能。 您還必須完成其他逐步解說。

您現在已了解如何使用 Restify 和 OAuth2 實作 REST API。 您也已經有足夠的程式碼可以繼續開發服務，並學習如何以此範例為基礎進行建置。

如果您對執行 ADAL 的後續步驟感興趣，以下是一些我們建議的支援 ADAL 用戶端，可供您繼續使用。

複製到您的開發人員機器，並如本逐步解說所述進行設定即可。

[ADAL for iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL for Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
