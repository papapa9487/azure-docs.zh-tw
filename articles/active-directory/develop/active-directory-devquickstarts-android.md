---
title: "Azure AD Android 入門 | Microsoft Docs"
description: "如何建置 Android 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 746cad19093fd2a1ad23ddd9412394f8d9da331c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="integrate-azure-ad-into-an-android-app"></a>將 Azure AD 整合至 Android 應用程式
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> 試用新的[開發人員入口網站](https://identity.microsoft.com/Docs/Android)預覽版本，這可協助您在短短幾分鐘內啟動並執行 Azure AD。 開發人員入口網站會逐步引導您完成註冊應用程式並將 Azure AD 整合至您的程式碼的程序。 當您完成時，您會有可驗證租用戶中使用者的簡單應用程式，以及可接受權杖並執行驗證的後端。
>
>

如果您正在開發桌面應用程式，Azure Active Directory (Azure AD) 讓您可以更簡單直接地用使用者的內部部署 Active Directory 帳戶來驗證他們。 它也可讓您的應用程式安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

對於需要存取受保護資源的 Android 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (ADAL)。 ADAL 的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。 為了示範究竟多麼簡單，我們將建置一個執行下列動作的 Android「待辦事項清單」應用程式：

* 取得存取權杖來使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)呼叫待辦事項清單 API。
* 取得使用者的待辦事項清單。
* 登出使用者。

首先，您需要 Azure AD 租用戶，供您建立使用者並註冊應用程式。 如果您還沒有租用戶， [了解如何取得租用戶](active-directory-howto-tenant.md)。

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>步驟 1：下載並執行 Node.js REST API TODO 範例伺服器
我們特別撰寫 Node.js REST API TODO 範例，以有別於為 Azure AD 建置單一租用戶 To-Do REST API 的現有範例。 這是快速入門的必要條件。

如需如何設置此 API 的資訊，請參閱現有範例[適用於 Node.js 的 Microsoft Azure Active Directory 範例 REST API 服務](active-directory-devquickstarts-webapi-nodejs.md)。


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>步驟 2：向 Azure AD 租用戶註冊 Web API
Active Directory 支援加入兩種類型的應用程式：

- 將服務提供給使用者的 Web API
- 會存取這些 Web API 的應用程式 (在 Web 或裝置上執行)

在此步驟中，您要註冊在本機執行且用來測試此範例的 Web API。 這個 Web API 通常是 REST 服務，提供您想要讓應用程式存取的功能。 Azure AD 可以保護任何端點。

我們假設您是註冊稍早提及的 TODO REST API。 但這個程序適用於任何您要讓 Azure Active Directory 保護的 Web API。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列中，按一下您的帳戶。 在 [目錄] 清單中，選擇您要註冊應用程式的 Azure AD 租用戶。
3. 按一下左側窗格中的 [更多服務]，然後選取 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選取 [新增]。
5. 輸入應用程式的易記名稱，例如 **TodoListService**)，選取 [Web 應用程式和/或 Web API]，然後按 [下一步]。
6. 在 [登入 URL] 中，輸入範例的基礎 URL。 依預設，這會是 `https://localhost:8080`。
7. 按一下 [確定] 完成註冊。
8. 仍是在 Azure 入口網站中，移至您的應用程式頁面，找出 [應用程式識別碼] 的值並複製。 您稍後在設定應用程式時需要此資訊。
9. 從 [設定]  ->  [屬性] 頁面，更新應用程式識別碼 URI - 輸入 `https://<your_tenant_name>/TodoListService`。 將 `<your_tenant_name>` 取代為您的 Azure AD 租用戶名稱。

## <a name="step-3-register-the-sample-android-native-client-application"></a>步驟 3：註冊範例 Android 原生用戶端應用程式
在此範例中，您必須註冊您的 Web 應用程式。 這可讓您的應用程式與剛註冊的 Web API 通訊。 除非註冊應用程式，否則 Azure AD 甚至會拒絕讓您的應用程式要求登入。 這是模型安全性的一環。

我們假設您是註冊稍早提及的範例應用程式。 但此程序適用於任何您正在開發的應用程式。

