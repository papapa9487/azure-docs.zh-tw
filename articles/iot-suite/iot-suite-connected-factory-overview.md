---
title: "連線處理站解決方案概觀 - Azure | Microsoft Docs"
description: "Azure IoT 套件連線處理站預先設定的解決方案說明。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 3b56c91accfb6fe6358032e1dd329a931abfc3c1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>開始使用 Azure IoT 套件連線處理站預先設定的解決方案

Azure IoT 套件[預先設定的解決方案][lnk-preconfigured-solutions]結合了多項 Azure IoT 服務，以提供可實作常見 IoT 商務案例的端對端解決方案。 「連線處理站廠」預先設定的解決方案會連線並監視您的工業裝置。 您可以使用此解決方案來分析裝置的資料流，以及提升營運生產力和獲利率。

本教學課程示範如何佈建連線處理站預先設定的解決方案。 其中也逐步說明預先設定解決方案的基本功能。 您可以從隨預先設定解決方案一起部署的解決方案儀表板中，存取其中許多功能：

![連線處理站預先設定的解決方案儀表板][img-cf-home]

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

> [!NOTE]
> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。
> 
> 

## <a name="provision-the-solution"></a>佈建解決方案

1. 使用 Azure 帳戶認證登入 azureiotsuite.com，然後按一下 "**+**" 以建立解決方案。
2. 按一下 [連線處理站] 圖格上的 [選取]。
3. 輸入連線處理站預先設定的解決方案的 [解決方案名稱]。
4. 選取您要用來佈建解決方案的 [訂用帳戶] 和 [區域]。
5. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間。

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>在您等候佈建程序完成時

1. 按一下狀態為 **佈建中** 的解決方案圖格。
2. 請注意， **佈建狀態** 說明您的 Azure 訂用帳戶中已經佈建 Azure 服務。
3. 佈建完成之後，狀態會變更為 **就緒**。
4. 按一下圖格，就會在右邊窗格中看到解決方案的詳細資料。

> [!NOTE]
> 如果您在佈建預先設定的解決方案時遇到問題，請參閱 [azureiotsuite.com 網站的權限][lnk-permissions]和[連線的處理站常見問題集](iot-suite-faq-cf.md)。 如果問題持續發生，請在[入口網站][lnk-portal]建立服務票證。

