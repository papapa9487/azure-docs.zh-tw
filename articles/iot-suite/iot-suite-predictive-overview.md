---
title: "預防性維護解決方案的概觀 - Azure | Microsoft Docs"
description: "預先設定之 Azure IoT 套件預測性維護解決方案的說明。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 369e61b091c79df8ad2d874693aedc3b23e512c8
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>預先設定的預測性維護解決方案概觀

預先設定的「預測性維護」[解決方案][lnk_preconfigured_solutions]是 [Microsoft Azure IoT 套件][lnk_iot_suite]預先設定的解決方案之一。 此解決方案應用了 [Azure Machine Learning][lnk-machine-learning]，整合了即時裝置遙測集合與預測模型。

有了 Azure IoT 套件，您可以又快又方便地連接和監視資產，並在儀表板和視覺效果中即時分析遙測資料。 在預測性維護解決方案中，儀表板和視覺效果提供新的情報，可提升效率並增加收益流。

## <a name="the-scenario"></a>案例

Fabrikam 是區域性航空公司，而以優惠的價格為客戶提供優良的服務，是該公司努力的目標。 維護問題是造成航班延誤的原因之一，而引擎維護又是其中最為棘手的項目。 Fabrikam 必須嚴防飛行期間發生引擎故障，所以會定期檢查其引擎，並根據計劃安排維護時間表。 但因為飛機引擎的問題不一， 所以有一些引擎維護工作並非必要。 但嚴重者若在執行維護工作之前發生問題，可能會造成飛機停飛。 如果飛機所在地點缺少適當的技術人員或備品零件，這些問題會更嚴重。

Fabrikam 飛機的引擎由各種感應器進行檢測，而這些感應器會監視飛行期間的引擎狀況。 Fabrikam 使用預測性維護解決方案來收集飛行期間所收集的感應器資料。 經過多年累積引擎運作資料與失敗資料之後，Fabrikam 的資料科學家製做出了一個模型，可以預測飛機引擎的剩餘使用壽命 (RUL)。 此模型會在四個引擎感應器的資料和最終導致故障的引擎耗損之間找出相互關聯。 Fabrikam 現在除了繼續執行定期檢查來確保安全之外，還會在每次飛行後，使用這些模型來計算每具引擎的 RUL。 這些模型會使用在飛行期間從引擎收集而來的遙測資料。 Fabrikam 現在已可以預測未來的失敗點，並預先規劃維護及維修計劃。

> [!NOTE]
> 解決方案模型會使用實際的引擎損耗資料。

因為可以預測必要維護時機，讓 Fabrikam 可以最佳化各項作業，進而降低成本。

維護協調者會使用排程器︰

- 規劃維護以符合停在特定位置的飛機。
- 確保有足夠的時間讓飛機中斷服務，而不會造成排程中斷。
- 安排技術人員，讓飛機無須浪費時間等待，就可獲得有效率的維修。

庫存控制管理員因為會收到維護計畫，所以可以據此最佳化其訂單程序與備用零件庫存。

這些活動可讓 Fabrikam 將飛機停飛時間縮到最短，並降低營運成本，同時確保乘客與機組員的安全。

如需了解 [Azure IoT 套件][lnk_iot_suite]如何提供這些功能，客戶必須先了解預測性維護，請檢閱這張[資訊圖][lnk_infographic]。

## <a name="how-the-predictive-maintenance-solution-is-built"></a>預測性維護解決方案的建置方式

解決方案會使用可作為範例的現有 Azure Machine Learning 模型，示範這些功能如何運用透過 IoT 套件服務收集而來的裝置遙測資料。 Microsoft 已根據公開可用的資料<sup>\[1\]</sup>，以及如何使用模型的逐步指引，建置了一套飛機引擎的[迴歸模型][lnk_regression_model]。

Azure IoT 預測性維護解決方案會利用從這個範本所建立的迴歸模型。 此模型會部署到您的 Azure 訂用帳戶，並透過自動產生的 API 加以公開。 此解決方案包含了代表 4 具 (全部共 100 具) 引擎之測試資料與 4 個 (全部共 21 個) 感應器之資料流的交集。 此資料足以從定型的模型提供精確的結果。

*\[1\] A. Saxena and K. Goebel (2008)。《渦輪風扇發動機性能下降模擬資料集》(Turbofan Engine Degradation Simulation Data Set)，NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>開始使用預測性維護

