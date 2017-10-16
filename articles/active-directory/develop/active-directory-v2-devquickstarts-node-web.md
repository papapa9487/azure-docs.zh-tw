---
title: "Azure Active Directory v2.0 Node.js Web 應用程式登入 | Microsoft Docs"
description: "了解如何建置可使用個人 Microsoft 帳戶及公司或學校帳戶將使用者登入的 Node.js Web 應用程式。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>將登入新增至 Node.js Web 應用程式

> [!NOTE]
> 並非所有的 Azure Active Directory 案例和功能都可以和 v2.0 端點搭配使用。 若要判斷您應該使用 v2.0 端點或 v1.0 端點，請參閱 [v2.0 限制](active-directory-v2-limitations.md)。
> 

在本教學課程中，我們會使用 Passport 執行下列工作：

* 在 Web 應用程式中，使用 Azure Active Directory (Azure AD) 和 v2.0 端點將使用者登入。
* 顯示使用者的相關資訊。
* 讓使用者登出 App。

**Passport** 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 架構或 resitify Web 應用程式。 在 Passport 中，一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他選項進行驗證。 我們已為 Azure AD 開發一個策略。 在本文中，我們會向您說明如何安裝模組，然後新增 Azure AD `passport-azure-ad` 外掛程式。

## <a name="download"></a>下載
本教學課程的程式碼保留在 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)。 若要依照教學課程執行，您可以[下載應用程式基本架構的 .zip 檔案](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)，或複製基本架構：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

您也可以在本教學課程結束時取得完整的應用程式。

## <a name="1-register-an-app"></a>1：註冊應用程式
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 建立新的應用程式，或遵循[這些詳細步驟](active-directory-v2-app-registration.md)來註冊應用程式。 請確定您已執行下列動作：

* 複製指派給您應用程式的「應用程式識別碼」。 您在本教學課程中會需要用到。
* 為您的應用程式新增 **Web** 平台。
* 複製入口網站的「重新導向 URI」  。 您必須使用預設 URI 值：`urn:ietf:wg:oauth:2.0:oob`。

## <a name="2-add-prerequisities-to-your-directory"></a>2：將必要條件新增至目錄
在命令提示字元中，將目錄變更至根資料夾 (如果您尚未在此目錄下)。 執行以下命令：

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

此外，我們會在快速入門的基本架構中使用 `passport-azure-ad`：

* `npm install passport-azure-ad`

這會安裝 `passport-azure-ad` 所使用的程式庫。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3：設定您的應用程式以使用 passport-node-js 策略
設定 Express 中介軟體以使用 OpenID Connect 驗證通訊協定。 您會使用 Passport 來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等作業。

1.  開啟專案根目錄中的 Config.js 檔案。 在 `exports.creds` 區段中，輸入您應用程式的設定值。
  
  * `clientID`：在 Azure 入口網站中指派給您應用程式的「應用程式識別碼」。
  * `returnURL`：您在入口網站中輸入的「重新導向 URI」。
  * `clientSecret`：您在入口網站中產生的密碼。

2.  開啟專案根目錄中的 App.js 檔案。 若要叫用隨附於 `passport-azure-ad` 的 OIDCStrategy 策略，請新增以下呼叫：

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  若要處理登入要求，請使用剛剛參考的策略：

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Passport 會針對其所有策略 (Twitter、Facebook 等) 使用類似的模式。 所有策略寫入器均遵守此模式。 將使用權杖和 `done` 作為參數的 `function()` 傳遞給策略。 策略會在完成所有工作之後傳回。 請儲存使用者並隱藏權杖，這樣一來，您就不必再次要求它。

  > [!IMPORTANT]
  > 上述程式碼會將可通過驗證的所有使用者帶往您的伺服器。 這就是所謂的自動註冊。 在生產伺服器中，您應該會想要讓所有人都必須先完成您選擇的註冊過程，才能進入您的伺服器。 這是您通常會在消費者應用程式中看到的模式。 應用程式可能會允許您使用 Facebook 進行註冊，但之後會要求您輸入其他資訊。 如果您沒有針對本教學課程使用命令列程式，可以從傳回的權杖物件中擷取電子郵件。 然後，您可能會要求使用者輸入其他資訊。 由於這是測試伺服器，您會將使用者直接加入記憶體中的資料庫。
  > 
  > 

