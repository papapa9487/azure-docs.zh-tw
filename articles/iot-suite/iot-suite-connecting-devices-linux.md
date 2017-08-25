---
title: "在 Linux 上使用 C 連接裝置| Microsoft Docs"
description: "描述如何在 Linux 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c844f9a84878f12fbdf7a5b4eaf7bbf19f4ce666
ms.openlocfilehash: 4a1615c4bea8c54d506c3252e2de42642bb55e46
ms.contentlocale: zh-tw
ms.lasthandoff: 02/27/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>將裝置連接至遠端監視預先設定方案 (Linux)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>建置並執行範例 C 用戶端 Linux
下列步驟說明如何建立用戶端應用程式來與預先設定的遠端監視解決方案進行通訊。 此應用程式是以 C 撰寫並在 Ubuntu Linux 上建置和執行。

若要完成這些步驟，您需要執行 Ubuntu 版本 15.04 或 15.10 的裝置。 繼續之前，使用下列命令在 Ubuntu 裝置上安裝的必要條件封裝：

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>在裝置上安裝用戶端程式庫
Azure IoT 中樞用戶端程式庫可當成封裝來使用，而您可以使用 **apt get** 命令在 Ubuntu 裝置上安裝這類封裝。 完成下列步驟來安裝套件，其中包含 Ubuntu 電腦上的 IoT 中樞用戶端程式庫和標頭檔：

1. 在殼層中，將 AzureIoT 儲存機制新增至電腦：
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. 安裝 azure-iot-sdk-c-dev 封裝
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>安裝 Parson JSON 剖析器
IoT 中樞用戶端程式庫會使用 Parson JSON 剖析器來剖析訊息承載。 在您電腦上的適當資料夾中，使用下列命令複製 Parson GitHub 儲存機制︰

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>準備您的專案
在 Ubuntu 電腦上，建立稱為 **remote\_monitoring** 的資料夾。 在 **remote\_monitoring** 資料夾中︰

- 建立四個檔案︰**main.c**、**remote\_monitoring.c**、**remote\_monitoring.h** 和 **CMakeLists.txt**。
- 建立一個名為 **parson** 的資料夾。

將 **parson.c** 和 **parson.h** 檔案從 Parson 儲存機制的本機複本複製到 **remote\_monitoring/parson** 資料夾中。

在文字編輯器中，開啟 **remote\_monitoring.c** 檔案。 新增下列 `#include` 陳述式：
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>呼叫 remote\_monitoring\_run 函式
在文字編輯器中，開啟 **remote_monitoring.h** 檔案。 新增下列程式碼：

```
void remote_monitoring_run(void);
```

在文字編輯器中，開啟 **main.c** 檔案。 新增下列程式碼：

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>建置並執行應用程式
下列步驟說明如何使用 *CMake* 建置用戶端應用程式。

1. 在文字編輯器中，開啟 **remote_monitoring** 資料夾中的 **CMakeLists.txt** 檔案。

1. 新增下列指示來定義如何建置用戶端應用程式：
   
    ```
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```
1. 在 **remote_monitoring** 資料夾中，建立資料夾來儲存 CMake 產生的 *make* 檔案，然後執行 **cmake** 和 **make** 命令，如下所示：
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 執行用戶端應用程式，並將遙測傳送至 IoT 中樞：
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


