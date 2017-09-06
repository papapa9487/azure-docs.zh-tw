---
title: "Azure CLI 指令碼範例 - 將自訂網域對應至函式應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在 Azure 中將自訂網域對應至函式應用程式。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 5a17830ad3b879382b7e82d435f1286c7ecdf7c5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>將自訂網域對應至函式應用程式

此範例指令碼會建立函式應用程式及相關資源，然後將 `www.<yourdomain>` 與其對應。 若要對應至自訂網域，必須在 App Service 方案中建立函式應用程式，而不是在取用方案中建立。 Azure Functions 只支援使用 A 記錄對應自訂網域。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，您必須使用 Azure CLI 2.0 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "將自訂網域對應至函式應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 建立函式應用程式所需的儲存體帳戶。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立對應自訂網域所需的 App Service 方案。 |
| [az functionapp create]() | 建立函式應用程式。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 將自訂網域對應至函式應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure Functions 文件]()中找到其他 Functions CLI 指令碼範例。