> [!NOTE]
> 也許您會納悶，為什麼要將應用程式和 Web API 都放在一個租用戶。 答案您可能已經猜到，您可以建置應用程式來存取在另一個租用戶的 Azure AD 中註冊的外部 API。 如果您這麼做，系統會提示您的客戶同意您可以使用應用程式中的 API。 Active Directory Authentication Library for iOS 會替您處理此同意舉動。 隨著我們深入更進階的功能，您將了解這是從 Azure 和 Office 及任何其他服務提供者存取 Microsoft API 套件時所需的一件重要工作。 現在，因為您在相同租用戶下註冊您的 Web API 和應用程式，因此您不會看到任何要求同意的提示。 如果您開發的應用程式僅供自己公司使用，通常會是這種情況。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列中，按一下您的帳戶。 在 [目錄] 清單中，選擇您要註冊應用程式的 Azure AD 租用戶。
3. 按一下左側窗格中的 [更多服務]，然後選取 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選取 [新增]。
5. 輸入應用程式的易記名稱，例如 **TodoListClient-Android**，選取 [原生用戶端應用程式]，然後按 [下一步]。
6. 在 [重新導向 URI] 中，輸入 `http://TodoListClient`。 按一下 [完成]。
7. 在應用程式頁面中，找到應用程式識別碼的值並複製。 您稍後在設定應用程式時需要此資訊。
8. 在 [設定] 頁面中，選取 [必要的權限]，然後選取 [新增]。  找出並選取 [TodoListService]，然後在 [委派的權限] 底下新增 [存取 TodoListService] 權限，按一下 [完成]。

若要使用 Maven 來建置，您可以使用最上層的 pom.xml：

1. 將此儲存機制複製到您選擇的目錄：

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  
2. 請遵循[為 Android 設定 Maven 環境的必要條件](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)中的步驟。
3. 使用 SDK 19 設定模擬器。
4. 移至您已複製儲存機制的根資料夾。
5. 執行這個命令：`mvn clean install`
6. 將目錄變更為快速入門範例：`cd samples\hello`
7. 執行這個命令：`mvn android:deploy android:run`

   您應該會看到應用程式啟動。
8. 輸入測試使用者認證來測試一下。

除了 AAR 套件，也會提交 JAR 套件。

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>步驟 4：下載 Android ADAL，並將它加入您的 Eclipse 工作區
我們提供多個選項，讓您輕鬆地在 Android 專案中使用 ADAL：

* 您可以使用原始程式碼將此程式庫匯入到 Eclipse，並連結至您的應用程式。
* 如果您使用 Android Studio，可以使用 AAR 套件格式並參考二進位檔。

### <a name="option-1-source-zip"></a>選項 1：原始檔 Zip
若要下載原始程式碼，按一下頁面右側的 [下載 ZIP]。 或者，可以[從 GitHub 下載](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz)。

