---
title: "Azure Active Directory 報告稽核 API 範例 | Microsoft Docs"
description: "如何開始使用 Azure Active Directory 報告 API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 98e1d09cbf638032fe1b5b9dcf19332f9158d7a7
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory 報告稽核 API 範例
本主題是 Azure Active Directory 報告 API 相關主題集合的一部分。  
Azure AD 報告提供的 API 可讓您使用程式碼或相關工具來存取稽核資料。
本主題的範疇是為您提供 **稽核 API**的範例程式碼。

請參閱：

* [稽核記錄](active-directory-reporting-azure-portal.md#activity-reports) 以取得詳細概念資訊
* [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md) 以取得報告 API 的詳細資訊。

如有相關疑問、問題或意見，請連絡 [AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## <a name="prerequisites"></a>必要條件
您必須先完成 [存取 Azure AD 報告 API 的必要條件](active-directory-reporting-api-prerequisites.md)，才能使用本主題中的範例。  

## <a name="known-issue"></a>已知問題
如果您的租用戶位於歐洲地區，「應用程式驗證」將無法運作。 請使用「使用者驗證」做為因應措施來存取稽核 API，直到我們修正此問題為止。 

## <a name="powershell-script"></a>PowerShell 指令碼
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }

            # save the query page to an output file
            Write-Output "Save the output to a file audit$i.json"
            $myReport.Content | Out-File -FilePath audit$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")


### <a name="executing-the-powershell-script"></a>執行 PowerShell 指令碼
完成指令碼編輯後，加以執行並確認從稽核記錄報告傳回預期的資料。

指令碼會以 JSON 格式傳回稽核報告的輸出。 它也會建立具有相同輸出的 `audit.json` 檔案。 您可透過修改指令碼以從其他報告傳回資料來進行實驗，以及取消註解您不需要的輸出格式。

## <a name="bash-script"></a>Bash 指令碼
    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.microsoftonline.com"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/activities/audit?api-version=beta&$filter=activityDate%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

## <a name="python-script"></a>Python 指令碼
    # Author: Michael McLaughlin (michmcla@microsoft.com)
    # Date: January 20, 2016
    # This requires the Python Requests module: http://docs.python-requests.org

    import requests
    import datetime
    import sys

    client_id = 'your-application-client-id-here'
    client_secret = 'your-application-client-secret-here'
    login_url = 'https://login.microsoftonline.com/'
    tenant_domain = 'your-directory-name-here.onmicrosoft.com'

    # Get an OAuth access token
    bodyvals = {'client_id': client_id,
                'client_secret': client_secret,
                'grant_type': 'client_credentials'}

    request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
    token_response = requests.post(request_url, data=bodyvals)

    access_token = token_response.json().get('access_token')
    token_type = token_response.json().get('token_type')

    if access_token is None or token_type is None:
        print "ERROR: Couldn't get access token"
        sys.exit(1)

    # Use the access token to make the API request
    yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

    header_params = {'Authorization': token_type + ' ' + access_token}
    request_string = 'https://graph.windows.net/' + tenant_domain + '/activities/audit?api-version=beta&$filter=activityDate%20gt%20' + yesterday   
    response = requests.get(request_string, headers = header_params)

    if response.status_code is 200:
        print response.content
    else:
        print 'ERROR: API request failed'





## <a name="next-steps"></a>後續步驟
* 您要自訂本主題中的範例嗎？ 請查看 [Azure Active Directory 稽核 API 參考](active-directory-reporting-api-audit-reference.md)。 
* 如果您想要查看使用 Azure Active Directory 報告 API 的完整概觀，請參閱 [開始使用 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。
* 如果您想要深入了解 Azure Active Directory 報告，請參閱 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  

