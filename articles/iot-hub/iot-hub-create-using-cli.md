---
title: "使用 Azure CLI (az.py) 建立 IoT 中樞 | Microsoft Docs"
description: "如何使用跨平台 Azure CLI 2.0 (az.py) 建立 Azure IoT 中樞。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 161089159999a4a63a39b059e69a08b7a9297445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure CLI 2.0 (az.py)，以程式設計方式建立和管理 Azure IoT 中樞。 本文將說明如何使用 Azure CLI 2.0 (az.py) 建立 IoT 中樞。

您可以使用下列其中一個 CLI 版本來完成工作︰

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI。
* Azure CLI 2.0 (az.py) - 適用於資源管理部署模型的新一代 CLI (如本文中所述)。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure CLI 2.0][lnk-CLI-install]。

## <a name="sign-in-and-set-your-azure-account"></a>登入並設定 Azure 帳戶

登入您的 Azure 帳戶並選取您的訂用帳戶。

1. 在命令提示字元中，執行[登入命令][lnk-login-command]：
    
    ```azurecli
    az login
    ```

    依照指示使用程式碼進行驗證，並透過網頁瀏覽器登入 Azure 帳戶。

2. 如果您有多個 Azure 訂用帳戶，則登入 Azure 會授予您所有與認證相關聯之 Azure 帳戶的存取權。 使用下列[命令來列出可供您使用的 Azure 帳戶][lnk-az-account-command]︰
    
    ```azurecli
    az account list 
    ```

    使用下列命令，選取您想要用來執行命令以建立 IoT 中樞的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 Azure CLI 建立資源群組，然後新增 IoT 中樞。

1. 在建立 IoT 中樞時，必須將其建立在資源群組內。 使用現有的資源群組，或執行下列[命令來建立資源群組][lnk-az-resource-command]：
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > 上一個範例會建立位於美國西部位置的資源群組。 您可以執行命令 `az account list-locations -o table`，以檢視可用位置清單。
    >
    >

2. 使用 IoT 中樞的全域唯一名稱，在資源群組中執行下列命令[建立 IoT 中樞][lnk-az-iot-command]：
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> 上一個命令會在您付費使用的 S1 定價層中建立 IoT 中樞。 如需詳細資訊，請參閱 [Azure IoT 中樞價格][lnk-iot-pricing]。
>
>

## <a name="remove-an-iot-hub"></a>移除 IoT 中樞

您可以使用 Azure CLI 來[刪除個別資源][lnk-az-resource-command](例如 IoT 中樞)，或刪除資源群組和其所有資源 (包括任何 IoT 中樞)。

若要刪除 IoT 中樞，請執行下列命令︰

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

若要刪除資源群組和其所有資源，請執行下列命令︰

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>後續步驟
若要深入了解如何開發 IoT 中樞，請參閱以下文章︰

* [IoT 中樞開發人員指南][lnk-devguide]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 Azure 入口網站管理 IoT 中樞][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