本教學課程示範如何佈建預測性維護解決方案。 其中也逐步說明預測性維護解決方案的基本功能。 您可以透過隨預先設定解決方案部署的解決方案儀表板來存取多項功能。

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

> [!NOTE]
> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。

1. 使用 Azure 帳戶認證登入 [azureiotsuite.com][lnk-azureiotsuite]，然後按一下 **+** 建立新的解決方案。
1. 按一下 [選取預測性維護] 圖格。
1. 輸入預先設定的預測性維護解決方案的 [解決方案名稱]。
1. 選取您想要用來佈建解決方案的 [區域] 和 [訂用帳戶]。
1. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間。

### <a name="wait-for-the-provisioning-process-to-complete"></a>等候佈建程序完成

1. 按一下狀態為 **佈建中** 的解決方案圖格。
1. 請注意， **佈建狀態** 說明您的 Azure 訂用帳戶中已經佈建 Azure 服務。
1. 佈建完成之後，狀態會變更為 **就緒**。
1. 按一下圖格，就會在右邊窗格中看到解決方案的詳細資料。 在此窗格中，您可以啟動方案儀表板並存取 Machine Learning 工作區。

> [!NOTE]
> 如果您在佈建預先設定的解決方案時遇到問題，請參閱 [azureiotsuite.com 網站的權限][lnk-permissions]和[常見問題集][lnk-faq]。 如果問題持續發生，請在[入口網站][lnk-portal]建立服務票證。

是否有您預期會看到但沒有列出的解決方案詳細資料？ 請在 [使用者心聲](https://feedback.azure.com/forums/321918-azure-iot)中提供功能建議給我們。

## <a name="view-the-solution"></a>檢視解決方案

本節會引導您了解解決方案 UI。

### <a name="predictive-maintenance-dashboard"></a>預測性維護儀表板

Web 應用程式中的此頁面會使用 PowerBI JavaScript 控制項 (請參閱 [PowerBI 視覺效果存放庫][lnk-powerbi]) 以視覺化方式呈現：

* Blob 儲存體中串流分析作業的輸出資料。
* 每個飛機引擎的 RUL 和週期計數。

### <a name="observing-the-behavior-of-the-cloud-solution"></a>觀察雲端解決方案的行為

在 Azure 入口網站中，瀏覽至具有您所選之解決方案名稱的資源群組以檢視已佈建的資源。

![][img-resource-group]

當您佈建預先設定的解決方案，您會收到一封電子郵件，其中包含機器學習服務工作區的連結。 您也可以從已佈建之解決方案的 [azureiotsuite.com][lnk-azureiotsuite] 頁面瀏覽至 Machine Learning 工作區。 當解決方案處於**就緒**狀態時，此頁面上會出現一個圖格。

![][img-machine-learning]

在解決方案入口網站中，您可以看到範例已佈建四個模擬裝置，代表各有兩個引擎的兩架飛機，而每個引擎有四個感應器。 當您第一次瀏覽至解決方案入口網站時，模擬便會停止。

![][img-simulation-stopped]

按一下 [開始模擬] 來開始模擬。 感應器記錄、RUL、週期和 RUL 記錄會填入儀表板。

![][img-simulation-running]

當 RUL 小於 160 時 (為了示範目的而選擇的任意臨界值)，解決方案入口網站會在 RUL 旁邊顯示警告符號。 解決方案入口網站也會將飛機引擎醒目標示成黃色。 請注意 RUL 值有整體向下的趨勢，但傾向於上下波動。 此行為起因於變動的週期長度和模型精確度。

![][img-simulation-warning]

完整模擬需要約 35 分鐘的時間才能完成 148 個週期。 160 RUL 臨界值第一次在大約 5 分鐘的時候達到，而這兩個引擎在大約 8 分鐘的時候同時達到臨界值。

模擬會徹底執行 148 個週期的完整資料集並確定最終的 RUL 和週期值。

您可以隨時停止模擬，但按一下 [開始模擬]  會從資料集的開頭重新執行模擬。

## <a name="next-steps"></a>後續步驟

如需深入了解 Azure IoT 如何實現預測性維護的案例，請參閱[擷取物聯網的價值][lnk_capture_value]。

進行預測性維護解決方案的[逐步解說][lnk-predictive-walkthrough]。

您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [IoT 套件的常見問題集][lnk-faq]
* [從頭建立 IoT 安全性][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/