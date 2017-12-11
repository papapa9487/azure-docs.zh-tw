---
title: "將 X.509 模擬裝置佈建到 Azure IoT 中樞 | Microsoft Docs"
description: "Azure 快速入門 - 使用 Azure IoT 中樞裝置佈建服務來建立及佈建 X.509 模擬裝置"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/08/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a51debfc80c28cd7d7e271448e30619f2bc72f86
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="create-and-provision-an-x509-simulated-device-using-iot-hub-device-provisioning-service"></a>使用 IoT 中樞裝置佈建服務來建立及佈建 X.509 模擬裝置
> [!div class="op_single_selector"]
> * [TPM](quick-create-simulated-device.md)
> * [X.509](quick-create-simulated-device-x509.md)

這些步驟顯示如何在執行 Windows OS 的開發電腦上模擬 X.509 裝置，並使用程式碼範例來連線此模擬裝置與裝置佈建服務和 IoT 中樞。 

繼續之前，請務必完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)中的步驟。

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>準備開發環境 

1. 確定您已在電腦上安裝 Visual Studio 2015 或 [Visual Studio 2017](https://www.visualstudio.com/vs/)。 為了 Visual Studio 安裝，您必須啟用「C ++ 桌面開發」工作負載。

2. 下載並安裝 [CMake 建置系統](https://cmake.org/download/)。

3. 確定 `git` 已安裝在電腦上，並已新增至命令視窗可存取的環境變數。 請參閱[軟體自由保護協會的 Git 用戶端工具](https://git-scm.com/download/)以取得所要安裝的最新版 `git` 工具，其中包括 **Git Bash** (您可用來與本機 Git 存放庫互動的命令列應用程式)。 

4. 開啟命令提示字元或 Git Bash。 複製裝置模擬程式碼範例的 GitHub 存放庫：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. 在此 GitHub 存放庫的本機複本中針對 CMake 建置流程建立資料夾。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. 執行下列命令，為佈建用戶端建立 Visual Studio 解決方案。

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>在裝置佈建服務中建立裝置註冊項目

1. 開啟在 *cmake* 資料夾中產生的方案 (名為 `azure_iot_sdks.sln`)，並且在 Visual Studio 中建置。

2. 以滑鼠右鍵按一下 **Provision\_Samples** 資料夾底下的 **dice\_device\_enrollment** 專案，然後選取 [設定為起始專案]。 執行方案。 在輸出視窗中，當出現提示時，針對個別註冊輸入 `i`。 輸出視窗會顯示針對您的模擬裝置在本機產生的 X.509 憑證。 將輸出 (從 -----BEGIN CERTIFICATE----- 開始並於 -----END PUBLIC KEY----- 結束) 複製到剪貼簿，並確定包含這兩行文字。 
 
3. 在 Windows 電腦上建立名為 X509testcertificate.pem 的檔案，在您選擇的編輯器中開啟該檔案，並將剪貼簿內容複製到這個檔案。 儲存檔案。 

4. 登入 Azure 入口網站，按一下左側功能表上的 [所有資源] 按鈕，然後開啟您的佈建服務。

4. 在裝置佈建服務摘要刀鋒視窗上，選取 [管理註冊]。 選取 [個別註冊] 索引標籤，然後按一下頂端的 [新增] 按鈕。 

5. 在 [新增註冊清單項目] 之下，輸入下列資訊：
    - 選取 [X.509] 作為身分識別證明「機制」。
    - 在 .pem 或 .cer 憑證檔案 之下，選取在先前步驟中使用 [檔案總管] widget 建立的憑證檔案 _X509testcertificate.pem_。
    - 您可以選擇性地提供下列資訊：
        - 選取與您的佈建服務連結的 IoT 中樞。
        - 輸入唯一的裝置識別碼。 替您的裝置命名時，務必避免使用敏感性資料。 
        - 使用裝置所需的初始組態更新**初始裝置對應項狀態**。
    - 完成後，按一下 [儲存] 按鈕。 

    ![在入口網站刀鋒視窗中輸入 X.509 裝置註冊資訊](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   註冊成功時，您的 X.509 裝置會在 [個別註冊] 索引標籤之下的 [註冊識別碼] 資料行中顯示為 **riot-device-cert**。 



<a id="firstbootsequence"></a>
## <a name="simulate-first-boot-sequence-for-the-device"></a>模擬裝置的第一個開機順序

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀] 刀鋒視窗，並記下 [識別碼範圍] 值。

    ![從入口網站刀鋒視窗擷取 DPS 端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在您電腦上的 Visual Studio 中，瀏覽到 **Provision\_Samples** 資料夾之下名為 **prov\_dev\_client\_sample** 的範例專案，然後開啟 **prov\_dev\_client\_sample.c** 檔案。

3. 將「識別碼範圍」值指派給 `scope_id` 變數。 

    ```c
    static const char* scope_id = "[ID Scope]";
    ```

4. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]。 執行範例。 請注意，模擬裝置開機並連線至裝置佈建服務的訊息，以取得您的 IoT 中樞資訊。 尋找指出向您的中樞註冊成功的訊息：「從服務收到的註冊資訊：yourhuburl!」。 在出現提示時關閉視窗。

5. 在入口網站中，瀏覽到連結至您的佈建服務的 IoT 中樞，並開啟 [Device Explorer] 刀鋒視窗。 X.509 模擬裝置成功佈建到 IoT 中樞時，其裝置識別碼會出現在 [Device Explorer] 刀鋒視窗上，且 [狀態] 顯示為 [已啟用]。 請注意，如果您在執行範例裝置應用程式之前開啟了刀鋒視窗，您可能需要按一下頂端的 [重新整理] 按鈕。 

    ![已向 IoT 中樞註冊裝置](./media/quick-create-simulated-device/hub-registration.png) 

> [!NOTE]
> 如果您在裝置的註冊項目中變更了*初始裝置對應項狀態* (變更自預設值)，它即可從中樞提取所需的對應項狀態並採取適當的動作。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用並探索裝置用戶端範例，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 在您的電腦上關閉裝置用戶端範例輸出視窗。
1. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後選取您的裝置佈建服務。 在 [所有資源] 刀鋒視窗的頂端，按一下 [刪除]。  
1. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]，然後選取您的 IoT 中樞。 在 [所有資源] 刀鋒視窗的頂端，按一下 [刪除]。  

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在 Windows 電腦上建立 X.509 模擬裝置，並使用 Azure IoT 中樞裝置佈建服務將它佈建到 IoT 中樞。 若要深入了解裝置佈建，請繼續在 Azure 入口網站中進行裝置佈建服務設定的教學課程。 

> [!div class="nextstepaction"]
> [Azure IoT 中樞裝置佈建服務教學課程](./tutorial-set-up-cloud.md)
