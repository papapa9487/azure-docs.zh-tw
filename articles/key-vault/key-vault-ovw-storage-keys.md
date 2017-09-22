---
ms.assetid: 
title: "Azure Key Vault 儲存體帳戶金鑰"
description: "儲存體帳戶金鑰提供 Azure Key Vault 與 Azure 儲存體帳戶的金鑰型存取之間的完美整合。"
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b7b8583e8923e65ff068a2bec060a27a14905485
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault 儲存體帳戶金鑰

在 Azure Key Vault 儲存體帳戶金鑰之前，開發人員必須管理自己的 Azure 儲存體帳戶 (ASA) 金鑰，並以手動方式或透過外部自動化來輪替金鑰。 現在，Key Vault 儲存體帳戶金鑰會實作為 [Key Vault 密碼](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)，以便向 Azure 儲存體帳戶進行驗證。 

Azure 儲存體帳戶 (ASA) 金鑰功能可管理密碼輪替， 也提供共用存取簽章 (SAS) 方法，讓您不需要直接接觸 ASA 金鑰。 

如需 Azure 儲存體帳戶的更多一般資訊，請參閱[關於 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account) 。

## <a name="supporting-interfaces"></a>支援的介面

Azure 儲存體帳戶金鑰功能最初是透過 REST、.NET/C# 和 PowerShell 介面提供。 如需詳細資訊，請參閱 [Key Vault 參考](https://docs.microsoft.com/azure/key-vault/)。


## <a name="what-key-vault-manages"></a>Key Vault 的管理內容

當您使用 Managed 儲存體帳戶金鑰時，Key Vault 會代表您執行幾個內部管理功能。

- Azure Key Vault 會管理 Azure 儲存體帳戶 (ASA) 的金鑰。
    - Azure Key Vault 可在內部列出 (同步) Azure 儲存體帳戶金鑰。  
    - Azure Key Vault 會定期重新產生 (輪替) 金鑰。 
    - 永遠不會傳回金鑰值以回應呼叫者。 
    - Azure Key Vault 會管理儲存體帳戶和傳統儲存體帳戶的金鑰。 
- Azure Key Vault 可讓身為保存庫/物件擁有者的您建立 SAS (帳戶或服務 SAS) 定義。
    - 系統會透過 REST URI 路徑，將使用 SAS 定義建立的 SAS 值傳回作為密碼。 如需詳細資訊，請參閱 [Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations) (Azure Key Vault 儲存體帳戶作業)。

## <a name="naming-guidance"></a>命名指導方針

- 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。  
- SAS 定義名稱的長度必須是 1-102 個字元，而且只能包含 0-9、a-z、A-Z。

## <a name="developer-experience"></a>開發人員體驗

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault 儲存體金鑰之前 

開發人員之前必須對儲存體帳戶金鑰執行下列實作，才能存取 Azure 儲存體。 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault 儲存體金鑰之後 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>開發人員指引

- 只允許 Key Vault 管理您的 ASA 金鑰。 請勿嘗試自行管理，您會干擾 Key Vault 的處理序。 
- 不允許多個 Key Vault 物件管理 ASA 金鑰。 
- 如果您需要以手動方式重新產生 ASA 金鑰，建議您透過 Key Vault 重新產生。 

## <a name="getting-started"></a>開始使用

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>設定角色型存取控制 (RBAC) 權限

Key Vault 需要「列出」及「重新產生」儲存體帳戶金鑰的權限。 請使用下列步驟來設定這些權限：

- 取得 Key Vault 的 ObjectId： 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`
    
     或
     
    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- 將儲存體金鑰操作員角色指派給 Azure Key Vault 身分識別： 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 若是傳統帳戶類型，請將角色參數設定為 [傳統儲存體帳戶金鑰操作員服務角色]。

## <a name="working-example"></a>實用範例

下列範例示範如何建立金鑰保存庫 Managed Azure 儲存體帳戶和相關聯的共用存取簽章 (SAS) 定義。

### <a name="assumptions"></a>假設

本實用範例定會使用到下列陳述式。

- 儲存體資源位於：*/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- 金鑰保存庫的名稱是：*yourtest1*

### <a name="get-a-service-principal"></a>取得服務主體

```powershell
Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093
```

上一個命令的輸出會包含您的 ServicePrincipal，我們稱之為 *yourServicePrincipalId*。 

### <a name="set-permissions"></a>設定權限

請確定您已將您的儲存體權限設定為*全部*。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId yourServicePrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>允許存取

您需要將金鑰保存庫服務的存取權授與儲存體帳戶，才能建立 Managed 儲存體帳戶和 SAS 定義。

```powershell
New-AzureRmRoleAssignment -ObjectId yourServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>建立儲存體帳戶

現在請建立一個 Managed 儲存體帳戶和兩個 SAS 定義。 帳戶 SAS 會以不同的權限提供 Blob 服務的存取權。

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>重新產生

使用下列命令設定重新產生期間。

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>設定 SAS 定義

在金鑰保存庫中為 Managed 儲存體帳戶設定 SAS 定義。

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>取得權杖

取得對應的 SAS 權杖，並對儲存體進行呼叫。

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>建立儲存體

請注意，嘗試使用 *$sastoken1* 存取會失敗，但是能夠使用 *$sastoken2* 進行存取。 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>範例摘要

您能夠使用具有寫入權限的 SAS 權杖來存取儲存體 Blob 內容。

### <a name="relevant-powershell-cmdlets"></a>相關的 Powershell Cmdlet

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>儲存體帳戶登入 

範例：您身為 Key Vault 物件擁有者，將儲存體帳戶物件新增至 Azure Key Vault，以登入儲存體帳戶。

在登入期間，Key Vault 需要驗證登入帳戶的身分識別具有「列出」及「重新產生」儲存體金鑰的存取權限。 為了確認這些權限，Key Vault 從驗證服務取得 OBO (代表) 權杖，將對象設為 Azure Resource Manager，並且對 Azure 儲存體服務發出「列出」金鑰呼叫。 如果「列出」呼叫失敗，則 Key Vault 物件建立會失敗，並出現 HTTP 狀態碼「禁止」。 以此方式列出的金鑰會透過 Key Vault 實體儲存體來快取。 

Key Vault 必須驗證身分識別具有「重新產生」權限，才能取得擁有權來重新產生您的金鑰。 若要驗證透過 OBO 權杖的身分識別及 Key Vault 的第一個憑證者身分識別具有這些權限：

- Key Vault 會列出儲存體帳戶資源的 RBAC 權限。
- Key Vault 會透過比對動作和非動作的規則運算式來驗證回應。 

在 [Key Vault - 受管理的儲存體帳戶金鑰範例](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167)中尋找一些支援的範例。

如果身分識別沒有「重新產生」權限，或如果 Key Vault 的第一個憑證者身分識別沒有「列出」或「重新產生」權限，則登入要求會失敗，並傳回適當的錯誤碼和訊息。 

只有在您使用 PowerShell 或 CLI 的第一個憑證者原生用戶端應用程式時，才能使用 OBO 權杖。

## <a name="other-applications"></a>其他應用程式

- 使用 Key Vault 儲存體帳戶金鑰建構的 SAS 權杖，會提供更受控制的 Azure 儲存體帳戶存取權。 如需詳細資訊，請參閱[使用共用存取簽章](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)。

## <a name="see-also"></a>另請參閱

- [關於金鑰、密碼與憑證](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 小組部落格](https://blogs.technet.microsoft.com/kv/)

