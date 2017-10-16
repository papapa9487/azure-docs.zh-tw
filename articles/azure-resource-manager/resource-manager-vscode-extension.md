---
title: "採用 VS Code 擴充功能的 Azure Resource Manager 範本 | Microsoft Docs"
description: "利用 Azure Resource Manager Tools 擴充功能來使用 Resource Manager 範本。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: fbc0fd12999c9085c3c364f0d7115eb1ab1ddd74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>使用 Visual Studio Code 擴充功能來建立 Azure Resource Manager 範本
本文說明在 Visual Studio Code 中安裝和使用 Azure Resource Manager Tools 擴充功能的優點。 不需使用擴充功能，即可在 VS Code 中建立 Resource Manager 範本，但擴充功能會提供可簡化範本開發的自動完成選項。 它會建議範本中可用的函式、參數和變數。

若要完成本文，您需要 [Visual Studio Code](https://code.visualstudio.com/)。

若要了解部署和管理 Azure 解決方案的相關概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

## <a name="create-the-template"></a>建立範本

本文是以您在[建立及部署第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)中建立的範本為基礎。 如果您已有範本，可以跳過這一節。

1. 如果您需要建立範本，請啟動 VS Code。 選取 [檔案] > [新增檔案]。 

   ![新增檔案](./media/resource-manager-vscode-extension/new-file.png)

2. 複製以下 JSON 語法並貼到您的檔案中：

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
     "outputs": {  }
   }
   ```

3. 將此檔案儲存為本機資料夾的 azuredeploy.json。

   ![儲存範本](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>安裝擴充功能

1. 在 VS Code 中，選取 [擴充功能]。

   ![選取擴充功能](./media/resource-manager-vscode-extension/select-extensions.png)

2. 搜尋 [Azure Resource Manager Tools] 並選取 [安裝]。

   ![安裝擴充功能](./media/resource-manager-vscode-extension/install-extension.png)

3. 若要完成擴充功能安裝，請選取 [重新載入]。

## <a name="edit-the-template"></a>編輯範本

1. 開啟 azuredeploy.json 檔案。

2. 延伸模組會擷取所有可用的[樣板函式](resource-group-template-functions.md)。 它也會讀取在範本中定義的參數和變數。 若要查看這項功能，您可將兩個值新增至輸出區段。 在範本中，將 outputs 區段取代為：

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. 將游標置於 **groupLocation** 中值的引號內。 鍵入左括弧 (`[`)。 請注意，擴充功能會立即建議可用的範本函式。

   ![顯示可用的功能](./media/resource-manager-vscode-extension/available-functions.png)

4. 開始鍵入 **resourceGroup**。 `resourceGroup()` 函式顯示後，請按 Tab 或 Enter 鍵。

   ![選取 resourceGroup 函式](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. 擴充功能會填入函式語法。 [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) 函式不接受參數。 在右括號後面加上句號。 擴充功能提供的屬性可用於 `resourceGroup()` 函式所傳回的物件。 選取 `location`。

   ![選取屬性](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. 在 **location** 之後，加上右括弧。

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. 現在，將游標置於 **storageUri** 的引號內。 再次鍵入左括弧。 開始鍵入 **reference**。 已選取該函式後，請按 Tab 或 Enter 鍵。

   ![選取參考](./media/resource-manager-vscode-extension/add-reference.png)

8. [reference](resource-group-template-functions-resource.md#reference) 會接受資源 ID 或資源名稱作為參數。 您已有變數中的儲存體帳戶名稱。 鍵入 **var**，然後選取 Ctrl+空格鍵。 擴充功能會建議變數函式。

   ![選取變數](./media/resource-manager-vscode-extension/add-variable.png)

   按 Tab 或 Enter 鍵。

9. [variables](resource-group-template-functions-deployment.md#variables) 函式需要變數名稱。 在括號內，新增單引號。 擴充功能會提供您在範本中定義的變數名稱。

   ![顯示變數](./media/resource-manager-vscode-extension/show-variables.png) 

10. 選取 **storageName** 變數。 加上右括弧。 下列範例顯示 outputs 區段：

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(variables('storageName'))]"
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
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageName'))]"
    }
  }
}
```

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

當部署完成時，系統會傳回輸出值。

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
* 若要深入了解範本的結構，請參閱 [編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要了解儲存體帳戶的屬性，請參閱[儲存體帳戶範本參考](/azure/templates/microsoft.storage/storageaccounts)。
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
