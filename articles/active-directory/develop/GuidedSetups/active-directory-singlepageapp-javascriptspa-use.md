---
title: "Azure AD v2 JS SPA 指引設定 - 使用 | Microsoft Docs"
description: "JavaScript SPA 應用程式如何呼叫需要來自 Azure Active Directory v2 端點之存取權杖的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: f52157df298ddfc1c1b29a18dc9a54aae59b52a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>使用 Microsoft Authentication Library (MSAL) 以登入使用者

1.  建立名為 `app.js` 的檔案。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：`Add` > `New Item` > `JavaScript File`：
2.  將下列程式碼新增至 `app.js` 檔案：

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });

    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - the token type: usually id_token
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>相關資訊

在使用者第一次按一下 [呼叫 Microsoft Graph API] 按鈕之後，`callGraphApi` 方法會呼叫 `loginRedirect` 以登入使用者。 這個方法會導致將使用者重新導向至 Microsoft Azure Active Directory v2 端點，以提示及驗證使用者的認證。 如果成功登入，使用者會被重新導向回到原始 index.html 分頁，並且會收到權杖，由 `msal.js` 處理，並且會快取權杖中包含的資訊。 此權杖也稱為「ID 權杖」且包含使用者的基本資訊，例如使用者顯示名稱。 如果您打算將此權杖所提供的任何資料用於任何目的，您必須確定後端伺服器已驗證此權杖，以保證權杖是發給您應用程式的有效使用者。

本指南所產生的 SPA 不會使用直接 ID 權杖 - 反而會呼叫 `acquireTokenSilent` 和/或 `acquireTokenRedirect`，以取得用來查詢 Microsoft Graph API 的「存取權杖」。 如果您需要可驗證 ID 權杖的範例，請看一下 GitHub 中的 [這個](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 範例")範例應用程式 – 此範例使用 ASP.NET Web API 進行權杖驗證。

#### <a name="getting-a-user-token-interactively"></a>以互動方式取得使用者權杖

初始登入之後，您不希望在使用者每次必須要求權杖來存取資源時，都要求使用者重新驗證 – 因此通常應該使用 acquireTokenSilent 以取得權杖。 然而，在某些情況下您需要強制使用者與 Azure Active Directory v2 端點互動 – 範例包括：
-   使用者可能需要重新輸入其認證，因為密碼已過期
-   您的應用程式要求的資源存取權，需要使用者同意
-   需要雙因素驗證

呼叫 acquireTokenRedirect(scope) 會導致將使用者重新導向至 Azure Active Directory v2 端點 (或者呼叫 acquireTokenPopup(scope) 會導致快顯視窗)，使用者必須藉由確認其認證、同意必要的資源，或完成雙因素驗證來進行互動。

#### <a name="getting-a-user-token-silently"></a>以無訊息方式取得使用者權杖
` acquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 `loginRedirect` (或 `loginPopup`) 在第一次執行之後，`acquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中使用那些權杖存取受保護的資源，並且會以無訊息方式進行要求或更新權杖的呼叫。
`acquireTokenSilent` 在某些情況下可能會失敗 – 例如，使用者的密碼已過期。 您的應用程式可以透過兩種方式處理此例外狀況：

1.  立即對 `acquireTokenRedirect` 進行呼叫，這會促使系統提示使用者登入。 此模式通常用於應用程式中沒有未經驗證內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例應用程式會使用此模式。

2. 應用程式也可以提供視覺指示，讓使用者知道需要透過互動方式登入，使用者就能選取正確的登入時機，或應用程式可以在之後重試 `acquireTokenSilent`。 此方式通常用於使用者可以使用應用程式的其他功能，不需要因此中斷作業的情況，例如，應用程式中有未經驗證內容可供使用者使用。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或者重新整理過期的資訊。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft 圖形 API

將下列程式碼新增至 `app.js` 檔案：

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的詳細資訊

在本指南建立的範例應用程式中，`callWebApiWithToken()` 方法是用來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中加入取得的權杖。 對於本指南建立的範例應用程式，資源為 Microsoft 圖形 API *me* 端點，它會顯示使用者的設定檔資訊。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

將下列程式碼新增至 `app.js` 檔案：

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
