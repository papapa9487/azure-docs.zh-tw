---
title: "從 Azure CLI 建立您的第一個函式 | Microsoft Docs"
description: "了解如何使用 Azure CLI 來建立您的第一個 Azure 函式以進行無伺服器執行。"
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/08/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 4356d00b2694224f52a9359cd4a57d3a70a34d18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>在 Azure CLI 建立您的第一個函式

本快速入門主題會逐步解說如何使用 Azure Functions 來建立您的第一個函式。 您會使用 Azure CLI 來建立函數應用程式，此應用程式是主控函式的[無伺服器](https://azure.microsoft.com/overview/serverless-computing/)基礎結構。 函式程式碼本身是從 GitHub 的範例存放庫部署而來的。    

您可以使用 Mac、Windows 或 Linux 電腦，依照下面步驟操作。 

## <a name="prerequisites"></a>必要條件 

在執行此範例之前，您必須具備下列項目︰

+ 作用中的 [GitHub](https://github.com) 帳戶。 
+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式會提供環境來讓您的函式程式碼進行無伺服器執行。 它可讓您將多個函式群組為邏輯單位，以方便您管理、部署和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#create) 命令來建立函式應用程式。 

在下列命令中，使用唯一函式應用程式名稱來替代您看見 `<app_name>` 預留位置的地方，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 會作為函式應用程式的預設 DNS 網域，所以此名稱在 Azure 的所有應用程式中都必須是唯一的名稱。 _deployment-source-url_ 參數是 GitHub 中的範例存放庫，其中包含 "Hello World" HTTP 觸發函式。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope --deployment-source-url https://github.com/Azure-Samples/functions-quickstart
```
設定 _consumption-plan-location_ 參數，即表示函數應用程式是裝載於取用主控方案。 在此方案中，會根據您的函式，視需要而動態新增資源，且只有在函式執行時才需要付費。 如需詳細資訊，請參閱[選擇正確的主控方案](functions-scale.md)。 

建立函式應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]