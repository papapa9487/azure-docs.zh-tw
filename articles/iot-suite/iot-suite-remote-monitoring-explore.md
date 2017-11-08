---
title: "開始使用遠端監視解決方案 - Azure | Microsoft Docs"
description: "本教學課程使用模擬的情節來介紹遠端監視預先設定的解決方案。 當您第一次部署遠端監視預先設定的解決方案時，會建立這些情節。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 222050410bfc809991f03a452eb6640e5f9e1802
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-preconfigured-solution"></a>探索遠端監視預先設定解決方案的功能

本教學課程會示範遠端監視解決方案的主要功能。 為了介紹這些功能，本教學課程會示範常見的客戶情節，將模擬的 IoT 應用程式用於一間名為 Contoso 的公司。

教學課程可協助您了解遠端監視解決方案所提供現成可用的典型 IoT 情節。

在本教學課程中，您將了解如何：

>[!div class="checklist"]
> * 將儀表板上的裝置視覺化及篩選
> * 回應警示
> * 更新您裝置中的韌體
> * 組織資產

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。

如果您尚未部署遠端監視解決方案，應該先完成[部署遠端監視預先設定的解決方案](iot-suite-remote-monitoring-deploy.md)教學課程。

## <a name="the-contoso-sample-iot-deployment"></a>Contoso 範例 IoT 部署

您可以使用 Contoso 範例 IoT 部署來了解遠端監視解決方案所提供現成可用的基本情節。 這些情節會以實際 IoT 部署作為基礎。 您很有可能要選擇自訂遠端監視解決方案來符合特定需求，但是 Contoso 範例可協助您了解基本概念。

> [!NOTE]
> 如果您使用 CLI 來部署預先設定的解決方案，`deployment-{your deployment name}-output.json` 檔案就會包含部署的相關資訊，例如存取已部署範例的 URL。

Contoso 範例中會佈建一組模擬的裝置和規則以便採取行動。 一旦您了解基本情節後，就可以在[使用遠端監視解決方案執行進階裝置監視](iot-suite-remote-monitoring-monitor.md)中，繼續探索更多解決方案功能。

Contoso 是一間管理不同環境中各種資產的公司。 Contoso 計劃使用雲端式 IoT 應用程式的功能，從集中式應用程式遠端監視及管理多個資產。 下列各節提供 Contoso 範例的初始組態摘要：

> [!NOTE]
> Contoso 示範只是其中一種可用來佈建模擬裝置及建立規則的方式。 其他的佈建選項還包括建立您自行自訂的裝置。 若要深入了解如何建立您自己的裝置和規則，請參閱[管理及設定您的裝置](iot-suite-remote-monitoring-manage.md)和[使用臨界值型規則來偵測問題](iot-suite-remote-monitoring-automate.md)。

### <a name="contoso-devices"></a>Contoso 裝置

Contoso 會使用不同類型的智慧型裝置。 這些裝置能滿足公司的不同角色，並傳送各種遙測串流。 此外，每種裝置類型皆具有不同的裝置屬性和支援的方法。

下表顯示已佈建裝置類型的摘要：

| 裝置類型        | 遙測                                  | 屬性                                  | 標記                    | 方法                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| Chiller            | 溫度、溼度、壓力            | 類型、韌體版本、模型               | 位置、樓層、校園 | 重新開機、韌體更新、緊急閥釋放、增加壓力                          |
| 原型裝置 | 溫度、壓力、地理位置        | 類型、韌體版本、模型               | 位置、模式          | 重新開機、韌體更新、移動裝置、停止裝置、溫度釋放、溫度增加 |
| 引擎             | 槽燃料層級、冷卻劑溫度感應器、震動 | 類型、韌體版本、模型               | 位置、樓層、校園 | 重新啟動、韌體更新、空槽、填滿槽                                              |
| 卡車              | 地理位置、速度、貨物溫度     | 類型、韌體版本、模型               | 位置、負載          | 較低的貨物溫度、增加貨物溫度、韌體更新                         |
| 電梯           | 樓層、振動、溫度              | 類型、韌體版本、模型、地理位置 | 位置、校園        | 停止電梯、啟動電梯、韌體更新                                               |

> [!NOTE]
> Contoso 示範的範例每個類型會佈建兩個裝置。 針對每個類型，正確位於界限內的其中一個函式由 Contoso 定義為正常，另一個則有某種程度無法正常運作。 在下一節中，您會了解 Contoso 針對裝置定義的規則。 這些規則會定義正確行為的界限。

### <a name="contoso-rules"></a>Contoso 規則

Contoso 的操作員知道判斷裝置是否正常運作的臨界值。 例如，如果它報告的壓力大於 250 PSI，Chiller 就無法正常運作。 下表顯示 Contoso 針對每種裝置類型所定義之以臨界值作為基礎的規則：

