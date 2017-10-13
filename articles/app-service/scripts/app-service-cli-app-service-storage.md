---
title: "Azure CLI 指令碼範例 - 將 Web 應用程式連接至儲存體帳戶 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 將 Web 應用程式連接至儲存體帳戶"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: a07c2ae64be2081a2dba172ada810b76bfd7dfd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-a-web-app-to-a-storage-account"></a>將 Web 應用程式連接至儲存體帳戶

在此案例中，您會學習如何建立 Azure 儲存體帳戶和 Azure Web 應用程式。 然後，您會使用應用程式設定將儲存體帳戶連結到 Web 應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、Web 應用程式、儲存體帳戶和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 建立 App Service 方案。 這就像是 Azure Web 應用程式的伺服器陣列。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | 建立 Azure Web 應用程式。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | 建立儲存體帳戶。 這是靜態資產的儲存位置。 |
| [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_show_connection_string) | |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | 建立或更新 Azure Web 應用程式的應用程式設定。 應用程式設定會顯示為應用程式的環境變數。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
