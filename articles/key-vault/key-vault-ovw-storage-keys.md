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
ms.date: 07/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3148088c88236c64e089fd25c98eb8ac7cdcbfea
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault 儲存體帳戶金鑰

在 Azure Key Vault 儲存體帳戶金鑰之前，開發人員必須管理自己的 Azure 儲存體帳戶 (ASA) 金鑰，並以手動方式或透過外部自動化來輪替金鑰。 現在，Key Vault 儲存體帳戶金鑰會實作為 [Key Vault 密碼](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)，以便向 Azure 儲存體帳戶進行驗證。 

ASA 金鑰功能會為您管理密碼輪替，並提供共用存取簽章 (SAS) 方法，讓您不需要直接接觸 ASA 金鑰。 

如需 Azure 儲存體帳戶的更多一般資訊，請參閱[關於 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account) 。

## <a name="supporting-interfaces"></a>支援的介面

Azure 儲存體帳戶金鑰功能最初是透過 REST、.NET/C# 和 PowerShell 介面提供。 如需詳細資訊，請參閱 [Key Vault 參考](https://docs.microsoft.com/azure/key-vault/)。


## <a name="storage-account-keys-behavior"></a>儲存體帳戶金鑰行為

### <a name="what-key-vault-manages"></a>Key Vault 的管理內容

當您使用儲存體帳戶金鑰時，Key Vault 會代表您執行幾個內部管理功能。

1. Azure Key Vault 會管理 Azure 儲存體帳戶 (ASA) 的金鑰。 
    - Azure Key Vault 可在內部列出 (同步) Azure 儲存體帳戶金鑰。  
    - Azure Key Vault 會定期重新產生 (輪替) 金鑰。 
    - 永遠不會傳回金鑰值以回應呼叫者。 
    - Azure Key Vault 會管理儲存體帳戶和傳統儲存體帳戶的金鑰。 
2. Azure Key Vault 可讓身為保存庫/物件擁有者的您建立 SAS (帳戶或服務 SAS) 定義。 
    - 系統會透過 REST URI 路徑，將使用 SAS 定義建立的 SAS 值傳回作為密碼。 如需詳細資訊，請參閱 [Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations) (Azure Key Vault 儲存體帳戶作業)。

### <a name="naming-guidance"></a>命名指導方針

儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。  
 
SAS 定義名稱的長度必須是 1-102 個字元，而且只能包含 0-9、a-z、A-Z。

## <a name="developer-experience"></a>開發人員體驗 

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault 儲存體金鑰之前 

開發人員之前必須對儲存體帳戶金鑰執行下列實作，才能存取 Azure 儲存體。 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault 儲存體金鑰之後 

```
//Please make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>開發人員最佳做法 

- 只允許 Key Vault 管理您的 ASA 金鑰。 請勿嘗試自行管理，您會干擾 Key Vault 的處理序。 
- 不允許多個 Key Vault 物件管理 ASA 金鑰。 
- 如果您需要以手動方式重新產生 ASA 金鑰，建議您透過 Key Vault 重新產生。 

## <a name="getting-started"></a>開始使用

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>設定角色型存取控制 (RBAC) 權限

Key Vault 需要「列出」及「重新產生」儲存體帳戶金鑰的權限。 請使用下列步驟來設定這些權限：

- 取得 Key Vault 的 ObjectId： 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- 將儲存體金鑰操作員角色指派給 Azure Key Vault 身分識別： 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 若是傳統帳戶類型，請將角色參數設定為 [傳統儲存體帳戶金鑰操作員服務角色]。

### <a name="storage-account-onboarding"></a>儲存體帳戶登入 

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

