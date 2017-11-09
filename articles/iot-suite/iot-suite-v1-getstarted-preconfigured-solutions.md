---
title: "開始使用預先設定解決方案 | Microsoft Docs"
description: "遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>開始使用預先設定的解決方案

Azure IoT 套件[預先設定的解決方案][lnk-preconfigured-solutions]結合了多項 Azure IoT 服務，以提供可實作常見 IoT 商務案例的端對端解決方案。 *遠端監視* 預先設定的解決方案會連接並監視您的裝置。 您可以使用此解決方案分析裝置的資料串流，並藉由讓處理程序自動回應該資料串流來提升業務績效。

本教學課程示範如何佈建遠端監視預先設定的解決方案。 其中也逐步說明預先設定解決方案的基本功能。 您可以從隨預先設定解決方案一起部署的解決方案儀表板中，存取其中許多功能：

![遠端監視預先設定解決方案儀表板][img-dashboard]

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

> [!NOTE]
> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>案例概觀

當您部署遠端監視預先設定解決方案時，它會預先填入資源，可讓您逐步執行常見的遠端監視案例。 在此案例中，連線至解決方案的數個裝置報告未預期的溫度值。 下列各節將示範如何：

* 識別傳送未預期溫度值的裝置。
* 設定這些裝置來傳送更多詳細遙測。
* 藉由更新這些裝置上的韌體來修正問題。
* 確認您的動作已經解決問題。

此案例的主要功能是您可以從解決方案儀表板遠端執行所有這些動作。 您不需要實際存取裝置。

## <a name="view-the-solution-dashboard"></a>檢視解決方案儀表板

解決方案儀表板可讓您管理部署的解決方案。 例如，您可以檢視遙測、新增裝置及設定規則。

1. 當佈建完成且預先設定解決方案的圖格指示 [就緒] 時，選擇 [啟動] 以在新的索引標籤中開啟遠端監視解決方案入口網站。

    ![啟動預先設定的解決方案][img-launch-solution]

1. 根據預設，此解決方案入口網站會顯示儀表板。 您可以使用頁面左側的功能表，瀏覽至解決方案入口網站的其他區域。

    ![遠端監視預先設定解決方案儀表板][img-menu]

儀表板會顯示下列資訊：

* 地圖會顯示每個裝置連線至解決方案的位置。 當您第一次執行解決方案時，有 25 個模擬裝置。 模擬裝置會實作為 Azure WebJobs，而解決方案會使用 Bing 地圖 API 在地圖上繪製資訊。 請參閱[常見問題集][lnk-faq]以了解如何讓地圖成為動態。
* [遙測歷程記錄] 面板會從所選的裝置繪製近乎即時的溼度和溫度遙測，並顯示彙總資料，例如最大、最小和平均溼度。
* [警示歷程記錄] 面板會在遙測值超過臨界值時顯示最近的警示事件。 除了預先設定的解決方案所建立的範例以外，您可以定義自己的警示。
* [作業] 面板顯示已排程作業的相關資訊。 您可以在 [管理作業] 頁面排程您自己的作業。

## <a name="view-alarms"></a>檢視警示

[警示歷程記錄] 面板會顯示五個裝置，這些裝置報告的值高於預期的遙測值。

![解決方案儀表板上的 TODO 警示歷程記錄][img-alarms]

