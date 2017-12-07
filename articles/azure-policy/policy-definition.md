---
title: "Azure 原則定義結構 | Microsoft Docs"
description: "說明「Azure 原則」如何使用資源原則定義，藉由描述強制執行原則的時機及所要採取的動作，為您組織中的資源建立慣例。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/31/2017
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: 1b8fd12e071bfbd01567803370e510e7e07ccb99
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

「Azure 原則」使用的資源原則定義可讓您藉由描述強制執行原則的時機及所要採取的動作，為組織中的資源建立慣例。 藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 所以，如果原則套用至資源群組，它會適用於該資源群組中的所有資源。

使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

* 模式
* 參數
* 顯示名稱
* 說明
* 原則規則
  * 邏輯評估
  * 效果

例如，下列 JSON 示範一個會限制資源部署位置的原則：

```json
{
  "properties": {
    "mode": "all",
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

所有「Azure 原則」範本範例都位於[Azure 原則的範本](json-samples.md)。

## <a name="mode"></a>模式

建議您將 `mode` 設定為 `all`，以便讓原則指派評估所有資源群組和類型。 若要查看對資源群組強制執行標籤的原則定義範例，請參閱[允許來自某個資源群組的自訂 VM 映像](scripts/allow-custom-vm-image.md)。

當您將它設定為 [所有] 時，會針對原則評估資源群組和所有資源類型。 入口網站會針對原則使用 [所有]。 如果您使用 PowerShell 或 Azure CLI，您需要指定 `mode` 參數，並將它設定為 [所有]。

所有使用入口網站來建立的原則定義都會使用 `all` 模式，不過，如果您想要使用 PowerShell 或 Azure CLI，就必須指定 `mode` 參數並將其設定為 `all`。

如果您將 mode 設定為 `indexed`，則只有在支援標籤和位置的資源類型上才會評估原則指派。


## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。 在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

例如，您可以為資源特性定義一個原則，來限制可部署資源的位置。 在此情況下，您會在建立原則時宣告下列參數：


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

參數的類型可以是字串或陣列。 中繼資料屬性會用於 Azure 入口網站之類的工具，可顯示使用者易懂的資訊。

在中繼資料屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。  **strongType** 所允許的值目前包括：

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

在原則規則中，您可以使用下列語法參考參數︰

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則定義，以及提供其使用時機的內容。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。

在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>邏輯運算子

支援的邏輯運算子包括︰

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 語法會反轉條件的結果。 **allOf** 語法 (類似於邏輯**And** 作業) 需要所有的條件為 true。 **anyOf** 語法 (類似於邏輯**Or** 作業) 需要一或多個條件為 true。

您可以巢狀邏輯運算子。 下列範例顯示 **allOf** 作業中的巢狀 **not** 作業。

```json
"if": {
  "allOf": [
    {
      "not": {
        "field": "tags",
        "containsKey": "application"
      }
    },
    {
      "field": "type",
      "equals": "Microsoft.Storage/storageAccounts"
    }
  ]
},
```

### <a name="conditions"></a>條件

條件會評估某個**欄位**是否符合特定準則。 支援的條件如下︰

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

當使用 **like**條件時，您可以在值中提供萬用字元 (*)。

使用**符合**條件時，請提供 `#` 來表示數字、`?` 來表示字母，以及任何其他字元來表示該實際字元。 例如，請參閱[已核准的 VM 映像](scripts/allowed-custom-images.md)。

### <a name="fields"></a>欄位
條件是透過欄位所形成。 欄位會顯示用來描述資源狀態的資源要求裝載屬性。  

