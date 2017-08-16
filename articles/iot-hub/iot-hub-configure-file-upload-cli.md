---
title: "使用 Azure CLI (az.py) 來設定 IoT 中樞的檔案上傳 | Microsoft Docs"
description: "如何使用跨平台 Azure CLI 2.0 (az.py) 來設定 Azure IoT 中樞的檔案上傳。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: e2cd7eae50006717dfc0da358436ae3553a81d00
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>使用 Azure CLI 來設定 IoT 中樞檔案上傳

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

若要使用 [IoT 中樞的檔案上傳功能][lnk-upload]，您必須先將 Azure 儲存體帳戶與您的 IoT 中樞建立關聯。 您可以使用現有的儲存體帳戶或建立新帳戶。

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。
* [Azure CLI 2.0][lnk-CLI-install]。
* Azure IoT 中樞。 如果您沒有 IoT 中樞，您可以使用 `az iot hub create` [命令][lnk-cli-create-iothub] 來建立一個，或使用入口網站來 [建立 IoT 中樞][lnk-portal-hub]。
* Azure 儲存體帳戶。 如果您沒有 Azure 儲存體帳戶，您可以使用 [Azure CLI 2.0 - 管理儲存體帳戶][lnk-manage-storage] 來建立一個，或使用入口網站來[建立儲存體帳戶][lnk-portal-storage]。

## <a name="sign-in-and-set-your-azure-account"></a>登入並設定 Azure 帳戶

登入您的 Azure 帳戶並選取您的訂用帳戶。

1. 在命令提示字元中，執行[登入命令][lnk-login-command]：

    ```azurecli
    az login
    ```

    依照指示使用程式碼進行驗證，並透過網頁瀏覽器登入 Azure 帳戶。

1. 如果您有多個 Azure 訂用帳戶，則登入 Azure 會授予您所有與認證相關聯之 Azure 帳戶的存取權。 使用下列[命令來列出可供您使用的 Azure 帳戶][lnk-az-account-command]︰

    ```azurecli
    az account list
    ```

    使用下列命令，選取您想要用來執行命令以建立 IoT 中樞的訂用帳戶。 您可以使用來自上一個命令之輸出內的訂用帳戶名稱或識別碼︰

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>擷取您的儲存體帳戶詳細資料

下列步驟假設您使用 [Resource Manager] 部署模型，而非 [傳統] 部署模型，建立了儲存體帳戶。

若要從裝置設定檔案上傳，您需要 Azure 儲存體帳戶的連接字串。 儲存體帳戶必須與您的 IoT 中樞位於相同的訂用帳戶中。 您也需要儲存體帳戶中 blob 容器的名稱。 使用下列命令來擷取儲存體帳戶金鑰：

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

記下 **connectionString** 值。 您需要在後續步驟中用到此值。

您可以使用現有的 blob 容器進行檔案上傳，或建立新容器：

* 若要列出儲存體帳戶中現有的 blob 容器，使用下列命令：

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* 若要在儲存體帳戶中建立 blob 容器，使用下列命令：

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>檔案上傳

您現在可以使用儲存體帳戶詳細資料來設定您的 IoT 中樞，以啟用[檔案上傳功能][lnk-upload]。

設定需要下列值：

**儲存體容器**︰您目前 Azure 訂用帳戶內 Azure 儲存體帳戶中的 blob 容器，會與您的 IoT 中樞產生關聯。 您已在上一節中擷取了必要的儲存體帳戶資訊。 IoT 中樞會自動產生具有此 Blob 容器寫入權限的 SAS URI，以供裝置上傳檔案時使用。

**接收已上傳檔案的通知**︰啟用或停用檔案上傳通知。

**SAS TTL**︰這個設定是「IoT 中樞」傳回給裝置之 SAS URI 的存留時間。 預設會設為一小時。

**檔案通知設定預設 TTL**：檔案上傳通知到期前的存留時間。 預設會設為一天。

**檔案通知最大傳遞計數**︰IoT 中樞可嘗試傳遞檔案上傳通知的次數。 預設會設為 10。

使用下列 Azure CLI 命令來設定 IoT 中樞上的檔案上傳設定：

在 Bash 殼層中使用︰

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

在 Windows 命令提示字元中使用︰

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

您可以使用下列命令，檢閱 IoT 中樞上的檔案上傳組態︰

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>後續步驟

如需 IoT 中樞檔案上傳功能的詳細資訊，請參閱[從裝置上傳檔案][lnk-upload]。

遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [大量管理 IoT 裝置][lnk-bulk]
* [IoT 中樞度量][lnk-metrics]
* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 IoT Edge 來模擬裝置][lnk-iotedge]
* [徹底保護您的 IoT 解決方案][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]: ../storage/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]: ../storage/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#create
