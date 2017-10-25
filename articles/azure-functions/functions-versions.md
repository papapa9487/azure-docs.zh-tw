---
title: "如何設定 Azure Functions 執行階段目標版本 | Microsoft Docs"
description: "Functions 支援多個執行階段版本。 了解如何指定 Azure 託管函式應用程式的執行階段版本。"
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>如何設定 Azure Functions 執行階段目標版本

Azure Functions 執行階段會在 Azure 中實作程式碼的無伺服器執行。 除了 Azure 中裝載的環境，您也可以在其他各種環境中發現此執行階段。 [Azure Functions Core Tools](functions-run-local.md) 會在開發電腦上實作此執行階段。 [Azure Functions 執行階段](functions-runtime-overview.md)可讓您在內部部署環境中使用 Functions。 

Functions 支援多個執行階段主要版本。 主要版本更新可以導入重大變更。 本主題描述將函式應用程式託管於 Azure 時，如何設定以特定的執行階段版本為目標。 

Functions 可讓您使用函數應用程式中的 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定，鎖定執行階段的特定主要版本。 這適用於這公用和預覽版本。 函數應用程式會保留指定的主要執行階段版本，直到您明確選擇移到新版本為止。 您的函數應用程式已在有可用更新時更新至執行階段的次要版本。 次要版本更新不會導入重大更新。  

新的主要版本已公開可用時，您可以在入口網站檢視函數應用程式時更新至該版本。 移至新版本之後，您隨時可以使用 `FUNCTIONS_EXTENSION_VERSION` 應用程式移回舊版執行階段版本。

每次執行階段版本進行變更，都會重新啟動函數應用程式。 所有執行階段的發行版本 (包含主要及次要) 均已發佈於 [GitHub 存放庫](https://github.com/Azure/azure-webjobs-sdk-script/releases)中。   
## <a name="view-the-current-runtime-version"></a>檢視目前的執行階段版本

使用下列程序來檢視您的函數應用程式目前使用的特定執行階段版本。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的函數應用程式，並在 [設定的功能] 下選擇 [函數應用程式設定]。 

    ![選取函數應用程式設定](./media/functions-versions/add-update-app-setting.png)

2. 在 [函數應用程式設定] 索引標籤上，找到 [執行階段版本]。 請注意特定的執行階段版本和要求的主要版本。 在下列範例中，主要版本已設定為 `~1.0`。
 
   ![選取函數應用程式設定](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>鎖定函數版本 2.0 執行階段

>[!IMPORTANT]   
> Azure Functions 執行階段 2.0 為預覽版本，目前尚未完全支援 Azure Functions 的所有功能。 如需詳細資訊，請參閱 [Azure Functions 執行階段 2.0 已知問題](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

您可以將函數應用程式移至 Azure 入口網站中的執行階段版本 2.0 預覽。 在 [函數應用程式設定] 索引標籤的 [執行階段版本] 下，選擇 [Beta]。  

   ![選取函數應用程式設定](./media/functions-versions/function-app-view-version.png)

這項設定就相當於將 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定為 `beta`。 選擇 **~1** 按鈕，移回目前公開支援的主要版本。 您也可以使用 Azure CLI 更新此應用程式設定。 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>鎖定入口網站中的特定執行階段版本

當您需要鎖定目前主要版本或或版本 2.0 以外的主要版本，必須設定 `FUNCTIONS_EXTENSION_VERSION` 應用程式設定。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的函數應用程式，並在 [設定的功能] 下選擇 [應用程式設定]。

    ![選取函數應用程式設定](./media/functions-versions/add-update-app-setting1a.png)

2. 在 [應用程式設定] 索引標籤上，尋找 `FUNCTIONS_EXTENSION_VERSION` 設定，並將值變更為 1.x 執行階段的有效的主要版本，或版本 2.0 的 `beta`。 

    ![設定函數應用程式設定](./media/functions-versions/add-update-app-setting2.png)

3. 按一下 [儲存] 來儲存應用程式設定更新。 

## <a name="target-a-specific-version-using-azure-cli"></a>使用 Azure CLI 鎖定特定版本

 您也可以從 Azure CLI 設定 `FUNCTIONS_EXTENSION_VERSION`。 使用 Azure CLI，以 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令在函式應用程式中更新應用程式設定。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
在此程式碼中，以您的函式應用程式名稱取代 `<function_app>`。 還要以函式應用程式的資源群組名稱取代 `<my_resource_group>`。 以 1.x 執行階段的有效主要版本，或版本 2.0 的 `beta` 來取代 `<version>`。 

您可以選擇上述程式碼範例中的 [試試看]，從 [Azure Cloud Shell][](../cloud-shell/overview.md) 執行此命令。 在執行 [az login](/cli/azure#az_login) 登入之後，您也可以使用[本機 Azure CLI](/cli/azure/install-azure-cli) 來執行此命令。
