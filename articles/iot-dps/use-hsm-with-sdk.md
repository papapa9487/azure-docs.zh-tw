---
title: "Azure 作法 - 使用不同的 HSM 搭配裝置佈建服務用戶端 SDK | Microsoft Docs"
description: "Azure 作法 - 透過裝置佈建服務用戶端 SDK 使用不同的 HSM 搭配實體裝置和模擬器"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: a5649ab873993d20803cb01a4b0ecc278c3ce16c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>如何使用不同的硬體安全性模組搭配裝置佈建服務用戶端 SDK
這些步驟顯示如何透過實體裝置和模擬器使用不同的[硬體安全性模組 (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)搭配 C 中的裝置佈建服務用戶端 SDK。  佈建服務支援兩種驗證模式：X**.**509 和受信任的平台模組 (TPM)。

## <a name="prerequisites"></a>必要條件

根據 [建立及佈建模擬的裝置] (./quick-create-simulated-device.md) 指南中標題為「準備開發環境」一節，準備您的開發環境。

## <a name="enable-authentication-with-different-hsms"></a>使用不同的 HSM 來啟用驗證

驗證模式 (X**.**509 或 TPM) 必須針對實體裝置或模擬器啟用後，才能在 Azure 入口網站中進行註冊。  瀏覽至 azure-iot-sdk-c 的根目錄。  根據您選擇的驗證模式來執行指定的命令。

### <a name="use-x509-with-simulator"></a>使用 X**.**509 搭配模擬器

佈建服務隨附於裝置識別組合引擎 (DICE) 模擬器，所產生的 X**.**509 憑證可驗證裝置。  執行下列命令以啟用 X**.**509 驗證：

```
cmake -Ddps_auth_type=x509 ..
```

可在[這裡](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)找到硬體與 DICE 的相關資訊。

### <a name="use-x509-with-hardware"></a>使用 X**.**509 搭配硬體

可在其他硬體上使用佈建服務搭配 X**.**509。  需要硬體與 SDK 之間的介面才能建立連線。  如需關於介面的資訊，請連絡您的 HSM 製造商。

### <a name="use-tpm"></a>使用 TPM

佈建服務可以使用 SAS 權杖連線到 Windows 和 Linux 硬體 TPM 晶片。  執行下列命令以啟用 TPM 驗證：

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>使用 TPM 搭配模擬器

如果您沒有搭載 TPM 晶片的裝置，可以在 Windows 作業系統上就開發目的使用模擬器。  執行下列命令以啟用 TPM 驗證並執行 TPM 模擬器：

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>建置 SDK
建立裝置註冊之前，請建置 SDK。

### <a name="linux"></a>Linux
- 若要在 Linux 中建置 SDK：
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- 若要建置偵錯二進位檔，請將對應的 CMake 選項新增至專案產生命令，例如：
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- 有許多 [CMake 設定選項](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html)可用來建置 SDK。 例如，您可以停用其中一個可用的通訊協定堆疊，方法是將引數新增至 CMake 專案產生命令：
    ```
    cmake -Duse_amqp=OFF ..
    ```
- 您也可以建置並執行單元測試：
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- 若要在 Windows 中建置 SDK，請採取下列步驟以產生專案檔：
    - 開啟「VS2015 的開發人員命令提示字元」
    - 從存放庫的根目錄執行下列 CMake 命令：
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    此命令會建置 x86 程式庫。 若要建置 x64 的程式庫，請修改 cmake 產生器引數： 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- 如果專案產生成功完成，應該會在 `cmake` 資料夾下看到 Visual Studio 解決方案檔 (.sln)。 若要建置 SDK：
    - 在 Visual Studio 中開啟 **cmake\azure_iot_sdks.sln** 並加以建置，**或**
    - 在您用來產生專案檔的命令提示字元中執行下列命令：
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- 若要建置偵錯二進位檔，請使用對應的 MSBuild 引數： 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- 有許多 CMake 設定選項可用來建置 SDK。 例如，您可以停用其中一個可用的通訊協定堆疊，方法是將引數新增至 CMake 專案產生命令：
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- 此外，您可以建置並執行單元測試：
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>要包含的程式庫
- 這些程式庫應包含在您的 SDK：
    - 佈建服務：dps_http_transport、dps_client、dps_security_client
    - IoTHub 安全性：iothub_security_client

## <a name="create-a-device-enrollment-entry-in-dps"></a>在 DPS 中建立裝置註冊項目

### <a name="tpm"></a>TPM
如果您是使用 TPM，請遵循[「使用 IoT 中樞裝置佈建服務來建立及佈建模擬的裝置」](./quick-create-simulated-device.md)中的指示，在 DPS 中建立裝置註冊項目，並模擬第一次開機。

### <a name="x509"></a>X**.**509
1. 若要在佈建服務中註冊裝置，您必須記下每個裝置的簽署金鑰和註冊 ID，這些都會顯示在用戶端 SDK 所提供的佈建工具中。 執行下列命令可列印出根 CA 憑證 (適用於註冊群組) 和簽署者憑證 (適用於個別註冊)：
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. 登入 Azure 入口網站，按一下左側功能表上的 [所有資源] 按鈕，然後開啟 DPS 服務。
   - X**.**509 個別註冊：在佈建服務摘要刀鋒視窗上，選取 [管理註冊]。 選取 [個別註冊] 索引標籤，然後按一下頂端的 [新增] 按鈕。 選取 **X**.**509** 作為身分識別證明機制，如刀鋒視窗所要求上傳簽署者憑證。 完成後，按一下 [儲存] 按鈕。 
   - X**.**509 群組註冊：在佈建服務摘要刀鋒視窗上，選取 [管理註冊]。 選取 [群組註冊] 索引標籤，然後按一下頂端的 [新增] 按鈕。 選取 **X**.**509** 作為身分識別證明機制，輸入群組名稱和憑證名稱，如刀鋒視窗所要求上傳根 CA 憑證。 完成後，按一下 [儲存] 按鈕。 

## <a name="connecting-to-iot-hub-after-provisioning"></a>佈建之後連線到 IoT 中樞

一旦使用佈建服務將裝置佈建後，此 API 會使用 HSM 驗證模式與 IoT 中樞連線： 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

