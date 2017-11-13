---
title: "Azure IoT 套件的預先設定解決方案概觀 | Microsoft Docs"
description: "說明 Azure IoT 套件的預先設定解決方案及其架構，且包含其他資源的連結。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 2d6a09e234ee8ec63454d42a91613ed8da9d1c4b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="what-is-azure-iot-suite"></a>什麼是 Azure IoT 套件？

Azure IoT 套件是一組「預先設定的解決方案」，其特色如下：

* 快速部署
* 協助您快速入門
* 可以自訂以符合特定需求

*IoT 套件*的預先設定解決方案皆根據相同原則和目標而設計。

## <a name="preconfigured-solutions-overview"></a>預先設定解決方案概觀

預先設定解決方案是常見 IoT 解決方案模式的開放原始碼實作，您可以使用您的訂用帳戶將這些模式部署到 Azure。 每個預先設定解決方案都會結合自訂程式碼和 Azure 服務，來實作特定的 IoT 案例。 您可以自訂任何案例以符合特定需求。 這些案例包括：

* 在豐富的儀表板上將資料視覺化，以取得深入分析和解決方案狀態。
* 透過即時 IoT 裝置遙測來設定規則和警示。
* 排程裝置管理作業，例如軟體和組態更新。
* 佈建您自己的自訂實體或模擬裝置。
* 針對 IoT 裝置群組內的問題進行移難排解並修復。

每個預先設定解決方案都是可以使用模擬或實體裝置建置的完整端對端實作，藉以產生遙測資料。 您可以使用預先設定解決方案作為解決方案加速器，來完成下列作業：

* 佈建您 IoT 解決方案的起點。
* 了解 IoT 解決方案設計與開發中的常見模式。

目前有三個預先設定解決方案可使用：

* [遠端監視](iot-suite-remote-monitoring-explore.md)
* [預測性維護](iot-suite-predictive-overview.md)
* [連線的處理站](iot-suite-connected-factory-overview.md)

下表顯示這些解決方案與特定的 IoT 功能如何對應：

| 方案 | 資料擷取 | 裝置身分識別 | 裝置管理 | 邊緣處理 | 命令與控制 | 執行和動作 | 預測性分析 |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [遠端監視](iot-suite-remote-monitoring-explore.md)  |是 |是 |是 |-   |是 |是 |-   |
| [預測性維護](iot-suite-predictive-overview.md)   |是 |是 |-   |-   |是 |是 |是 |
| [連線的處理站](iot-suite-connected-factory-overview.md) |是 |是 |是 |是 |是 |是 |-   |

* *資料擷取*：雲端規模的資料輸入。
* *裝置身分識別*：管理唯一裝置身分識別，以及控制裝置對解決方案的存取。
* *裝置管理*：管理裝置中繼資料，以及執行裝置重新開機和韌體升級等作業。
* 命令與控制項：若要讓裝置執行動作，請從雲端傳送訊息給裝置。
* 規則和動作：為了處理特定的裝置對雲端資料，解決方案後端會使用規則。
* *預測性分析*：解決方案後端會分析裝置到雲端的資料，以預測何時應執行特定動作。 例如，分析飛機引擎遙測，以判斷何時應維護引擎。