支援下列欄位：

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*`
* 屬性別名 - 如需清單，請參閱[別名](#aliases)。

### <a name="effect"></a>效果
原則支援下列類型的效果：

* **Deny**：會在稽核記錄中產生事件，並讓要求失敗
* **Audit**：會在稽核記錄中產生事件，但不會讓要求失敗
* **Append**：會在要求中加入一組已定義的欄位
* **AuditIfNotExists**：如果資源不存在，便啟用稽核
* **DeployIfNotExists**：如果資源不存在，便部署該資源。 目前，僅支援透過內建原則達到這個效果。
* **DenyIfNotExists**：如果資源不存在，便拒絕建立資源

對於 **append**，您必須提供下列詳細資料：

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

值可以是字串或 JSON 格式物件。

使用 **AuditIfNotExists****DeployIfNotExists**及 **DenyIfNotExists** 時，您可以評估子資源是否存在，並且在該資源不存在時套用規則及對應的效果。 例如，您可以要求針對所有虛擬網路部署網路監看員。
如需在未部署虛擬機器擴充功能時進行稽核的範例，請參閱[稽核擴充功能是否不存在](scripts/audit-ext-not-exist.md)。


## <a name="aliases"></a>別名

您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您限制某個資源上的某個值所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

**Microsoft.Cache/Redis**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | 設定是否啟用非 SSL Redis 伺服器連接埠 (6379)。 |
| Microsoft.Cache/Redis/shardCount | 設定要在進階叢集快取上建立的分區數目。  |
| Microsoft.Cache/Redis/sku.capacity | 設定要部署的 Redis 快取大小。  |
| Microsoft.Cache/Redis/sku.family | 設定要使用的 SKU 系列。 |
| Microsoft.Cache/Redis/sku.name | 設定要部署的 Redis 快取類型。 |

**Microsoft.Cdn/profiles**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | 設定定價層的名稱。 |

**Microsoft.Compute/disks**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | 設定用來建立虛擬機器的平台映像或 Marketplace 映像供應項目。 |
| Microsoft.Compute/imagePublisher | 設定用來建立虛擬機器的平台映像或 Marketplace 映像發行者。 |
| Microsoft.Compute/imageSku | 設定用來建立虛擬機器的平台映像或 Marketplace 映像 SKU。 |
| Microsoft.Compute/imageVersion | 設定用來建立虛擬機器的平台映像或 Marketplace 映像版本。 |


**Microsoft.Compute/virtualMachines**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 設定用來建立虛擬機器的映像識別碼。 |
| Microsoft.Compute/imageOffer | 設定用來建立虛擬機器的平台映像或 Marketplace 映像供應項目。 |
| Microsoft.Compute/imagePublisher | 設定用來建立虛擬機器的平台映像或 Marketplace 映像發行者。 |
| Microsoft.Compute/imageSku | 設定用來建立虛擬機器的平台映像或 Marketplace 映像 SKU。 |
| Microsoft.Compute/imageVersion | 設定用來建立虛擬機器的平台映像或 Marketplace 映像版本。 |
| Microsoft.Compute/licenseType | 將映像或磁碟設定為內部部署授權。 這個值只會用於包含 Windows Server 作業系統的映象。  |
| Microsoft.Compute/virtualMachines/imageOffer | 設定用來建立虛擬機器的平台映像或 Marketplace 映像供應項目。 |
| Microsoft.Compute/virtualMachines/imagePublisher | 設定用來建立虛擬機器的平台映像或 Marketplace 映像發行者。 |
| Microsoft.Compute/virtualMachines/imageSku | 設定用來建立虛擬機器的平台映像或 Marketplace 映像 SKU。 |
| Microsoft.Compute/virtualMachines/imageVersion | 設定用來建立虛擬機器的平台映像或 Marketplace 映像版本。 |
| Microsoft.Compute/virtualMachines/osDisk.Uri | 設定 vhd URI。 |
| Microsoft.Compute/virtualMachines/sku.name | 設定虛擬機器的大小。 |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | 設定擴充功能發行者的名稱。 |
| Microsoft.Compute/virtualMachines/extensions/type | 設定擴充功能的類型。 |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | 設定擴充功能的版本。 |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 設定用來建立虛擬機器的映像識別碼。 |
| Microsoft.Compute/imageOffer | 設定用來建立虛擬機器的平台映像或 Marketplace 映像供應項目。 |
| Microsoft.Compute/imagePublisher | 設定用來建立虛擬機器的平台映像或 Marketplace 映像發行者。 |
| Microsoft.Compute/imageSku | 設定用來建立虛擬機器的平台映像或 Marketplace 映像 SKU。 |
| Microsoft.Compute/imageVersion | 設定用來建立虛擬機器的平台映像或 Marketplace 映像版本。 |
| Microsoft.Compute/licenseType | 將映像或磁碟設定為內部部署授權。 這個值只會用於包含 Windows Server 作業系統的映象。 |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | 設定擴展集中所有虛擬機器的電腦名稱前置詞。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | 設定使用者映像的 Blob URI。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | 設定用於儲存擴展集作業系統磁碟的容器 URL。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | 設定擴展集中虛擬機器的大小。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | 設定擴展集中的虛擬機器層。 |

**Microsoft.Network/applicationGateways**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | 設定閘道的大小。 |

**Microsoft.Network/virtualNetworkGateways**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | 設定此虛擬網路閘道的類型。 |
| Microsoft.Network/virtualNetworkGateways/sku.name | 設定閘道 SKU 名稱。 |

**Microsoft.Sql/servers**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Sql/servers/version | 設定伺服器的版本。 |

**Microsoft.Sql/databases**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | 設定資料庫的版本。 |
| Microsoft.Sql/servers/databases/elasticPoolName | 設定資料庫所在彈性集區的名稱。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | 設定伺服器所設定的服務層級目標識別碼。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | 設定資料庫所設定之服務等級目標的名稱。  |

**Microsoft.Sql/elasticpools**

| Alias | 說明 |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | 設定資料庫彈性集區的所有共用 DTU。 |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | 設定彈性集區的版本。 |

**Microsoft.Storage/storageAccounts**

| Alias | 說明 |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | 設定用於計費的存取層。 |
| Microsoft.Storage/storageAccounts/accountType | 設定 SKU 名稱。 |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | 設定服務是否對儲存在 Blob 儲存體服務中的資料進行加密。 |
| Microsoft.Storage/storageAccounts/enableFileEncryption | 設定服務是否對儲存在檔案儲存體服務中的資料進行加密。 |
| Microsoft.Storage/storageAccounts/sku.name | 設定 SKU 名稱。 |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | 設定對於儲存體服務僅允許 HTTPS 流量。 |

## <a name="initiatives"></a>計畫

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將所有相關的標籤原則定義組成單一計畫。 您可以套用該計畫，而不個別指派每個原則。

下列範例說明如何建立一個處理兩個標籤 (`costCenter` 和 `productName`) 的計畫。 它會使用兩個內建的原則來套用預設標籤值。


```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>後續步驟

- 檢閱位於 [Azure 原則的範本](json-samples.md)的「Azure 原則」範本範例。
