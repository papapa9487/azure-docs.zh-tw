---
title: "從 Web 應用程式使用 Azure 金鑰保存庫 | Microsoft Docs"
description: "使用本教學課程來幫助您了解如何從 Web 應用程式使用 Azure 金鑰保存庫。"
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 1846305e6834145046cf9903714c68e9a6fd4f7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>從 Web 應用程式使用 Azure 金鑰保存庫

## <a name="introduction"></a>簡介

使用此教學課程來幫助您了解如何從 Azure 中的 Web 應用程式使用 Azure 金鑰保存庫。 它會引導您完成從 Azure 金鑰保存庫存取密碼的程序，以便在 Web 應用程式中使用該密碼。

**預估完成時間：** 15 分鐘。

如需 Azure 金鑰保存庫的概觀資訊，請參閱 [什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須具備下列項目：

* Azure 金鑰保存庫中密碼的 URI
* 已在 Azure Active Directory 註冊，且有權存取您金鑰保存庫之 Web 應用程式的用戶端識別碼和用戶端密碼
* Web 應用程式。 我們將會說明 ASP.NET MVC 應用程式在 Azure 中做為 Web 應用程式部署的步驟。

>[!IMPORTANT]
>* 此範例端需使用手動佈建 AAD 身分識別的舊方式。 目前，有一個稱為[受管理服務識別 (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview) 的預覽版新功能，可用來自動佈建 AAD 身分識別。 如需進一步的詳細資料，請參考以下在 [github](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) 上的範例。

> [!NOTE]
>* 在本教學課程中，完成在 [開始使用 Azure 金鑰保存庫](key-vault-get-started.md) 中所列步驟是很重要的，這樣您才會有 Web 應用程式的密碼 URI 和用戶端識別碼和用戶端密碼。


已在 Azure Active Directory 註冊、且已獲權存取金鑰保存庫的 Web 應用程式將會被用來存取金鑰保存庫。 如果情況不是這樣，請回到開始使用教學課程中的註冊應用程式，並重複列出的步驟。

本教學課程是針對 Web 開發人員所設計，這些開發人員必須了解在 Azure 上建立 Web 應用程式的基本概念。 如需有關 Azure Web Apps 的詳細資訊，請參閱 [Web Apps 概觀](../app-service/app-service-web-overview.md)。

## <a id="packages"></a>新增 Nuget 封裝

有二個 Web 應用程式必須已安裝的封裝。

* Active Directory 驗證程式庫 - 包含與 Azure Active Directory 互動及管理使用者身分識別的方法
* Azure 金鑰保存庫資源庫 - 包含與 Azure 金鑰保存庫互動的方法

您可以使用 Package Manager Console 的 Install-Package 命令安裝這二個封裝。

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>修改 Web.Config

有三個必須加入至 web.config 檔案的應用程式設定，如下所示。

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

如果您不打算將您的應用程式做為 Azure Web 應用程式裝載，則您應該將實際的 ClientId、用戶端密碼和密碼 URI 值加入 web.config。否則，請保留這些虛設值，因為我們將在 Azure 入口網站中新增實際值來額外增加一層安全性。

## <a id="gettoken"></a>新增方法以取得存取權杖

為了能夠使用金鑰保存庫 API，您需要存取權杖。 金鑰保存庫用戶端會處理金鑰保存庫 API 的呼叫，但是您必須提供具有取得存取權杖的函式。  

以下是從 Azure Active Directory 取得存取權杖的程式碼。 此程式碼可以放置在應用程式的任何位置。 我想要新增 Utils 或 EncryptionHelper 類別。  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

> [!NOTE]
>* 目前，新功能的受管理服務身分識別 (MSI) 是最簡單的驗證方式。 如需進一步的詳細資料，請參閱下列[在 .NET 中的應用程式使用 Key Vault 與 MSI](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) 範例連結以及相關的 [MSI 與 App Service 和功能教學課程](https://docs.microsoft.com/en-us/azure/app-service/app-service-managed-service-identity)。 
>* 使用「用戶端識別碼」和「用戶端密碼」是另一種驗證 Azure AD 應用程式的方式。 此外，如果在 Web 應用程式中使用該密碼，您將能夠區分職責，並充分掌控您的金鑰管理。 但是，此方式有賴於將「用戶端密碼」存放在組態設定中，而就某種程度而言，與將您要保護的密碼存放在組態設定中相比，兩者的風險一樣高。 如需關於如何使用「用戶端識別碼」與「憑證」(而非「用戶端識別碼」與「用戶端密碼」) 來驗證 Azure AD 應用程式的討論，請參閱接下來的內容。

## <a id="appstart"></a>在應用程式啟動時擷取密碼

現在我們需要呼叫金鑰保存庫 API，並擷取密碼的程式碼。 下列程式碼可以放置在任何位置，只要在您需要使用它之前呼叫即可。 我已將程式碼放入 Global.asax 的應用程式啟動事件中，因此它在啟動時會執行一次，並讓密碼可在應用程式中使用。

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>在 Azure 入口網站中新增應用程式設定 (選擇性)

如果您已有 Azure Web 應用程式，現在就可以在 Azure 入口網站中為 AppSettings 新增實際值。 如此一來，實際值將不會存在於 web.config 中，但會透過您有個別存取控制功能的入口網站受到保護。 這些值會被您在 web.config 中輸入的值取代。請確定名稱都相同。

![Azure 入口網站中顯示的應用程式設定][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>使用憑證 (而非用戶端密碼) 進行驗證。

若要驗證 Azure AD 應用程式，另一種方式是使用用戶端識別碼和憑證 (而非用戶端識別碼和用戶端密碼)。 以下是在 Azure Web 應用程式中使用憑證的步驟：

1. 取得或建立憑證
2. 將憑證與 Azure AD 應用程式產生關聯
3. 將程式碼加入 Web 應用程式以使用憑證
4. 將憑證加入 Web 應用程式

### <a name="get-or-create-a-certificate"></a>取得或建立憑證

針對我們的目的，我們將建立一個測試憑證。 以下幾個命令可讓您在開發人員命令提示字元中用來建立憑證。 變更您要建立憑證檔案的目錄位置。  此外，對於憑證的開始和結束日期，使用目前日期加上 1 年。

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 03/07/2017 -e 03/07/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

記下 .pfx 的結束日期和密碼 (在此範例中為：07/31/2016 和 test123)。 您將需要在下面使用這些資訊。

如需如何建立測試憑證的詳細資訊，請參閱 [做法：自行建立測試憑證](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>將憑證與 Azure AD 應用程式產生關聯

既然您已經有憑證，現在就必須讓憑證與 Azure AD 應用程式產生關聯。 目前 Azure 入口網站並不支援此工作流程；您可以透過 PowerShell 來完成此工作流程。 請執行下列命令將憑證與 Azure AD 應用程式建立關聯：

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

執行這些命令之後，您就可以在 Azure AD 中看到應用程式。 搜尋時，在搜尋對話方塊中，請務必選取 [我公司所擁有的應用程式]，而不是 [我公司所使用的應用程式]。

若要深入了解「Azure AD 應用程式物件」和「ServicePrincipal 物件」，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>將程式碼加入 Web 應用程式以使用憑證

現在，我們將把程式碼新增到您的 Web 應用程式以存取憑證，並使用它來進行驗證。

首先，使用程式碼存取憑證。

```cs
public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```

請注意，StoreLocation 是 CurrentUser，而不是 LocalMachine。 另外，由於我們使用測試憑證，因此我們必須提供 "false" 憑證給 Find 方法。

接著是使用 CertificateHelper 並建立驗證所需之 ClientAssertionCertificate 的程式碼。

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

以下是取得存取 Token 的新程式碼。 這會取代先前範例中的 GetToken 方法。 為方便起見，我已將其改名。

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

為了方便使用，我已將所有此類程式碼放入我的 Web 應用程式專案的公用程式類別。

最後的程式碼變更位在 Application_Start 方法中。 首先，我們需要呼叫 GetCert() 方法以載入 ClientAssertionCertificate。 接著，在建立新的 KeyVaultClient 時，變更我們所提供的回呼方法。 請注意，這會取代先前範例中的程式碼。

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>透過 Azure 入口網站將憑證新增到您的 Web 應用程式

將憑證新增到您的 Web 應用程式很簡單，只需兩個步驟。 首先，請移至 Azure 入口網站並瀏覽至您的 Web 應用程式。 在 Web 應用程式的 [設定] 刀鋒視窗中，按一下 [自訂網域及 SSL] 的項目。 在開啟的刀鋒視窗中，您將能夠上傳在先前範例中建立的憑證 KVWebApp.pfx，並請確定您記得該 pfx 的密碼。

![在 Azure 入口網站中將憑證新增到 Web 應用程式][2]

您需要做的最後一件事，就是將應用程式設定新增至名為WEBSITE\_LOAD\_CERTIFICATES 與值為 * 的 Web 應用程式。 如此可確保載入所有憑證。 如果您只想載入已上傳的憑證，則可輸入其憑證指紋的逗號分隔清單。

若要深入了解將憑證加入 Web 應用程式的程序，請參閱 [在 Azure 網站應用程式中使用憑證](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>將憑證新增到 Key Vault 中作為祕密

您可以不要將憑證直接上傳到 Web 應用程式服務，而是將它儲存在 Key Vault 中作為祕密，然後從該處部署憑證。 這是兩個步驟的程序，以下的部落格文章有大概的描述： [透過金鑰保存庫部署 Azure Web 應用程式憑證](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>接續步驟

如需程式設計參考，請參閱 [Azure 金鑰保存庫 C# 用戶端 API 參考](https://msdn.microsoft.com/library/azure/dn903628.aspx)。

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
