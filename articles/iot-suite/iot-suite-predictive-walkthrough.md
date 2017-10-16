---
title: "預測性維護逐步解說 | Microsoft Docs"
description: "預先設定之 Azure IoT 預測性維護解決方案的逐步解說。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: a68a8fdc3976ade0d1036d5ed58c8b2eb6d32a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>預先設定的預測性維護解決方案逐步解說

預先設定的預測性維護解決方案是一個端對端解決方案，適用於預測可能發生失敗之時間點的商務案例。 您可以針對最佳化維護等活動，主動使用此預先設定的解決方案。 此解決方案結合了主要 Azure IoT 套件服務，例如 IoT 中樞、串流分析和 [Azure Machine Learning][lnk-machine-learning] 工作區。 此工作區包含以公開範例資料集為基礎的模型，用來預測飛機引擎的剩餘使用年限 (RUL)。 此解決方案完整提供 IoT 商務案例的實作作為起點，讓您規劃和實作能滿足特定商務需求的解決方案。

## <a name="logical-architecture"></a>邏輯架構

下圖概述預先設定解決方案的邏輯元件：

![][img-architecture]

藍色項目是在您佈建預先設定之解決方案的區域中所佈建的 Azure 服務。 您可以在其中部署預先設定之解決方案的地區清單會顯示於[佈建頁面][lnk-azureiotsuite]。

綠色項目是表示飛機引擎的模擬裝置。 您可以在下一節中進一步了解這些模擬裝置。

灰色項目代表可實作「裝置管理」功能的元件。 目前預先設定的預測性維護解決方案版本不會佈建這些資源。 若要深入了解裝置管理，請參閱[遠端監視預先設定的解決方案][lnk-remote-monitoring]。

## <a name="simulated-devices"></a>模擬的裝置

在預先設定的解決方案中，模擬裝置代表飛機引擎。 此解決方案已佈建兩個對應至單一飛機的引擎。 每個引擎會發出四種類型的遙測：感應器 9、感應器 11、感應器 14 和感應器 15 會提供 Machine Learning 模型來計算該引擎的 RUL 所需的資料。 每個模擬的裝置會將下列遙測訊息傳送至 IoT 中樞：

*週期計數*。 週期表示已完成持續期間介於 2 小時與 10 小時之間的飛行。 在飛行期間，每半小時擷取一次遙測資料。

*遙測*。 有四個代表引擎屬性的感應器。 這些感應器會一般會標示為感應器 9、感應器 11、感應器 14 和感應器 15。 這 4 個感應器代表足以從 RUL 模型取得有用結果的遙測。 用於預先設定之解決方案中的模型是根據包含實際引擎感應器資料的公用資料集建立而來。 如需有關如何從原始資料集建立模型的詳細資訊，請參閱 [Cortana Intelligence Gallery 預測性維護範本][lnk-cortana-analytics]。

模擬的裝置可以處理從解決方案中 IoT 中樞傳送的下列命令：

| 命令 | 說明 |
| --- | --- |
| StartTelemetry |控制模擬的狀態。<br/>傳送遙測以啟動裝置 |
| StopTelemetry |控制模擬的狀態。<br/>傳送遙測以停止裝置 |

IoT 中樞會提供裝置命令通知。

## <a name="azure-stream-analytics-job"></a>Azure 串流分析作業

**作業：遙測**會使用兩個陳述式來操作傳入裝置遙測串流：

* 第一個陳述式會從裝置選取所有遙測資料，然後將此資料傳送至 bob 儲存體。 從這裡，它會呈現在 Web 應用程式中。
* 第二個陳述式會透過兩分鐘的滑動視窗計算感應器平均值，然後透過事件中樞將此資料傳送至**事件處理器**。

## <a name="event-processor"></a>事件處理器
**事件處理器主機**會在 Azure Web 作業中執行。 **事件處理器** 需要一個完整的週期來處理平均感應器值。 接著將這些值傳遞至 API，該 API 會公開定型模型來計算引擎的 RUL。 此 API 是由佈建為方案一部分的 Machine Learning 工作區所公開。

## <a name="machine-learning"></a>機器學習服務
Machine Learning 元件使用的模型衍生自從真實飛機引擎收集而來的資料。 您可以從已佈建之解決方案的 [azureiotsuite.com][lnk-azureiotsuite] 頁面上的圖格，瀏覽至 Machine Learning 工作區。 當解決方案處於**就緒**狀態時，就會出現此圖格。


## <a name="next-steps"></a>後續步驟
您現在已看到預先設定之預測性維護解決方案的主要元件，您可加以自訂。 請參閱[自訂預先設定的解決方案指引][lnk-customize]。

您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [IoT 套件的常見問題集][lnk-faq]
* [從頭建立 IoT 安全性][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/