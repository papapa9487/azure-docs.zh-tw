---
title: "Azure 資源原則 | Microsoft Docs"
description: "描述如何使用 Azure Resource Manager 原則以確保在部署期間設定一致的資源內容。 原則可以套用在訂用帳戶或資源群組。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 0ee2624f45a1de0c23cae4538a38ae3e302eedd3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="resource-policy-overview"></a>資源原則概觀
資源原則可讓您為組織中的資源建立慣例。 藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 所以，如果原則套用至資源群組，它會適用於該資源群組中的所有資源。

有兩個概念可了解原則︰

* 原則定義 - 您說明何時會強制執行原則及要採取的動作
* 原則指派 - 您將原則定義套用至範圍 (訂用帳戶或資源群組)

本主題著重於原則定義。 如需原則指派的相關資訊，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)或[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。

建立和更新資源 (PUT 和 PATCH 作業) 時，會評估原則。

> [!NOTE]
> 目前，原則不會評估不支援標記、種類及位置的資源類型，例如 Microsoft.Resources/deployments 資源類型。 未來將加入此支援。 若要避免向下相容問題，撰寫原則時應該明確指定類型。 例如，會對所有類型套用未指定類型的標記原則。 在此情況下，如果有不支援標記的巢狀資源，且部署資源類型已新增原則評估中，範本部署可能會失敗。 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>它和 RBAC 有什麼不同？
原則和角色型存取控制 (RBAC) 之間有幾個主要差異。 RBAC 著重於不同範圍的**使用者**動作。 例如，若您被加入所需範圍內之資源群組的參與者角色中，您便能對該資源群組做出變更。 原則在部署期間著重於**資源**屬性。 例如，您能夠透過原則控制可以佈建的資源類型。 或者，您可以限制資源可以佈建的位置。 不同於 RBAC，原則是個預設允許和明確拒絕的系統。 

若要使用原則，您必須透過 RBAC 驗證。 具體來說，您的帳戶需要：

* 定義原則的 `Microsoft.Authorization/policydefinitions/write` 權限
* 指派原則的 `Microsoft.Authorization/policyassignments/write` 權限 

這些權限不包括在**參與者**角色中。

## <a name="built-in-policies"></a>內建原則

Azure 提供一些內建原則定義，可能會降低您需要定義的原則數目。 在繼續處理原則定義之前，您應該先考慮內建原則是否已提供所需的定義。 內建的原則定義如下：

* 允許的位置
* 允許的資源類型
* 允許的儲存體帳戶 SKU
* 允許的虛擬機器 SKU
* 套用標籤和預設值
* 強制執行標籤和值
* 不允許的資源類型
* 需要 SQL Server 12.0 版
* 需要儲存體帳戶加密

您可以透[入口網站](resource-manager-policy-portal.md)、[PowerShell](resource-manager-policy-create-assign.md#powershell) 或 [Azure CLI](resource-manager-policy-create-assign.md#azure-cli) 來指派任何這些原則。

## <a name="policy-definition-structure"></a>原則定義結構
使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

* 參數
* 顯示名稱
* 說明
* 原則規則
  * 邏輯評估
  * 效果

下列範例示範的原則會限制資源的部署之處︰

```json
{
  "properties": {
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

## <a name="parameters"></a>參數
使用參數會減少原則定義的數量，有助於簡化原則管理。 定義資源屬性的原則 (例如，限制可以在其中部署資源的位置)，並在定義中包含參數。 然後，您藉由在指派原則時傳入不同的值 (例如，指定訂用帳戶的一組位置)，針對不同的案例重複使用該原則定義。

當您建立原則定義時，宣告參數。

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

參數的類型可以是字串或陣列。 中繼資料屬性是由 Azure 入口網站等工具所使用，用來顯示人性化的資訊。 

在原則規則中，您可以使用下列語法參考參數︰ 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>顯示名稱和描述

使用 **displayName** 和 **description**找出原則定義，並提供使用時的內容。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。 在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

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

使用**符合**條件時，請提供 `#` 來表示數字、`?` 來表示字母，以及任何其他字元來表示該實際字元。 例如，請參閱[為名稱和文字套用資源原則](resource-manager-policy-naming-convention.md)。

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
原則支援三種效果類型 - `deny`、`audit` 和 `append`。 

* **拒絕**會在稽核記錄中產生事件，並且使要求失敗
* **稽核**會在稽核記錄中產生事件，但不會使要求失敗
* **附加**會在要求中加入一組已定義的欄位 

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


## <a name="policy-examples"></a>原則範例

下列主題包含原則範例︰

* 如需標籤原則的範例，請參閱[套用標籤的資源原則](resource-manager-policy-tags.md)。
* 如需命名和文字模式的範例，請參閱[為名稱和文字套用資源原則](resource-manager-policy-naming-convention.md)。
* 如需儲存體原則的範例，請參閱[將資源原則套用至儲存體帳戶](resource-manager-policy-storage.md)。
* 如需虛擬機器原則的範例，請參閱[將資源原則套用至 Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) 和[將資源原則套用至 Windows VM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)


## <a name="next-steps"></a>後續步驟
* 在定義原則規則後，將它指派給範圍。 若要透過入口網站來指派原則，請參閱[使用 Azure 入口網站來指派和管理資源原則](resource-manager-policy-portal.md)。 若要透過 REST API、PowerShell 或 Azure CLI 來指派原則，請參閱[透過指令碼來指派和管理原則](resource-manager-policy-create-assign.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂閱的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。
* 原則結構描述會發佈於 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)。 


