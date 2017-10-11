---
title: "使用 Azure AD v2.0 端點將登入新增至 iOS 應用程式 | Microsoft Docs"
description: "如何建置可使用個人 Microsoft 帳戶及公司或學校帳戶登入使用者的 iOS 應用程式 (採用協力廠商程式庫)。"
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf1455dc3d55ea3581195f7a315556d134c23a26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>使用 v2.0 端點，透過圖形 API 將登入新增至使用協力廠商程式庫的 iOS 應用程式
Microsoft 身分識別平台會使用開放式標準，例如 OAuth2 和 OpenID Connect。 開發人員可以使用任何想要的程式庫，來與我們的服務整合。 為了協助開發人員使用我們的平台搭配其他程式庫，我們撰寫了數篇逐步解說，示範如何設定協力廠商程式庫以連接到 Microsoft 身分識別平台。 大部分實作 [RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749) 的程式庫都能連接到 Microsoft 身分識別平台。

使用此篇逐步解說建立的應用程式，使用者可以使用圖形 API 來登入其組織，然後在組織中搜尋其他人。

如果您是 OAuth2 或 OpenID Connect 新手，此範例組態可能不太適合您。 建議您閱讀 [v2.0 通訊協定 - OAuth 2.0 授權碼流程](active-directory-v2-protocols-oauth-code.md)以瞭解背景。

> [!NOTE]
> 我們的平台中有些功能沒有採用 OAuth2 或 OpenID Connect 標準的運算式 (例如條件式存取和 Intune 原則管理)，所以會要求您使用開放原始碼 Microsoft Azure 身分識別程式庫。
> 
> 

v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。

> [!NOTE]
> 若要判斷是否應該使用 v2.0 端點，請閱讀相關的 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

