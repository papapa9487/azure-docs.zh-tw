---
title: "以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器 | Microsoft Docs"
description: "涵蓋如何執行自動安裝 Azure AD 應用程式 Proxy 連接器，為內部部署的應用程式提供安全的遠端存取。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf3058832ba656a1a18aea194bf4e5ce4e800e76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>建立 Azure AD 應用程式 Proxy 連接器的自動安裝指令碼

本主題會協助您建立 Windows PowerShell 指令碼，以便自動安裝和註冊 Azure AD 應用程式 Proxy 連接器。

當您想要執行下列工作時，此功能很實用︰

* 在未啟用使用者介面或您無法使用遠端桌面存取的 Windows 伺服器上安裝連接器。
* 一次安裝並註冊許多連接器。
* 將連接器安裝與註冊整合成另一個程序的一部分。
* 建立一個包含連接器位元但未註冊的標準伺服器映像。

[應用程式 Proxy 連接器](application-proxy-understand-connectors.md)必須使用全域系統管理員和密碼向 Azure AD 目錄註冊後才能運作。 通常，此資訊是在連接器安裝期間於一個快顯對話方塊中輸入的，但是您可以改用 PowerShell 使此程序自動進行。

自動安裝有兩個步驟。 首先，安裝連接器。 其次，向 Azure AD 註冊連接器。 

## <a name="install-the-connector"></a>安裝連接器
使用下列步驟安裝連接器而不註冊連接器：

1. 開啟命令提示字元。
2. 執行下列命令，/q 代表無訊息安裝。 無訊息安裝不會提示您接受《使用者授權合約》。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>向 Azure AD 註冊連接器
有兩種方法可用來註冊連接器︰

* 使用 Windows PowerShell 認證物件註冊連接器
* 使用離線時建立的權杖註冊連接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>使用 Windows PowerShell 認證物件註冊連接器
1. 建立 Windows PowerShell 認證物件 `$cred`，其中含有目錄的系統管理使用者名稱和密碼。 執行下列命令，取代 *\<使用者名稱\>*和*\<密碼\>*：
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. 移至 **C:\Program Files\Microsoft AAD App Proxy Connector**，然後使用您建立的 `$cred` 物件來執行下列指令碼：
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>使用離線時建立的權杖註冊連接器
1. 使用此程式碼片段中的值，建立使用 AuthenticationContext 類別的離線權杖：

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. 建立權杖後，請使用該權杖建立一個 SecureString：

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. 執行下列 Windows PowerShell 命令，將\<租用戶 GUID\> 取代為您的目錄識別碼︰

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>後續步驟 
* [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)


