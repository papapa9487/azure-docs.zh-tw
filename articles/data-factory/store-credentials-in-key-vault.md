---
title: "在 Azure Key Vault 中儲存認證 | Microsoft Docs"
description: "了解如何為 Azure Key Vault 中使用的資料存放區儲存認證，Azure Data Factory 可以在執行階段自動擷取。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中儲存認證

您可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中為資料存放區儲存認證。 執行活動 (該活動使用資料存放區) 時，Azure Data Factory 會擷取認證。

> [!NOTE]
> 目前，只有[動態連接器](connector-dynamics-crm-office-365.md)支援這項功能。 

## <a name="steps"></a>步驟

建立資料處理站時，會隨著處理站建立一起建立服務識別。 服務識別是向 Azure Active Directory 註冊的受管理應用程式，代表這個特定資料處理站。 您可以從 Azure 入口網站 -> 您的資料處理站 -> 屬性，找到服務識別資訊： 

- 服務識別識別碼
- 服務識別租用戶
- 服務識別應用程式識別碼

若要參考儲存在 Azure Key Vault 中的認證，您需要：

1. 複製隨著您的資料處理站一起產生的「服務識別應用程式識別碼」。
2. 將服務識別存取權授與您的 Azure Key Vault。 在金鑰保存庫 -> 存取控制 -> 新增 -> 搜尋此服務識別應用程式識別碼，以新增讀者權限。 這樣可以讓這個指定的處理站存取金鑰保存庫中的密碼。
3. 建立指向您的 Azure Key Vault 的已連結服務。 請參閱 [Azure Key Vault 已連結服務](#azure-key-vault-linked-service)。
4. 建立資料存放區已連結服務，其中參考儲存在金鑰保存庫的對應密碼。 請參閱[參考儲存在金鑰保存庫的認證](#reference-credential-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 已連結服務

以下是針對 Azure Key Vault 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | type 屬性必須設為：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL (DNS 名稱)。 | 是 |

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

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
