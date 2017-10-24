---
title: "使用 Java 建立 Azure IoT Edge 模組 | Microsoft 文件"
description: "本教學課程示範如何使用最新的 Azure IoT Edge Maven 套件撰寫 BLE 資料轉換器模組。"
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: eddeb5cc13aac7ab33305adcd266465a5b143462
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>使用 Java 建立 Azure IoT Edge 模組

本教學課程示範如何使用 Java 建置 Azure IoT Edge 的模組。

在本教學課程中，我們將逐步檢視環境設定，以及如何使用最新的 Azure IoT Edge Maven 套件撰寫 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 資料轉換器模組。

## <a name="prerequisites"></a>必要條件

在本節中，您將針對 IoT Edge 模組設定您的環境。 它同時適用於「64 位元 Windows」和「64 位元 Linux (Ubuntu/Debian 8)」作業系統。

需要下列軟體：

* [Git 用戶端](https://git-scm.com/downloads)。
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
* [Maven](https://maven.apache.org/install.html).

開啟命令列終端機視窗，然後複製下列存放庫：

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`。
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>整體架構

Azure IoT Edge 平台大量採用 [Von Neumann 架構](https://en.wikipedia.org/wiki/Von_Neumann_architecture)。 這表示整個 Azure IoT Edge 架構是用來處理輸入及產生輸出的系統，而每個個別模組也是小型輸入-輸出子系統。 在本教學課程中，我們將介紹下列兩個模組︰

1. 可接收模擬 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 訊號，並將它轉換為格式化 [JSON](https://en.wikipedia.org/wiki/JSON) 訊息的模組。
2. 可列印已接收 [JSON](https://en.wikipedia.org/wiki/JSON) 訊息的模組。

下圖顯示這個專案的典型端對端資料流程：

![三個模組之間的資料流程](media/iot-hub-iot-edge-create-module/dataflow.png "輸入：模擬 BLE 模組；處理器：轉換器模組；輸出：印表機模組")

## <a name="understanding-the-code"></a>了解程式碼

### <a name="maven-project-structure"></a>Maven 專案結構

因為 Azure IoT Edge 套件是以 Maven 為基礎，所以我們需要建立典型 Maven 專案結構，其中包含 `pom.xml` 檔案。

POM 繼承自 `com.microsoft.azure.gateway.gateway-module-base` 套件，其宣告模組專案需要的所有相依性，包括執行階段二進位檔、閘道器組態檔路徑，以及執行行為。 這可節省很多時間，且不需要一再地撰寫及重寫數百行的程式碼。

我們需要藉由宣告所需的相依性/外掛程式和我們的模組所用的組態檔名稱，進而更新 pom.xml 檔案，如下列程式碼片段所示。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Azure IoT Edge 模組的基本認識

您可以將 Azure IoT Edge 模組視為資料處理器，其作業是：接收輸入、加以處理，以及產生輸出。

輸入可能是來自硬體 (例如動作偵測器) 的資料、來自其他模組的訊息，或任何其他資料 (例如計時器定期產生的隨機數字)。

輸出類似於輸入，它可能會觸發硬體行為 (例如閃爍的 LED)、給其他模組的訊息，或任何其他作業 (例如列印至主控台)。

模組會使用 `com.microsoft.azure.gateway.messaging.Message` 類別彼此通訊。 `Message` 的 [內容] 是一個位元組陣列，其可代表任一種您想要的資料。 [屬性] 也適用於 `Message`，且只是字串與字串的對應。 您可以將**屬性**想成是 HTTPS 要求中的標頭或檔案的中繼資料。

若要使用 Java 開發 Azure IoT Edge 模組，您必須建立新的模組類別 (繼承自`com.microsoft.azure.gateway.core.GatewayModule`) 並實作必要的抽象方法 `receive()` 和 `destroy()`。 此時，您也可以選擇實作選擇性 `start()` 或 `create()` 方法。 下列程式碼片段會示範如何開始撰寫 Azure IoT Edge 模組。

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>轉換器模組

| 輸入                    | 處理器                              | 輸出                 | 來源檔案            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 溫度資料訊息 | 剖析並建構新的 JSON 訊息 | 建構 JSON 訊息 | `ConverterModule.java` |

此模組是典型的 Azure IoT Edge 模組。 它可接受來自其他模組 (硬體模組，或此情況下我們的模擬 BLE 模組) 的溫度訊息，然後將溫度訊息正常化成為結構化 JSON 訊息 (包括附加訊息識別碼、設定我們是否需要觸發溫度警示的屬性等等)。

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>印表機模組

| 輸入                          | 處理器 | 輸出                     | 來源檔案          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 來自其他模組的任何訊息 | N/A       | 將訊息記錄至主控台 | `PrinterModule.java` |

這是簡單、不言而喻的模組，它會將收到的訊息輸出至終端機視窗。

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT Edge 組態

執行模組之前的最後一個步驟是設定 Azure IoT Edge 並建立模組之間的連線。

我們必須先宣告 Java 載入器 (因為 Azure IoT Edge 支援不同語言的載入器)，以在後續各節中依照其 `name` 予以參照。

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

宣告載入器後，我們也必須宣告我們的模組。 類似於宣告載入器，也可以依照其 `name` 屬性予以參照。 宣告模組時，我們需要指定它應使用的載入器 (這應該是我們先前定義的載入器) 和每個模組的進入點 (應該是我們模組的正常化類別名稱)。 `simulated_device` 模組是 Azure IoT Edge 核心執行階段套件內含的原生模組。 您一律要將 `args` 包含在 JSON 檔案中 (即使它是 `null`)。

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
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

在設定結束時，我們會建立連線。 每個連線是以 `source` 和 `sink` 表示。 它們應會參照預先定義的模組。 `source` 模組的輸出訊息將轉送至 `sink` 模組的輸入。

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>執行模組

使用 `mvn package` 將所有項目建置到 `target/` 資料夾中。 也建議將 `mvn clean package` 用於全新組建。

使用 `mvn exec:exec` 來執行 Azure IoT Edge，而您應會發現溫度資料和所有屬性都會以固定速率列印至主控台。

如果您想要終止應用程式，請按 `<Enter>` 鍵。

> [!IMPORTANT]
> 不建議使用 Ctrl + C 來終止 IoT Edge 閘道應用程式， 因為這可能會造成程序異常終止。