### <a name="option-2-source-via-git"></a>選項 2：透過 Git 取得原始檔
若要透過 Git 取得 SDK 的原始程式碼，請輸入：

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>選項 3：透過 Gradle 取得二進位檔
您可以從 Maven 中央儲存機制取得二進位檔。 AAR 套件可以在 AndroidStudio 中加入您的專案中，如下所示：

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>選項 4：透過 Maven 取得 AAR
如果您使用 M2E 外掛程式，可以在 pom.xml 檔案中指定相依性：

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>選項 5：libs 資料夾內的 JAR 套件
您可以從 Maven 儲存機制取得 JAR 檔案，並放入專案的 **libs** 資料夾中。 您也需要將必要的資源複製到專案，因為 JAR 套件中沒有它們。

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>步驟 5：將 Android ADAL 參考加入至您的專案
1. 在專案中加入參考，並指定為 Android 程式庫。 如果您不確定怎麼做，可以從 [Android Studio 網站](http://developer.android.com/tools/projects/projects-eclipse.html)取得詳細資訊。
2. 加入專案相依性，以針對您的專案設定進行偵錯。
3. 更新專案的 AndroidManifest.xml 檔案來包含：

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. 在您的主要活動建立 AuthenticationContext 的執行個體。 此呼叫的詳細資料超出本主題的範圍，但您可以查看 [Android 原生用戶端範例](https://github.com/AzureADSamples/NativeClient-Android)，由此開始也很不錯。 在以下範例中，SharedPreferences 是預設快取，Authority 格式為 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`：

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. 複製此程式碼區塊，以便於使用者輸入認證並收到授權碼之後處理 AuthenticationActivity 的結束工作：

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. 若要要求權杖，定義回呼：

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. 最後，使用該回呼來要求權杖：

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

以下是參數的說明：

* resource 是必要參數，是您嘗試存取的資源。
* clientid 是必要參數，來自 AzureAD。
* RedirectUri 在 acquireToken 呼叫中不是必要參數。 您可以將它設定為您的套件名稱。
* PromptBehavior 有助於在要求認證時略過快取和 Cookie。
* 授權碼兌換成權杖之後，就會呼叫 callback。 它有一個 AuthenticationResult 物件，提供存取權杖、過期日期和識別碼權杖資訊。
* acquireTokenSilent 為選擇性。 您可以呼叫它來處理快取和權杖重新整理。 它也提供同步處理版本。 它接受 userid 作為參數。

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

經過這個逐步解說，您應該已擁有與 Azure Active Directory 成功整合所需的項目。 如需此工作的更多範例，請瀏覽 GitHub 上的 AzureADSamples/ 儲存機制。

## <a name="important-information"></a>重要資訊
### <a name="customization"></a>自訂
您的應用程式資源可以覆寫程式庫專案資源。 當您建置應用程式時會發生這種情況。 基於這個理由，您可以依想要的方式自訂驗證活動配置。 請記下 ADAL 使用的控制項 (WebView) 的識別碼。

### <a name="broker"></a>Broker
Microsoft Intune 公司入口網站應用程式提供「訊息代理程式」元件。 帳戶會在 AccountManager 中建立。 帳戶類型為 "com.microsoft.workaccount"。 AccountManager 只允許一個 SSO (單一登入) 帳戶。 完成其中一個應用程式的裝置挑戰之後，就會建立此使用者的 SSO Cookie。

如果在這個驗證器上建立使用者帳戶，且您選擇不要略過它，ADAL 會使用訊息代理程式帳戶。 您可以使用以下方法來略過訊息代理程式使用者：

   `AuthenticationSettings.Instance.setSkipBroker(true);`

您必須註冊特殊的 RedirectUri 供訊息代理程式使用。 RedirectUri 的格式為 `msauth://packagename/Base64UrlencodedSignature`。 您可以使用指令碼 brokerRedirectPrint.ps1 或使用 API 呼叫 mContext.getBrokerRedirectUri，以取得應用程式的 RedirectUri。 簽章與您的簽署憑證有關。

目前的訊息代理程式模型僅能用於一位使用者。 AuthenticationContext 提供 API 方法來取得訊息代理程式使用者。

   `String brokerAccount =  mContext.getBrokerUser(); //Broker user is returned if account is valid.`

您的應用程式資訊清單應有下列權限才能使用 AccountManager 帳戶。 如需詳細資訊，請參閱[Android 網站上的 AccountManager 資訊](http://developer.android.com/reference/android/accounts/AccountManager.html)。

* GET_ACCOUNTS
* USE_CREDENTIALS
* MANAGE_ACCOUNTS

### <a name="authority-url-and-ad-fs"></a>授權單位 URL 和 AD FS
Active Directory 同盟服務 (AD FS) 不視為正式的 STS，因此您需要開啟執行個體探索，並在 AuthenticationContext 建構函式上傳遞 false。

授權單位 URL 需要 STS 執行個體和[租用戶名稱](https://login.microsoftonline.com/yourtenant.onmicrosoft.com)。

### <a name="querying-cache-items"></a>查詢快取項目
ADAL 在 SharedPreferences 中提供預設快取與一些簡單的快取查詢函式。 您可以使用以下方法從 AuthenticationContext 取得目前的快取︰

    ITokenCacheStore cache = mContext.getCache();

如果想要自訂它，您也可以提供您的快取實作。

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>提示行為
ADAL 提供可指定提示行為的選項。 如果重新整理權杖無效，而且需要使用者認證，PromptBehavior.Auto 會顯示 UI。 PromptBehavior.Always 會略過快取使用，並一律顯示 UI。

### <a name="silent-token-request-from-cache-and-refresh"></a>從快取無訊息地要求權杖並重新整理
無訊息要求權杖不會使用 UI 快顯，也不需要活動。 它會從快取傳回權杖 (若有的話)。 如果權杖已過期，這個方法會嘗試重新整理它。 如果重新整理權杖已過期或失效，則傳回 AuthenticationException。

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

您也可以使用此方法進行同步呼叫。 您可以將 callback 設為 null，或使用 acquireTokenSilentSync。

### <a name="diagnostics"></a>診斷
診斷問題的主要資訊來源有：

* 例外狀況
* 記錄檔
* 網路追蹤

請注意，相互關聯識別碼在程式庫中是診斷的核心。 如果您想要將 ADAL 要求與程式碼中其他的作業相互關聯，您可以依每一個要求來設定相互關聯識別碼。 如果您沒有設定相互關聯識別碼，ADAL 會隨機產生一個。 然後所有記錄訊息和網路呼叫加上相互關聯識別碼的戳記。 自行產生的識別碼隨每個要求而不同。

#### <a name="exceptions"></a>例外狀況
例外狀況是第一個診斷。 我們試著提供有用的錯誤訊息。 如果您發現沒有幫助的錯誤訊息，請提出問題來告訴我們。 請同時提供裝置資訊，例如機型和 SDK 號碼。

#### <a name="logs"></a>記錄檔
您可以設定程式庫來產生記錄訊息，用以協助診斷問題。 設定記錄時，請執行下列呼叫來設定回呼，供 ADAL 用來移交每一個產生的記錄訊息。

    Logger.getInstance().setExternalLogger(new ILogger() {
        @Override
        public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
        ...
        // You can write this to log file depending on level or error code.
        writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
        }
    }

可以在自訂記錄檔中寫入訊息，如以下程式碼所示。 不幸的是，從裝置取得記錄檔沒有標準方法。 有一些服務可協助您處理這部份。 您可以也自創方法，例如將檔案傳送到伺服器。

    private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
    }

記錄層級分為：
* Error (例外狀況)
* Warn (警告)
* Info (參考用途)
* Verbose (更多詳細資料)

設定記錄層級的方法如下：

    Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

 所有記錄訊息除了傳送至任何自訂記錄檔回呼，也會傳送至 logcat。
您可以將記錄傳送至檔案形式 logcat，如下所示：

    adb logcat > "C:\logmsg\logfile.txt"

 如需有關 adb 命令的詳細資訊，請參閱 [Android 網站上的 logcat 資訊](https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat)。

#### <a name="network-traces"></a>網路追蹤
您可以使用各種工具來擷取 ADAL 產生的 HTTP 流量。  如果您熟悉 OAuth 通訊協定，或如果您需要提供診斷資訊給 Microsoft 或其他支援管道，這最有用。

Fiddler 是最簡單的 HTTP 追蹤工具。 請使用下列連結來設定它，以正確記錄 ADAL 網路流量。 為了讓追蹤工具 (如 Fiddler、Charles) 更實用，必須將其設定為記錄未加密的 SSL 流量。  

> [!NOTE]
> 這種方式產生的追蹤可能包含極機密的資訊，例如存取權杖、使用者名稱和密碼。 如果您使用實際執行帳戶，請勿將這些追蹤洩漏給第三方。 如果您需要提供追蹤給某人以取得支援，請使用您不介意共用使用者名稱和密碼的暫時帳戶來重現問題。

* 從 Telerik 網站：[設定適用於 Android 的 Fiddler](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
* 從 GitHub：[設定適用於 ADAL 的 Fiddler 規則](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)

### <a name="dialog-mode"></a>對話方塊模式
沒有活動的 acquireToken 方法支援對話方塊提示。

### <a name="encryption"></a>加密
根據預設，ADAL 會加密權杖並儲存在 SharedPreferences 中。 您可以在 StorageHelper 類別查看詳細資料。 Android 引進 Android KeyStore 4.3 (API 18) 安全儲存體來存放私密金鑰。 ADAL 對 API 18 和更新版本使用此機制。 如果您想要使用較低 SDK 版本的 ADAL，您需要在 AuthenticationSettings.INSTANCE.setSecretKey 提供秘密金鑰。

### <a name="oauth2-bearer-challenge"></a>OAuth2 持有者挑戰
AuthenticationParameters 類別提供從 OAuth2 持有者挑戰取得 authorization_uri 的功能。

### <a name="session-cookies-in-webview"></a>WebView 中的工作階段 Cookie
在應用程式關閉後，Android WebView 不會清除工作階段 Cookie。 您可以使用以下範例程式碼來處理這部分：

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

如需有關 Cookie 的詳細資訊，請參閱 [Android 網站上的 CookieSyncManager 資訊](http://developer.android.com/reference/android/webkit/CookieSyncManager.html)。

### <a name="resource-overrides"></a>資源覆寫
ADAL 程式庫包含 ProgressDialog 訊息的英文字串。 如果想要當地語系化的字串，您的應用程式應該覆寫這些英文字串。

     <string name="app_loading">Loading...</string>
     <string name="broker_processing">Broker is processing</string>
     <string name="http_auth_dialog_username">Username</string>
     <string name="http_auth_dialog_password">Password</string>
     <string name="http_auth_dialog_title">Sign In</string>
     <string name="http_auth_dialog_login">Login</string>
     <string name="http_auth_dialog_cancel">Cancel</string>

### <a name="ntlm-dialog-box"></a>NTLM 對話方塊
ADAL 1.1.0 版支援 NTLM 對話方塊，此對話方塊是透過 WebViewClient 的 onReceivedHttpAuthRequest 事件來處理。 您可以自訂對話方塊的版面配置和字串。

### <a name="cross-app-sso"></a>跨應用程式的 SSO
了解[如何使用 ADAL 啟用跨應用程式的 SSO](active-directory-sso-android.md)。  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
