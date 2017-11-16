---
title: "使用 C 將 Raspberry Pi 佈建到遠端監視 - Azure | Microsoft Docs"
description: "描述如何使用 C 中已寫入的應用程式，將 Raspberry Pi 裝置連線至 Azure IoT 套件預先設定遠端監視方案。"
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 45f4de7e9ec880775f9ccf77b7d945766d465aa7
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>將 Raspberry Pi 裝置連線至遠端監視預先設定方案 (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教學課程會示範如何將實體裝置連線到遠端監視預先設定的解決方案。 如同大部分在受條件約束裝置上執行的內嵌應用程式，Raspberry Pi 裝置應用程式的用戶端程式碼是以 C 撰寫的。在此教學課程中，您要在執行 Raspbian OS 的 Raspberry Pi 上建置應用程式。

### <a name="required-hardware"></a>必要的硬體

一部桌上型電腦，可讓您從遠端連線到 Raspberry Pi 上的命令列。

[適用於 Raspberry Pi 3 的 Microsoft IoT 入門套件](https://azure.microsoft.com/develop/iot/starter-kits/)或對等的元件。 本教學課程會使用套件中的下列項目：

- Raspberry Pi 3
- MicroSD 卡 (具有 NOOBS)
- USB Mini 連接線
- 乙太網路連接線

### <a name="required-desktop-software"></a>必要的桌面軟體

您需要透過桌上型電腦上的 SSH 用戶端，才能從遠端存取 Raspberry Pi 上的命令列。

- Windows 不包含 SSH 用戶端。 我們建議使用 [PuTTY](http://www.putty.org/)。
- 大部分的 Linux 散發套件和 Mac OS 都包含命令列 SSH 公用程式。 如需詳細資訊，請參閱[使用 Linux 或 Mac OS 的 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)。

### <a name="required-raspberry-pi-software"></a>必要的 Raspberry Pi 軟體

下列步驟說明如何準備 Raspberry Pi 以建置連線到預先設定方案的 C 應用程式：

1. 使用 `ssh` 連線至您的 Raspberry Pi。 如需詳細資訊，請參閱 [Raspberry Pi 網站](https://www.raspberrypi.org/)上的 [SSH (安全殼層)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)。

1. 若要更新 Raspberry Pi，請使用下列命令︰

    ```sh
    sudo apt-get update
    ```

1. 使用下列命令，將必要的開發工具和程式庫新增至 Raspberry Pi：

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. 使用下列命令來安裝 IoT 中樞用戶端程式庫：

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. 使用下列命令，將 Parson JSON 剖析器複製到您的 Raspberry Pi：

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>建立專案

使用 `ssh` 與 Raspberry Pi 的連線完成下列步驟：

1. 在 Raspberry Pi 的主資料夾中，建立名為 `remote_monitoring` 的資料夾。 在命令列中瀏覽至此資料夾：

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. 在 `remote_monitoring` 資料夾中，建立 `main.c`、`remote_monitoring.c`、`remote_monitoring.h` 和 `CMakeLists.txt` 四個檔案。

1. 在 `remote_monitoring` 資料夾中，建立一個名為 `parson` 的資料夾。

1. 將 `parson.c` 和 `parson.h` 檔案從 Parson 存放庫的本機複本複製到 `remote_monitoring/parson` 資料夾。

1. 在文字編輯器中，開啟 `remote_monitoring.c` 檔案。 在 Raspberry Pi 上，您可以使用 `nano` 或 `vi` 文字編輯器。 新增下列 `#include` 陳述式：

    ```c
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

## <a name="add-code-to-run-the-app"></a>新增程式碼以執行應用程式

在文字編輯器中開啟 `remote_monitoring.h` 檔案。 新增下列程式碼：

```c
void remote_monitoring_run(void);
```

在文字編輯器中開啟 `main.c` 檔案。 新增下列程式碼：

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>建置並執行應用程式

下列步驟說明如何使用 *CMake* 建置用戶端應用程式。

1. 在文字編輯器中，開啟 `remote_monitoring` 資料夾中的 **CMakeLists.txt** 檔案。

1. 新增下列指示來定義如何建置用戶端應用程式：

    ```cmake
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

1. 在 `remote_monitoring` 資料夾中，建立資料夾來儲存 CMake 產生的 make 檔案。 然後執行 **cmake** 和 **make** 命令，如下所示：

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 執行用戶端應用程式，並將遙測傳送至 IoT 中樞：

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
