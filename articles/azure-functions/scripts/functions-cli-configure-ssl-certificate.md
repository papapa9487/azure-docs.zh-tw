---
title: "Azure CLI 指令碼範例 - 將自訂 SSL 憑證繫結至函式應用程式 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在 Azure 中將自訂 SSL 憑證繫結至函式應用程式"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: ddabb701d7d5615232d1f6163aa6fb166efe5cb0
ms.contentlocale: zh-tw
ms.lasthandoff: 06/20/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>將自訂 SSL 憑證繫結至函式應用程式

此範例指令碼會在 App Service 中建立函式應用程式及其相關資源，然後將自訂網域名稱的 SSL 憑證與其繫結。 針對此範例，您需要：

* 存取網域註冊機構的 DNS 設定頁面。
* 對於想要上傳並繫結的 SSL 憑證，您具備有效的 .PFX 檔案和其密碼。

若要繫結 SSL 憑證，必須在 App Service 方案中建立函式應用程式，而不是在取用方案中建立。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "將自訂 SSL 憑證繫結至 Web 應用程式")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意事項 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 建立用來存放所有資源的資源群組。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 建立繫結 SSL 憑證所需的 App Service 方案。 |
| [az functionapp create]() | 建立函式應用程式。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 將自訂網域對應至函式應用程式。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | 將 SSL 憑證上傳至函式應用程式。 |
| [az appservice web config ssl upload](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | 將上傳的 SSL 憑證繫結至函式應用程式。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure App Service 文件]()中找到其他的 App Service CLI 指令碼範例。