> [!NOTE]
> 若要部署預先設定解決方案並深入了解如何自訂，請瀏覽 [Microsoft Azure IoT 套件](https://www.azureiotsuite.com/)。

## <a name="azure-services"></a>Azure 服務

當您部署預先設定解決方案時，佈建程序會設定許多 Azure 服務。 下表顯示預先設定解決方案中使用的服務：

|                      | 遠端監視  | 預測性維護 | 連線的處理站 |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| IoT 中樞              | 是                |                        | 是               |
| 事件中樞           |                    | 是                    |                   |
| Time Series Insights |                    |                        | 是               |
| 容器服務   | 是                |                        | 是               |
| 串流分析     |                    | 是                    |                   |
| Web Apps             | 是                | 是                    | 是               |
| Cosmos DB            | 是                | 是                    | 是               |
| Azure 資料表         |                    | 是                    | 是               |

> [!NOTE]
> 如需有關部署在遠端監視預先設定解決方案中資源的詳細資訊，請在 GitHub 上參閱此[文章](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments)。

* [Azure IoT 中樞](../iot-hub/index.md)。 這項服務提供裝置到雲端和雲端到裝置的傳訊功能，並作為雲端到其他重要的 IoT 套件服務的閘道器。 此服務可讓您從您的裝置大量接收訊息，並將命令傳送給您的裝置。 此服務還可讓您[管理您的裝置](../iot-hub/iot-hub-device-management-overview.md)。 例如，您可以在一或多個連接到中樞的裝置上，進行設定、重新啟動，或執行恢復出場預設值的作業。
* [Azure 事件中樞](../event-hubs/index.md). 此服務可以將大量事件擷取到雲端。 請參閱 [Azure IoT 中樞和 Azure 事件中樞的比較](../iot-hub/iot-hub-compare-event-hubs.md)。
* [Azure 時間序列深入解析](../time-series-insights/index.md)。 預先設定解決方案會使用此服務來分析及顯示來自您裝置的遙測資料。
* [Azure Container Service](../container-service/index.yml)。 此服務會主控及管理預先設定解決方案中的微服務。
* 用於資料儲存的 [Azure Cosmos DB](../cosmos-db/index.yml) 和 [Azure 儲存體](../storage/index.yml)。
* [Azure 串流分析](../stream-analytics/index.md)。 預測性維護的預先設定解決方案會使用這項服務來處理內送的遙測資料、執行彙總，以及偵測事件。 此預先設定解決方案也會使用串流分析來處理資訊訊息，它包含像是中繼資料或是從裝置回應的命令的資料。
* [Azure Web Apps](../app-service/index.yml)，主控預先設定解決方案中的自訂應用程式程式碼。

如需典型 IoT 解決方案架構的概觀，請參閱 [Microsoft Azure 和物聯網 (IoT)](iot-suite-what-is-azure-iot.md)。

## <a name="whats-new-in-preconfigured-solutions"></a>預先設定解決方案有什麼新功能？

Microsoft 正在將預先設定解決方案更新為最新的微服務型架構。 下表顯示預先設定解決方案的目前狀態：

| 預先設定解決方案 | 架構  | 語言     |
| ---------------------- | ------------- | ------------- |
| 遠端監視      | 微服務 | Java 和.NET |
| 預測性維護 | MVC           | .NET          |
| 連線的處理站      | MVC           | .NET          |

下一節會說明微服務型預先設定解決方案的新功能：

### <a name="microservices"></a>微服務

新版的遠端監視預先設定解決方案會使用微服務架構。 此預先設定解決方案結合多種微服務，例如 *IoT 中樞管理員*和*存放管理員*。 每個微服務的 Java 和 .NET 版本皆可下載，並隨附於相關的開發人員文件中。 如需有關微服務的詳細資訊，請參閱[遠端監視架構](iot-suite-remote-monitoring-sample-walkthrough.md)。

此微服務架構是經過實證的雲端解決方案模式，其特色如下：

* 可調整。
* 可擴充。
* 容易了解。
* 可替換個別服務。

> [!TIP]
> 若要了解微服務架構的詳細資訊，請參閱 [.NET 應用程式架構](https://www.microsoft.com/net/learn/architecture)和[微服務︰採用雲端技術的應用程式革命](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)。

當您部署新版遠端監視時，您必須選取下列其中一個部署選項：

* **基本：**低成本的示範版本，或用來測試部署。 所有微服務都將部署到單一 Azure 虛擬機器。
* **標準：**適用於開發生產環境部署的擴展基礎結構部署。 Azure Container Service 會將微服務部署到多個 Azure 虛擬機器。 Kubernetes 會協調裝載個別微服務的 Docker 容器。

### <a name="language-choices-java-and-net"></a>語言選擇：Java 和 .NET

每個微服務實作皆可使用 Java 和 .NET。 如同 .NET 程式碼，Java 原始程式碼是開放原始碼，可讓您用來自訂特定需求：

* [遠端監視 .NET GitHub 存放庫](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [遠端監視 Java GitHub 存放庫](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

如果您想要查看其他語言實作，可在 [Azure IoT 使用者心聲](https://feedback.azure.com/forums/321918-azure-iot)中新增要求。

### <a name="react-user-interface-framework"></a>React 使用者介面架構

UI 是使用 [React](https://facebook.github.io/react/) javascript 程式庫所建置。 原始程式碼是開放原始碼並可供您下載和自訂。

## <a name="next-steps"></a>後續步驟

既然您已初步了解 IoT 套件的預先設定方案，以下是每個預先設定解決方案的建議後續步驟：

* [瀏覽 Azure IoT 套件的遠端監視解決方案 Resource Manager 部署模型](iot-suite-remote-monitoring-explore.md)。
* [預測性維護預先設定的解決方案概觀](iot-suite-predictive-overview.md)。
* [開始使用 Azure IoT 套件連線處理站預先設定的解決方案](iot-suite-connected-factory-overview.md)。

如需 IoT 解決方案架構的詳細資訊，請參閱 [Microsoft Azure IoT 服務︰參考架構](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf)。
