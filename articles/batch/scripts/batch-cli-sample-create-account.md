---
title: "Azure CLI 指令碼範例 - 建立 Batch 帳戶 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立 Batch 帳戶"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: fd2f4682a04c557b69bbfce115f41c54a96d462c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>使用 Azure CLI 建立 Batch 帳戶

此指令碼會建立 Azure Batch 帳戶，並顯示各種可以查詢和更新的帳戶屬性。

## <a name="prerequisites"></a>必要條件

如果您尚未安裝 Azure CLI，請使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中所提供的指示來安裝 Azure CLI。

## <a name="batch-account-sample-script"></a>Batch 帳戶範例指令碼

當您建立 Batch 帳戶時，根據預設，其計算節點是由 Batch 服務在內部指派。 配置的計算節點將受個別的核心配額限制，帳戶可透過共用金鑰認證或 Azure Active Dirctory 權杖驗證。

[!code-azurecli[主要](../../../cli_scripts/batch/create-account/create-account.sh "建立帳戶")]

## <a name="batch-account-using-user-subscription-sample-script"></a>使用使用者訂閱範例指令碼的 Batch 帳戶

您也可以選擇讓 Batch 在您自己的 Azure 訂用帳戶中建立其計算節點。
將計算節點配置到您的訂用帳戶的帳戶必須透過 Azure Active Directory 權杖驗證，配置的計算節點將計入您的訂用帳戶配額。 若要在此模式建立帳戶，使用者必須在建立帳戶時指定 Key Vault 參考。

[!code-azurecli[主要](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "使用使用者訂用帳戶建立帳戶")]

## <a name="clean-up-deployment"></a>清除部署

執行上述範例指令碼之後，請執行下列命令以移除資源群組和所有相關資源 (包括 Batch 帳戶、Azure 儲存體帳戶和 Azure Key Vault)。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Batch 帳戶和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_create) | 建立 Batch 帳戶。  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_set) | 更新 Batch 帳戶的屬性。  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_show) | 擷取指定的 Batch 帳戶的詳細資料。  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#az_batch_account_keys_list) | 擷取指定的 Batch 帳戶的存取金鑰。  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | 建立儲存體帳戶。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | 建立金鑰保存庫。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 更新指定的 Key Vault 的安全性原則。 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。

