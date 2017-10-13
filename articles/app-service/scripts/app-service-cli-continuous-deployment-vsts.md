---
title: "Azure CLI 指令碼範例 - 建立可從 Visual Studio Team Services 連續部署的 Web 應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 建立可從 Visual Studio Team Services 連續部署的 Web 應用程式"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 6c10b7654c02fae631e34572f12482928b39ceb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>建立可從 Visual Studio Team Services 連續部署的 Web 應用程式

此範例指令碼會在 App Service 中建立 Web 應用程式及其相關資源，然後設定從 Visual Studio Team Services 存放庫進行的連續部署。 針對此範例，您將需要：

* 具有應用程式程式碼的 Visual Studio Team Services 存放庫 (您必須有此存放庫的系統管理權限)。
* 您 Visual Studio Team Services 帳戶的[個人存取權杖 (PAT)](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create a web app with continuous deployment from Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | 建立 App Service 方案。 |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | 建立 Azure Web 應用程式。 |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | 將 Azure Web 應用程式關聯到 Git 或 Mercurial 存放庫。 |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | 在瀏覽器中開啟 Azure Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件](../app-service-cli-samples.md)中找到其他的 App Service CLI 指令碼範例。
