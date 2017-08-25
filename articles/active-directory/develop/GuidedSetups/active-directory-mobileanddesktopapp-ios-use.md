---
title: "開始使用 Azure AD v2 iOS - 使用 | Microsoft Docs"
description: "iOS (Swift) 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 81d5ae3775cca7cea5abf1922a25be0663a8a949
ms.contentlocale: zh-tw

---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 Microsoft Authentication Library (MSAL) 取得 Microsoft 圖形 API 的權杖

開啟 `ViewController.swift` 並且將程式碼取代為：

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>相關資訊
#### <a name="getting-a-user-token-interactively"></a>以互動方式取得使用者權杖
呼叫 `acquireToken` 方法時會顯示一個瀏覽器視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源，或取得權杖的無訊息作業失敗 (例如使用者的密碼過期) 時，應用程式通常會要求使用者以互動方式登入。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖
`acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 `acquireToken` 在第一次執行之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源，並且會以無訊息方式進行要求或更新權杖的呼叫。

最後，`acquireTokenSilent` 將會失敗，例如，使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MSALErrorCode.interactionRequired` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

1.  立即針對 `acquireToken` 進行呼叫，這會促使系統提示使用者登入。 此模式通常用於應用程式中沒有離線內容可供使用者使用的線上應用程式。 此指引設定所產生的範例應用程式會使用此模式：您可以在第一次執行應用程式時看到它運作。 因為沒有任何使用者曾經用過該應用程式，`applicationContext.users().first` 會包含 null 值，且會擲回 ` MSALErrorCode.interactionRequired ` 例外狀況。 然後範例中的程式碼會透過呼叫 `acquireToken` 來處理例外狀況，進而提示使用者登入。

2.  應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 此方式通常用於使用者可以使用應用程式的其他功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用者使用。 在此案例中，使用者可以決定他們要登入以存取受保護資源，或重新整理過時資訊的時機，或者您的應用程式可以決定在網路暫時中斷之後恢復連線時重試 `acquireTokenSilent`。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

將下面的新方法新增至 `ViewController.swift`。 此方法是用來使用 HTTP 授權標題針對 Microsoft Graph API 提出 `GET` 要求：

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫

在這個範例應用程式中，`getContentWithToken()` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於此範例，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="set-up-sign-out"></a>設定登出

將下列方法新增到 `ViewController.swift` 來將使用者登出：

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>關於登出的詳細資訊

`signoutButton` 方法會將使用者從 MSAL 使用者快取中移除，這能夠有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。

雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例，例如，使用者擁有多個帳戶的電子郵件應用程式。
<!--end-collapse-->

## <a name="register-the-callback"></a>註冊回呼

一旦使用者通過驗證，瀏覽器會將使用者重新導向回應用程式。 請遵循下列步驟登錄您的回呼：

1.  開啟 `AppDelegate.swift` 並匯入 MSAL：

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
將下列方法新增至您的 <code>AppDelegate</code> 類別以處理回呼：
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```


