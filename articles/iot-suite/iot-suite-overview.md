---
title: "Microsoft Azure IoT 套件概觀 | Microsoft Docs"
description: "大致說明 Azure IoT 套件如何提供物聯網預先設定的解決方案，以收集、分析和儲存資料、提供視覺效果，以及與其他系統整合。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-azure-iot-suite"></a>Azure IoT 套件的概觀

Azure 物聯網 (IoT) 服務提供廣泛的功能。 這些企業等級的服務可讓您：

* 從裝置收集資料
* 分析移動中的資料串流
* 儲存和查詢大型資料集
* 視覺化即時和歷程記錄資料
* 與後端辦公室系統整合
* 管理您的裝置

為了提供這些功能，Azure IoT 套件將多個 Azure 服務與自訂擴充功能封裝在一起作為「預先設定的解決方案」。 這些預先設定的解決方案是常見 IoT 解決方案模式的基礎實作，可幫助您減少實行 IoT 解決方案所花費的時間。 透過使用 [IoT 軟體開發套件][lnk-sdks]，您將可自訂和擴充這些解決方案來滿足您自己的需求。 您也可以使用這些解決方案作為開發新 IoT 解決方案時的範例或範本。

下列影片提供 Azure IoT 套件的簡介：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT 套件中的 Azure IoT 服務
預先設定的解決方案通常使用下列服務：

* 「Azure IoT 套件」的核心是 [Azure IoT 中樞][lnk-iot-hub]服務。 這項服務提供裝置到雲端和雲端到裝置的傳訊功能，並作為雲端到其他重要的 IoT 套件服務的閘道器。 此服務可讓您從您的裝置大量接收訊息，並將命令傳送給您的裝置。 此服務還可讓您[管理您的裝置][lnk-device-management]。 例如，您可以在一或多個連接到中樞的裝置上，進行設定、重新啟動，或執行恢復出場預設值的作業。
* [Azure 串流分析][lnk-asa]提供移動中的資料分析。 「IoT 套件」會使用這項服務來處理內送的遙測資料、執行彙總，以及偵測事件。 預先設定的解決方案也會使用串流分析來處理資訊訊息，它包含像是中繼資料或是從裝置回應的命令的資料。 這些解決方案使用「串流分析」來處理來自您裝置的訊息，並將這些訊息傳送給其他服務。
* [Azure 儲存體][lnk-azure-storage]和 [Azure Cosmos DB][lnk-document-db] 提供資料儲存體功能。 預先設定的解決方案使用 Blob 儲存體來儲存遙測，並且讓它可用於分析。 這些解決方案使用 Cosmos DB 來儲存裝置中繼資料，以及將解決方案的裝置管理功能啟用。
* [Azure Web Apps][lnk-web-apps] 和 [Microsoft Power BI][lnk-power-bi] 提供資料視覺化功能。 Power BI 的彈性可讓您快速建置自己的互動式儀表板 (使用 IoT 套件資料)。

如需典型 IoT 解決方案架構的概觀，請參閱 [Microsoft Azure 和物聯網 (IoT)][iot-suite-what-is-azure-iot]。

## <a name="preconfigured-solutions"></a>預先設定的解決方案

「IoT 套件」包括預先設定的解決方案，可讓您快速開始使用及探索常見的 IoT 案例，例如：

* 遠端監視
* 預測性維護
* 連線的處理站

您可以將這些解決方案部署到您的 Azure 訂用帳戶，然後執行完整的端對端 IoT 案例。

## <a name="next-steps"></a>後續步驟

既然您已概略了解「IoT 套件」的功能及其主要元件，您現在可以深入了解「IoT 套件」中預先設定的解決方案。 如需詳細資訊，請參閱[什麼是 Azure IoT 套件預先設定的解決方案？][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
