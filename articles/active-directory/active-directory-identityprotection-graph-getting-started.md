---
title: "開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph | Microsoft Docs"
description: "簡介如何查詢 Microsoft Graph，以從 Azure Active Directory 取得風險事件和關聯資訊的清單。"
services: active-directory
keywords: "azure active directory identity protection, 風險事件, 弱點, 安全性原則, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 568cad4e394dfedddce1bfce66ddf627947d7568
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph
Microsoft Graph 是 Microsoft 統一 API 端點，也是 [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API 的寄居地。 第一個 API **identityRiskEvents** 可讓您查詢 Microsoft Graph，以取得[風險事件](active-directory-identityprotection-risk-events-types.md)清單和關聯的資訊。 本文可協助您開始查詢此 API。 如需深入的簡介、完整文件以及 Graph 總管的存取權，請參閱 [Microsoft Graph 網站](https://graph.microsoft.io/)。

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。

透過 Microsoft Graph 存取 Identity Protection 資料需要三個步驟︰

1. 新增應用程式與用戶端密碼。 
2. 使用此密碼和其他幾項資訊向 Microsoft Graph 驗證，以從中收到驗證權杖。 
3. 使用此權杖對 API 端點發出要求，並取回 Identity Protection 資料。

開始之前，您需要下列項目：

* 系統管理員權限，以便在 Azure AD 中建立應用程式
* 租用戶網域的名稱 (例如 contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>新增應用程式與用戶端密碼
1. [登入](https://manage.windowsazure.com) Azure 傳統入口網站。 
2. 在左方的瀏覽窗格中，按一下 [Active Directory] 。 
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. 從 [目錄]  清單中，選取要啟用目錄整合的目錄。
4. 在頂端的功能表中，按一下 [應用程式] 。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. 按一下頁面底部的 [新增]  。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. 在 [欲執行動作] 對話方塊上，按一下 [加入我的組織正在開發的應用程式]。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. 在 [告訴我們您的應用程式]  對話方塊上，執行以下步驟：
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. 在 [名稱] 文字方塊中，輸入應用程式的名稱 (例如︰AADIP Risk Event API Application)。
   
    b. 在 [類型]，選取 [Web 應用程式和/或 Web API]。
   
    c. 按一下 [下一步] 。
8. 在 [應用程式屬性]  對話方塊上，執行下列步驟：
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. 在 [登入 URL] 文字方塊中，輸入 `http://localhost`。
   
    b. 在 [應用程式識別碼 URI] 文字方塊中，輸入 `http://localhost`。
   
    c. 按一下頁面底部的 [新增] 。

現在您可以設定您的應用程式了。

![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>授與您的應用程式使用 API 的權限
1. 在應用程式頁面頂端的功能表中，按一下 [設定] 。 
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. 在 [其他應用程式的權限] 區段中，按一下 [加入應用程式]。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. 在 [其他應用程式的權限]  對話方塊中，執行下列步驟︰
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. 選取 [Microsoft Graph]。
   
    b. 按一下頁面底部的 [新增] 。
4. 按一下 [應用程式權限: 0]，然後選取 [讀取所有身分識別風險事件資訊]。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. 按一下頁面底部的 [儲存]  。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>取得存取金鑰
1. 在應用程式頁面的 [金鑰]  區段中，選取 1 年做為持續時間。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. 按一下頁面底部的 [儲存]  。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. 在 [金鑰] 區段中，複製新建立的金鑰值並貼到安全的位置。
   
    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > 如果遺失此金鑰，則必須返回本區段並建立新的金鑰。 請勿將此金鑰告知他人︰任何人只要擁有此金鑰就可以存取您的資料。
   > 
   > 
4. 在 [屬性] 區段中複製 [用戶端識別碼]，然後將它貼到安全的位置。 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>向 Microsoft Graph 驗證和查詢身分識別風險事件 API
到目前為止，您應該已擁有︰

* 上面複製的用戶端識別碼
* 上面複製的金鑰
* 租用戶網域的名稱

若要驗證，請傳送 post 要求至 `https://login.microsoft.com` ，並在主體中加入下列參數︰

* grant_type: “**client_credentials**”
* resource: “**https://graph.microsoft.com**”
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> 您必須提供 **client_id** 和 **client_secret** 參數的值。
> 
> 

如果成功，這會傳回驗證權杖。  
若要呼叫 API，請建立具有下列參數的標頭︰

    `Authorization`=”<token_type> <access_token>"


驗證時，您可以在傳回的權杖中找到權杖類型和存取權杖。

將此標頭做為要求來傳送至下列 API URL： `https://graph.microsoft.com/beta/identityRiskEvents`

回應 (如果成功) 是身分識別風險事件和關聯資料的集合 (格式為 OData JSON)，並可視需要加以剖析和處理。

以下是使用 Powershell 驗證並呼叫 API 的範例程式碼。  
您只需要加入用戶端識別碼、金鑰和租用戶網域。

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>後續步驟
恭喜，您剛剛完成了對 Microsoft Graph 的第一次呼叫！  
現在，您可以查詢身分識別風險事件，並依照需要任意使用資料。

若要深入了解 Microsoft Graph 以及如何使用 Graph API 來建置應用程式，請查看[說明文件](https://graph.microsoft.io/docs)，而在 [Microsoft Graph 網站](https://graph.microsoft.io/)上還能找到更多資訊。 此外，請務必要將 [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) 頁面加入書籤，因為此頁面會列出 Graph 中提供的所有 Identity Protection API。 由於我們增加了透過 API 使用 Identity Protection 的新方法，因此您會在該頁面上看到這些方法。

## <a name="additional-resources"></a>其他資源
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Azure Active Directory Identity Protection 偵測到的風險事件類型](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Microsoft Graph 概觀](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection 服務根目錄](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

