---
title: "建立第一個 Azure Resource Manager 範本 | Microsoft Docs"
description: "說明如何建立第一個 Azure Resource Manager 範本的逐步指南。 它說明如何使用儲存體帳戶的範本參考來建立範本。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 7d20469aaf2dfdd7a5f3650983b59152de837837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>建立及部署第一個 Azure Resource Manager 範本
本主題會逐步引導您完成建立第一個 Azure Resource Manager 範本的步驟。 Resource Manager 範本是 JSON 檔案，該檔案定義您需要為您的解決方案部署的資源。 若要了解部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。 如果您有現成的資源且想要取得這些資源的範本，請參閱[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。

若要建立並修改範本，您需要 JSON 編輯器。 [Visual Studio Code](https://code.visualstudio.com/) 是輕量型、開放原始碼、跨平台的程式碼編輯器。 強烈建議使用 Visual Studio Code 來建立 Resource Manager 範本。 本文假設您使用 VS Code。 如果您有其他 JSON 編輯器 (如 Visual Studio)，您可以使用該編輯器。

## <a name="prerequisites"></a>必要條件

* Visual Studio Code。 如有需要，請從 [https://code.visualstudio.com/](https://code.visualstudio.com/) 進行安裝。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-template"></a>建立範本

讓我們從將儲存體帳戶部署到訂用帳戶的簡單範本著手。

1. 選取 [檔案] > [新增檔案]。 

   ![新增檔案](./media/resource-manager-create-first-template/new-file.png)

2. 複製以下 JSON 語法並貼到您的檔案中：

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   儲存體帳戶名稱有幾項限制，以致它們難以設定。 名稱的長度必須介於 3 到 24 個字元，只能使用數字和小寫字母，而且必須是唯一的。 前述版本會使用 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函式來產生雜湊值。 為了要讓此雜湊值更有意義，它會新增前置詞*storage*。 

3. 將此檔案儲存為本機資料夾的 azuredeploy.json。

   ![儲存範本](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>部署範本

您已準備好部署此範本。 您可以使用 PowerShell 或 Azure CLI 建立資源群組。 然後，將儲存體帳戶部署到該資源群組。

* 對於 PowerShell，從包含範本的資料夾使用下列命令：

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* 對於 Azure CLI 的本機安裝，從包含範本的資料夾使用下列命令：

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

部署完成時，您的儲存體帳戶會儲存在資源群組中。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

針對 Azure CLI，使用下列命令：

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
```

目前，PowerShell 在 Cloud Shell 中以預覽形式提供使用。 針對 PowerShell，使用下列命令：

```powershell
New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile $home\CloudDrive\templates\azuredeploy.json
```

部署完成時，您的儲存體帳戶會儲存在資源群組中。

## <a name="customize-the-template"></a>自訂範本

範本正常運作，但不具彈性。 它一律會將本機備援儲存體部署至美國中南部。 名稱一律為 *storage* 後面接著雜湊值。 若要讓範本能使用於不同的案例，請將參數新增至範本。

以下範例顯示包含兩個參數的 parameters 區段。 第一個參數 `storageSKU` 可讓您指定備援類型。 它會將您可傳入的值限制為對儲存體帳戶有效的值。 它也會指定預設值。 第二個參數 `storageNamePrefix` 會設定為最多允許 11 個字元。 它會指定預設值。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

在 variables 區段中，新增名為 `storageName` 的變數。 它結合了來自 parameters 的前置詞值與來自 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函式的雜湊值。 它會使用 [toLower](resource-group-template-functions-string.md#tolower) 函式，將所有字元轉換成小寫。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

若要對您的儲存體帳戶使用這些新值，請變更資源定義：

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

請注意，儲存體帳戶的名稱現在會設定為您新增的變數。 SKU 名稱會設定為參數的值。 位置會設定為與資源群組相同的位置。

儲存您的檔案。 

您的範本現在如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>重新部署範本

使用不同的值重新部署範本。

對於 PowerShell，請使用：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

對於 Azure CLI，請使用：

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

對於 Cloud Shell，將已變更的範本上傳到檔案共用。 覆寫現有的檔案。 然後，使用下列命令：

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>使用自動完成

到目前為止，您在範本上的工作只包含從本文複製並貼上 JSON。 不過，在開發自己的範本時，您需要尋找並指定資源類型可用的屬性和值。 VS Code 會讀取資源類型的結構描述，並建議屬性和值。 若要查看自動完成功能，請移至您範本的 properties 元素並新增一行。 輸入引號，並注意 VS Code 會立即建議 properties 元素內可用的名稱。

![顯示可用的屬性](./media/resource-manager-create-first-template/show-properties.png)

選取 [encryption]。 輸入冒號 (:)，而 VS Code 會建議您新增物件。

![新增物件](./media/resource-manager-create-first-template/add-object.png)

按下 Tab 或 Enter 鍵來新增物件。

再次輸入引號，您會看到 VS Code 現在會建議可用於加密的屬性。

![顯示加密屬性](./media/resource-manager-create-first-template/show-encryption-properties.png)

選取 [services] 並繼續根據 VS Code 擴充功能來新增值，直到您有：

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

您已啟用儲存體帳戶的 blob 加密。 不過，VS Code 已發現問題。 請注意，加密有一則警告。

![加密警告](./media/resource-manager-create-first-template/encryption-warning.png)

若要查看警告，請將滑鼠停留在綠色線條上。

![遺漏屬性](./media/resource-manager-create-first-template/missing-property.png)

您會看到 encryption 元素需要有 keySource 屬性。 在服務物件之後新增逗號，然後新增 keySource 屬性。 VS Code 建議以 **"Microsoft.Storage"** 作為有效值。 完成時，properties 元素如下所示：

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

最終範本如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>部署已加密的儲存體

同樣地，部署範本並提供新的儲存體帳戶名稱。

對於 PowerShell，請使用：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

對於 Azure CLI，請使用：

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

對於 Cloud Shell，將已變更的範本上傳到檔案共用。 覆寫現有的檔案。 然後，使用下列命令：

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>清除資源

不再需要資源時，可藉由刪除資源群組來清除您所部署的資源。

對於 PowerShell，請使用：

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

對於 Azure CLI，請使用：

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>後續步驟
* 若要取得更多關於開發範本的協助，您可以安裝 VS Code 擴充功能。 如需詳細資訊，請參閱[使用 Visual Studio Code 擴充功能來建立 Azure Resource Manager 範本](resource-manager-vscode-extension.md)。
* 若要深入了解範本的結構，請參閱 [編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要了解儲存體帳戶的屬性，請參閱[儲存體帳戶範本參考](/azure/templates/microsoft.storage/storageaccounts)。
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
