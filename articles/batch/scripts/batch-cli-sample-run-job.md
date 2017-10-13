---
title: "Azure CLI 指令碼範例 - 使用 Batch 執行工作 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Batch 執行工作"
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
ms.openlocfilehash: 73d93622d418359be421e043d0af4e4befc6f4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>在 Azure Batch 上使用 Azure CLI 執行工作

此指令碼會建立 Batch 工作，並將一系列作業加入至工作。 它也示範如何監視工作和其作業。 最後，它會示範如何有效率地查詢 Batch 服務，以取得作業工作的相關資訊。

## <a name="prerequisites"></a>必要條件

- 如果您尚未安裝 Azure CLI，請使用 [Azure CLI 安裝指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中所提供的指示來安裝 Azure CLI。
- 建立 Batch 帳戶 (如果您還沒有帳戶的話)。 如需用以建立帳戶的指令碼範例，請參閱[使用 Azure CLI 建立 Batch 帳戶](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account)。
- 將應用程式設定為從啟動工作來執行 (如果您尚未設定)。 如需指令碼範例以建立應用程式並將應用程式套件上傳至 Azure，請參閱[使用 Azure CLI 將應用程式新增至 Azure Batch](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application)。
- 設定用來執行作業的集區。 如需指令碼範例以使用雲端服務組態或虛擬機器組態來建立集區，請參閱[使用 Azure CLI 管理 Azure Batch 集區](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-job"></a>清除工作

執行上述範例指令碼之後，請執行下列命令以移除工作和其所有作業。 請注意，您必須另外刪除集區。 如需建立和刪除集區的詳細資訊，請參閱[使用 Azure CLI 管理 Azure Batch 集區](./batch-cli-sample-manage-pool.md)。

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立 Batch 工作和作業。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | 對 Batch 帳戶進行驗證。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | 建立 Batch 工作。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | 更新 Batch 工作的屬性。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | 擷取指定的 Batch 工作的詳細資料。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | 將作業加入至指定的 Batch 工作。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | 從指定的 Batch 工作擷取作業的詳細資料。  |
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_list) | 列出指定作業的相關工作。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Batch CLI 文件](../batch-cli-samples.md)中找到其他的 Batch CLI 指令碼範例。
