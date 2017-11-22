---
title: "金鑰保存庫密碼與 Azure Resource Manager 範本 | Microsoft Docs"
description: "示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署期間使用 Azure Key Vault 以傳遞安全的參數值

當您需要在部署期間，傳送安全值 (例如密碼) 做為參數時，您可以從 [Azure Key Vault](../key-vault/key-vault-whatis.md) 擷取值。 您可以藉由參考金鑰保存庫和參數檔案中的密碼來擷取值。 您只參考其金鑰保存庫識別碼，因此該值絕不會公開。 您不需要在每次部署資源時手動輸入密碼的值。 金鑰保存庫可以存在於與您部署的資源群組的不同訂用帳戶中。 當參考金鑰保存庫時，您納入訂用帳戶識別碼。

建立金鑰保存庫時，將 *enabledForTemplateDeployment* 屬性設定為 *true*。 將此值設定為 true，即表示您允許在部署期間從 Resource Manager 範本存取。

## <a name="deploy-a-key-vault-and-secret"></a>部署金鑰保存庫和密碼

若要建立金鑰保存庫與密碼，請使用 Azure CLI 或 PowerShell。 請注意，金鑰保存庫會針對範本部署啟用。 

對於 Azure CLI，請使用：

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

對於 PowerShell，請使用：

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>啟用密碼的存取權

不論您使用新的金鑰保存庫或現有的金鑰保存庫，請確定使用者部署的範本可以存取密碼。 部署範本以參考密碼的使用者必須具有金鑰保存庫的 `Microsoft.KeyVault/vaults/deploy/action` 權限。 [擁有者](../active-directory/role-based-access-built-in-roles.md#owner)和[參與者](../active-directory/role-based-access-built-in-roles.md#contributor)角色皆可授與此權限。

## <a name="reference-a-secret-with-static-id"></a>使用靜態識別碼參考密碼

接收金鑰保存庫密碼的範本與其他任何範本都很像。 這是因為**您參考了參數檔案中的金鑰保存庫，而非範本。** 例如，下列範本部署了包含系統管理員密碼的 SQL 資料庫。 密碼參數會設定為安全字串。 但是，範本並未指定該值來自何處。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

現在，請為前述範本建立參數檔案。 在參數檔案中，指定符合範本中參數名稱的參數。 針對參數值，參考來自金鑰保存庫的密碼。 您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼。 在下列範例中，金鑰保存庫密碼必須已經存在，而且您要針對其資源識別碼提供靜態值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

現在可部署範本，並在參數檔案中傳遞。 對於 Azure CLI，請使用：

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

對於 PowerShell，請使用：

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>使用動態識別碼參考密碼

上一節已說明如何傳遞金鑰保存庫密碼的靜態資源識別碼。 不過，在某些情況下，您需要參考會隨目前的部署而變化的金鑰保存庫密碼。 在該情況下，您將無法在參數檔中硬式編碼資源識別碼。 不幸的是，因為參數檔中不允許使用範本運算式，因此您無法在參數檔中以動態方式產生資源識別碼。

若要以動態方式產生金鑰保存庫密碼的資源識別碼，您必須將需要密碼的資源移動到巢狀範本。 在主要範本中，您必須新增巢狀範本，並傳入包含動態產生的資源識別碼的參數。 巢狀的範本必須可透過外部 URI 使用。 本文接下來的部分會假設您已將上述範本新增至儲存體帳戶，且可透過 URI- `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json` 使用。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

請部署上述範本，並提供參數值。

## <a name="next-steps"></a>後續步驟
* 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)。
* 如需參考金鑰密碼的完整範例，請參閱 [金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
