---
title: "在 Azure 容器執行個體中掛接 Azure 檔案磁碟區"
description: "了解如何掛接 Azure 檔案磁碟區來保存 Azure 容器執行個體的狀態"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 4248a3769ba8a0fb067b3904d55d487fe67e5778
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---

# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>使用 Azure 容器執行個體來掛接 Azure 檔案共用

根據預設，Azure 容器執行個體都是無狀態的。 如果容器損毀或停止，其所有狀態都會遺失。 若要在容器超過存留期後保存其狀態，您必須從外部存放區掛接磁碟區。 本文說明如何掛接 Azure 檔案共用以便與 Azure 容器執行個體搭配使用。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在搭配使用 Azure 檔案共用與 Azure 容器執行個體前，您必須先建立 Azure 檔案共用。 請執行下列指令碼來建立儲存體帳戶，以裝載檔案共用和共用本身。 請注意，儲存體帳戶名稱必須是全域唯一的，因此指令碼會在基底字串中加上隨機值。

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>取得儲存體帳戶的存取詳細資料

若要在 Azure 容器執行個體中掛接 Azure 檔案共用來作為磁碟區，您需要三個值：儲存體帳戶名稱、共用名稱和儲存體存取金鑰。 

如果您使用上述指令碼，所建立的儲存體帳戶名稱尾端會加上隨機值。 若要查詢最終字串 (包括隨機部分)，請使用下列命令：

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group myResourceGroup --query "[?contains(name,'mystorageaccount')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

我們已知道共用名稱 (在上述指令碼中是 acishare)，因此只差儲存體帳戶金鑰，此金鑰可以使用下列命令來找到：

```azurecli-interactive
$STORAGE_KEY=$(az storage account keys list --resource-group myResourceGroup --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>使用 Azure 金鑰保存庫來儲存儲存體帳戶的存取詳細資料

儲存體帳戶金鑰可保護資料的存取，因此建議您將金鑰儲存在 Azure 金鑰保存庫中。 

使用 Azure CLI 來建立金鑰保存庫：

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g myResourceGroup
```

在部署時，`enabled-for-template-deployment` 參數可讓 Azure Resource Manager 從金鑰保存庫提取祕密。

將儲存體帳戶金鑰儲存為金鑰保存庫中的新祕密：

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>掛接磁碟區

在容器中掛接 Azure 檔案共用來作為磁碟區的程序需要兩個步驟。 首先，您要提供共用的詳細資料，以在定義容器群組時輸入，然後您要指定您想要如何在群組的一或多個容器內掛接磁碟區。

若要定義想要可供掛接的磁碟區，請將 `volumes` 陣列新增到 Azure Resource Manager 範本中的容器群組定義，然後在個別容器的定義中參考這些磁碟區。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }  
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

範本中會納入儲存體帳戶名稱和金鑰來作為參數，並可在不同的參數檔案中提供。 若要填入參數檔案，您需要三個值：儲存體帳戶名稱、Azure 金鑰保存庫的資源識別碼，以及您用來儲存儲存體金鑰的金鑰保存庫祕密名稱。 如果您有遵循上述步驟，您可以使用下列指令碼取得這些值：

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

將這些值插入參數檔案中：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },    
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>部署容器及管理檔案

透過定義的範本，您可以使用 Azure CLI 建立容器並掛接其磁碟區。 假設範本檔案的名稱為 azuredeploy.json，參數檔案的名稱為 azuredeploy.parameters.json，則命令列如下：

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

一旦啟動容器，您即可使用透過 **seanmckenna/aci-hellofiles** 映像部署的簡單 Web 應用程式，以管理位於指定掛接路徑之 Azure 檔案共用中的檔案。 透過下列操作取得 Web 應用程式的 IP 位址：

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

您可以使用像 [Microsoft Azure 儲存體總管](http://storageexplorer.com)這類工具擷取及檢查寫入至檔案共用的檔案。

>[!NOTE]
> 若要深入了解如何使用 Azure Resource Manager 範本、參數檔案以及使用 Azure CLI 進行部署，請參閱[使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 容器執行個體部署您的第一個容器[快速入門](container-instances-quickstart.md)
- 了解 [Azure 容器執行個體與容器 Orchestrator 之間的關聯性](container-instances-orchestrator-relationship.md)

