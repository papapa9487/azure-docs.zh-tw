---
title: "將 Azure AD 整合至 iOS 應用程式 | Microsoft Docs"
description: "如何建置 iOS 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
services: active-directory
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 57f465df99ac234466459b8031f61805d8334b59
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-ad-into-an-ios-app"></a>將 Azure AD 整合至 iOS 應用程式
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> 試用新[開發人員入口網站](https://identity.microsoft.com/Docs/iOS) 的預覽版本，這可協助您在短短幾分鐘內啟動並執行 Azure Active Directory！  開發人員入口網站會引導您完成註冊應用程式並將 Azure AD 整合至您的程式碼的程序。  當您完成時，您會有可驗證租用戶中使用者的簡單應用程式，以及可接受權杖並執行驗證的後端。 
> 
> 

Azure Active Directory (Azure AD) 提供 Active Directory 驗證程式庫 (ADAL) 給需要存取受保護資源的 iOS 用戶端。 ADAL 可簡化您的應用程式用來取得存取權杖的程序。 為了示範究竟多麼簡單，我們會在本文中建置一個可執行下列動作的 Objective C 待辦事項清單應用程式：

* 使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)來取得呼叫 Azure AD Graph API 的存取權杖。
* 在目錄中搜尋具有指定別名的使用者。

若要建立可完整運作的應用程式，您必須：

1. 向 Azure AD 註冊您的應用程式。
2. 安裝及設定 ADAL。
3. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。 您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。 如果您還沒有租用戶， [了解如何取得租用戶](active-directory-howto-tenant.md)。