是否有您預期會看到但沒有列出的解決方案詳細資料？ 請在[使用者心聲](https://feedback.azure.com/forums/321918-azure-iot)中提供功能建議。

## <a name="scenario-overview"></a>案例概觀

當您部署連線處理站預先設定的解決方案時，它會預先填入可讓您逐步執行常見產業案例的資源。 在此案例中，連線至解決方案的數個工廠會報告計算整體設備效率 (OEE) 和關鍵效能指標 (KPI) 所需的資料值。 下列各節將示範如何：

* 監視處理站、生產線、站區 OEE 和 KPI 值
* 使用 Azure Time Series Insights 分析這些裝置產生的遙測資料
* 處理警示以修正問題

此案例的主要功能是您可以從解決方案儀表板遠端執行所有這些動作。 您不需要實際存取裝置。

## <a name="view-the-solution-dashboard"></a>檢視解決方案儀表板

解決方案儀表板可讓您管理部署的解決方案。 它是全域處理站組態的階層式表示法。 例如，您可以檢視 OEE 和 KPI、針對遙測和動作警示發佈新節點。

1. 當佈建完成且預先設定解決方案的圖格指示 [就緒] 時，選擇 [啟動] 以在新的索引標籤中開啟連線處理站解決方案入口網站。

    ![啟動預先設定的解決方案][img-launch-solution]

1. 根據預設，此解決方案入口網站會顯示儀表板。 若要瀏覽至入口網站的其他區域，請使用頁面左側的功能表。

    ![連線處理站預先設定的解決方案儀表板][cf-img-menu]

儀表板會顯示下列資訊：

* **處理站清單**面板，可顯示解決方案的狀態、位置和目前生產組態。 當您第一次執行解決方案時，有數個模擬裝置。 生產線模擬是由每條生產線上執行模擬工作及共用資料的三部實際 OPC UA 伺服器所構成。 如需 OPC UA 的詳細資訊，請參閱[連線的處理站常見問題集](iot-suite-faq-cf.md)。
* **地圖**，可顯示連線至解決方案之每個裝置的位置。 解決方案可以使用 Bing 地圖服務 API 在地圖上繪製資訊。 如果您的訂用帳戶已啟用 Bing 地圖服務企業版 API，則會自動使用這項功能。 如未啟用，請參閱[常見問題集][lnk-faq]以了解如何讓地圖變成動態。
* **警示**面板，可顯示遙測或 OEE/KPI 值超過特定閾值時所產生的警示。
* **整體設備效率**面板，可顯示整個企業或您所檢視之處理站/生產線/站區的 OEE 值。 此值會從站區檢視彙總至企業層級。 您可以進一步分析 OEE 數字及其組成元素。
* **關鍵效能指標**面板，可顯示所產生的單位數，以及整個企業或您所檢視之處理站/生產線/站區所使用的能源。 這些值會從站區檢視彙總至企業層級。

## <a name="view-factories"></a>檢視工廠

*工廠*面板，可顯示解決方案中所有工廠的地理位置、其狀態和目前的生產組態。 從 [位置] 清單，您可以瀏覽至解決方案階層中的其他層級。 清單中的資料列是一些超連結，可連結該位置的生產線詳細資料。 您可以接著深入探詢生產線詳細資料，並向下切入到站區層級檢視。 您也可以將篩選條件套用至清單。

![連線處理站預先設定的解決方案處理站][cf-img-factories] 

1. **處理站面板**會顯示此解決方案的處理站清單。

2. 處理站清單最初會顯示佈建程序所建立的 6 個處理站。 您可以將其他模擬裝置和實體裝置新增至解決方案。

3. 若要檢視處理站的詳細資訊，請按一下處理站清單中的資料列。

4. 若要檢視生產線的詳細資訊，請按一下清單中的資料列。

5. 若要檢視生產線上站區的已發佈 OPC UA 節點，請按一下清單中的資料列。

6. 若要檢視站區中特定節點的詳細資料，請按一下清單中的資料列。 這個動作會啟動具有 Time Series Insights 視覺效果的內容面板。 按一下這些圖形，在 Time Series Insights 總管環境中做進一步分析。

## <a name="view-map"></a>檢視地圖

如果您的訂用帳戶可存取 Bing 地圖服務 API，則「工廠」地圖會顯示解決方案中所有工廠的地理位置和狀態。 若要深入探詢位置詳細資料，請按一下地圖上顯示的位置。

![連線處理站預先設定的解決方案地圖][cf-img-map]

## <a name="view-alerts"></a>檢視警示

[警示] 面板會顯示因為回報的值或計算的 OEE/KPI 值超過其設定的閾值而產生的警示。 此面板會顯示階層供每個層級的警示 (從站區層級檢視到全域檢視)。 警示包含警示描述、日期、時間、位置和發生次數。 您可以使用 Time Series Insights 資料深入解析造成警示的資料。 Time Series Insights 資料會在適用的警示中顯現。 如果您是系統管理員，您可以對警示採取預設動作，例如︰

* 關閉警示。
* 認知警示。

您可以選擇性地採取更複雜的動作。 例如，對於組件的壓力 OPC UA 節點，您可以︰

* 在新的瀏覽器視窗中的網頁顯示支援資訊。
* 在裝置上呼叫 OPC UA 方法，以緩和警示的原因。
* 隱藏預設動作的可用性。

    ![連線處理站預先設定的解決方案警示][cf-img-alerts]

> [!NOTE]
> 這些警示是由預先設定之解決方案中的組態檔中指定的規則所產生。 當 OEE 或 KPI 數字或 OPC UA 節點值超過其設定的閾值時，這些規則即可產生警示。

1. [警示] 面板會顯示在此解決方案中產生的警示。

2. 若要檢視警示的詳細資訊，請按一下 [警示] 面板中的警示。

3. 若要進一步分析警示資料，請按一下警示面板中的圖表，以開啟 Time Series Insights 總管環境。

4. 若要解決警示，可在 [警示] 面板中執行數個動作。 選擇適當的選項，然後按一下 [執行動作命令] 按鈕。

## <a name="view-overall-equipment-efficiency"></a>檢視整體設備效率

OEE 會使用重要生產相關作業參數來評比製造程序的效率。 OEE 是一種業界標準量值，其計算方式是將可用率、效能率和品質率相乘︰OEE = 可用項 x 效能 x 品質。

![連線處理站預先設定的解決方案 OEE][cf-img-oee]

1. 若要檢視階層中任何層級的 OEE，請瀏覽至您所需的特定檢視。 適用於該檢視的 OEE 會連同構成 OEE 百分比的每個元素顯示在面板中。

2. 若要進一步分析階層資料中任何層級的 OEE，請按一下 OEE、可用性、效能或品質百分比。 內容面板會以 Time Series Insights 支援的視覺效果呈現，其中顯示過去一小時、過去 24 小時和過去 7 天的資料。

    ![連線處理站預先設定的解決方案 TSI 視覺效果][cf-img-tsi-visualization]

3. 若要進一步分析警示資料，請按一下警示面板上的圖表。 這個動作會開啟 Time Series Insights 總管環境。

    ![連線處理站預先設定的解決方案 TSI 總管][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>檢視關鍵效能指標

此解決方案會提供兩個關鍵效能指標：「每小時單位數」和「所用能源 (kwh)」。

![連線處理站預先設定的解決方案 KPI][cf-img-kpi]

1. 若要檢視階層中任何層級的每小時單位數或所用能源，請瀏覽至您所需的特定檢視。 每小時單位數和所用能源會顯示在面板中。

2. 若要進一步分析階層中任何層級的每小時單位數和所用能源，請按一下 [關鍵效能指標] 面板中的量測計。 內容面板會以 Time Series Insights 支援的視覺效果呈現，可讓您檢視過去一小時、過去 24 小時和過去 7 天的資料。

## <a name="scenario-review"></a>案例檢閱

在此案例中，您會在儀表板中監視您的工廠 OEE 和 KPI 值。 接著使用 Time Series Insights 來提供詳細資訊，協助深入探詢 OEE 和 KPI 遙測資料，進而協助偵測異常狀況。 您也使用警示面板來檢視工廠問題，並使用可供您使用的動作來解決警示。

## <a name="other-features"></a>其他功能

下列各節說明連線的處理站解決方案的一些其他功能，這些功能在前一個情節中並未說明。

## <a name="apply-filters"></a>套用篩選條件

1. 按一下 [>形箭號] 可在 [處理站位置] 面板或 [警示] 面板中顯示可用的篩選條件清單。

2. [篩選條件] 面板隨即為您顯示。 

    ![連線處理站預先設定的解決方案篩選條件][cf-img-alert-filter]

3. 選擇您需要的篩選條件。 也可以在篩選欄位中輸入任意文字。

4. 接著會為您套用此篩選條件。 透過工廠和警示資料表中顯示的漏斗圖示，篩選條件狀態也會顯示在儀表板中。

    ![連線處理站預先設定的解決方案篩選條件][cf-img-alert-filter-funnel]

    > [!NOTE]
    > 使用中篩選條件不會影響已顯示的 OEE 和 KPI 值，它只會篩選清單內容。

5. 若要清除篩選條件，請按一下漏斗圖示並按一下篩選內容面板中的篩選條件。 [所有] 這兩個字會顯示在工廠和警示資料表中。

## <a name="browse-an-opc-ua-server"></a>瀏覽 OPC UA 伺服器

當您部署預先設定的解決方案時，會自動佈建您可透過解決方案瀏覽器瀏覽的模擬 OPC UA 伺服器。 這些伺服器是「模擬的 OPC UA 伺服器」。 模擬伺服器可讓您輕鬆對預先設定的解決方案進行實驗，而不需要部署真實的實體伺服器。 若要將真實 OPC UA 伺服器連線至解決方案，請參閱[將 OPC UA 裝置連線至連線處理站預先設定的解決方案][lnk-connect-cf]教學課程。

1. 按一下儀表板導覽列中的**處理站圖示**。

    ![連線處理站預先設定的解決方案伺服器瀏覽器][cf-img-server-browser]

2. 從預先設定的清單中選擇其中一部伺服器。 這份清單會顯示在預先設定的解決方案中為您部署的伺服器。

    ![連線處理站預先設定的解決方案伺服器選取][cf-img-server-choice]

3. 按一下 [連線]，[安全性] 對話方塊隨即顯示。 若要進行模擬，您可以放心按一下 [繼續]

4. 若要展開伺服器樹狀目錄中的任何節點，請按一下該節點。 正在發佈遙測的節點旁邊有勾號。

    ![連線處理站預先設定的解決方案伺服器樹狀目錄][cf-img-server-tree]

5. 以滑鼠右鍵按一下要讀取、寫入、發佈或呼叫該節點的項目。 您可使用的動作取決於您的節點權限和屬性。 讀取選項會顯示一個內容面板，以顯示特定節點的值。 寫入選項會顯示一個內容面板，您可以在此輸入新值。 呼叫選項會顯示一個節點，您可以在此輸入呼叫的參數。

## <a name="publish-a-node"></a>發佈節點

當您瀏覽*模擬的 OPC UA 伺服器*時，也可以選擇發佈新的節點。 您可以分析解決方案中這些節點的遙測資料。 這些「模擬的 OPC UA 伺服器」可讓您輕鬆對解決方案進行實驗，而不需要部署真實的實體裝置。

1. 瀏覽至 OPC UA 伺服器瀏覽器樹狀目錄中您想要發佈的節點。

2. 以滑鼠右鍵按一下節點。

3. 選擇 [發佈]。

    ![連線的處理站發佈節點][cf-img-publish-node]

4. 隨即顯示一個內容面板，告訴您發佈成功。 此節點會出現在站區層級檢視中，且旁邊有核取記號。

    ![連線處理站預先設定的解決方案發佈成功][cf-img-publish-success]

## <a name="command-and-control"></a>命令與控制

連線處理站可讓您直接從雲端指揮及控制您的工業裝置。 您可以使用這項功能來回應裝置所產生的警示。 例如，您可以從雲端將命令傳送到裝置。 您可以在 OPC UA 伺服器瀏覽器樹狀目錄的 **StationCommands** 節點中找到可用的命令。 在此情節中，您會開啟慕尼黑生產線的裝配站上的洩壓閥。 若要使用命令與控制功能，您必須具備預先設定解決方案部署的 [系統管理員] 角色。

1. 瀏覽至 OPC UA 伺服器瀏覽器樹狀目錄中的 **StationCommands** 節點。

2. 選擇您想要使用的命令。

3. 以滑鼠右鍵按一下節點。

4. 選擇 [呼叫]。

    ![連線處理站預先設定的解決方案呼叫命令][cf-img-call-command]

5. 隨即顯示一個內容面板，告知您即將呼叫的方法以及適用的任何參數詳細資料。

6. 選擇 [呼叫]。

    ![連線處理站預先設定的解決方案呼叫內容][cf-img-call-context]

7. 內容面板會進行更新，告知您方法呼叫成功。 您可以讀取因為呼叫而更新的壓力節點值，藉此確認呼叫成功。

    ![連線處理站預先設定的解決方案呼叫成功][cf-img-call-success]


## <a name="behind-the-scenes"></a>在幕後

當您部署預先設定的解決方案時，部署程序會在您選取的 Azure 訂用帳戶中建立多個資源。 您可以在 Azure [入口網站][lnk-portal]中檢視這些資源。 部署程序會建立 **資源群組** ，其名稱是以您為預先設定的解決方案選擇的名稱為基礎︰

![Azure 入口網站中的預先設定解決方案][img-cf-portal]

選取資源群組中的資源清單中的資源，即可檢視該資源的設定。

您也可以檢視預先設定的解決方案的原始程式碼。 連線處理站預先設定的解決方案原始程式碼位於 [azure-iot-connected-factory][lnk-cfgithub] GitHub 存放庫中：

完成之後，您可以從 [azureiotsuite.com][lnk-azureiotsuite] 網站上的 Azure 訂用帳戶中刪除預先設定解決方案。 這個網站可讓您輕鬆刪除在建立預先設定解決方案時已佈建的所有資源。

> [!NOTE]
> 若要確保您刪除與預先設定解決方案相關的所有項目，請在 [azureiotsuite.com][lnk-azureiotsuite] 網站上進行刪除。 請勿刪除入口網站中的資源群組。

## <a name="next-steps"></a>後續步驟

您現已部署運作中預先設定的解決方案，您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [連線處理站預先設定的解決方案逐步解說][lnk-rm-walkthrough]
* [將裝置連線至連線處理站預先設定的解決方案][lnk-connect-cf]
* [azureiotsuite.com 網站的權限][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md