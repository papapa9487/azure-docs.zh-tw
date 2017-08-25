---
title: "建立私人 Docker 容器登錄 - Azure CLI | Microsoft Docs"
description: "使用 Azure CLI 2.0 開始建立及管理私人 Docker 容器登錄"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立私人 Docker 容器登錄
在 Linux、Mac 或 Windows 電腦上，使用 [Azure CLI 2.0](https://github.com/Azure/azure-cli) 中的命令建立容器登錄及管理其設定。 您也可以使用 [Azure 入口網站](container-registry-get-started-portal.md)或以程式設計方式用容器登錄 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 來建立及管理容器登錄。


* 如需相關背景和概念，請參閱[概觀](container-registry-intro.md)
* 如需容器登錄庫 CLI 命令 (`az acr` 命令) 的說明，請傳遞 `-h` 參數至任何命令。


## <a name="prerequisites"></a>必要條件
* **Azure CLI 2.0**若要安裝並開始使用 CLI 2.0，請參閱[安裝指示](/cli/azure/install-azure-cli)。 執行 `az login`登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[開始使用 CLI 2.0](/cli/azure/get-started-with-azure-cli)。
* **資源群組**：先建立[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)再建立容器登錄，或使用現有的資源群組。 請確定資源群組是位於[可使用](https://azure.microsoft.com/regions/services/)容器登錄庫服務的位置。 若要使用 CLI 2.0 建立資源群組，請參閱 [CLI 2.0 參考](/cli/azure/group)。
* **儲存體帳戶** (選用)：建立標準 Azure [儲存體帳戶](../storage/common/storage-introduction.md)以支援相同位置中的容器登錄。 如果您以 `az acr create` 建立登錄庫時沒有指定儲存體帳戶，此命令會為您建立一個儲存體帳戶。 若要使用 CLI 2.0 建立儲存體帳戶，請參閱 [CLI 2.0 參考](/cli/azure/storage/account)。 目前不支援進階儲存體。
* **服務主體** (選用)：當您使用 CLI 建立登錄，依預設不會做存取設定。 您可以將現有的 Azure Active Directory 服務主體指派至登錄庫 (或建立並指派新的)，或是啟用登錄庫的管理員使用者帳戶，根據您的需求而定。 請參閱本文稍後的章節。 如需登錄庫存取權的詳細資訊，請參閱[驗證容器登錄庫](container-registry-authentication.md)。

## <a name="create-a-container-registry"></a>建立容器登錄庫
執行 `az acr create` 命令建立容器登錄庫。

> [!TIP]
> 當您建立登錄庫時，請指定含有字母和數字的全域唯一最上層網域名稱。 範例中的登錄庫名稱是 `myRegistry1`，請換成您自己的唯一名稱。
>
>

下列命令使用最少的參數的 `myResourceGroup` 資源群組中建立容器登錄 `myRegistry1`，並使用基本 sku：

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* `--storage-account-name` 為選擇性。 如果未指定，則會在指定的資源群組中使用由登錄名稱和時間戳記所組成的名稱建立儲存體帳戶。

建立登錄時，輸出大致如下：

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


請特別注意︰

* `id` - 登錄庫在訂用帳戶中的識別碼，如果您要指派服務主體，則需要此識別碼。
* `loginServer` - 您指定用來[登入登錄庫](container-registry-authentication.md)的完整名稱。 在此範例中，此名稱為 `myregistry1.exp.azurecr.io` (全部小寫)。

## <a name="assign-a-service-principal"></a>指派服務主體
使用 CLI 2.0 的命令將 Azure Active Directory 服務主體指派到登錄。 這些範例中的服務主體是指派至擁有者角色，但如果您想要，可以指派至[其他角色](../active-directory/role-based-access-control-configure.md)。

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>建立服務主體並指派對登錄庫的存取權
在下列命令中，將「對登錄庫的擁有者角色存取權」指派給新服務主體；此處登錄庫的識別碼是以 `--scopes` 參數傳遞。 以 `--password` 參數指定強式密碼。

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>指派現有的服務主體
如果您已經有服務主體，想將對登錄庫的擁有者角色存取權指派給它，請執行類似下列範例的命令。 您使用 `--assignee` 參數傳遞服務主體應用程式識別碼：

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>管理管理員認證
系統會自動建立每個容器登錄庫的管理員帳戶，並預設停用此帳戶。 下列範例示範用 `az acr` CLI 命令來管理容器登錄庫的管理員認證。

### <a name="obtain-admin-user-credentials"></a>取得管理員使用者認證
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>啟用現有登錄庫的管理員使用者
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>停用現有登錄庫的管理員使用者
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>列出映像和標籤
使用 `az acr` CLI 命令來查詢儲存機制中的映像和標籤。

> [!NOTE]
> 目前，容器登錄庫不支援用 `docker search` 命令查詢映像和標籤。


### <a name="list-repositories"></a>列出儲存機制
下列範例會以 JSON (JavaScript 物件標記法) 格式列出登錄庫中的儲存機制︰

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>列出標籤
下列範例會以 JSON 格式列出 **samples/nginx** 儲存機制上的標籤：

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>後續步驟
* [使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)

