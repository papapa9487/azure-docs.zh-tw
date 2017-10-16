---
title: "如何管理 Azure IoT 中樞的裝置註冊 | Microsoft Docs"
description: "如何在 Azure 入口網站中管理 DPS 服務的裝置註冊"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>如何在 IoT 中樞裝置佈建服務中管理裝置註冊

「裝置註冊」會建立單一裝置或裝置群組的記錄，這些裝置可能會在某個時間點向 Azure IoT 中樞裝置佈建服務進行註冊。 註冊記錄包含屬於該註冊一部分之裝置一開始所需的設定，包括所需的 IoT 中樞。 本文說明如何管理佈建服務的裝置註冊。


## <a name="create-a-device-enrollment"></a>建立裝置註冊

有兩種方式可供您向佈建服務註冊裝置：

1. **註冊群組**是裝置群組項目，這些裝置會共用由相同根 CA 所簽署之 X.509 憑證的通用證明機制。 對於共用所需初始設定的大量裝置，或是全都要進入同一個租用戶的裝置，建議您使用註冊群組。 請注意，您只能將使用 X.509 證明機制的裝置註冊為「註冊群組」。 

    您可以使用下列步驟，在入口網站中為裝置群組建立註冊群組。

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中按一下要用來註冊裝置的裝置佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [註冊群組] 索引標籤。
    4. 按一下頂端的 [新增] 按鈕，然後輸入註冊清單項目所需的資訊。 上傳裝置群組的根憑證。 
    5. 按一下 [儲存] 。 在成功建立註冊群組後，您應該會看到群組名稱出現在 [註冊群組] 索引標籤底下。 

        ![入口網站中的註冊群組](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. **個別註冊**是可能會註冊之單一裝置的項目。 個別註冊可使用 x509 憑證或 SAS 權杖 (在實際或虛擬的 TPM 中) 來作為證明機制。 對於需要唯一初始設定的裝置，或是只能透過 TPM 或虛擬 TPM 將 SAS 權杖作為證明機制的裝置，建議您使用個別註冊。 個別註冊可能會指定所需的 IoT 中樞裝置識別碼。

    您可以使用下列步驟在入口網站中建立個別註冊。 

    1. 登入 Azure 入口網站，然後在左側功能表中按一下 [所有資源]。
    2. 在資源清單中按一下要用來註冊裝置的裝置佈建服務。
    3. 在佈建服務中，按一下 [管理註冊]，然後選取 [個別註冊] 索引標籤。
    4. 按一下位於頂端的 [新增] 按鈕。 
    5. 選取裝置的安全性機制，然後輸入註冊清單項目所需的資訊。 如果裝置實作 X.509，請上傳簽署的憑證。 
    6. 按一下 [儲存] 。 在成功建立註冊群組後，您應該會看到裝置出現在 [個別註冊] 索引標籤底下。 

        ![入口網站中的個別註冊](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>更新註冊項目
您可以使用下列步驟在入口網站中更新現有的註冊項目。

1. 在 Azure 入口網站中開啟裝置佈建服務，然後按一下 [管理註冊]。 
2. 瀏覽至您要修改的註冊項目。 按一下該項目，以開啟裝置註冊的摘要資訊。 
3. 在此頁面上，您可以修改安全性類型和認證以外的項目，例如裝置應該連結的 IoT 中樞以及裝置識別碼。 您也可以修改裝置對應項的初始狀態。 
4. 完成後，按一下 [儲存] 以更新裝置註冊。 

    ![在入口網站中更新註冊](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>移除裝置註冊
如果您不需要將裝置佈建到任何 IoT 中樞，則可以使用下列步驟在入口網站中移除相關的註冊項目。

1. 在 Azure 入口網站中開啟裝置佈建服務，然後按一下 [管理註冊]。 
2. 瀏覽至您要移除的註冊項目並加以選取。 
3. 按一下頂端的 [刪除] 按鈕，然後在系統提示您確認時選取 [是]。 
5. 完成此動作之後，您會看到您的項目已從裝置註冊清單中移除。 
 
    ![在入口網站中移除註冊](./media/how-to-manage-enrollments/remove-enrollment.png)



