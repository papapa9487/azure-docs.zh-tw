---
title: "Azure CLI 指令碼範例 - 在 Batch 中加入應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在 Batch 中加入應用程式"
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
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>使用 Azure CLI 將應用程式加入 Azure Batch

此指令碼示範如何設定應用程式，以搭配 Azure Batch 集區或作業使用。 設定應用程式，將可執行檔及任何相依性封裝到 .zip 檔案。 在此範例中，可執行的 zip 檔案稱為 'my-application-exe.zip'。

## <a name="prerequisites"></a>必要條件

- 如果您尚未安裝 Azure CLI，請使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中所提供的指示來安裝 Azure CLI。
- 建立 Batch 帳戶 (如果您還沒有帳戶的話)。 如需用以建立帳戶的指令碼範例，請參閱[使用 Azure CLI 建立 Batch 帳戶](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-application"></a>清除應用程式

執行上述範例指令碼之後，請執行下列命令以移除應用程式和其所有已上傳的應用程式封裝。

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>指令碼說明

這個指令碼使用下列命令來建立應用程式和上傳應用程式封裝。
下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | 建立應用程式。  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | 更新應用程式的屬性。  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | 將應用程式封裝加入指定的應用程式。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。
