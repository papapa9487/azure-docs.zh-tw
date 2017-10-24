---
title: "使用 Node.js 建立 Azure IoT Edge 模組 | Microsoft 文件"
description: "本教學課程示範如何使用最新的 Azure IoT Edge NPM 套件和 Yeoman 產生器撰寫 BLE 資料轉換器模組。"
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: e23c4aa7bb3eb4fab18d5a13cbad28e07c18d8f2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>使用 Node.js 建立 Azure IoT Edge 模組

本教學課程示範如何使用 JS 建立 Azure IoT Edge 的模組。

在本教學課程中，我們會逐步說明環境設定，以及如何使用最新的 Azure IoT Edge NPM 套件撰寫 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 資料轉換器模組。

## <a name="prerequisites"></a>必要條件

在本節中，您會針對 IoT Edge 模組設定您的環境。 它同時適用於「64 位元 Windows」和「64 位元 Linux (Ubuntu 14+)」作業系統。

需要下列軟體：
* [Git 用戶端](https://git-scm.com/downloads)。
* [Node LTS](https://nodejs.org)。
* `npm install -g yo`。
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>架構

Azure IoT Edge 平台大量採用 [Von Neumann 架構](https://en.wikipedia.org/wiki/Von_Neumann_architecture)。 這表示整個 Azure IoT Edge 架構是用來處理輸入及產生輸出的系統，而每個個別模組也是小型輸入-輸出子系統。 在本教學課程中，我們會介紹下列兩個模組︰

1. 可接收模擬 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 訊號，並將它轉換為格式化 [JSON](https://en.wikipedia.org/wiki/JSON) 訊息的模組。
2. 可列印已接收 [JSON](https://en.wikipedia.org/wiki/JSON) 訊息的模組。

下圖顯示這個專案的典型端對端資料流程：

![三個模組之間的資料流程](media/iot-hub-iot-edge-create-module/dataflow.png "輸入：模擬 BLE 模組；處理器：轉換器模組；輸出：印表機模組")

## <a name="set-up-the-environment"></a>設定 Azure 環境
我們在下面示範如何快速設定環境，以便開始使用 JS 撰寫第一個 BLE 轉換器模組。

### <a name="create-module-project"></a>建立模組專案
1. 開啟命令列視窗，執行 `yo az-iot-gw-module`。
2. 遵循畫面上的步驟，完成模組專案的初始化。

### <a name="project-structure"></a>專案結構
JS 模組專案由下列元件組成：

`modules` - 自訂的 JS 模組原始程式檔。 以您自己的模組檔案取代預設 `sensor.js` 和 `printer.js`。

`app.js` - 用來啟動 Edge 執行個體的輸入檔案。

`gw.config.json` - 自訂由 Edge 所要載入之模組的組態檔。

`package.json` - 模組專案的中繼資料資訊。

`README.md` - 模組專案的基本文件。


### <a name="package-file"></a>套件檔案

此 `package.json` 會宣告模組專案需要的所有中繼資料資訊，包括名稱、版本、項目、指令碼、執行階段及開發相依性。

下列程式碼片段示範如何設定 BLE 轉換器範例專案。
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>輸入檔案
`app.js` 定義初始化邊緣執行個體的方式。 我們在此不需要進行任何變更。

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>模組的介面
您可以將 Azure IoT Edge 模組視為資料處理器，其作業是：接收輸入、加以處理，以及產生輸出。

輸入可能是來自硬體 (例如動作偵測器) 的資料、來自其他模組的訊息，或任何其他資料 (例如計時器定期產生的隨機數字)。

輸出類似於輸入，它可能會觸發硬體行為 (例如閃爍的 LED)、給其他模組的訊息，或任何其他作業 (例如列印至主控台)。

模組會使用 `message` 物件彼此通訊。 `message` 的 [內容] 是一個位元組陣列，其可代表任一種您想要的資料。 [屬性] 也適用於 `message`，且只是字串與字串的對應。 您可以將**屬性**想成是 HTTPS 要求中的標頭或檔案的中繼資料。

若要使用 JS 開發 Azure IoT Edge 模組，您必須建立新的模組物件，以實作必要的方法 `receive()`。 此時，您也可以選擇實作選擇性 `create()``start()` 或 `destroy()` 方法。 下列程式碼片段顯示 JS 模組物件的 Scaffolding。

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>轉換器模組
| 輸入                    | 處理器                              | 輸出                 | 來源檔案            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 溫度資料訊息 | 剖析並建構新的 JSON 訊息 | 建構 JSON 訊息 | `converter.js` |

此模組是典型的 Azure IoT Edge 模組。 它可接受來自其他模組 (硬體模組，或此情況下我們的模擬 BLE 模組) 的溫度訊息，然後將溫度訊息正常化成為結構化 JSON 訊息 (包括附加訊息識別碼、設定我們是否需要觸發溫度警示的屬性等等)。

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>印表機模組
| 輸入                          | 處理器 | 輸出                     | 來源檔案          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 來自其他模組的任何訊息 | N/A       | 將訊息記錄至主控台 | `printer.js` |

這是簡單、不言而喻的模組，它會將收到的訊息 (屬性、內容) 輸出至終端機視窗。

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>組態
執行模組之前的最後一個步驟是設定 Azure IoT Edge 並建立模組之間的連線。

我們必須先宣告 `node` 載入器 (因為 Azure IoT Edge 支援不同語言的載入器)，以在後續各節中依照其 `name` 予以參照。

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

宣告載入器後，我們也必須宣告我們的模組。 類似於宣告載入器，也可以依照其 `name` 屬性予以參照。 宣告模組時，我們需要指定它應使用的載入器 (這應該是我們先前定義的載入器) 和每個模組的進入點 (應該是我們模組的正常化類別名稱)。 `simulated_device` 模組是 Azure IoT Edge 核心執行階段套件內含的原生模組。 將 `args` 包含在 JSON 檔案中 (即使它是 `null`)。

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

在設定結束時，我們會建立連線。 每個連線是以 `source` 和 `sink` 表示。 它們應會參照預先定義的模組。 `source` 模組的輸出訊息會轉送至 `sink` 模組的輸入。

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>執行模組
1. `npm install`
2. `npm start`

如果您想要終止應用程式，請按 `<Enter>` 鍵。

> [!IMPORTANT]
> 不建議使用 Ctrl + C 來終止 IoT Edge 應用程式， 因為這可能會造成程序異常終止。
