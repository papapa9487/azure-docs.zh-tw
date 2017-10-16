---
title: "使用 PowerShell Cmdlet 建立 Azure IoT 中樞 | Microsoft Docs"
description: "如何使用 PowerShell Cmdlet 建立 IoT 中樞。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 02227adeb8a9a7463506efa44ddc2977f8aae65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>使用 New-AzureRmIotHub Cmdlet 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>簡介

您可以使用 Azure PowerShell Cmdlet 建立及管理 Azure IoT 中樞。 此教學課程會示範如何使用 PowerShell 建立 IoT 中樞。

> [!NOTE]
> Azure 建立和處理資源的部署模型有兩種：[Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) 和傳統。 本文涵蓋使用 Azure Resource Manager 部署模型的部分。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure PowerShell Cmdlet][lnk-powershell-install]。

## <a name="connect-to-your-azure-subscription"></a>連接到 Azure 訂用帳戶
在 PowerShell 命令提示字元中，輸入下列命令來登入您的 Azure 訂用帳戶：

```powershell
Login-AzureRmAccount
```

如果您有多個 Azure 訂用帳戶，則登入 Azure 即會為您授與和您認證相關聯之所有 Azure 訂用帳戶的存取權。 使用下列命令來列出可供您使用的 Azure 訂用帳戶：

```powershell
Get-AzureRMSubscription
```

使用下列命令，選取您想要用來執行命令以建立 IoT 中樞的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>建立資源群組

您需要一個資源群組來部署 IoT 中樞。 您可以使用現有的資源群組，或建立一個新的群組。

您可以使用下列命令，探索您可以在其中部署 IoT 中樞的位置：

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

若要在下列其中一個支援的 IoT 中樞位置中建立 IoT 中樞的資源群組，請使用下列命令。 此範例會在**美國東部**區域中建立一個稱為 **MyIoTRG1** 的資源群組：

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

若要在您於上一個步驟中建立的資源群組中建立 IoT 中樞，請使用下列命令。 此範例會在**美國東部**區域中建立一個稱為 **MyTestIoTHub** 的 **S1** 中樞：

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 中樞名稱必須是唯一的。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


您可以使用下列命令來列出您訂用帳戶中的所有 IoT 中樞︰

```powershell
Get-AzureRmIotHub
```

前一個範例會新增一個向您收費的 S1 標準 IoT 中樞。 您可以使用下列命令刪除該 IoT 中樞：

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

或者，您可以使用下列命令移除資源群組，及其包含的所有資源：

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>後續步驟

現在您已經使用 PowerShell Cmdlet 部署 IoT 中樞，您可能想要進一步探索︰

* 探索其他[搭配您的 IoT 中樞使用的 PowerShell Cmdlet][lnk-iothub-cmdlets]。
* 閱讀 [IoT 中樞資源提供者 REST API][lnk-rest-api] 功能的相關資訊。

若要深入了解如何開發 IoT 中樞，請參閱以下文章︰

* [C SDK 簡介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [使用 IoT Edge 來模擬裝置][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
