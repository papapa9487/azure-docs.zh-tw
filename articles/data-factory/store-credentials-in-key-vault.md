---
title: "在 Azure Key Vault 中儲存認證 | Microsoft Docs"
description: "了解如何為 Azure Key Vault 中使用的資料存放區儲存認證，Azure Data Factory 可以在執行階段自動擷取。"
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 193d7c77f01384106b3e0d932d02ba6cdff9e750
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中儲存認證

您可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中為資料存放區儲存認證。 執行活動 (該活動使用資料存放區) 時，Azure Data Factory 會擷取認證。 目前，只有[動態連接器](connector-dynamics-crm-office-365.md)支援這項功能。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您是使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="steps"></a>步驟

建立資料處理站時，服務識別可隨著處理站建立一起建立。 服務識別是向 Azure Active Directory 註冊的受管理應用程式，代表這個特定資料處理站。

- 透過 **Azure 入口網站或 PowerShell** 建立資料處理站時，自公開預覽時一律會自動建立服務識別。
- 透過 **SDK** 建立資料處理站時，只有您在用於建立的處理站物件中指定 "Identity = new FactoryIdentity()" 時，才會建立服務識別。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 透過 **REST API** 建立資料處理站時，只有您在要求本文中指定 "identity" 區段時，才會建立服務識別。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

若要參考儲存在 Azure Key Vault 中的認證，您需要：

1. 複製隨著您的資料處理站一起產生的「服務識別應用程式識別碼」。 請參閱[擷取服務識別](#retrieve-service-identity)。
2. 將服務識別存取權授與您的 Azure Key Vault。 在金鑰保存庫 -> 存取控制 -> 新增 -> 搜尋此服務識別應用程式識別碼，以新增讀者權限。 這樣可以讓這個指定的處理站存取金鑰保存庫中的密碼。
3. 建立指向您的 Azure Key Vault 的已連結服務。 請參閱 [Azure Key Vault 已連結服務](#azure-key-vault-linked-service)。
4. 建立資料存放區已連結服務，其中參考儲存在金鑰保存庫的對應密碼。 請參閱[參考儲存在金鑰保存庫的認證](#reference-credential-stored-in-key-vault)。

## <a name="retrieve-service-identity"></a>擷取服務識別

您可以從 Azure 入口網站或以程式設計的方式擷取服務識別。 下列各節會顯示一些範例。

>[!TIP]
> 如果您沒有看到服務識別，請藉由更新您的處理站來[產生服務識別](#generate-service-identity)。

### <a name="using-azure-portal"></a>使用 Azure 入口網站

您可以從 Azure 入口網站 -> 您的資料處理站 -> 設定 -> 屬性，找到服務識別資訊：

- 服務識別識別碼
- 服務識別租用戶
- **服務識別應用程式識別碼** > 複製此值，以在 Key Vault 中授與存取權

![擷取服務識別](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>使用 PowerShell

當您取得特定的資料處理站時，將會傳回服務識別主體識別碼和租用戶識別碼，如下所示：

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

複製主體識別碼，然後以主體識別碼作為參數來執行下面的 Azure Active Directory 命令，以取得您可用於在 Key Vault 中授與存取權的 **ApplicationId**：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 已連結服務

以下是針對 Azure Key Vault 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | type 屬性必須設為：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL。 | 是 |

**範例：**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>參考儲存在金鑰保存庫的認證

當您在參考金鑰保存庫密碼的已連結服務中設定欄位時，支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 欄位的 type 屬性必須設定為：**AzureKeyVaultSecret**。 | 是 |
| secretName | Azure Key Vault 中密碼的名稱。 | 是 |
| secretVersion | Azure Key Vault 中密碼的版本。<br/>如果未指定，它會一律使用最新版本的密碼。<br/>如果指定，則它會遵循指定的版本。| 否 |
| store | 代表您用來儲存認證的 Azure Key Vault 已連結服務。 | 是 |

**範例：(請參閱「密碼」一節)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>產生服務識別

如果您發現資料處理站沒有與下列[擷取服務識別](#retrieve-service-identity)指示相關聯的服務識別，您可藉由以程式設計方式透過身分識別啟動器更新資料處理站來產生一個。

> [!NOTE]
> - **無法變更服務識別**。 更新已有服務識別的資料處理站不會有任何影響，服務識別將維持不變。
> - **無法刪除服務識別**。 如果您更新已有服務識別的資料處理站，但未在處理站物件中指定 "identity" 參數，或未在 REST 要求本文中指定 "identity" 區段，您將會收到錯誤。

下列各節會顯示為處理站產生服務識別 (如果不存在) 的一些範例。

### <a name="using-powershell"></a>使用 PowerShell

再次呼叫 **Set-AzureRmDataFactoryV2** 命令，您就會看到新產生的 "Identity" 欄位：

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>使用 REST API

在要求本文中以 "identity" 區段呼叫下面的 API：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**要求本文**：新增 "identity": { "type": "SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**回應**：系統會自動建立服務識別，並據此填入 "identity" 區段。

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>使用 SDK

以 Identity=new FactoryIdentity() 呼叫資料處理站 create_or_update 函式。 使用 .NET 的範例程式碼：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。