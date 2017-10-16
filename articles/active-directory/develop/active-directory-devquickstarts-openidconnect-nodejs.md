---
title: "開始使用 Node.js 進行 Azure AD 登入和登出 | Microsoft Docs"
description: "了解如何建立可整合 Azure AD 以進行登入的 Node.js Express MVS Web 應用程式。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13317b016f9ff3955f376b858645c42668b0de42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>搭配 Azure AD 的 Node.js Web 應用程式登入和登出
在此我們使用 Passport 來執行下列動作：

* 使用 Azure Active Directory (Azure AD) 將使用者登入應用程式。
* 顯示使用者的相關資訊。
* 讓使用者登出 App。

Passport 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 架構或 resitify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他驗證方法。 我們已為 Microsoft Azure Active Directory 開發一項策略。 我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

若要這麼做，遵循下列步驟：

1. 註冊應用程式。
2. 設定應用程式為使用 `passport-azure-ad` 策略。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印使用者的相關資料。

本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)。  若要遵循執行，您可以[下載應用程式基本架構的 .zip 檔](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

本教學課程最後也會提供完整的應用程式。

## <a name="step-1-register-an-app"></a>步驟 1：註冊應用程式
1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在頁面頂端的功能表中，選取您的帳戶。 在 [目錄] 清單下，選擇您要註冊應用程式的 Active Directory 租用戶。

3. 選取畫面左側功能表中的 [更多服務]，然後選取 [Azure Active Directory]。

4. 選取 [應用程式註冊]，然後選取 [新增]。

5. 遵照提示建立 **Web 應用程式**和/或 **Web API**。
  * 應用程式的 [名稱] 對使用者說明您的應用程式。

  * [ **登入 URL** ] 是指應用程式的基底 URL。  基本架構的預設值是 `http://localhost:3000/auth/openid/return`` 。

6. 註冊之後，Azure AD 會指派唯一的應用程式識別碼給您的應用程式。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。
7. 從應用程式的 [設定]  ->  [屬性] 頁面，更新應用程式識別碼 URI。 [ **應用程式識別碼 URI** ] 是指應用程式的唯一識別碼。 慣例是使用 `https://<tenant-domain>/<app-name>` 格式，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`。

## <a name="step-2-add-prerequisites-to-your-directory"></a>步驟 2︰在目錄中新增必要條件
1. 從命令列中，將目錄位置變更為根資料夾 (若目錄位置原本不在該處)，然後執行下列命令：

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. 此外，您需要 `passport-azure-ad`：
    * `npm install passport-azure-ad`

這會安裝 `passport-azure-ad` 所仰賴的程式庫。

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>步驟 3︰設定應用程式使用 passport-node-js 策略
在這裡，我們將設定 Express 使用 OpenID Connect 驗證通訊協定。  Passport 用來做許多事，包括發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊。

1. 若要開始，開啟專案根目錄中的 `config.js` 檔案，並在 `exports.creds` 區段中輸入應用程式的組態值。

  * `clientID` 是在註冊入口網站中指派給應用程式的**應用程式識別碼**。

  * `returnURL` 是您在入口網站中輸入的**重新導向 URI**。

  * `clientSecret` 是您在入口網站中輸入的密碼。

2. 接下來，開啟專案根目錄中的 `app.js` 檔案。 然後新增下列呼叫來叫用 `passport-azure-ad` 隨附的 `OIDCStrategy` 策略。

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. 之後，使用我們剛才提及的策略來處理登入要求。

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。 查看策略，您會看見我們將它當成函式來傳遞，其中帶有一個 token 和一個 done 做為參數。 策略完成所有工作之後會回到我們這邊。 然後我們會想要儲存使用者並隱藏權杖，我們便不需要再次要求它。

> [!IMPORTANT]
先前程式碼會讓所有使用者經歷伺服器的驗證。 這就是所謂的自動註冊。 建議不要讓任何使用者未先透過您所決定的程序進行註冊，就向生產伺服器進行驗證。 此模式通常見於消費者應用程式，可允許您向 Facebook 進行註冊，但接著會要求您提供其他資訊。 如果這不是一個範例應用程式，我們就會從所傳回的權杖物件中擷取使用者的電子郵件地址，然後要求該使用者填寫其他資訊。 由於這是一部測試伺服器，因此我們會將它們新增到記憶體內部資料庫。


4. 接下來，我們會新增方法，讓我們可以如 Passport 所要求追蹤已登入的使用者。 這些方法包括將使用者資訊序列化和還原序列化。

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
            var users = [];

            var findByEmail = function(email, fn) {
            for (var i = 0, len = users.length; i < len; i++) {
                var user = users[i];
            log.info('we are using user: ', user);
                if (user.email === email) {
                return fn(null, user);
                }
            }
            return fn(null, null);
            };
    ```

5.  接下來，加入可載入 Express 引擎的程式碼。 在此處可看到我們使用 Express 所提供的預設 /views 和 /routes 模式。

    ```JavaScript

        // configure Express (section 2)

            var app = express();
            app.configure(function() {
          app.set('views', __dirname + '/views');
          app.set('view engine', 'ejs');
          app.use(express.logger());
          app.use(express.methodOverride());
          app.use(cookieParser());
          app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
          app.use(bodyParser.urlencoded({ extended : true }));
          // Initialize Passport!  Also use passport.session() middleware, to support
          // persistent login sessions (recommended).
          app.use(passport.initialize());
          app.use(passport.session());
          app.use(app.router);
          app.use(express.static(__dirname + '/../../public'));
        });

    ```

6. 最後，新增路由以將實際的登入要求遞交給 `passport-azure-ad` 引擎：


       ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
       ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>步驟 4：使用 Passport，向 Azure AD 發出登入和登出要求
您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與端點通訊。  `passport-azure-ad` 已經處理有關製作驗證訊息、驗證 Azure AD 的權杖及維護使用者工作階段的所有細節。 剩下的工作就是讓使用者有辦法登入和登出，以及收集關於已登入使用者的其他資訊。

1. 首先，在 `app.js` 檔案中加入預設、登入、帳戶和登出方法：

    ```JavaScript

        //Routes (section 4)

        app.get('/', function(req, res){
          res.render('index', { user: req.user });
        });

        app.get('/account', ensureAuthenticated, function(req, res){
          res.render('account', { user: req.user });
        });

        app.get('/login',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  讓我們詳細檢閱這些方法：

  * `/` 路由將重新導向至 index.ejs 檢視，並在要求中傳遞使用者 (若有的話)。
  * `/account` 路由會先「確定我們已通過驗證」 (我們將在以下範例中實作)，然後在要求中傳遞使用者，讓我們能夠取得關於該使用者的其他資訊。
  * `/login` 路由會從 `passport-azuread` 呼叫我們的 azuread openidconnect 驗證器。 如果沒有成功，路由便會將使用者重新導向回 /login。
  * `/logout` 路由會直接呼叫 logout.ejs (和路由)，後者會清除 Cookie，然後讓使用者返回 index.ejs。

3. 針對 `app.js` 的最後一個部分，加入在 `/account` 中使用的 **EnsureAuthenticated** 方法，如先前所述。

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. 最後，在 `app.js` 中建立伺服器本身：

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>步驟 5︰若要在網站中顯示使用者，在 Express 中建立檢視與路由
現在，`app.js` 已經完成。 我們只需新增路由和檢視即可，這兩者會向使用者顯示我們取得的資訊，以及控制我們建立的 `/logout` 和 `/login` 路由。

1. 在根目錄下方建立 `/routes/index.js` 路由。

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. 在根目錄下方建立 `/routes/user.js` 路由。

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 這些會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

3. 在根目錄底下建立 `/views/index.ejs` 檢視。 這是簡單網頁，可呼叫我們的登入和登出方法，且讓我們能夠抓取帳戶資訊。 請注意，我們可以使用條件式 `if (!user)`，因為在要求中傳遞使用者就證實我們擁有已登入的使用者。

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. 在根目錄下方建立 `/views/account.ejs` 檢視，如此即可檢視 `passport-azuread` 放置於使用者要求的其他資訊。

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. 新增版面配置，使它看起來更美觀。 在根目錄底下建立 '/views/layout.ejs' 檢視。

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>後續步驟
最後，建置並執行您的應用程式。 執行 `node app.js`，然後前往 `http://localhost:3000`。

使用個人的 Microsoft 帳戶或工作或學校帳戶登入，並注意 /account 清單中使用者身分識別的反映狀態。 您的 Web 應用程式現在使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

[這裡以 .zip 檔案提供](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)完整範例 (不含您的設定值) 供您參考。 或者，您也可以從 Github 複製它：

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

您現在可以進入更進階的主題。 您可能想嘗試：

[使用 Azure AD 保護 Web API](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
