---
title: "使用 Azure IoT 中樞裝置佈建服務來佈建裝置 | Microsoft Docs"
description: "使用 Azure IoT 中樞裝置佈建服務將裝置佈建到單一 IoT 中樞"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中樞裝置佈建服務將裝置佈建到 IoT 中樞

在上一個教學課程中，您已了解如何將裝置設定為連線到裝置佈建服務。 在本教學課程中，您將了解如何使用這項服務，以透過**_註冊清單_**將裝置佈建到單一 IoT 中樞。 本教學課程說明如何：

> [!div class="checklist"]
> * 註冊裝置
> * 啟動裝置
> * 確認裝置已註冊

## <a name="prerequisites"></a>必要條件

在繼續之前，請務必依照[將裝置設定為使用 Azure IoT 中樞裝置佈建服務來進行佈建](./tutorial-set-up-device.md)教學課程所討論的方法，來設定裝置和其*硬體安全模組*。


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>註冊裝置

此步驟涉及將裝置的唯一安全性構件新增至裝置佈建服務。 這些安全性構件如下所示：

- 若為 TPM 架構的裝置：
    - 簽署金鑰，每個 TPM 晶片或模擬都會有唯一的簽署金鑰。 如需詳細資訊，請參閱[了解 TPM 簽署金鑰](https://technet.microsoft.com/library/cc770443.aspx)。
    - 註冊識別碼，用來唯一識別命名空間/範圍中的裝置。 註冊識別碼與裝置識別碼不一定相同。 每個裝置都必須有註冊識別碼。 若為 TPM 架構的裝置，註冊識別碼可能會衍生自該 TPM 自身，例如，TPM 簽署金鑰的 SHA-256 雜湊。

    ![入口網站中的 TPM 註冊資訊](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- 若為 X.509 架構的裝置：
    - [核發給 X.509 晶片或模擬的憑證](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)，格式為 .pem 或 .cer 檔案。 若要進行個別註冊，您必須對 X.509 系統使用「簽章者憑證」，若要進行群組註冊，則必須使用「根憑證」。

    ![入口網站中的 X.509 註冊資訊](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


有兩種方式可向裝置佈建服務註冊裝置：

- **註冊群組** 這代表一組共用特定證明機制的裝置。 對於共用所需初始設定的大量裝置，或是全都要進入同一個租用戶的裝置，建議您使用註冊群組。

    ![入口網站中的 X.509 註冊群組](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **個別註冊** 這代表可能會向裝置佈建服務註冊的單一裝置項目。 個別註冊可使用 x509 憑證或 SAS 權杖 (在實際或虛擬的 TPM 中) 來作為證明機制。 對於需要唯一初始設定的裝置，或是只能透過 TPM 或虛擬 TPM 將 SAS 權杖作為證明機制的裝置，建議您使用個別註冊。 個別註冊可能會指定所需的 IoT 中樞裝置識別碼。

在入口網站中註冊裝置的步驟如下：

1. 請記下裝置上的 HSM 安全性構件。 您可能需要在開發環境中使用上一個教學課程的[擷取安全性構件](./tutorial-set-up-device.md#extractsecurity)一節所提到的 API。

1. 登入 Azure 入口網站，按一下左側功能表上的 [所有資源] 按鈕，然後開啟您的裝置佈建服務。

1. 在裝置佈建服務摘要刀鋒視窗上，選取 [管理註冊]。 根據您的裝置設定選取 [個別註冊] 索引標籤或 [註冊群組] 索引標籤。 按一下位於頂端的 [新增] 按鈕。 選取 [TPM] 或 [X.509] 來作為識別證明「機制」，然後如先前所討論的輸入適當的安全性構件。 您可以輸入新的 [IoT 中樞裝置識別碼]。 完成後，按一下 [儲存] 按鈕。 

1. 在成功註冊裝置後，您應該會看到它顯示在入口網站中，如下所示：

    ![入口網站中的成功 TPM 註冊](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>啟動裝置

至此，下列設定已可用來註冊裝置：

1. 您的裝置或裝置群組已向裝置佈建服務註冊，而且 
2. 您的裝置已設定好 HSM 晶片而做好準備，且可透過應用程式使用裝置佈建服務用戶端 SDK 來加以存取。

請啟動裝置，讓用戶端應用程式開始向裝置佈建服務進行註冊。  


## <a name="verify-the-device-is-registered"></a>確認裝置已註冊

裝置開機後應該會發生下列動作。 如需詳細資訊，請參閱 TPM 模擬器應用程式範例 [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c)。 

1. 裝置會向裝置佈建服務傳送註冊要求。
2. 若為 TPM 裝置，裝置佈建服務會回傳註冊挑戰供您的裝置回應。 
3. 註冊成功後，裝置佈建服務就會對裝置回傳 IoT 中樞 URI、裝置識別碼和加密金鑰。 
4. 裝置上的 IoT 中樞用戶端應用程式接著會連線到您的中樞。 
5. 成功連線至中樞後，您應該會看到裝置出現在 IoT 中樞的 **Device Explorer** 中。 

    ![在入口網站中成功連線至中樞](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 註冊裝置
> * 啟動裝置
> * 確認裝置已註冊

前進到下一個教學課程，以了解如何跨負載平衡中樞來佈建多個裝置。 

> [!div class="nextstepaction"]
> [跨負載平衡 IoT 中樞來佈建裝置](./tutorial-provision-multiple-hubs.md)
