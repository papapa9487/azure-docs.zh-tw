---
title: "建立及發佈 Azure 受服務類別目錄管理的應用程式 | Microsoft Docs"
description: "示範如何建立 Azure 受管理的應用程式，以供組織成員使用。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 39b74984ec2f89ed39753963de7fe3ff79577c9e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="publish-a-managed-application-for-internal-consumption"></a>發佈受管理的應用程式，以供內部使用

您可以建立及發佈 Azure [受管理的應用程式](managed-application-overview.md)，以供組織成員使用。 例如，IT 部門可以發佈受管理的應用程式，以確保符合組織標準。 這些受管理的應用程式可透過服務類別目錄 (而不是 Azure Marketplace) 取得。

若要發佈受服務類別目錄管理的應用程式，您必須：

* 建立包含三個必要範本檔案的 .zip 套件。
* 決定需要存取使用者訂用帳戶中的資源群組之使用者、群組或應用程式。
* 建立指向 .zip 套件並要求存取身分識別的受管理應用程式定義。

## <a name="create-a-managed-application-package"></a>建立受管理的應用程式套件

第一個步驟是建立三個必要的範本檔案。 將這三個檔案封裝為 .zip 檔案，並將它上傳至可存取的位置，例如儲存體帳戶。 建立受管理的應用程式定義時，您會傳遞此 .zip 檔案的連結。

* **applianceMainTemplate.json**：此檔案會將佈建作為受管理應用程式一部分的 Azure 資源進行定義。 此範本與一般 Resource Manager 範本不同。 例如，若要透過受管理的應用程式來建立儲存體帳戶，applianceMainTemplate.json 會包含：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **mainTemplate.json**：建立受管理的應用程式時，使用者會部署此範本。 它會定義受管理的應用程式資源，也就是 Microsoft.Solutions/appliances 資源類型。 此檔案包含 applianceMainTemplate.json 中的資源所需之所有參數。

  您會在此範本中設定兩個重要的屬性。 第一個屬性是 **applianceDefinitionId**，該屬性是受管理應用程式定義的識別碼。 您將在本主題稍後建立定義。 設定此值時，您必須決定要用於儲存受管理應用程式定義的訂用帳戶和資源群組。 此外，您必須決定定義的名稱。 識別碼的格式為：

  `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Solutions/applianceDefinitions/<definition-name>`

  第二個屬性是 **managedResourceGroupId**，該屬性是資源群組的識別碼，其中會建立 Azure 資源。 您可以指派值給這個資源群組名稱，或讓使用者提供名稱。 識別碼的格式為：

  `/subscriptions/<subscription-id>/resourceGroups/<resoure-group-name>`。

  下列範例顯示 mainTemplate.json 檔案。 它會為已部署的資源指定資源群組。 定義識別碼是設定為使用名為 **managedApplicationGroup** 的資源群組中名為 **storageApp** 的定義。 您可以變更這些值來使用不同的名稱。 在定義識別碼中提供您自己的訂用帳戶識別碼。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "variables": {
        "managedRGId": "[concat(resourceGroup().id,'-application-resources')]",
        "managedAppName": "[concat('managedStorage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/appliances",
            "name": "[variables('managedAppName')]",
            "apiVersion": "2016-09-01-preview",
            "location": "[resourceGroup().location]",
            "kind": "ServiceCatalog",
            "properties": {
                "managedResourceGroupId": "[variables('managedRGId')]",
                "applianceDefinitionId": "/subscriptions/<subscription-id>/resourceGroups/managedApplicationGroup/providers/Microsoft.Solutions/applianceDefinitions/storageApp",
                "parameters": {
                    "storageAccountNamePrefix": {
                        "value": "[parameters('storageAccountNamePrefix')]"
                    }
                }
            }
        }
    ]
  }
  ```

* **applianceCreateUiDefinition.json**：Azure 入口網站會使用這個檔案，為建立受管理應用程式的使用者產生使用者介面。 您可以定義使用者提供每個參數輸入的方式。 您可以使用諸如下拉式清單、文字方塊、密碼方塊和其他輸入工具等選項。 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。

  下列範例顯示 applianceCreateUiDefinition.json 檔案，可讓使用者透過文字方塊指定儲存體帳戶名稱前置詞。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

所有必要檔案準備就緒後，請將這些檔案封裝為 .zip 檔案。 這三個檔案必須位於 .zip 檔案的根層級。 如果您將這些檔案放在資料夾中，當建立受管理的應用程式時，您會收到錯誤，指出必要的檔案不存在。 將套件上傳至可從中取用它的可存取位置。 本文其餘部分假設 .zip 檔案存在於可公開存取的儲存體 Blob 容器。

## <a name="create-an-azure-active-directory-user-group-or-application"></a>建立 Azure Active Directory 使用者群組或應用程式

第二個步驟是選取要代表客戶管理資源的使用者群組或應用程式。 此使用者群組或應用程式會根據指派的角色，取得受管理資源群組的權限。 角色可以是任何內建的角色型存取控制 (RBAC) 角色，例如擁有者或參與者。 您也可以將管理資源的權限提供給個別使用者，但您通常要將此權限指派給使用者群組。 若要建立新的 Active Directory 使用者群組，請參閱[在 Azure Active Directory 中建立群組和新增成員](../active-directory/active-directory-groups-create-azure-portal.md)。

您需要使用者群組的物件識別碼，以便用於管理資源。 下列範例會示範如何從群組的顯示名稱取得物件識別碼：

```azurecli-interactive
az ad group show --group exampleGroupName
```

範例命令會傳回下列輸出︰

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

若只要擷取物件識別碼，請使用：

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

## <a name="get-the-role-definition-id"></a>取得角色定義識別碼

接下來，您需要的角色定義識別碼，是您想要授與使用者、使用者群組或應用程式存取權的 RBAC 內建角色。 您通常會使用「擁有者」或「參與者」或「讀取者」角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：


```azurecli-interactive
az role definition list --name owner
```

該命令會傳回下列輸出︰

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
         ],
         "notActions": []
        }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

您需要上述範例中的 "name" 屬性值。 您可以透過下列程式碼只擷取該屬性：

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

## <a name="create-the-managed-application-definition"></a>建立受管理的應用程式定義

如果您尚未有可儲存受管理應用程式定義的資源群組，請立即建立一個：

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

現在，建立受管理的應用程式定義資源。

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

上述範例中使用的參數：

* **resource-group**：資源群組的名稱，其中會建立受管理的應用程式定義。
* **lock-level**：放在受管理資源群組的鎖定類型。 它可以避免客戶在此資源群組上執行非預期的作業。 目前唯一支援的鎖定等級是 ReadOnly。 指定 ReadOnly 時，客戶只能讀取受管理資源群組中存在的資源。
* **authorizations**：描述用來授與權限給受管理資源群組的主體識別碼及角色定義識別碼。 它是以 `<principalId>:<roleDefinitionId>` 格式來指定。 這個屬性也可以指定多個值。 如果需要多個值，應該以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 格式來指定。 多個值之間以空格分隔。
* **package-file-uri**：包含範本檔案之受管理應用程式套件的位置，它可以是 Azure 儲存體 Blob。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱[受管理的應用程式概觀](managed-application-overview.md)。
* 如需檔案的範例，請參閱[受管理的應用程式範例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
* 如需將受管理的應用程式發佈到 Azure Marketplace 的資訊，請參閱 [Marketplace 中的 Azure 受管理應用程式](managed-application-author-marketplace.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
* 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。

