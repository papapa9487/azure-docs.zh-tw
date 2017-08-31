---
title: "Azure IoT 中樞開發人員指南 | Microsoft Docs"
description: "Azure IoT 中樞開發人員指南中討論到端點、安全性、身分識別登錄、裝置管理、直接方法、裝置對應項、檔案上傳、作業、IoT 中心查詢語言及傳訊。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: d534ff9d-2de5-4995-bb2d-84a02693cb2e
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: adb9a12899e9040cd83d522c734448989636fe29
ms.contentlocale: zh-tw
ms.lasthandoff: 06/01/2017

---
# <a name="azure-iot-hub-developer-guide"></a>Azure IoT 中樞開發人員指南

Azure IoT 中樞是一項完全受管理的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。

Azure IoT 中樞可提供您︰

* 使用每一裝置的安全性認證和存取控制來保護通訊的安全。
* 多個裝置到雲端和雲端到裝置的超大規模通訊選項。
* 每一裝置狀態資訊和中繼資料的可查詢儲存體。
* 透過最受歡迎的語言和平台的裝置程式庫，進行簡單的裝置連線。

此 IoT 中樞開發人員指南包含下列文章︰

* [裝置對雲端通訊指引][lnk-d2c-guidance]可協助您在裝置對雲端訊息、裝置對應項報告屬性和檔案上傳之間做出選擇。
* [雲端對裝置通訊指引][lnk-c2d-guidance]可協助您在直接方法、裝置對應項所需屬性和雲端對裝置訊息之間做出選擇。
* [IoT 中樞的裝置到雲端及雲端到裝置傳訊][devguide-messaging]說明 IoT 中樞所公開的傳訊功能 (裝置到雲端和雲端到裝置)。
  * [將裝置到雲端訊息傳送至 IoT 中樞][devguide-messages-d2c]。
  * [從內建端點讀取裝置對雲端訊息][devguide-builtin]。
  * [使用適用於裝置對雲端訊息的自訂端點和路由規則][devguide-custom]。
  * [從 IoT 中樞傳送雲端到裝置訊息][devguide-messages-c2d]。
  * [建立及讀取 IoT 中樞訊息][devguide-format]。
* [從裝置上傳檔案][devguide-upload]說明如何從裝置上傳檔案。 本文也包含上傳程序可傳送之通知等主題的相關資訊。
* [在 IoT 中樞管理裝置身分識別][devguide-identities]說明各 IoT 中樞的身分識別登錄所儲存的資訊，以及您可以存取和修改資訊的方式。
* [控制 IoT 中樞的存取權][devguide-security]說明用來將存取權授與裝置和雲端元件之 IoT 中樞功能的安全性模型。 本文包含使用權杖和 X.509 憑證的相關資訊，以及您可授與之權限的詳細資料。
* [使用裝置對應項同步處理狀態和組態][devguide-device-twins]說明*裝置對應項*概念和其所公開的功能，例如同步處理裝置與其裝置對應項。 本文包含裝置對應項中儲存之資料的相關資訊。
* [叫用裝置上的直接方法][devguide-directmethods]說明直接方法的生命週期，以及如何從後端應用程式叫用裝置上方法及處理裝置上直接方法的相關資訊。
* [排程多個裝置上的作業][devguide-jobs]說明如何排程多個裝置上的作業。 本文說明如何將執行工作的作業提交為執行直接方法，使用裝置對應項更新裝置。 它也說明如何查詢作業的狀態。
* [參考 - 選擇通訊協定][devguide-protocol]說明 IoT 中樞進行裝置通訊所支援的通訊協定，並列出應開啟的連接埠。
* [參考 - IoT 中樞端點][devguide-endpoints]說明每個 IoT 中樞針對執行階段和管理作業所公開的各種端點。 本文也說明如何在 IoT 中樞中建立額外端點，以及如何使用現場閘道器啟用裝置在非標準案例中對 IoT 中樞端點的連線能力。
* [參考 - 裝置對應項、作業和訊息路由的 IoT 中樞查詢語言][devguide-query]說明的 IoT 中樞查詢語言可讓您從中樞擷取有關裝置對應項和作業的資訊。
* [參考 - 配額和節流][devguide-quotas]摘要說明 IoT 中樞服務中設定的配額，以及超過配額時的預期節流行為。
* [參考 - 定價][devguide-pricing]會提供關於 IoT 中樞之不同 SKU 和定價的一般資訊，以及 IoT 中樞如何以訊息的形式來針對各種 IoT 中樞功能進行計量的詳細資料。
* [參考 - 裝置和服務 SDK][devguide-sdks] 列出的 Azure IoT SDK 可用來開發與 IoT 中樞互動的裝置和服務應用程式。 本文包含線上 API 文件的連結。
* [參考 - IoT 中樞 MQTT 支援][devguide-mqtt]提供 IoT 中樞如何支援 MQTT 通訊協定的詳細資訊。 本文說明 Azure IoT SDK 內建之 MQTT 通訊協定的支援，並提供直接使用 MQTT 通訊協定的相關資訊。
* [詞彙][devguide-glossary]是常見 IoT 中樞相關術語的清單。

[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md
[devguide-pricing]: iot-hub-devguide-pricing.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[devguide-messages-d2c]: iot-hub-devguide-messages-d2c.md
[devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[devguide-custom]: iot-hub-devguide-messages-read-custom.md
[devguide-messages-c2d]: iot-hub-devguide-messages-c2d.md
[devguide-format]: iot-hub-devguide-messages-construct.md
[devguide-protocol]: iot-hub-devguide-protocols.md

