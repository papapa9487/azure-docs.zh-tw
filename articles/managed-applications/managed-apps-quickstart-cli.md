---
title: "使用 Azure CLI 建立 Azure 受管理的應用程式 | Microsoft Docs"
description: "示範如何建立 Azure 受管理的應用程式，以供組織成員使用。"
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/20/2017
ms.author: tomfitz
ms.openlocfilehash: e104778db445d078ffca7a9ddb9d28fc84a93c81
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>使用 Azure CLI 建立及部署 Azure 受管理的應用程式

本文將簡介如何使用受管理的應用程式。 您可以為您組織中的使用者，將受管理的應用程式定義新增至內部目錄。 然後，將受管理的應用程式部署至您的訂用帳戶。 為簡化指示，我們已為受管理的應用程式建置檔案。 有一個檔案會定義解決方案的基礎結構。 第二個檔案會定義使用者介面，以透過入口網站部署受管理的應用程式。 這些檔案皆可透過 GitHub 取得。 您可以從[建立服務目錄應用程式](publish-service-catalog-app.md)教學課程中，了解如何建置這些檔案。

當您完成時，您會有三個資源群組，其中包含受管理應用程式的不同部分。

| 資源群組 | Contains | 說明 |
| -------------- | -------- | ----------- |
| appDefinitionGroup | 受管理的應用程式定義。 | 發行者會建立此資源群組和受管理的應用程式定義。 只要有受管理的應用程式定義存取權，任何人都可以對其進行部署。 |
| applicationGroup | 受管理的應用程式執行個體。 | 取用者會建立此資源群組和受管理的應用程式執行個體。 取用者可以透過此執行個體更新受管理的應用程式。 |
| infrastructureGroup | 受管理的應用程式資源。 | 建立受管理的應用程式時，系統會自動建立此資源群組。 發行者可以存取此資源群組，以管理應用程式。 取用者具有資源群組的有限存取權。 |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>建立定義的資源群組

資源群組中存在受管理的應用程式定義。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

若要建立資源群組，請使用下列命令：

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>建立受管理的應用程式定義

定義受管理的應用程式時，您會代表取用者選取管理資源的使用者、群組或應用程式。 此身分識別會根據指派的角色，取得受管理資源群組的權限。 一般來說，您會建立 Azure Active Directory 群組來管理資源。 不過，在本文中您會使用自己的身分識別。

若要取得您身分識別的物件識別碼，請在下列命令中提供您的使用者主體名稱：

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

接下來，您需要的角色定義識別碼，是您想要授與使用者存取權的 RBAC 內建角色。 下列命令會顯示如何取得「擁有者」角色的角色定義識別碼：

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

現在，建立受管理的應用程式定義資源。 受管理的應用程式只包含儲存體帳戶。

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

命令完成時，您的資源群組中會有受管理的應用程式定義。 

上述範例中使用的部分參數：

* **resource-group**：資源群組的名稱，其中會建立受管理的應用程式定義。
* **lock-level**：放在受管理資源群組的鎖定類型。 它可以避免客戶在此資源群組上執行非預期的作業。 目前唯一支援的鎖定等級是 ReadOnly。 指定 ReadOnly 時，客戶只能讀取受管理資源群組中存在的資源。
* **authorizations**：描述用來授與權限給受管理資源群組的主體識別碼及角色定義識別碼。 它是以 `<principalId>:<roleDefinitionId>` 格式來指定。 這個屬性也可以指定多個值。 如果需要多個值，應該以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 格式來指定。 多個值之間以空格分隔。
* **package-file-uri**：該位置有包含必要檔案的.zip 套件。 套件中最少會包含 **mainTemplate.json** 和 **createUiDefinition.json** 檔案。 **mainTemplate.json**會將佈建作為受管理應用程式一部分的 Azure 資源進行定義。 此範本與一般 Resource Manager 範本不同。 **createUiDefinition.json**會透過 入口網站為建立受管理應用程式的使用者產生使用者介面。

## <a name="create-resource-group-for-managed-application"></a>為受管理的應用程式建立資源群組

此時，您已經準備好要部署受管理的應用程式。 

若要包含部署的受管理應用程式，您需要資源群組。 使用 **westcentralus** 作為位置。

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>部署受管理的應用程式

您會使用下列命令來部署應用程式：

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"<your-prefix>\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

上述範例中使用的部分參數：

* **managedapp-definition-id**：您稍早在本文中建立的定義識別碼。
* **managed-rg-id**：資源群組的識別碼，資源群組中的資源與受管理的應用程式相關聯。 此命令會建立此資源群組。 此識別碼**必然不會在執行命令前就存在**。 此資源群組是由發行者所管理。 
* **resource-group**：受管理應用程式資源建立之資源群組。
* **parameters**：與受管理應用程式相關聯的資源會需要這些參數。

順利完成部署之後，您會看到在 applicationGroup 中建立了受管理的應用程式。 storageAccount 資源會建立於 infrastructureGroup 中。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱[受管理的應用程式概觀](overview.md)。
* 如需檔案的範例，請參閱[受管理的應用程式範例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 如需將受管理的應用程式發佈到 Azure Marketplace 的資訊，請參閱 [建立 Marketplace 應用程式](publish-marketplace-app.md)。
* 若要了解如何建立受管理應用程式的 UI 定義檔案，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