> [!TIP]
> 試用新的[開發人員入口網站](https://identity.microsoft.com/Docs/iOS)預覽版本，這可協助您在短短幾分鐘內啟動並執行 Azure AD。 開發人員入口網站會引導您完成註冊應用程式並將 Azure AD 整合至您的程式碼的程序。 當您完成時，您會有可驗證租用戶中使用者的簡單應用程式，以及可接受權杖並執行驗證的後端。 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1.決定您 iOS 的重新導向 URI
為了安全地在特定 SSO 案例啟動您的應用程式，您必須以特定格式建立「重新導向 URI」。 重新導向 URI 可確保應用程式要求的權杖會正確地傳回給它們。


iOS 格式的重新導向 URI：

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** - 這已在您的 XCode 專案中註冊。 它是其他應用程式呼叫您的方式。 您可以在 Info.plist -> URL types -> URL Identifier 下找到此項目。 如果您尚未設定任何一個，建議您建立一個。
* **bundle-id** - 這是在您的 XCode 專案設定中，[identity] 下可找到的 [Bundle Identifier]。

此 QuickStart 程式碼的範例：***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2.註冊 DirectorySearcher 應用程式
若要設定應用程式以取得權杖，您必須先在 Azure AD 租用戶中註冊該應用程式，然後授與它存取 Azure AD 圖形 API 的權限：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端列中，按一下您的帳戶。 在 [目錄] 清單下，選擇您要註冊應用程式的 Active Directory 租用戶。
3. 按一下最左邊瀏覽窗格中的 [更多服務]，然後選取 [Azure Active Directory]。
4. 按一下 [應用程式註冊]，然後選取 [新增]。
5. 請遵照提示建立新的 [原生用戶端應用程式]。
  * 應用程式的 [名稱] 對使用者說明您的應用程式。
  * [重新導向 URI] 是配置與字串的組合，Azure AD 會用它來傳回權杖回應。  根據上面的重新導向 URI 資訊，輸入您的應用程式特有的值。
6. 完成註冊之後，Azure AD 會為應用程式指派一個唯一的應用程式識別碼。  您會在後續小節中用到這個值，所以請從應用程式索引標籤中複製此值。
7. 從 [設定] 頁面中，選取 [必要權限]，然後選取 [新增]。 選取 [Microsoft Graph] 作為 API，然後在 [委派權限] 底下新增 [讀取目錄資料] 權限。  這會設定您的應用程式以查詢 Azure AD 圖形 API 的使用者。

## <a name="3-install-and-configure-adal"></a>3.安裝及設定 ADAL
既然您在 Azure AD 中已經擁有應用程式，您可以安裝 ADAL，並撰寫身分識別相關程式碼。  為了讓 ADAL 能與 Azure AD 通訊，您需要提供一些應用程式註冊的相關資訊。

1. 請從使用 Cocoapods 將 ADAL 新增至 DirectorySearcher 專案開始。

    ```
    $ vi Podfile
    ```
2. 將下列加入此 Podfile：

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. 現在使用 CocoaPods 來載入該 Podfile。 此步驟會建立您會載入的新 XCode 工作區。

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. 在 QuickStart 專案中，開啟 plist 檔案 `settings.plist`。  取代區段中的元素值，以反映您在 Azure 入口網站中輸入的值。 每當您的程式碼使用 ADAL 時，都會參考這些值。
  * `tenant` 是指您的 Azure AD 租用戶網域，例如 contoso.onmicrosoft.com。
  * `clientId` 是您從入口網站複製的應用程式用戶端識別碼。
  * `redirectUri` 是您在入口網站中註冊的重新導向 URL。

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.  使用 ADAL 從 Azure AD 取得權杖
ADAL 的基本原則是每當您的應用程式需要存取權杖時，它只需呼叫 completionBlock `+(void) getToken : `，ADAL 就會進行其餘工作。  

1. 在 `QuickStart` 專案中，開啟 `GraphAPICaller.m` 並找出接近頂端的 `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` 註解。  您在這裡將 ADAL 與 Azure AD 通訊透過 CompletionBlock 將座標傳給 ADAL，並告訴它如何快取權杖。

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. 現在我們需要使用此權杖搜尋圖形中的使用者。 尋找 `// TODO: implement SearchUsersList` 註解。 這個方法會對 Azure AD Graph API 提出 GET 要求，以查詢 UPN 開頭為指定搜尋詞彙的使用者。  若要查詢 Azure AD 圖形 API，您必須在要求的 `Authorization` 標頭中包含 access_token。 這就是 ADAL 的切入點。

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

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
         }];

    }

    ```


3. 當應用程式透過呼叫 `getToken(...)` 來要求權杖時，ADAL 會嘗試在不向使用者要求認證的情況下傳回權杖。  如果 ADAL 決定使用者需要登入才能取得權杖，它會顯示可供登入的對話方塊、收集使用者的認證，然後在成功驗證後傳回權杖。  如果 ADAL 基於任何原因無法傳回權杖，則會擲回 `AdalException`。

> [!Note] 
> `AuthenticationResult` 物件包含 `tokenCacheStoreItem` 物件，可用來收集您的應用程式可能需要的資訊。 在 QuickStart 中，`tokenCacheStoreItem` 用來判斷驗證是否已經完成。
>
>

## <a name="5-build-and-run-the-application"></a>5.建置並執行應用程式
恭喜！ 您現在有一個運作中的 iOS 應用程式，可以驗證使用者、使用 OAuth 2.0 安全地呼叫 Web API，以及取得使用者的基本資訊。  如果您還沒有這麼做，現在是將一些使用者植入租用戶的時候。  啟動 QuickStart 應用程式，然後使用其中一個使用者登入。  根據 UPN 搜尋其他使用者。  關閉應用程式，然後重新啟動它。  請注意，使用者工作階段會維持不變。

ADAL 可讓您輕鬆地將這些常見的身分識別功能全部納入您的應用程式。  它會為您處理所有麻煩的工作，包括快取管理、OAuth 通訊協定支援、向使用者顯示 UI 以便登入，以及重新整理過期權杖。  您唯一需要知道的就是單一 API 呼叫， `getToken`。

[GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) 提供完成的範例供您參考 (不含您的設定值)。  

## <a name="next-steps"></a>後續步驟
您現在可以繼續探索其他案例。  您可以嘗試：

* [使用 Azure AD 保護 Node.js Web API](active-directory-devquickstarts-webapi-nodejs.md)
* 了解[如何使用 ADAL 在 iOS 上啟用跨應用程式的 SSO](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

