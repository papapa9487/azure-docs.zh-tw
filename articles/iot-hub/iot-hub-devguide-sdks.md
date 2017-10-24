---
title: "了解 Azure IoT SDK | Microsoft Docs"
description: "開發人員指南 - 可用來建置裝置應用程式和後端應用程式的各種 Azure IoT 裝置和服務 SDK 的相關資訊和連結。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e299de0953cefac925b0015a15983d25d456576f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="understand-and-use-azure-iot-sdks"></a>了解和使用 Azure IoT SDK

有三種類別的軟體開發套件 (SDK) 可與「IoT 中樞」搭配使用：

* **裝置 SDK** 可讓您建立在 IoT 裝置上執行的應用程式。 這些應用程式會將遙測傳送至您的IoT 中樞，並選擇性地接收來自 IoT 中樞的訊息。

* **服務 SDK** 可讓您管理 IoT 中樞，並選擇性地將訊息傳送到 IoT 裝置。

* **Azure IoT Edge** 可讓您為不使用其中一種支援之通訊協定的裝置建立閘道。 閘道也可以處理邊緣上的訊息。

提供 SDK 的目的是支援多種程式設計語言。

## <a name="azure-iot-device-sdks"></a>Azure IoT 裝置 SDK

Microsoft Azure IoT 裝置 SDK 包含有助於建置裝置和應用程式的程式碼，並使裝置和應用程式連接到 Azure IoT 中心服務，且由其進行管理。

您可從 GitHub 下載下列 Azure IoT 裝置 SDK：

* [Azure IoT 裝置 SDK for .NET][lnk-dotnet-device-sdk]
* [Azure IoT 裝置 SDK for Java][lnk-java-device-sdk]
* [Azure IoT 裝置 SDK for Node.js][lnk-node-device-sdk]
* [適用於 Python 的 Azure IoT 裝置 SDK][lnk-python-device-sdk]
* 為了可攜性和廣泛平台相容性，以 ANSI C (C99) 撰寫之 [Azure IoT 裝置 SDK for C][lnk-c-device-sdk]。 有兩個適用於 C 的裝置用戶端程式庫，即低階 **iothub_client** 和 **serializer**。

> [!NOTE]
> 請參閱 GitHub 儲存機制中的讀我檔案，以取得使用語言和平台特定套件管理員在開發電腦上安裝二進位檔和相依項目的相關資訊。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>作業系統平台和硬體相容性

如需 SDK 與特定硬體裝置之相容性的詳細資訊，請參閱 [Azure IoT 認證裝置目錄][lnk-certified]。

## <a name="azure-iot-service-sdks"></a>Azure IoT 服務 SDK

Microsoft Azure IoT 服務 SDK 包含可協助建置應用程式的程式碼，這些應用程式可直接與「IoT 中樞」互動來管理裝置和安全性。

您可從 GitHub 下載下列 Azure IoT 服務 SDK：

* [Azure IoT 服務 SDK for .NET][lnk-dotnet-service-sdk]
* [Azure IoT 服務 SDK for Java][lnk-java-service-sdk]
* [Azure IoT 服務 SDK for Node.js][lnk-node-service-sdk]
* [適用於 Python 的 Azure IoT 服務 SDK][lnk-python-service-sdk]
* [適用於 C 的 Azure IoT 服務 SDK][lnk-c-service-sdk]

> [!NOTE]
> 請參閱 GitHub 儲存機制中的讀我檔案，以取得使用語言和平台特定套件管理員在開發電腦上安裝二進位檔和相依項目的相關資訊。

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge 包含用來建立 IoT 閘道解決方案的基礎結構和模組。 您可以擴充 IoT Edge 來建立適合任何端對端案例的閘道。

您可以從 GitHub 下載 [Azure IoT Edge][lnk-iot-edge]。

## <a name="online-api-reference-documentation"></a>線上 API 參考文件

以下清單包含 Azure IoT 裝置、服務及閘道器程式庫的線上 API 參考文件連結：

* [物聯網 (IoT) .NET (英文)][lnk-dotnet-ref]
* [Azure IoT 裝置 SDK for Java][lnk-java-ref]
* [Azure IoT 服務 SDK for Java][lnk-java-service-ref]
* [Azure IoT 裝置 SDK for Node.js][lnk-node-ref]
* [Azure IoT 服務 SDK for Node.js][lnk-node-service-ref]
* [Azure IoT 裝置 SDK for C][lnk-c-ref]
* [IoT 中樞 REST (英文)][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>後續步驟

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點][lnk-devguide-endpoints]
* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][lnk-devguide-query]
* [配額和節流][lnk-devguide-quotas]
* [IoT 中樞的 MQTT 支援][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