| 規則名稱 | 說明 | 閾值 | 嚴重性 | 受影響的裝置 |
| --------- | ----------- | --------- | -------- | ---------------- |
| Chiller 壓力過高 | 如果 Chiller 觸達高於正常壓力層級就發出警示   |P>250 psi       | 重要 | Chillers            |
| 原型裝置溫度過高  | 如果原型裝置觸達高於正常溫度層級，就發出警示  |T>80&deg; F |重要 | 原型裝置 |
| 引擎槽空白  | 如果引擎油箱變成空白，就發出警示                     | F < 5 加侖 | 資訊     | 引擎             |
| 高於正常貨物溫度 | 如果卡車的貨物超過正常溫度，就發出警示                 | T<45&deg; F |警告  | 卡車              |
| 電梯震動停止      | 如果電梯完全停止 (以震動層級作為基礎)，就發出警示                     | V < 0.1 公釐 |警告  | 電梯           |

### <a name="operate-the-contoso-sample-deployment"></a>操作 Contoso 範例部署

您現在已看到 Contoso 範例中的初始設定。 下列章節會說明 Contoso 中的三個情節，闡述操作員可能會如何使用預先設定的解決方案。

## <a name="respond-to-a-pressure-alarm"></a>回應壓力警示

本情節會示範如何找出並回應由 chiller 裝置觸發的警示。 Chiller 位於 Redmond，43 大樓，2 樓。

身為操作員，您會在儀表板中看到與 chiller 壓力相關的警示。 您可以平移及放大地圖以查看詳細資料。

1. 在 [儀表板] 頁面的 [系統警示] 格線中，您可以看到 [Chiller 壓力過高] 警示。 Chiller 在地圖上也會反白顯示：

    ![儀表板會在地圖上顯示壓力警示和裝置](media/iot-suite-remote-monitoring-explore/dashboardalarm.png)

1. 若要檢視裝置詳細資料和遙測，請按一下地圖上反白顯示的 Chiller。 遙測會顯示壓力突然增加：

    ![選擇地圖上的裝置以檢視詳細資料](media/iot-suite-remote-monitoring-explore/dashboarddetail.png)

1. 關閉**裝置詳細資料**。

1. 若要瀏覽至 [維護] 頁面，請在警示格線中警示旁的 [探索警示] 資料行中按一下 [...]。

在 [維護] 頁面上，您可以檢視觸發 chiller 壓力警示的規則詳細資料。

1. 您可以看到已觸發警示的次數、通知，並開啟和關閉警示：

    ![[維護] 頁面會顯示已觸發警示的清單](media/iot-suite-remote-monitoring-explore/maintenancealarmlist.png)

1. 清單中的第一個警示是最新的。 按一下 [Chiller 壓力] 警示以檢視相關聯的裝置和遙測。 遙測會顯示 chiller 的壓力突然增加：

    ![[維護] 頁面會顯示所選警示的遙測](media/iot-suite-remote-monitoring-explore/maintenancetelemetry.png)

您現在已識別觸發警示和相關聯的裝置問題。 身為操作員，後續步驟是認知警示及解決問題。

1. 若要指示您目前在處理警示，請將 [警示狀態] 變更為 [已認知]：

    ![選取並認知警示](media/iot-suite-remote-monitoring-explore/maintenanceacknowledge.png)

1. 若要在 Chiller 上採取行動，請加以選取然後選擇 [排程]。 選取 **EmergencyValveRelease**、新增作業名稱 **ChillerPressureRelease**，然後選擇 [套用]。 這些設定會建立可立即執行的作業：

    ![選取裝置及排程動作](media/iot-suite-remote-monitoring-explore/maintenanceschedule.png)

1. 若要檢視作業狀態，請返回 [維護] 頁面，並在 [系統狀態] 檢視中檢視作業清單。 您可以看到已執行作業來釋放 Chiller 上的閥壓力：

    ![[系統狀態] 中的作業狀態](media/iot-suite-remote-monitoring-explore/maintenancerunningjob.png)

最後，請確認 Chiller 的遙測值回到正常。

1. 若要檢視警示格線，請瀏覽至 [儀表板] 頁面。

1. 若要檢視裝置遙測，請在地圖上選取原始警示的裝置，並確認已回復正常。

