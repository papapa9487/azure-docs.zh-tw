---
title: "Azure IoT 中樞裝置佈建服務的概念 | Microsoft Docs"
description: "說明具有 DPS 和 IoT 中樞之裝置的特定服務佈建概念"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 1a37f0753ab576aba9154e148b026b70877176f2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT 中樞裝置佈建服務服務概念

IoT 中樞裝置佈建服務是 IoT 中樞適用的協助程式服務，用於設定在指定 IoT 中樞上的全自動佈建裝置作業。 這項裝置佈建服務可以讓您以安全且可調整的方式佈建數百萬個裝置。

裝置佈建程序分為兩個部分。 第一個部分是*申請*裝置，以在裝置和 IoT 解決方案之間建立初始連線。 第二個部分是根據解決方案的特定需求套用正確的*設定*。 這兩個步驟都完成之後，該裝置才算*佈建*完成。 裝置佈建服務會自動進行這兩個步驟，以提供順暢的裝置佈建體驗。

本文說明最適用於管理*服務*的佈建概念概觀。 本文與為裝置進行[雲端設定步驟](about-iot-dps.md#cloud-setup-step)，也就是部署準備工作的角色相關。

## <a name="service-operations-endpoint"></a>服務作業端點

服務作業端點是管理服務設定，和維護申請清單的端點。 只有服務管理員才能使用此端點；裝置不行。

## <a name="device-provisioning-endpoint"></a>裝置佈建端點

裝置佈建端點是讓所有裝置連接，以進行佈建的中央端點。 所有佈建服務都有相同的 URL，這樣就不需以供應鏈案例中的連線資訊重新刷新裝置。 [識別碼範圍](#id-scope)會確保租用戶隔離。

## <a name="linked-iot-hubs"></a>連結的 IoT 中樞

裝置佈建服務只能將裝置佈建到已和自身連結的 IoT 中樞。 將 IoT 中樞連結至裝置佈建服務可為 IoT 中樞的裝置登錄提供該服務的讀/寫權限；這項連結可以讓裝置佈建服務申請裝置識別碼，以及在裝置對應項中進行初始設定。 連結的 IoT 中樞可能位於任何 Azure 區域。 您可以將其他訂用帳戶的中樞連結至佈建服務。

## <a name="allocation-policy"></a>配置原則

服務等級的設定決定裝置佈建服務將裝置指派給 IoT 中樞的方式。 共有三個支援的配置原則：
* **平均加權分佈**：裝置佈建到每個已連結 IoT 中樞的機率都是一樣的。 預設設定。 如果您只要將裝置佈建到一個 IoT 中樞，可以保留此設定。
* **最低延遲時間**：系統會以裝置的最低延遲時間將該裝置佈建到 IoT 中樞。 如果多個已連結 IoT 中樞都提供相同的最低延遲時間，佈建服務就能在這些中樞裡為裝置建立雜湊
* **透過申請清單進行靜態設定**：您在申請清單中指定的所需 IoT 中樞，其優先順序高於服務等級的配置原則。

## <a name="enrollment"></a>申請

申請是記載某種程度上已註冊的裝置，或裝置群組的記錄。 申請記錄中有裝置或裝置群組的資訊，包含裝置的證明方法，也可能包含一開始的所需設定、所需 IoT 中樞和所需裝置識別碼。 裝置佈建服務支援兩種類型的申請。

### <a name="enrollment-group"></a>申請群組

申請群組為一組共用特定證明機制的裝置。 申請群組中的所有裝置都有相同根 CA 簽署的 X.509 憑證。 申請群組只能使用 X.509 證明機制。

> [!TIP]
> 對於一大批共用所需初始設定的裝置，或是全都設定為相同租用戶的裝置，我們建議使用申請群組。

### <a name="individual-enrollment"></a>個別申請

個別申請為表示可能已註冊之單一裝置的項目。 個別申請可使用 X.509 憑證或 SAS 權杖 (適用於實際或虛擬 TPM) 作為證明機制。 個別申請可能會指定所需的 IoT 中樞裝置識別碼。

> [!TIP]
> 對於需要唯一初始設定的裝置，或是只能透過 TPM 或虛擬 TPM 使用 SAS 權杖作為證明機制的裝置，我們建議您使用個別申請。

## <a name="registration"></a>註冊

註冊是記載裝置透過裝置佈建服務成功向 IoT 中樞申請以及佈建至該處的記錄。 系統會自動建立註冊記錄；您可以刪除但無法更新這些記錄。

## <a name="operations"></a>作業

作業是裝置佈建服務的計費單位。 一個作業代表成功完成該服務需進行的一個指示。 作業包括裝置註冊和重新註冊，也包括服務端的變更，例如新增和更新申請清單項目。

