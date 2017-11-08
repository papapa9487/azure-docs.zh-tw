---
title: "建立及發佈 Azure 受服務類別目錄管理的應用程式 | Microsoft Docs"
description: "示範如何建立 Azure 受管理的應用程式，以供組織成員使用。"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 6b1d609b7b1b21e80cc7f68f05e16e3c1e8eebba
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>發佈受管理的應用程式，以供內部使用

您可以建立及發佈 Azure [受管理的應用程式](overview.md)，以供組織成員使用。 例如，IT 部門可以發佈受管理的應用程式，以確保符合組織標準。 這些受管理的應用程式可透過服務類別目錄取得，而非 Azure Marketplace。

若要發佈受服務類別目錄管理的應用程式，您必須：

* 建立範本，該範本會定義要與受管理應用程式一起部署的資源。
* 部署受管理的應用程式時，定義入口網站的使用者介面元素。
* 建立包含必要範本檔案的 .zip 套件。
* 決定需要存取使用者訂用帳戶中的資源群組之使用者、群組或應用程式。
* 建立指向 .zip 套件並要求存取身分識別的受管理應用程式定義。

針對這篇文章，受管理的應用程式只包含儲存體帳戶。 它是用來說明發佈受管理應用程式的步驟。 如需完整範例，請參閱[適用於 Azure 受管理的應用程式之範例專案](sample-projects.md)。

## <a name="create-the-resource-template"></a>建立資源範本

每個受管理的應用程式定義包含名為 **mainTemplate.json** 的檔案。 您可以在其中定義要佈建的 Azure 資源。 此範本與一般 Resource Manager 範本不同。

建立名為 **mainTemplate.json** 的檔案。 名稱有區分大小寫。

將下列 JSON 新增至檔案。 它會定義用來建立儲存體帳戶的參數，並且指定儲存體帳戶的屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

儲存 mainTemplate.json 檔案。

## <a name="create-the-user-interface-definition"></a>建立使用者介面定義

Azure 入口網站會使用 **createUiDefinition.json** 檔案，為建立受管理應用程式的使用者產生使用者介面。 您可以定義使用者提供每個參數輸入的方式。 您可以使用諸如下拉式清單、文字方塊、密碼方塊和其他輸入工具等選項。 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。

建立名為 **createUiDefinition.json** 的檔案。 名稱有區分大小寫。

將下列 JSON 新增至檔案。

```json
{
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

儲存 createUIDefinition.json 檔案。

## <a name="package-the-files"></a>封裝檔案

將兩個檔案新增至名為 app.zip 的 .zip 檔案。 這兩個檔案必須位於 .zip 檔案的根層級。 如果您將這些檔案放在資料夾中，當建立受管理的應用程式時，您會收到錯誤，指出必要的檔案不存在。 

將套件上傳至可從中取用它的可存取位置。 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>建立受管理的應用程式定義

### <a name="create-an-azure-active-directory-user-group-or-application"></a>建立 Azure Active Directory 使用者群組或應用程式

下一個步驟是選取要代表客戶管理資源的使用者群組或應用程式。 此使用者群組或應用程式會根據指派的角色，取得受管理資源群組的權限。 角色可以是任何內建的角色型存取控制 (RBAC) 角色，例如擁有者或參與者。 您也可以將管理資源的權限提供給個別使用者，但您通常要將此權限指派給使用者群組。 若要建立新的 Active Directory 使用者群組，請參閱[在 Azure Active Directory 中建立群組和新增成員](../active-directory/active-directory-groups-create-azure-portal.md)。

您需要使用者群組的物件識別碼，以便用於管理資源。 

![取得群組識別碼](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>取得角色定義識別碼

接下來，您需要的角色定義識別碼，是您想要授與使用者、使用者群組或應用程式存取權的 RBAC 內建角色。 您通常會使用「擁有者」或「參與者」或「讀取者」角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>建立受管理的應用程式定義

如果您尚未有可儲存受管理應用程式定義的資源群組，請立即建立一個：

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

現在，建立受管理的應用程式定義資源。

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>使用入口網站建立受管理的應用程式

現在，讓我們使用入口網站來部署受管理的應用程式。 您會在套件中看到您建立的使用者介面。

1. 移至 Azure 入口網站。 選取 [+ 新增]，並搜尋**服務類別目錄**。

   ![服務類別目錄](./media/publish-service-catalog-app/select-new.png)

1. 選取 [服務類別目錄受管理的應用程式]。

   ![選取服務類別目錄](./media/publish-service-catalog-app/select-service-catalog.png)

1. 選取 [ **建立**]。

   ![選取 [建立]](./media/publish-service-catalog-app/select-create.png)

1. 從可用的解決方案清單中，尋找並選取您想要建立的受管理應用程式。 選取 [ **建立**]。

   ![尋找受管理的應用程式](./media/publish-service-catalog-app/find-application.png)

1. 提供受管理應用程式所需的基本資訊。 指定要包含受管理應用程式的訂用帳戶和新資源群組。 選取 [美國中西部] 為位置。 完成時，選取 [確認]。

   ![提供受管理的應用程式參數](./media/publish-service-catalog-app/provide-basics.png)

1. 提供專屬於受管理應用程式中資源的值。 完成時，選取 [確認]。

   ![提供資源參數](./media/publish-service-catalog-app/provide-resource-values.png)

1. 範本會驗證您所提供的值。 如果驗證成功，請選取 [確定] 以開始部署。

   ![驗證受管理的應用程式](./media/publish-service-catalog-app/validate.png)

在部署完成之後，受管理的應用程式會存在名為 applicationGroup 的資源群組中。 儲存體帳戶會存在名為 applicationGroup (加上雜湊字串值) 的資源群組中。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱[受管理的應用程式概觀](overview.md)。
* 如需範例專案，請參閱[適用於 Azure 受管理的應用程式之範例專案](sample-projects.md)。
* 如需將受管理的應用程式發佈到 Azure Marketplace 的資訊，請參閱 [Marketplace 中的 Azure 受管理應用程式](publish-marketplace-app.md)。
* 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。