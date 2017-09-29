---
title: "Azure 快速入門 - 使用 Azure CLI 建立儲存體帳戶 | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 建立新的儲存體帳戶。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-storage-account-using-the-azure-cli"></a>使用 Azure CLI 建立儲存體帳戶

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門詳細說明如何使用 Azure CLI 建立 Azure 儲存體帳戶。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#create) 命令建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 此範例會在 *eastus* 區域中建立名為 *myResourceGroup* 的資源群組。

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

如果您不確定要針對 `--location` 參數指定哪一個區域，您可以使用 [az account list-locations](/cli/azure/account#list) 命令擷取訂用帳戶所支援的區域清單。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>建立一般用途的標準儲存體帳戶

有幾種儲存體帳戶類型適用於不同的使用案例，其中每一種類型都支援一或多個儲存體服務 (Blob、檔案、表格或佇列)。 下表詳細說明可用的儲存體帳戶類型。

|**儲存體帳戶的類型**|**一般用途：標準**|**一般用途：進階**|**Blob 儲存體、經常性存取和非經常性存取層**|
|-----|-----|-----|-----|
|**支援的服務**| Blob、檔案、表格、佇列服務 | Blob 服務 | Blob 服務|
|**支援的 Blob 類型**|區塊 Blob、分頁 Blob、附加 Blob | 分頁 Blob | 區塊 Blob 和附加 Blob|

使用 [az storage account create](/cli/azure/storage/account#create) 命令建立一般用途的標準儲存體帳戶。

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包括在本快速入門中所建立的儲存體帳戶)，請使用 [az group delete](/cli/azure/group#delete) 命令刪除資源群組。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立資源群組和一般用途的標準儲存體帳戶。 若要了解如何在儲存體帳戶之間傳輸資料，請繼續進行 Blob 儲存體快速入門。

> [!div class="nextstepaction"]
> [使用 Azure CLI 在 Azure Blob 儲存體之間傳送物件](../blobs/storage-quickstart-blobs-cli.md)
