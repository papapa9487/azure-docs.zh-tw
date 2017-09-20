---
title: "Azure IoT 預先設定解決方案 | Microsoft Docs"
description: "說明 Azure IoT 預先設定解決方案及其架構，且包含其他資源的連結。"
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
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: d66dece63d2ba944c8f3828ba68c6202485d47e0
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>什麼是 Azure IoT 套件預先設定的解決方案？

Azure IoT 套件預先設定的解決方案是常見 IoT 解決方案模式的實作，您可以使用您的訂用帳戶將這些模式部署到 Azure。 您可以使用預先設定的解決方案：

* 做為您的 IoT 解決方案的起點。
* 深入了解 IoT 解決方案設計與開發中的常見模式。

每個預先設定解決方案都是使用模擬裝置建置的完整端對端實作，藉以產生遙測資料。

您可以下載完整原始程式碼來自訂及擴充解決方案，以符合您的特定 IoT 需求。

> [!NOTE]
> 若要部署其中一個預先設定解決方案，請造訪 [Microsoft Azure IoT 套件][lnk-azureiotsuite]。 [開始使用 IoT 預先設定的解決方案][lnk-getstarted-preconfigured]一文提供如何部署並執行其中的一個解決方案的相關詳細資訊。

下表顯示這些解決方案與特定的 IoT 功能如何對應：

| 方案 | 資料擷取 | 裝置身分識別 | 裝置管理 | 命令與控制 | 執行和動作 | 預測性分析 |
| --- | --- | --- | --- | --- | --- | --- |
| [遠端監視][lnk-getstarted-preconfigured] |是 |是 |是 |是 |是 |- |
| [預測性維護][lnk-predictive-maintenance] |是 |是 |- |是 |是 |是 |
| [連線的處理站][lnk-getstarted-factory] |是 |是 |是 |是 |是 |- |

* *資料擷取*：雲端規模的資料輸入。
* *裝置身分識別*：管理唯一裝置身分識別，以及控制裝置對解決方案的存取。
* *裝置管理*：管理裝置中繼資料，以及執行裝置重新開機和韌體升級等作業。
* 命令與控制項：若要讓裝置執行動作，請從雲端傳送訊息給裝置。
* 規則和動作：為了處理特定的裝置對雲端資料，解決方案後端會使用規則。
* *預測性分析*：解決方案後端會分析裝置到雲端的資料，以預測何時應執行特定動作。 例如，分析飛機引擎遙測，以判斷何時應維護引擎。

## <a name="remote-monitoring-preconfigured-solution-overview"></a>遠端監視預先設定解決方案概觀

在本文中，我們選擇討論遠端監視預先設定解決方案，因為它說明了其他解決方案共用的許多常見設計元素。

下圖說明遠端監視解決方案的主要元素。 下列各節會提供關於這些元素的詳細資訊。

![遠端監視預先設定解決方案架構][img-remote-monitoring-arch]

## <a name="devices"></a>裝置

當您部署遠端監視預先設定的解決方案時，有四個模擬裝置會預先佈建於解決方案中，以便模擬冷卻裝置。 這些模擬裝置具有可發出遙測資訊的內建溫度和溼度模型。 包含這些模擬裝置是為了：

- 說明透過此解決方案進行的端對端資料流程。
- 提供便利的遙測資料來源。
- 提供方法或命令的目標 (如果您是使用此解決方案作為自訂實作起點的後端開發人員的話)。

解決方案中的模擬裝置可以回應下列雲端到裝置通訊：

- 方法 ([直接方法][lnk-direct-methods])：一種連接的裝置應立即回應的雙向通訊方法。
- 命令 (雲端到裝置訊息)：一種裝置會從持久佇列擷取命令的單向通訊方法。

如需這些不同方法的比較，請參閱[雲端到裝置通訊指引][lnk-c2d-guidance]。

當裝置第一次連線到預先設定之解決方案中的 IoT 中樞時，它會將裝置資訊訊息傳送給該中樞。 此訊息會列舉裝置可回應的方法。 在遠端監視預先設定解決方案中，模擬裝置可支援下列方法：

* *起始韌體更新*：此方法會在裝置上起始一個非同步工作來執行韌體更新。 非同步工作會使用回報的屬性，將狀態更新傳遞到解決方案儀表板。
* *重新啟動*：此方法會讓模擬裝置重新啟動。
* *FactoryReset*：此方法會在模擬裝置上觸發恢復出廠預設值的作業。

當裝置第一次連線到預先設定之解決方案中的 IoT 中樞時，它會將裝置資訊訊息傳送給該中樞。 此訊息會列舉裝置可回應的方法。 在遠端監視預先設定解決方案中，模擬裝置可支援下列命令：

* *偵測裝置*：裝置會以確認來回應此命令。 此命令可用來檢查裝置是否仍處於作用中且在進行接聽。
* *開始遙測*：指示裝置開始傳送遙測資料。
* *停止遙測*：指示裝置停止傳送遙測資料。
* *變更設定點溫度*：控制裝置傳送的模擬溫度遙測值。 此命令可用來測試後端邏輯。
* *診斷遙測*：控制裝置是否應傳送外部溫度做為遙測資料。
* *變更裝置狀態*：設定裝置所報告的裝置狀態中繼資料屬性。 此命令可用來測試後端邏輯。

您可以將更多模擬裝置新增至解決方案，來發出相同的遙測資料並回應相同的方法和命令。