1. 若要關閉事件，請瀏覽至 [維護] 頁面，選取警示，然後將狀態設為 [已關閉]：

    ![選取並關閉警示](media/iot-suite-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>更新裝置的軔體

Contoso 會在現場測試裝置的新類型。 在測試週期內，您必須確定裝置的韌體更新正確運作。 下列步驟會示範如何使用遠端監視解決方案在多個裝置上更新韌體。

若要執行必要的裝置管理工作，請使用 [裝置] 頁面。 開始篩選所有原型的裝置：

1. 瀏覽至 [裝置] 頁面。 在 [篩選條件] 下拉式清單中選擇 [原型裝置] 篩選條件：

    ![使用 [裝置] 頁面上的篩選條件](media/iot-suite-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > 按一下 [管理] 以管理可用的篩選條件。

1. 選取其中一個原型裝置：

    ![選取 [裝置] 頁面上的裝置](media/iot-suite-remote-monitoring-explore/devicesselect.png)

1. 按一下 [排程] 按鈕，然後選擇 [韌體更新]。 輸入**作業名稱**和**韌體 URI** 的值。 選擇 [套用] 以排程作業立即執行：

    ![在裝置上排程韌體更新](media/iot-suite-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > 透過模擬的裝置，您可以使用任何所需的 URL 作為**韌體 URI** 值。 模擬的裝置不會存取 URL。

1. 請注意作業會影響多少裝置，然後選擇 [套用]：

    ![提交韌體更新作業](media/iot-suite-remote-monitoring-explore/devicessubmitupdate.png)

您可以使用 [維護] 頁面，在作業執行時加以追蹤。

1. 若要檢視作業清單，請瀏覽至 [維護] 頁面，然後按一下 [系統狀態]。

1. 找出與您所建立的作業相關的事件。 請確認已正確起始韌體更新程序。

您可以建立篩選條件，以確認韌體版本正確更新。

1. 若要建立篩選條件，請瀏覽至 [裝置] 頁面，然後選取 [管理]：

    ![管理裝置篩選條件](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. 建立篩選條件，當中僅包含具有新韌體版本的裝置：

    ![建立裝置篩選條件](media/iot-suite-remote-monitoring-explore/devicescreatefilter.png)

1. 返回 [裝置] 頁面，並確定裝置有新的韌體版本。

## <a name="organize-your-assets"></a>組織資產

Contoso 有兩個不同的小組會提供現場服務活動：

* Smart Building 小組會管理 chiller、電梯和引擎。
* Smart Vehicle 小組會管理卡車和原型裝置。

若要讓操作員能更輕鬆組織及管理您的裝置，您需要使用適當的小組名稱將它們標記。

您可以建立要與裝置搭配使用的標記名稱。

1. 若要顯示所有的裝置，請瀏覽至 [裝置] 頁面，然後選擇 [所有裝置] 篩選條件：

    ![顯示所有裝置](media/iot-suite-remote-monitoring-explore/devicesalldevices.png)

1. 選取**卡車**和**原型**裝置。 然後選擇 [標記]：

    ![選取原型和卡車裝置](media/iot-suite-remote-monitoring-explore/devicesmultiselect.png)

1. 選擇 [標記]，然後使用 **ConnectedVehicle** 值來建立名為 **FieldService** 的新字串標記。 選擇作業名稱。 然後，按一下 [套用]：

    ![將標記新增至原型和卡車裝置](media/iot-suite-remote-monitoring-explore/devicesaddtag.png)

1. 選取 **Chiller**、**電梯**和**引擎**裝置。 然後選擇 [標記]：

    ![選取 chiller、引擎及電梯裝置](media/iot-suite-remote-monitoring-explore/devicesmultiselect2.png)

1. 選擇 [標記]，然後使用 **SmartBuilding** 值來建立名為 **FieldService** 的新標記。 選擇作業名稱。 然後按一下 [儲存]：

    ![將標記新增至 chiller、引擎及電梯裝置](media/iot-suite-remote-monitoring-explore/devicesaddtag2.png)

您可以使用標記值來建立篩選條件。

1. 在 [裝置] 頁面上，選擇 [管理篩選條件]：

    ![管理裝置篩選條件](media/iot-suite-remote-monitoring-explore/devicesmanagefilters.png)

1. 建立使用標記名稱 **FieldService** 和 **SmartBuilding** 值的新篩選條件。 將篩選條件儲存為 [Smart Building]。

1. 建立使用標記名稱 **FieldService** 和 **ConnectedVehicle** 值的新篩選條件。 將篩選條件儲存為 [Connected Vehicle]。

現在，Contoso 操作員可以作業小組作為基礎查詢裝置，而不需要變更裝置上的任何項目。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您了解：

>[!div class="checklist"]
> * 將儀表板上的裝置視覺化及篩選
> * 回應警示
> * 更新您裝置中的韌體
> * 組織資產

既然您已探索遠端監視解決方案，建議的後續步驟是了解遠端監視解決方案的進階功能：

* [監視您的裝置](./iot-suite-remote-monitoring-monitor.md)。
* [管理您的裝置](./iot-suite-remote-monitoring-manage.md)。
* [使用規則將解決方案自動化](./iot-suite-remote-monitoring-automate.md)。
* [維護您的方案](./iot-suite-remote-monitoring-maintain.md)。
