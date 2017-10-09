---
title: "允許應用程式取出 Azure Stack Key Vault 密碼 | Microsoft Docs"
description: "使用範例應用程式來處理 Azure Stack Key Vault"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: 9b39c949e33dfbcc75ba8c09d6b38e4bdb243766
ms.openlocfilehash: 7cfb78cc5219d4adab5ceddc9d7eb8d1fc71b678
ms.contentlocale: zh-tw
ms.lasthandoff: 09/19/2017

---

# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>使用金鑰保存庫中所儲存金鑰和密碼的範例應用程式

在本文章中，我們將說明如何執行範例應用程式 (HelloKeyVault) 以取出 Azure Stack 中金鑰保存庫的金鑰和密碼。

## <a name="prerequisites"></a>必要條件 

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件：

* 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。  
* 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>建立並取得金鑰保存庫和應用程式設定

首先，您應該在 Azure Stack 中建立金鑰保存庫，並在 Azure Active Directory (Azure AD) 中註冊應用程式。 您可以使用 Azure 入口網站或 PowerShell 來建立及註冊金鑰保存庫。 此文章說明以 PowerShell 的方式來執行該作業。 依預設，此 PowerShell 指令碼會在 Active Directory 中建立新的應用程式。 不過，您也可以使用您的其中一個現有的應用程式。 請確定提供 `aadTenantName` 和 `applicationPassword` 變數的值。 如果您並未指定 `applicationPassword` 變數的值，則此指令碼將會產生隨機密碼。 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

下列螢幕擷取畫面會顯示先前指令碼的輸出：

![應用程式組態](media/azure-stack-kv-sample-app/settingsoutput.png)

記下先前指令碼所傳回 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 的值。 您將使用這些值來執行 HelloKeyVault 應用程式。

## <a name="download-and-run-the-sample-application"></a>下載並執行範例應用程式

下載 Azure [金鑰保存庫用戶端範例](https://www.microsoft.com/en-us/download/details.aspx?id=45343)頁面所提供的金鑰保存庫範例。 將.zip 檔的內容解壓縮至您的開發工作站上。 範例資料夾中有兩個範例。 我們在本主題中會使用 HellpKeyVault 範例。 瀏覽至 **Microsoft.Azure.KeyVault.Samples** > **範例** > **HelloKeyVault** 資料夾，然後在 Visual Studio 中開啟 HelloKeyVault 應用程式。 

開啟 HelloKeyVault\App.config 檔案並取代 <appSettings> 元素以及先前指令碼所傳回 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 的值。 請注意，依預設 App.config 會包含 *AuthCertThumbprint* 的預留位置，但您將會改用 *AuthClientSecret*。 取代設定之後，請重建解決方案並啟動應用程式。

![應用程式設定](media/azure-stack-kv-sample-app/appconfig.png)
 
應用程式會登入 Azure AD，然後使用該權杖來驗證 Azure Stack 中的金鑰保存庫。 應用程式會對金鑰保存庫的金鑰和密碼執行如建立、加密、包裝和刪除等作業。 您也可以將特定參數如 *encrypt* 和 *decrypt* 等傳遞至應用程式，以確保應用程式僅對保存庫執行那些作業。 


## <a name="next-steps"></a>後續步驟
[使用金鑰保存庫密碼部署 VM](azure-stack-kv-deploy-vm-with-secret.md)

[使用 Key Vault 憑證部署 VM](azure-stack-kv-push-secret-into-vm.md)




