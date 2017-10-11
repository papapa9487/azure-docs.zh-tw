---
title: "在 Windows 上使用 C 連接裝置 | Microsoft Docs"
description: "描述如何在 Windows 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d222bcbd64f288d4091acb0ecd2922b9ceee57e5
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>將裝置連接至遠端監視預先設定方案 (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>在 Windows 上建立 C 範例方案
下列步驟說明如何建立用戶端應用程式來與預先設定的遠端監視解決方案進行通訊。 此應用程式是以 C 撰寫並在 Windows 上建置和執行。

在 Visual Studio 2015 或 Visual Studio 2017 中建立入門專案，並新增 IoT 中樞的裝置用戶端 NuGet 套件︰

1. 在 Visual Studio 中，使用 Visual C++ **Win32 主控台應用程式** 範本建立 C 主控台應用程式。 將專案命名為 **RMDevice**。
2. 在 [Win32 應用程式精靈] 的 [應用程式設定] 頁面中，確定已選取 [主控台應用程式]，並取消核取 [預先編譯的標頭] 和 [安全性開發生命週期 (SDL) 檢查]。
3. 在 [方案總管] 中刪除檔案 stdafx.h、targetver.h 和 stdafx.cpp。
4. 在 [方案總管] 中將檔案 RMDevice.cpp 重新命名為 RMDevice.c。
5. 在 [方案總管] 中，以滑鼠右鍵按一下 [RMDevice] 專案，然後按一下 [管理 NuGet 套件]。 按一下 [瀏覽]，然後搜尋並安裝下列 NuGet 套件︰
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. 在 [方案總管] 中，以滑鼠右鍵按一下 [RMDevice] 專案，然後按一下 [屬性] 開啟專案的 [屬性頁] 對話方塊。 如需詳細資訊，請參閱[設定 Visual C++ 專案屬性 (英文)][lnk-c-project-properties]。 
7. 按一下 [連結器]資料夾，然後按一下 [輸入] 屬性頁。
8. 將 **crypt32.lib** 新增至 [其他相依性] 屬性。 按一下 [確定]，然後再按一下 [確定] 以儲存專案屬性值。

將 Parson JSON 程式庫新增至 **RMDevice** 專案，並新增所需的 `#include` 陳述式︰

1. 在您電腦上的適當資料夾中，使用下列命令複製 Parson GitHub 儲存機制︰

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. 將 Parson.h 和 parson.c 檔案從 Parson 儲存機制的本機複本複製到 **RMDevice** 專案資料夾。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **RMDevice** 專案，按一下 [新增]，然後按一下 [現有項目]。

1. 在 [新增現有項目] 對話方塊中，選取 **RMDevice** 專案資料夾中的 parson.h 和 parson.c 檔案。 然後按一下 [新增]，將這兩個檔案新增至您的專案。

1. 在 Visual Studio 中，開啟 RMDevice.c 檔案。 以下列程式碼取代現有 `#include` 陳述式：
   
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

    > [!NOTE]
    > 現在，您可以藉由建置專案來確認專案已設定正確的相依性。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>建置並執行範例

新增程式碼來叫用 **remote\_monitoring\_run** 函式，然後建置並執行裝置應用程式。

1. 將 **main** 函式取代為下列程式碼，以叫用 **remote\_monitoring\_run** 函式︰
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. 依序按一下 [建置] 和 [建置方案] 以建置裝置應用程式。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [RMDevice] 專案，並按一下 [偵錯]，然後按一下 [開始新執行個體] 來執行範例。 主控台會在應用程式將範例遙測傳送至預先設定的解決方案時顯示訊息，接收在解決方案儀表板中設定的所需屬性值，以及回應從解決方案儀表板叫用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