除了回應命令和方法之外，解決方案還會使用[裝置對應項][lnk-device-twin]。 裝置會使用裝置對映項將屬性值回報給解決方案後端。 解決方案儀表板會使用裝置對應項，在裝置上設定成所需的新屬性值。 例如，在進行韌體更新的過程中，模擬裝置會回使用回報的屬性來回報更新狀態。

## <a name="iot-hub"></a>IoT 中樞

在此預先設定解決方案中，IoT 中樞執行個體會對應至典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中的「雲端閘道器」。

IoT 中樞會從單一端點接收來自裝置的遙測資料。 IoT 中樞也會維護裝置可從中擷取命令的裝置特定端點。

IoT 中樞可讓您透過服務端遙測讀取端點接收遙測資料。

IoT 中樞的裝置管理功能可讓您從解決方案入口網站管理裝置屬性，以及排定執行類似下列作業的工作：

- 重新啟動裝置
- 變更裝置狀態
- 韌體更新

## <a name="azure-stream-analytics"></a>Azure 串流分析

預先設定解決方案使用三個 [Azure 串流分析][lnk-asa] (ASA) 作業來篩選來自裝置的遙測串流：

* DeviceInfo 作業 - 將資料輸出到事件中樞，此中樞會將裝置註冊的特定訊息傳送到解決方案裝置登錄。 此裝置登錄是 Azure Cosmos DB 資料庫。 當裝置第一次連線或回應「變更裝置狀態」命令時，就會傳送這些訊息。
* *遙測作業* - 將所有的原始遙測資訊傳送到 Azure Blob 儲存體進行冷儲存，並計算會顯示在解決方案儀表板中的遙測彙總。
* *規則作業* - 篩選遙測串流中超出任何規則臨界值的值，並將資料輸出到事件中樞。 當規則引發時，解決方案入口網站儀表板檢視會將此事件顯示為警示歷程記錄資料表中的新資料列。 這些規則也可以根據解決方案入口網站中的 [規則] 和 [動作] 檢視上定義的設定來觸發動作。

在此預先設定解決方案中，ASA 作業會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中 **IoT 解決方案後端**的一部分。

## <a name="event-processor"></a>事件處理器

在此預先設定解決方案中，事件處理器會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中 **IoT 解決方案後端**的一部分。

**DeviceInfo** 和**規則** ASA 作業會將它們的輸出傳送到事件中樞，以傳遞給其他後端服務。 解決方案會使用在 [WebJob][lnk-web-job] 中執行的 [EventPocessorHost][lnk-event-processor] 執行個體，來從這些事件中樞讀取訊息。 **EventProcessorHost** 會使用：
- **DeviceInfo** 資料來更新 Cosmos DB 資料庫中的裝置資料。
- 「規則」資料來叫用邏輯應用程式，然後更新解決方案入口網站中的警示顯示。

## <a name="device-identity-registry-device-twin-and-cosmos-db"></a>裝置身分識別登錄、裝置對應項及 Cosmos DB

每個 IoT 中樞都包括儲存裝置金鑰的[裝置身分識別登錄][lnk-identity-registry]。 IoT 中樞會使用此資訊驗證裝置 - 裝置必須先登錄並擁有有效的金鑰，才能連線至中樞。

[裝置對應項][lnk-device-twin] 是由 IoT 中樞管理的 JSON 文件。 裝置的裝置對應項包含：

- 裝置傳送給中樞的回報屬性。 您可以在解決方案入口網站中檢視這些屬性。
- 您想要傳送給裝置的所需屬性。 您可以在解決方案入口網站中設定這些屬性。
- 只存在於裝置對應項中而不存在於裝置上的標籤。 您可以在解決方案入口網站中使用這些標籤來篩選裝置清單。

此解決方案會使用裝置對應項來管理裝置中繼資料。 此解決方案也會使用 Cosmos DB 資料庫來儲存額外的解決方案特定裝置資料，例如每個裝置支援的命令及命令歷程記錄。

解決方案也必須讓裝置身分識別登錄中的資訊與 Cosmos DB 資料庫中的內容保持同步。 **EventProcessorHost** 會使用來自 **DeviceInfo** 串流分析作業的資料來管理同步處理。

## <a name="solution-portal"></a>解決方案入口網站

![解決方案入口網站][img-dashboard]

解決方案入口網站是 Web 型 UI，部署至雲端做為預先設定解決方案的一部分。 可以讓您：

* 檢視儀表板中的遙測和警示歷程記錄。
* 佈建新裝置。
* 管理和監視裝置。
* 將命令傳送至特定裝置。
* 在特定裝置上叫用方法。
* 管理規則和動作。
* 排定作業在一或多個裝置上執行。

在此預先設定解決方案中，解決方案入口網站會形成典型 [IoT 解決方案架構][lnk-what-is-azure-iot]中 **IoT 解決方案後端**及**處理和業務連線**的一部分。

## <a name="next-steps"></a>後續步驟

如需 IoT 解決方案架構的詳細資訊，請參閱 [Microsoft Azure IoT 服務 ︰參考架構][lnk-refarch]。

您現已知道預先設定的解決方案是什麼，您可以部署「遠端監視」預先設定的解決方案來開始使用︰[開始使用預先設定的解決方案][lnk-getstarted-preconfigured]。

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-getstarted-factory]: iot-suite-connected-factory-overview.md

