---
title: "使用 Azure 受管理的應用程式 | Microsoft Docs"
description: "描述客戶如何從已發佈的檔案建立 Azure 受管理的應用程式。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-internal-managed-application"></a>使用內部受管理的應用程式

您可以使用 Azure [受管理的應用程式](managed-application-overview.md)，以供組織成員使用。 例如，您可以從 IT 部門選取受管理的應用程式，以確保符合組織標準。 這些受管理的應用程式要透過 Service Catalog 取得，而非 Azure Marketplace。

在繼續本文之前，您必須先為您的訂用帳戶在 Service Catalog 中提供受管理的應用程式。 如果組織中有人尚未建立受管理的應用程式，請參閱[發佈受管理的應用程式以供內部使用](managed-application-publishing.md)。

目前，您可以使用 Azure CLI 或 Azure 入口網站來取用受管理的應用程式。

## <a name="create-the-managed-application-by-using-the-portal"></a>使用入口網站建立受管理的應用程式

若要透過入口網站部署受管理的應用程式，請依照下列步驟：

1. 移至 Azure 入口網站。 搜尋 **Service Catalog 受管理的應用程式**。

   ![Service Catalog 受管理的應用程式](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. 從可用的解決方案清單中選取您想要建立的受管理應用程式。 選取 [ **建立**]。

   ![受管理的應用程式選取](./media/managed-application-consumption/select-offer.png)

1. 提供佈建資源所需的參數值。 選取 [美國中西部] 為位置。 選取 [確定] 。

   ![受管理的應用程式參數](./media/managed-application-consumption/input-parameters.png)

1. 範本會驗證您所提供的值。 如果驗證成功，請選取 [確定] 以開始部署。

   ![受管理的應用程式驗證](./media/managed-application-consumption/validation.png)

部署完成之後，範本中定義的適當資源會佈建在您提供的受管理資源群組中。

## <a name="create-the-managed-application-by-using-azure-cli"></a>使用 Azure CLI 建立受管理的應用程式

有兩種方式可以使用 Azure CLI 來建立受管理的應用程式：

* 使用命令以建立受管理的應用程式。
* 使用一般範本部署命令。

### <a name="use-the-template-deployment-command"></a>使用範本部署命令

部署廠商所建立的 applianceMainTemplate.json 檔案。

然後建立兩個資源群組。 第一個資源群組是建立受管理應用程式資源的位置：Microsoft.Solutions/appliances。 第二個資源群組包含 mainTemplate.json 中定義的所有資源。 此資源群組是由 ISV 所管理。

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> 使用 `westcentralus` 作為資源群組的位置。
>

若要在 mainResourceGroup 中部署 applianceMainTemplate.json，請使用下列命令︰

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

在上述範本執行後，它會提示您提供範本中所定義的參數值。 除了在範本中佈建資源所需的參數之外，您需要兩個金鑰參數值︰

- **managedResourceGroupId**：資源群組的識別碼包含 applianceMainTemplate.json 中所定義的資源。 識別碼的格式為 `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`。 在上述範例中，則為 `managedResourceGroup` 的識別碼。
- **applianceDefinitionId**：受管理應用程式定義資源的識別碼。 這個值是由 ISV 所提供。

> [!NOTE]
> 發行者必須授與存取權給包含受管理應用程式定義的資源群組。 定義資源將在發行者訂用帳戶中建立。 因此，客戶租用戶中的使用者、使用者群組或應用程式需要這項資源的讀取權限。

順利完成部署之後，您會看到在 mainResourceGroup 中建立了受管理應用程式。 storageAccount 資源會建立於 managedResourceGroup 中。

### <a name="use-the-create-command"></a>使用 create 命令

您可以使用 `az managedapp create` 命令，從受管理的應用程式定義建立受管理的應用程式。

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**：在先前步驟中建立之受管理應用程式定義的資源識別碼。 若要取得這個識別碼，請執行下列命令：

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  此命令會傳回受管理的應用程式定義。 您需要 ID 屬性的值。

* **managed-rg-id**：資源群組的名稱包含 applianceMainTemplate.json 中所定義的資源。 這個資源群組是受管理的資源群組。 它是由發行者進行管理。 如果它不存在，則會為您建立。
* **resource-group**：受管理應用程式資源建立之資源群組。 Microsoft.Solutions/appliance 資源存在於此資源群組。
* **parameters**：applianceMainTemplate.json 中所定義之資源所需的參數。

## <a name="known-issues"></a>已知問題

此預覽版本包含下列問題︰

* 受管理的應用程式建立期間會出現 500 內部伺服器錯誤。 如果您遇到此問題，有可能是間歇性問題。 重試作業。
* 受管理的資源群組需要新的資源群組。 如果您使用現有的資源群組，則部署會失敗。
* 包含 Microsoft.Solutions/appliances 資源的資源群組必須建立於 **westcentralus** 位置。

## <a name="next-steps"></a>後續步驟

* 如需受管理應用程式的簡介，請參閱[受管理的應用程式概觀](managed-application-overview.md)。
* 如需發佈 Service Catalog 受管理應用程式的資訊，請參閱[建立和發佈 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 如需將受管理的應用程式發佈到 Azure Marketplace 的資訊，請參閱 [Marketplace 中的 Azure 受管理應用程式](managed-application-author-marketplace.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