> [!NOTE]
> 這些警示是由包含在預先設定解決方案中的規則所產生。 當裝置傳送的溫度值超過 60 時，此規則會產生警示。 您可以選擇左側功能表中的[規則](#add-a-rule)和[動作](#add-an-action)，定義您自己的規則和動作。

## <a name="view-devices"></a>檢視裝置

裝置清單會顯示解決方案中所有已註冊的裝置。 從裝置清單中，您可以檢視和編輯裝置中繼資料、新增或移除裝置，並在裝置上叫用方法。 您可以篩選並排序裝置清單中的裝置。 您也可以自訂裝置清單中顯示的資料行。

1. 選擇 [裝置] 以顯示此解決方案的裝置清單。

   ![在解決方案入口網站中檢視裝置清單][img-devicelist]

1. 裝置清單最初會顯示佈建程序所建立的 25 個模擬裝置。 您可以將其他模擬裝置和實體裝置新增至解決方案。

1. 若要檢視裝置的詳細資料，選擇裝置清單中的裝置。

   ![在解決方案入口網站中檢視裝置詳細資料][img-devicedetails]

[裝置詳細資料] 面板包含六個區段：

* 一組連結，可讓您自訂裝置圖示、停用裝置、新增規則、叫用方法，或傳送命令。 如需命令 (裝置對雲端的訊息) 和方法 (直接方法) 的比較，請參閱[雲端對裝置的通訊指引][lnk-c2d-guidance]。
* [裝置對應項 - 標籤] 區段可讓您編輯裝置的標籤值。 您可以在裝置清單中顯示標籤值，並使用標籤值來篩選裝置清單。
* [裝置對應項 - 所需屬性] 區段可讓您設定要傳送至裝置的屬性值。
* [裝置對應項 - 回報屬性] 區段顯示從裝置傳來的屬性值。
* [裝置屬性] 區段顯示身分識別登錄中的資訊，例如裝置識別碼和驗證金鑰。
* [最近的作業] 區段顯示最近針對這個裝置執行之任何作業的相關資訊。

## <a name="filter-the-device-list"></a>篩選裝置清單

您可以使用篩選，僅顯示會傳送未預期溫度值的裝置。 遠端監視預先設定解決方案包括 [狀況不良裝置]篩選，來顯示平均溫度值大於 60 的裝置。 您也可以[建立自己的篩選](#add-a-filter)。

1. 選擇 [開啟儲存的篩選] 以顯示可用篩選的清單。 然後選擇 [狀況不良裝置] 以套用篩選︰

    ![顯示篩選的清單][img-unhealthy-filter]

1. 裝置清單現在僅會顯示平均溫度值大於 60 的裝置。

    ![檢視已篩選裝置清單，其中顯示狀況不良裝置][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>更新所需屬性

您現在已識別一組裝置，可能需要修復。 不過，您決定 15 秒資料頻率不足以進行問題的清楚診斷。 將遙測頻率變更為五秒，為您提供更多資料點，以更佳地診斷問題。 您可以從解決方案入口網站將這個組態變更推送至遠端裝置。 您可以進行變更一次、評估影響，然後處理結果。

請遵循這些步驟來執行作業，以變更受影響裝置的 **TelemetryInterval** 所需屬性。 當裝置收到新的 **TelemetryInterval** 屬性值，它們會變更其組態，每 5 秒 (而不是每 15 秒) 傳送遙測︰

1. 當您顯示裝置清單中的狀況不良裝置時，選擇 [作業排程器]，然後選擇 [編輯裝置對應項]。

1. 呼叫作業**變更遙測間隔**。

1. 將**所需屬性**名稱 **desired.Config.TelemetryInterval** 的值變更為 5 秒。

1. 選擇 [排程]。

    ![將 TelemetryInterval 屬性變更為 5 秒][img-change-interval]

1. 您可以在入口網站中的 [管理作業] 頁面上，監視作業的進度。

> [!NOTE]
> 如果您想要變更個別裝置的所需屬性值，使用**裝置詳細資料**面板中的 [所需屬性] 區段，而不是執行作業。

此作業會針對篩選選取的所有裝置，在裝置對應項中設定 **TelemetryInterval** 所需屬性的值。 裝置會從裝置對應項擷取此值，並且更新它們的行為。 當裝置從裝置對應項擷取並處理所需屬性時，它會設定對應的報告值屬性。

## <a name="invoke-methods"></a>叫用方法

作業執行時，您注意到在狀況不良裝置清單中，所有這些裝置都有舊的 (低於 1.6 版) 韌體版本。

![檢視狀況不良裝置報告的韌體版本][img-old-firmware]

此韌體版本可能是未預期溫度值的根本原因，因為您知道其他狀況良好的裝置最近已更新至 2.0 版。 您可以使用內建**舊版韌體裝置**篩選來識別具有舊版韌體版本的任何裝置。 然後，您可以從入口網站遠端更新仍在執行舊版韌體版本的所有裝置︰

1. 選擇 [開啟儲存的篩選] 以顯示可用篩選的清單。 然後選擇 [舊版韌體裝置] 以套用篩選︰

    ![顯示篩選的清單][img-old-filter]

1. 裝置清單現在僅會顯示具有舊版韌體版本的裝置。 這份清單包含由**狀況不良裝置**篩選所識別的五個裝置和三個額外的裝置︰

    ![檢視已篩選裝置清單，其中顯示舊的裝置][img-filtered-old-list]

1. 選擇 [作業排程器]，然後選擇 [叫用方法]。

1. 將 [作業名稱] 設定為**韌體更新為 2.0 版**。

1. 選擇 **InitiateFirmwareUpdate** 做為 [方法]。

1. 將 **FwPackageUri** 參數設定為 **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**。

1. 選擇 [排程]。 預設值適用於要立即執行的作業。

    ![建立作業以更新所選取裝置的韌體][img-method-update]

> [!NOTE]
> 如果您想要在個別裝置上叫用方法，選擇 [裝置詳細資料] 面板中的 [方法]，而不是執行作業。

這項作業會在篩選所選取的所有裝置上叫用 **InitiateFirmwareUpdate** 直接方法。 裝置會立即回應 IoT 中樞，然後以非同步方式初始韌體更新處理。 裝置透過報告屬性值提供韌體更新處理的狀態資訊，如下列螢幕擷取畫面所示。 選擇 [重新整理] 圖示，以更新裝置和作業清單中的資訊︰

![顯示韌體更新清單正在執行的作業清單][img-update-1]
![顯示韌體更新狀態的裝置清單][img-update-2]
![顯示韌體更新清單完成的作業清單][img-update-3]

> [!NOTE]
> 在生產環境中，您可以排程作業在指定的維護期間執行。

## <a name="scenario-review"></a>案例檢閱

在此案例中，您使用儀表板和篩選上的警示歷程記錄來識別某些遠端裝置的潛在問題。 然後您會使用篩選和作業來遠端設定裝置，以提供詳細資訊來協助診斷問題。 最後，您會使用篩選和作業，來排程受影響裝置的維護。 如果您返回儀表板，您可以檢查解決方案中不再有來自裝置的任何警示。 您可以使用篩選以確認解決方案中所有裝置上的韌體都是最新的，而且沒有狀況不良裝置：

![篩選會顯示所有裝置具有最新版本的韌體][img-updated]

![篩選會顯示所有裝置狀況良好][img-healthy]

## <a name="other-features"></a>其他功能

下列各節說明遠端監視預先設定解決方案的一些其他功能，未說明為先前案例的一部分。

### <a name="customize-columns"></a>自訂資料行

您可以選擇 [資料行編輯器]，自訂裝置清單中顯示的資訊。 資料行顯示回報屬性和標籤值，您可以新增和移除這些資料行。 您也可以重新排列及重新命名資料行︰

   ![裝置清單上的資料行編輯器][img-columneditor]

### <a name="customize-the-device-icon"></a>自訂裝置圖示

您可以從 [裝置詳細資料] 面板，自訂裝置清單中顯示的裝置圖示，如下所示︰

1. 按一下鉛筆圖示，開啟裝置的 [編輯映像] 面板︰

   ![開啟裝置影像編輯器][img-startimageedit]

1. 上傳新的映像或使用其中一個現有的映像，然後選擇 [儲存]：

   ![編輯裝置影像編輯器][img-imageedit]

1. 您選取的影像現在顯示在裝置的 [圖示]資料行。

> [!NOTE]
> 影像儲存在 Blob 儲存體中。 裝置對應項中的標籤包含 Blob 儲存體中的影像連結。

### <a name="add-a-device"></a>新增裝置

當您部署預先設定的解決方案時，會自動佈建您在裝置清單中可見的 25 個範例裝置。 這些裝置是在 Azure WebJob 中執行的 *模擬裝置* 。 模擬裝置讓您可以輕鬆對預先設定的解決方案進行實驗，而不需要部署真實的實體裝置。 若要將真實裝置連接至解決方案，請參閱[將裝置連接至遠端監視預先設定解決方案][lnk-connect-rm]教學課程。

下列步驟顯示如何將模擬裝置新增至解決方案︰

1. 瀏覽回到裝置清單。

1. 若要新增裝置，請選擇左下角的 [+ 新增裝置]。

   ![將裝置新增到預先設定的解決方案][img-adddevice]

1. 選擇 [模擬裝置] 圖格上的 [新增]。

   ![在儀表板中設定新的裝置詳細資料][img-addnew]

   如果您選擇建立 **自訂裝置**，則除了建立新的模擬裝置，也可以新增實體裝置。 如需將實體裝置連接至解決方案的詳細資訊，請參閱[將裝置連接至 IoT 套件遠端監視預先設定解決方案][lnk-connect-rm]。

1. 選取 [自行定義裝置識別碼]，然後輸入唯一的裝置識別碼名稱，例如 **mydevice_01**。

1. 選擇 [建立] 。

   ![儲存新的裝置][img-definedevice]

1. 在 [新增模擬裝置] 的步驟 3 中，選擇 [完成] 以返回裝置清單。

1. 您可以在裝置清單中檢視 [執行中]  裝置。

    ![檢視裝置清單中的新裝置][img-runningnew]

1. 您也可以在儀表板上檢視來自新裝置的模擬遙測︰

    ![從新裝置檢視遙測][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>停用並刪除裝置

您可以停用裝置，並且在已停用之後移除：

![停用並移除裝置][img-disable]

### <a name="add-a-rule"></a>新增規則

您剛才加入的新裝置沒有規則。 在本節中，您會新增一個規則，以便在新裝置所報告的溫度超過 47 度時觸發警示。 在開始之前，請注意儀表板上新裝置的遙測歷程記錄顯示裝置溫度絕不會超出 45 度。

1. 瀏覽回到裝置清單。

1. 若要新增裝置的規則，請在 [裝置清單] 中選取您的新裝置，然後選擇 [新增規則]。

1. 建立規則，其使用**溫度**做資料欄位，以及使用 **AlarmTemp** 做為溫度超過 47 度時的輸出︰

    ![新增裝置規則][img-adddevicerule]

1. 若要儲存變更，請選擇 [儲存並檢視規則]。

1. 選擇新裝置之裝置詳細資料窗格中的 [命令]。

    ![新增裝置規則][img-adddevicerule2]

1. 從命令清單中選取 **ChangeSetPointTemp** 並將 **SetPointTemp** 設定為 45。 然後選擇 [傳送命令]：

    ![新增裝置規則][img-adddevicerule3]

1. 瀏覽回儀表板。 一小段時間之後，您會在新裝置報告溫度超過 47 度臨界值時，在 [警示歷程記錄]  窗格中看到新的項目︰

    ![新增裝置規則][img-adddevicerule4]

1. 您可以在儀表板的 **規則** 頁面上檢閱和編輯所有規則：

    ![列出裝置規則][img-rules]

1. 您可以在儀表板的 **動作** 頁面上檢閱和編輯為了回應規則而可採取的所有動作︰

    ![列出裝置動作][img-actions]

> [!NOTE]
> 您可以透過[邏輯應用程式][lnk-logic-apps]定義可傳送電子郵件或簡訊的動作，以回應規則或與特定業務系統進行整合。 如需詳細資訊，請參閱[將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案][lnk-logicapptutorial]。

### <a name="manage-filters"></a>管理篩選

在裝置清單中，您可以建立、儲存和載入篩選，以顯示連接至中樞的自訂裝置清單。 若要建立篩選：

1. 選擇裝置清單上方的編輯篩選圖示︰

    ![開啟篩選編輯器][img-editfiltericon]

1. 在 [篩選編輯器] 中，新增欄位、運算子和值來篩選裝置清單。 您可以新增多個子句來精簡篩選。 選擇 [篩選] 來套用篩選︰

    ![建立篩選][img-filtereditor]

1. 在此範例中，清單依製造商和型號篩選︰

    ![篩選的清單][img-filterelist]

1. 若要以自訂名稱儲存篩選，請選擇 [另存新檔] 圖示︰

    ![新增篩選][img-savefilter]

1. 若要重新套用您先前儲存的篩選，請選擇 [開啟儲存的篩選] 圖示︰

    ![開啟篩選][img-openfilter]

您可以根據裝置識別碼、裝置狀態、所需屬性、回報屬性和標籤，以建立篩選。 您將您自己的自訂標籤新增至 [裝置詳細資料] 面板上 [標籤] 區段中的裝置，或者執行作業以在多部裝置上更新標籤。

> [!NOTE]
> 在 [篩選編輯器] 中，您可以使用 [進階檢視]，直接編輯查詢文字。

### <a name="commands"></a>命令

從 [裝置詳細資料] 面板中，您可以將命令傳送至裝置。 在裝置第一次啟動時，便會傳送其支援的命令相關資訊給解決方案。 如需命令和方法之間差異的討論，請參閱 [Azure IoT 中樞雲端對裝置選項][lnk-c2d-guidance]。

1. 在所選裝置的 [裝置詳細資料] 面板中，選擇 [命令]：

   ![儀表板中的裝置命令][img-devicecommands]

1. 選取命令清單中的 [PingDevice]  。

1. 選擇 [傳送命令]。

1. 您可以在命令歷程記錄中看見命令的狀態。

   ![儀表板中的命令狀態][img-pingcommand]

解決方案會追蹤其傳送的每個命令的狀態。 結果最初是 **處理中**。 當裝置報告它已執行命令時，結果會設定為 **成功**。

## <a name="behind-the-scenes"></a>在幕後

當您部署預先設定的解決方案時，部署程序會在您選取的 Azure 訂用帳戶中建立多個資源。 您可以在 Azure [入口網站][lnk-portal]中檢視這些資源。 部署程序會建立 **資源群組** ，其名稱是以您為預先設定的解決方案選擇的名稱為基礎︰

![Azure 入口網站中的預先設定解決方案][img-portal]

選取資源群組中的資源清單中的資源，即可檢視該資源的設定。

您也可以檢視預先設定的解決方案的原始程式碼。 遠端監視預先設定的解決方案原始程式碼位於 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 儲存機制：

* **DeviceAdministration** 資料夾包含儀表板的原始程式碼。
* **Simulator** 資料夾包含模擬裝置的原始程式碼。
* **EventProcessor** 資料夾包含後端程序的原始程式碼，可用於處理內送的遙測。

完成之後，您可以從 [azureiotsuite.com][lnk-azureiotsuite] 網站上的 Azure 訂用帳戶中刪除預先設定解決方案。 這個網站可讓您輕鬆刪除在建立預先設定解決方案時已佈建的所有資源。

> [!NOTE]
> 若要確保您刪除與預先設定解決方案相關的所有項目，請在 [azureiotsuite.com][lnk-azureiotsuite] 網站上進行刪除，而不是刪除入口網站中的資源群組。

## <a name="next-steps"></a>後續步驟

您現已部署運作中預先設定的解決方案，您可以繼續閱讀下列文章，了解如何開始使用 IoT 套件︰

* [遠端監視預先設定解決方案逐步解說][lnk-rm-walkthrough]
* [將裝置連接至遠端監視預先設定方案][lnk-connect-rm]
* [azureiotsuite.com 網站的權限][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md