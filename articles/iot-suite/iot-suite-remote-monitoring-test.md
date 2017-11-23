---
title: "遠端監視解決方案中的裝置模擬 - Azure | Microsoft Docs"
description: "本教學課程會示範如何使用裝置模擬器搭配遠端監視預先設定的解決方案。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 8b84b90e72f8cac1fc1f8a90391b7a5a4f6be1f4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>使用模擬裝置來測試您的解決方案

本教學課程會示範如何使用自訂裝置模擬器微服務搭配遠端監視預先設定的解決方案。 為說明裝置模擬器的功能，本教學課程會使用 Contoso IoT 應用程式中的兩個情節。

在第一個情節中，Contoso 需要測試新的智慧型燈泡裝置。 若要執行測試，您可以建立具有下列特性的新模擬裝置：

*屬性*

| 名稱                     | 值                      |
| ------------------------ | --------------------------- |
| 色彩                    | 白色、紅色、藍色            |
| 亮度               | 0 到 100                    |
| 估計的剩餘時間 | 10,000 個小時倒數計時 |

*遙測*

下表會顯示燈泡向雲端報告作為資料流的資料：

| 名稱   | 值      |
| ------ | ----------- |
| 狀態 | 「開啟」、「關閉」 |
| 線上 | true、false |

> [!NOTE]
> **線上**遙測值會強制用於所有模擬類型。

*方法*

下表會顯示新裝置所支援的動作：

| 名稱        |
| ----------- |
| 開啟   |
| 關閉  |

*初始狀態*

下表會顯示裝置的初始狀態：

| 名稱                     | 值 |
| ------------------------ | -------|
| 初始色彩            | 白色  |
| 初始亮度       | 75     |
| 初始剩餘時間   | 10,000 |
| 初始遙測狀態 | 「開啟」   |

在第二個情節中，您要將新的遙測類型新增至 Contoso 的現有 **Chiller** 裝置。

本教學課程會示範如何使用裝置模擬器搭配遠端監視預先設定的解決方案：

在本教學課程中，您將了解如何：

>[!div class="checklist"]
> * 建立新的裝置類型
> * 模擬自訂裝置行為
> * 將新的裝置類型新增至儀表板
> * 從現有的裝置類型傳送自訂遙測

## <a name="prerequisites"></a>必要條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。

如果您尚未部署遠端監視解決方案，就應該先完成[部署遠端監視預先設定的解決方案](iot-suite-remote-monitoring-deploy.md)教學課程。

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>裝置模擬服務

預先設定解決方案中的裝置模擬服務，可讓您變更內建的模擬裝置類型，並建立新的模擬裝置類型。 在將實體裝置連線到解決方案之前，您可以使用自訂的裝置類型來測試遠端監視解決方案的行為。

## <a name="create-a-simulated-device-type"></a>建立模擬裝置類型

在模擬微服務中建立新裝置類型最簡單的方式，是複製現有的類型並加以修改。 下列步驟會示範如何複製內建 **Chiller** 裝置來建立新的 **Lightbulb** 裝置：

1. 使用下列命令，將 **device-simulation** GitHub 存放庫複製到本機電腦：

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. 每種裝置類型在 `Services/data/devicemodels` 資料夾中都具有 JSON 模型檔和相關聯的指令碼。 複製 **Chiller** 檔案，以建立 **Lightbulb** 檔案，如下表所示：

    | 來源                      | 目的地                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>定義新裝置類型的特性

`lightbulb-01.json` 檔案會定義類型的特性，例如它所產生的遙測和它所支援的方法。 下列步驟會更新 `lightbulb-01.json` 檔案來定義 **Lightbulb** 裝置：

1. 在 `lightbulb-01.json` 檔案中，更新裝置中繼資料，如下列程式碼片段所示：

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. 在 `lightbulb-01.json` 檔案中，更新模擬定義，如下列程式碼片段所示：

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. 在 `lightbulb-01.json` 檔案中，更新裝置類型屬性，如下列程式碼片段所示：

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. 在 `lightbulb-01.json` 檔案中，更新裝置類型遙測定義，如下列程式碼片段所示：

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. 在 `lightbulb-01.json` 檔案中，更新裝置類型方法，如下列程式碼片段所示：

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. 儲存 `lightbulb-01.json` 檔案。

### <a name="simulate-custom-device-behavior"></a>模擬自訂裝置行為

`scripts/lightbulb-01-state.js` 檔案會定義 **Lightbulb** 類型的模擬行為。 下列步驟會更新 `scripts/lightbulb-01-state.js` 檔案來定義 **Lightbulb** 裝置的行為：

1. 在 `scripts/lightbulb-01-state.js` 檔案中編輯狀態定義，如下列程式碼片段所示：

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. 將 **vary** 函式取代為下列 **flip** 函式：

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 編輯 **main** 函式來實作行為，如下列程式碼片段所示：

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. 儲存 `scripts/lightbulb-01-state.js` 檔案。

`scripts/SwitchOn-method.js` 檔案會在 **Lightbulb** 裝置中實作**開啟**方法。 下列步驟會更新 `scripts/SwitchOn-method.js` 檔案：

1. 在 `scripts/SwitchOn-method.js` 檔案中編輯狀態定義，如下列程式碼片段所示：

    ```js
    var state = {
       status: "on"
    };
    ```