4.  按照 Passport 的要求，新增可用來追蹤已登入使用者的方法。 這包括將使用者資訊序列化和還原序列化：

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  新增可載入 Express 引擎的程式碼。 您可以使用 Express 所提供的預設 /views 和 /routes 模式：

  ```JavaScript

  // Set up Express (section 2)

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

6.  新增 POST 路由以將實際的登入要求遞交給 `passport-azure-ad` 引擎：

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4：使用 Passport，向 Azure AD 發出登入和登出要求
您的應用程式現在已設定為可使用 OpenID Connect 驗證通訊協定與 v2.0 端點通訊。 `passport-azure-ad` 策略會處理有關製作驗證訊息、驗證來自 Azure AD 的權杖，以及維護使用者工作階段的所有細節。 剩下的工作就是提供使用者登入和登出的方法，以及收集關於已登入使用者的詳細資訊。

1.  首先，將 **default**、**login**、**account** 及 **logout** 方法加入 App.js 檔案：

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
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  詳細資料如下：
    
    * `/` 路由會重新導向到 index.ejs 檢視。 它會在要求 (如果有的話) 中傳遞使用者。
    * `/account` 路由會先「確保您已通過驗證」 (您會在以下程式碼中實作此功能)。 然後，它會在要求中傳遞使用者。 這可讓您取得更多有關該使用者的資訊。
    * `/login` 路由會從 `passport-azuread` 呼叫您的 `azuread-openidconnect` 驗證器。 如果沒有成功，路由便會將使用者重新導向回 `/login`。
    * `/logout` 路由會呼叫 logout.ejs 檢視 (並進行路由)。 這會清除 Cookie，然後讓使用者回到 index.ejs。

2.  新增您之前在 `/account` 中使用的 **EnsureAuthenticated** 方法：

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  在 App.js 中建立伺服器：

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5：在 Express 中建立會在網站中向使用者顯示的檢視與路由
新增向使用者顯示資訊的路由和檢視。 路由和檢視也會處理您建立的 `/logout` 和 `/login` 路由。

1. 在根目錄中建立 `/routes/index.js` 路由。

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  在根目錄中建立 `/routes/user.js` 路由。

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` 和 `/routes/user.js` 為簡易路由，會將要求 (如果有的話) 一起傳遞至您的檢視，包括使用者。

3.  在根目錄中建立 `/views/index.ejs` 檢視。 此頁面會呼叫您的 **login** 和 **logout** 方法。 您也會使用 `/views/index.ejs` 檢視來擷取帳戶資訊。 您可以使用條件式 `if (!user)` 做為在要求中傳遞的使用者。 這可以證明您已經有使用者登入。

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  在根目錄中建立 `/views/account.ejs` 檢視。 `/views/account.ejs` 檢視可允許您檢視 `passport-azuread` 置於使用者要求中的其他資訊。

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  新增版面配置。 在根目錄中建立 `/views/layout.ejs` 檢視。

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  若要建置並執行您的應用程式，請執行 `node app.js`。 然後前往 `http://localhost:3000`。

7.  使用個人 Microsoft 帳戶或是公司或學校帳戶登入。 請注意，使用者的身分識別會反映在 /account 清單中。 

您現在已擁有使用業界標準通訊協定保護的 Web 應用程式了。 您可以在應用程式中利用使用者的個人和公司或學校帳戶驗證他們的身分。

## <a name="next-steps"></a>後續步驟
做為參考，我們以 [.zip 檔案](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)的形式提供已完成的範例 (不含您的設定值)。 您也可以從 GitHub 加以複製：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

接下來，您可以進入更進階的主題。 您可能想嘗試：

[使用 v2.0 端點保護 Node.js Web API](active-directory-v2-devquickstarts-node-api.md)

以下是一些其他資源：

* [Azure AD v2.0 開發人員指南](active-directory-appmodel-v2-overview.md)
* [Stack Overflow "azure-active-directory" 標籤 (英文)](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新
我們建議您註冊，即可在發生安全性事件時收到通知。 請在 [Microsoft 技術安全性通知](https://technet.microsoft.com/security/dd252948)頁面上，訂閱資訊安全摘要報告警示。