## <a name="download-code-from-github"></a>從 GitHub 下載程式碼
本教學課程的程式碼保留在 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2)。  若要遵循執行，您可以 [用 .zip 格式下載應用程式的基本架構](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ，或複製基本架構：

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

您也可以下載範例，並立即開始著手使用︰

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>註冊應用程式
在[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)建立新的應用程式，或遵循[如何註冊應用程式與 v2.0 端點](active-directory-v2-app-registration.md)的詳細步驟進行。  請確定：

* 複製所指派給您的「應用程式識別碼」  ，因為您很快就會用到。
* 為您的應用程式新增 **行動** 平台。
* 複製入口網站的「重新導向 URI」  。 您必須使用 `urn:ietf:wg:oauth:2.0:oob`的預設值。

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>下載 NXOAuth2 協力廠商程式庫並建立工作區
在此逐步解說中，您將使用 GitHub 提供的 OAuth2Client，這是適用於 Mac OS X 與 iOS 的 OAuth2 程式庫 (Cocoa 與 Cocoa Touch)。 此程式庫是以 OAuth2 規格的第 10 版草稿為基礎。 它會實作原生應用程式設定檔，並支援使用者的授權端點。 您需要上述各項，才能與 Microsoft 身分識別平台整合。

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>使用 CocoaPods 將程式庫加入您的專案
CocoaPods 是 Xcode 專案的相依性管理員。 它會自動管理上述安裝步驟。

```
$ vi Podfile
```
1. 將下列加入此 Podfile：
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. 使用 CocoaPods 來載入該 Podfile。 這會建立您將載入的新 XCode Workspace。
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>探索專案結構
在基本架構中為專案設定下列結構︰

* 具有 UPN 搜尋功能的「主要檢視」
* 所選使用者的相關資料的「詳細資料檢視」
* 使用者可登入應用程式來查詢圖形的「登入檢視」

我們會移至基本架構中各種不同的檔案，以加入驗證。 程式碼的其他部分 (如視覺化程式碼) 與身分識別無關，所以不會提供給您。

## <a name="set-up-the-settingsplst-file-in-the-library"></a>在程式庫中設定 settings.plst 檔案
* 在 QuickStart 專案中，開啟 `settings.plist` 檔案。 取代區段中的元素值，以反映您在 Azure 入口網站中所使用的值。 每當使用 Active Directory 驗證程式庫時，您的程式碼就會參考這些值。
  * `clientId` 是您從入口網站複製的應用程式用戶端識別碼。
  * `redirectUri` 是入口網站所提供的重新導向 URL。

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>在 LoginViewController 中設定 NXOAuth2Client 程式庫
NXOAuth2Client 程式庫要求設定一些值。 完成這項工作之後，您可以使用取得的權杖以呼叫圖形 API。 因為每當需要驗證時都會呼叫 `LoginView` ，所以合理的做法是將組態值放入該檔案中。

* 讓我們在 `LoginViewController.m` 檔案中新增一些值，以便針對驗證和授權來設定內容。 和放在程式碼後面的值有關的詳細資訊。
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

讓我們看看程式碼的詳細資料。

第一個字串是用於 `scopes`。  `User.Read` 值可讓您讀取已登入使用者的基本設定檔。

您可以在 [Microsoft Graph 權限範圍](https://graph.microsoft.io/docs/authorization/permission_scopes)，深入了解所有可用範圍。

對於 `authURL`、`loginURL`、`bhh` 及 `tokenURL`，您應該使用上面提供的值。 如果您使用開放原始碼 Microsoft Azure 身分識別程式庫，我們會使用中繼資料端點為您提取此資料。 我們已努力完成為您擷取這些值的工作。

`keychain` 值是一個容器，NXOAuth2Client 程式庫將用來建立金鑰鏈來儲存您的權杖。 如果您想要取得跨多個應用程式的單一登入 (SSO)，可以在每個應用程式中指定相同的金鑰鏈，以及要求在您的 XCode 權利中使用該金鑰鏈。 這會在 Apple 文件中說明。

這些值的其餘部分都必須使用此程式庫，並且為您建立位置，即可將這些值送至內容。

### <a name="create-a-url-cache"></a>建立 URL 快取
在載入檢視之後，一律會呼叫 `(void)viewDidLoad()`，其內部的下列程式碼會裝填快取以供我們使用。

新增下列程式碼：

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>建立用於登入的 Web 檢視
Web 檢視可提示使用者提供其他因素 (例如已設定的簡訊) 或將錯誤訊息傳回給使用者。 您將在此處設定 Web 檢視，然後撰寫程式碼，以從身分識別服務處理將會在 Web 檢視中發生的回呼。

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>覆寫 Web 檢視方法來處理驗證
如先前所述，當使用者需要登入時，若要告訴 Web 檢視發生什麼情況，您可以將下列程式碼貼上。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>撰寫程式碼來處理 OAuth2 要求的結果
下列程式碼會處理從 Web 檢視傳回的 redirectURL。 如果驗證未成功，此程式碼將會再試一次。 同時，程式庫會提供錯誤，讓您可在主控台中查看或以非同步方式處理。

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>設定 OAuth 內容 (稱為「帳戶存放區」)
您可以針對您想要從應用程式存取的每個服務，在共用帳戶存放區上呼叫 `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` 。 帳戶類型是字串，其可做為特定服務的識別碼。 因為您要存取圖形 API，所以程式碼會將其視為 `"myGraphService"`。 接著，您要設定觀察者，以在權杖發生任何變更時告訴您。 在您取得權杖之後，可讓使用者回到 `masterView`。

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>設定 MasterView 以從圖形 API 搜尋和顯示使用者
在方格中顯示所傳回資料的 Master-View-Controller (MVC) 應用程式出本逐步解說的範圍，而且有許多說明如何建置的線上教學課程。 此程式碼全都在基本架構檔案中。 不過，您必須在此 MVC 應用程式中處理一些事項︰

* 當使用者在搜尋欄位中輸入資料時進行攔截
* 將資料的物件提供給 MasterView，以便在格線中顯示結果

我們會在下方進行這些動作。

### <a name="add-a-check-to-see-if-youre-logged-in"></a>加入檢查以查看您是否已經登入
如果使用者未登入，應用程式沒什麼作為，所以檢查快取中是否已有權杖會是明智之舉。 如果沒有，您可重新導向至 LoginView 以讓使用者登入。 如果您還記得，在檢視載入時執行動作的最佳方式，就是使用 Apple 提供的 `viewDidLoad()` 方法。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>在收到資料時更新資料表檢視
當圖形 API 傳回資料時，您必須顯示該資料。 為了簡單起見，以下是可更新資料表的全部程式碼。 您可以直接在 MVC 未定案程式碼中貼上正確的值。

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>提供有人在搜尋欄位中輸入資料時呼叫圖形 API 的方法
使用者在方塊中輸入搜尋資料時，您需要將該資料塞到圖形 API。 您將在下列程式碼建置的 `GraphAPICaller` 類別，會將查閱功能從展示當中分離出來。 現在，讓我們撰寫會將任何搜尋字元送入圖形 API 的程式碼。 我們的做法是提供稱為 `lookupInGraph`的方法，其採用我們想要搜尋的字串。

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>撰寫協助程式類別以存取圖形 API
這是我們應用程式的核心。 而其餘就是將程式碼插入 Apple 提供的預設 MVC 模式，您在這裡撰寫的程式碼會在使用者輸入時查詢圖形，然後傳回該資料時。 程式碼如下所示，而後面還有其詳細說明。

### <a name="create-a-new-objective-c-header-file"></a>建立新的 Objective C 標頭檔
將檔案命名為 `GraphAPICaller.h`，然後新增下列程式碼。

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

如您所見，指定的方法會取得字串並傳回 completionBlock。 此 completionBlock (如您所猜測) 提供的物件會隨著使用者搜尋，即時填入資料，藉此更新資料表。

### <a name="create-a-new-objective-c-file"></a>建立新的 Objective C 檔案
將檔案命名為 `GraphAPICaller.m`，然後新增下列方法。

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

我們會詳細解說這個方法。

此程式碼的核心在於 `NXOAuth2Request`，該方法會採用您已經在 settings.plist 檔案中定義的參數。

第一個步驟是建構正確的圖形 API 呼叫。 因為您正在呼叫 `/users`，所以您會將它附加到圖形 API 資源和版本來進行指定。 因為這些都會隨著 API 演進而改變，所以合理的做法是將這些放在外部設定檔案。

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

接下來，您需要指定您也會提供給圖形 API 呼叫的參數。 切記  不要將參數放在資源端點中，因為它會在執行階段針對所有非 URI 符合字元而虛設。 必須在參數中提供所有的查詢程式碼。

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

您可能發現這會呼叫您尚未撰寫的 `convertParamsToDictionary` 方法。 讓我們立即在檔案的結尾這樣做︰

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
接下來，我們將使用 `NXOAuth2Request` 方法，以從 API 取回 JSON 格式的資料。

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

最後，來看看您要如何將資料傳回至 MasterViewController。 資料會以序列化方式傳回，而且該資料必須還原序列化並載入 MainViewController 可取用的物件。 基於此目的，基本架構具有的 `User.m/h` 檔案可以建立使用者物件。 您會以圖形中的資訊填入該使用者物件。

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>執行範例
如果您使用基本架構或遵循本逐步解說，則您的應用程式現在應會執行。 啟動模擬器，然後按一下 [登入]  以使用應用程式。

## <a name="get-security-updates-for-our-product"></a>取得產品的安全性更新
我們鼓勵您造訪 [安全性 TechCenter](https://technet.microsoft.com/security/dd252948) 並訂閱資訊安全摘要報告警示，以在安全性事件發生時收到通知。