1. 若要開啟燈泡，請編輯 **main** 函式，如下所示：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. 儲存 `scripts/SwitchOn-method.js` 檔案。

1. 複製稱為 `scripts/SwitchOff-method.js` 的 `scripts/SwitchOn-method.js` 檔案。

1. 若要關閉燈泡，請編輯 `scripts/SwitchOff-method.js` 檔案中的 **main** 函式，如下所示：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. 儲存 `scripts/SwitchOff-method.js` 檔案。

### <a name="test-the-lightbulb-device-type"></a>測試燈泡裝置類型

若要測試 **Lightbulb** 裝置類型，可以先測試您行為如預期的裝置類型，方法為執行 **device-simulation** 服務的本機副本。 當您已在本機上測試並偵錯您的新裝置類型時，可以重建容器，並將 **device-simulation** 服務重新部署至 Azure。

若要在本機測試及偵錯您的變更，請參閱[裝置模擬概觀](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)。

設定專案，以將新的 **Lightbulb** 裝置檔案複製到輸出目錄：

* 如果您使用 Visual Studio，務必將您在上一節中建立的三個新 lightbulb 檔案新增到方案中的 [服務] 專案。 然後使用 [方案總管] 將它們標示為要複製到輸出目錄。

* 如果您使用 Visual Studio Code，請開啟 **Services.csproj** 檔案，然後新增您在上一節中建立的三個新 lightbulb 檔案。 請參閱 **Services.csproj** 檔案中的現有裝置模型檔案項目作為範例。

若要在已部署的解決方案中測試新的裝置，請參閱以下其中之一：

* [從自訂 docker-hub 帳戶部署容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [透過手動複製更新已部署的容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

在 [裝置] 頁面上，您可以佈建您新類型的執行個體：

![檢視可用模擬的清單](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

您可以從模擬裝置檢視遙測：

![檢視燈泡遙測](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

您可以在裝置上呼叫 **SwitchOn** 和 **SwitchOff** 方法：

![呼叫燈泡方法](media/iot-suite-remote-monitoring-test/devicesmethods.png)

若要使用部署的新裝置類型將 Docker 映像建置到 Azure，請參閱[建置自訂的 Docker 映像](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)。

## <a name="add-a-new-telemetry-type"></a>新增遙測類型

本章節會說明如何修改現有的模擬裝置類型，以支援新的遙測類型。

### <a name="locate-the-chiller-device-type-files"></a>找出 Chiller 裝置類型檔案

下列步驟會示範如何找出定義內建 **Chiller** 裝置的檔案：

1. 如果您尚未這樣做，使用下列命令，將 **device-simulation** GitHub 存放庫複製到本機電腦：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 每種裝置類型在 `data/devicemodels` 資料夾中都具有 JSON 模型檔和相關聯的指令碼。 定義模擬 **Chiller** 裝置類型的檔案為：
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>指定新的遙測類型

下列步驟會示範如何將新的**內部溫度**類型新增至 **Chiller** 裝置類型：

1. 開啟 `chiller-01.json` 檔案。

1. 更新 **SchemaVersion** 值，如下所示：

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. 在 **InitialState** 區段中，新增下列兩個定義：

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. 在**遙測** 陣列中，新增下列定義：

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. 儲存 `chiller-01.json` 檔案。

1. 開啟 `scripts/chiller-01-state.js` 檔案。

1. 將下列欄位新增至 **state** 變數：

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. 將下列字行新增至 **main** 函式：

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. 儲存 `scripts/chiller-01-state.js` 檔案。

### <a name="test-the-chiller-device-type"></a>測試 Chiller 裝置類型

若要測試 **Chiller** 裝置類型，可以先測試您行為如預期的裝置類型，方法為執行 **device-simulation** 服務的本機副本。 當您已在本機上測試並偵錯您更新的裝置類型時，可以重建容器，並將 **device-simulation** 服務重新部署至 Azure。

當您在本機執行 **device-simulation** 服務時，它會將遙測傳送至遠端監視解決方案。 在 [裝置] 頁面上，您可以佈建您更新類型的執行個體。

若要在本機測試及偵錯您的變更，請參閱[使用 Visual Studio 執行服務](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio)或[從命令列建置和執行](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line)。

若要在已部署的解決方案中測試新的裝置，請參閱以下其中之一：

* [從自訂 docker-hub 帳戶部署容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [透過手動複製更新已部署的容器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

在 [裝置] 頁面上，您可以佈建您更新類型的執行個體：

![新增更新的 Chiller](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

您可以從模擬裝置檢視新的**內部溫度**遙測。

若要使用部署的新裝置類型將 Docker 映像建置到 Azure，請參閱[建置自訂的 Docker 映像](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)。

## <a name="next-steps"></a>後續步驟

本教學課程已示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 建立新的裝置類型
> * 模擬自訂裝置行為
> * 將新的裝置類型新增至儀表板
> * 從現有的裝置類型傳送自訂遙測

現在您已了解如何使用裝置模擬服務，建議的下一個步驟是了解如何[將實體裝置連線到遠端監視解決方案](iot-suite-connecting-devices-node.md)。

如需關於遠端監視解決方案的開發人員詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->