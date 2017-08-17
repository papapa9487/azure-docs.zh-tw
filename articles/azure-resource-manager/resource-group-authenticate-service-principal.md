---
title: "使用 PowerShell 建立 Azure App 的身分識別 | Microsoft Docs"
description: "描述如何使用 Azure PowerShell 建立 Azure Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。 它示範如何使用密碼或憑證來驗證應用程式。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 36b4cd0674e0f9cec6fb2b00e809c71ee38a80c0
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>使用 Azure PowerShell 建立用來存取資源的服務主體

當您有 App 或指令碼需要存取資源時，可以設定 App 的身分識別，並使用 App 自己的認證進行驗證。 此身分識別就是所謂的服務主體。 這種方法可讓您︰

* 將權限指派給不同於您自己權限的 App 身分識別。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
* 使用憑證在執行無人看管的指令碼時進行驗證。

本主題說明如何使用 [Azure PowerShell](/powershell/azure/overview) 來設定所需的一切項目，以便讓應用程式利用自己的認證和身分識別來執行。

## <a name="required-permissions"></a>所需的權限
若要完成本主題，您必須在 Azure Active Directory 和您的 Azure 訂用帳戶中有足夠的權限。 具體來說，您必須能夠在 Azure Active Directory 中建立應用程式，並將服務主體指派給角色。 

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[檢查必要的權限](resource-group-create-service-principal-portal.md#required-permissions)。

現在，繼續使用下列方式進行驗證的區段：

* [password](#create-service-principal-with-password)
* [自我簽署憑證](#create-service-principal-with-self-signed-certificate)
* [來自憑證授權單位的憑證](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell 命令

若要設定服務主體，您要使用︰

| 命令 | 說明 |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | 建立 Azure Active Directory 服務主體 |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | 在指定的範圍中，將指定的 RBAC 角色指派給指定的主體。 |


## <a name="create-service-principal-with-password"></a>使用密碼建立服務主體

若要使用您訂用帳戶的參與者角色來建立服務主體，請使用︰ 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

範例會睡 20 秒，留一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「PrincipalNotFound︰主體 {id} 不存在於目錄中」。

下列指令碼可讓您指定預設訂用帳戶以外的範圍，然後如果發生錯誤，就重試角色指派︰

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

有關指令碼的一些注意事項︰

* 若要授與身分識別存取預設訂用帳戶，您不需要提供 ResourceGroup 或 SubscriptionId 參數。
* 只有當您想要將角色指派的範圍限制為資源群組時，才指定 ResourceGroup 參數。
*  在此範例中，您將服務主體新增至參與者角色。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。
* 指令碼會睡 15 秒，留一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「PrincipalNotFound︰主體 {id} 不存在於目錄中」。
* 如果您要授與服務主體存取更多訂用帳戶或資源群組，請以不同範圍再次執行 `New-AzureRMRoleAssignment` Cmdlet。


### <a name="provide-credentials-through-powershell"></a>透過 PowerShell 提供認證
現在，您需要以應用程式的形式登入以執行作業。 針對使用者名稱，使用您針對應用程式建立的 `ApplicationId`。 針對密碼，使用您在建立帳戶時所指定的密碼。 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-id}
```

租用戶識別碼不區分大小寫，因此您可以直接將它內嵌在指令碼中。 如果您要擷取租用戶識別碼，請使用︰

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>使用自我簽署憑證建立服務主體

若要使用自我簽署的憑證和訂用帳戶的參與者角色來建立服務主體，請使用︰ 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

範例會睡 20 秒，留一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「PrincipalNotFound︰主體 {id} 不存在於目錄中」。

下列指令碼可讓您指定預設訂用帳戶以外的範圍，然後如果發生錯誤，就重試角色指派。 您必須擁有 Windows 10 或 Windows Server 2016 的 Azure PowerShell 2.0。

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

有關指令碼的一些注意事項︰

* 若要授與身分識別存取預設訂用帳戶，您不需要提供 ResourceGroup 或 SubscriptionId 參數。
* 只有當您想要將角色指派的範圍限制為資源群組時，才指定 ResourceGroup 參數。
* 在此範例中，您將服務主體新增至參與者角色。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。
* 指令碼會睡 15 秒，留一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「PrincipalNotFound︰主體 {id} 不存在於目錄中」。
* 如果您要授與服務主體存取更多訂用帳戶或資源群組，請以不同範圍再次執行 `New-AzureRMRoleAssignment` Cmdlet。

如果您 **不是執行 Windows 10 或 Windows Server 2016 Technical Preview**，則必須從 Microsoft 指令碼中心下載 [自我簽署憑證產生器](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) 。 將其內容解壓縮，並匯入您需要的 Cmdlet。

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
在指令碼中，取代下列兩行來產生憑證。
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>透過自動化的 PowerShell 指令碼提供憑證
每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure Active Directory 的執行個體。 如果您只有一個訂用帳戶，您可以使用：

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

應用程式識別碼和租用戶識別碼不區分大小寫，因此您可以直接將它們內嵌在您的指令碼中。 如果您要擷取租用戶識別碼，請使用︰

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

如果您要擷取應用程式識別碼，請使用︰

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>使用憑證授權中心的憑證來建立服務主體
若要使用憑證授權中心所發行的憑證來建立服務主體，請使用下列指令碼︰

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId

 $KeyId = (New-Guid).Guid
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
 $KeyCredential.StartDate = $PFXCert.NotBefore
 $KeyCredential.EndDate= $PFXCert.NotAfter
 $KeyCredential.KeyId = $KeyId
 $KeyCredential.CertValue = $KeyValue

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -KeyCredentials $keyCredential
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

有關指令碼的一些注意事項︰

* 存取限域為訂用帳戶。
* 在此範例中，您將服務主體新增至參與者角色。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。
* 指令碼會睡 15 秒，留一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 如果您的指令碼等得不夠久，您會看到錯誤指出：「PrincipalNotFound︰主體 {id} 不存在於目錄中」。
* 如果您要授與服務主體存取更多訂用帳戶或資源群組，請以不同範圍再次執行 `New-AzureRMRoleAssignment` Cmdlet。

### <a name="provide-certificate-through-automated-powershell-script"></a>透過自動化的 PowerShell 指令碼提供憑證
每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure Active Directory 的執行個體。

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

應用程式識別碼和租用戶識別碼不區分大小寫，因此您可以直接將它們內嵌在您的指令碼中。 如果您要擷取租用戶識別碼，請使用︰

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

如果您要擷取應用程式識別碼，請使用︰

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>管理認證

若要變更 AD App 的認證，不管是因為安全性危害或認證過期，請使用 [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) 和 [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) Cmdlet。

若要移除應用程式的所有認證，使用︰

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

若要新增密碼，使用：

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

若要新增憑證值，請建立自我簽署的憑證，如本主題中所述。 然後，使用︰

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>儲存存取權杖以簡化登入
若要避免每次需要登入時都要提供服務主體認證，您可以儲存存取權杖。

若要在稍後的工作階段中使用目前的存取權杖，請儲存設定檔。
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
開啟設定檔，並檢查其內容。 請注意其中包含存取權杖。 請不要以手動方式再次登入，只要載入設定檔即可。
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> 存取權杖會過期，因此，只要權杖有效，就只使用可使用的已儲存設定檔。
>  

或者，您可以從 PowerShell 叫用 REST 作業來登入。 從驗證回應，您可以擷取存取權杖以用於其他作業。 如需叫用 REST 作業擷取存取權杖的範例，請參閱[產生存取權杖](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="debug"></a>偵錯

建立服務主體時，您可能會遇到下列錯誤︰

* **Authentication_Unauthorized」**或**「在內容中找不到訂用帳戶。」** - 當您的帳戶在 Azure Active Directory 上未具備註冊應用程式的[必要權限](#required-permissions)時，您就會看到此錯誤。 通常，只有 Azure Active Directory 中的管理使用者可以註冊應用程式，且您的帳戶不是系統管理員時，就會看到此錯誤。 要求系統管理員將您指派給系統管理員角色，或是讓使用者註冊應用程式。

* 您的帳戶**「沒有在範圍 '/subscriptions/{guid}' 中執行 'Microsoft.Authorization/roleAssignments/write' 動作的權限。」** - 當您的帳戶沒有足夠權限可將角色指派給身分識別時，您就會看到此錯誤。 要求訂用帳戶管理員將您新增至「使用者存取系統管理員」角色。

## <a name="sample-applications"></a>範例應用程式
如需透過不同平台以應用程式身分登入的資訊，請參閱：

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>後續步驟
* 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。
* 如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。 
* 如需 Azure Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](../active-directory/active-directory-authentication-scenarios.md)。
* 如需可授與或拒絕使用者的可用動作清單，請參閱 [Azure Resource Manager 資源提供者作業](../active-directory/role-based-access-control-resource-provider-operations.md)。


