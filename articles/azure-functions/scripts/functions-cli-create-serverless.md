---
title: "Azure CLI 指令碼範例 - 建立無伺服器也可執行的函式應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立無伺服器也可執行的函式應用程式"
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: d3244fe97464acdea162ef65ba1a473dcdf0219d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>建立無伺服器也可執行的函式應用程式

此範例指令碼會建立 Azure 函式應用程式，這是您的函式容器。 將會使用[取用方案](../functions-scale.md#consumption-plan)建立函數應用程式，這適用於事件驅動的無伺服器工作負載。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

此指令碼會使用[取用方案](../functions-scale.md#consumption-plan)建立 Azure 函數應用程式。

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "在取用方案上建立 Azure 函式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

下表中的每個命令都會連結至命令特定的文件。 此指令碼會使用下列命令：

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account#create) | 建立 Azure 儲存體帳戶。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | 建立 Azure 函式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件](../functions-cli-samples.md)中找到其他 Azure Functions CLI 指令碼範例。

