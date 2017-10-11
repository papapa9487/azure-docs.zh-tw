---
title: "使用 Azure 入口網站建立 IoT 中樞 | Microsoft Docs"
description: "如何透過 Azure 入口網站建立、管理和刪除 Azure IoT 中樞。 其中包括定價層、調整、安全性和傳訊組態的相關資訊。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.openlocfilehash: bca7eea5f44bbed3b784b56edaac235161b43e5e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>使用 Azure 入口網站建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文章說明：

* 如何在 Azure 入口網站中找到 IoT 中樞服務。
* 如何建立和管理 IoT 中樞。

## <a name="where-to-find-the-iot-hub-service"></a>IoT 中樞服務的所在位置

您可以在入口網站的下列位置中找到 IoT 中樞服務：

* 選擇 [+ 新增]，然後選擇 [物聯網]。
* 在 Marketplace 中，選擇 [物聯網]。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

您可以使用下列方法建立 IoT 中樞：

* [+ 新增] 選項會開啟下列螢幕擷取畫面中顯示的刀鋒視窗。 透過這個方法以及透過 Marketplace 建立 IoT 中樞的步驟完全相同。
* 在 Marketplace 中，選擇 [建立] 以開啟下列螢幕擷取畫面中顯示的刀鋒視窗。

下列幾節說明建立 IoT 中樞的幾個步驟：

### <a name="choose-the-name-of-the-iot-hub"></a>選擇 IoT 中樞的名稱

若要建立 IoT 中樞，您必須替 IoT 中樞命名。 此名稱不得與任何 IoT 中樞之名稱重複。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>選擇定價層。

您可以從 4 個層級中選擇：**免費**、**標準 1**、**標準 2** 和**標準 S3**。 免費層只允許 500 個裝置連接至 IoT 中樞，每天最多 8,000 封訊息。

**標準 S1**：如果 IoT 解決方案擁有大量裝置，但每個裝置只會產生少量資料，請使用 S1 版本。 S1 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 400,000 封訊息。

**標準 S2**：如果 IoT 解決方案中的裝置會產生大量資料，請使用 S2 版本。 S2 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 6 百萬封訊息。

**標準 S3**：如果 IoT 解決方案會產生大量資料，請使用 S3 版本。 S3 版本的每個單位可讓您跨所有連接的裝置，每天傳輸高達 3 億封訊息。

![][4]

> [!NOTE]
> IoT 中樞只允許每個 Azure 訂用帳戶有一個免費中樞。

### <a name="iot-hub-units"></a>IoT 中樞單位

每天每單位允許的訊息數目取決於您的中樞定價層。 例如，如果您想要 IoT 中樞支援 700,000 封訊息的輸入，您可以選擇 2 個 S1 層單位。

### <a name="device-to-cloud-partitions-and-resource-group"></a>裝置到雲端分割及資源群組

您可以變更 IoT 中樞的分割數目。 分割的預設數目是 4，您可以從下拉式清單中選擇不同的數字。

您不需要明確建立空的資源群組。 您可以在建立資源時，選擇建立新的資源群組，或使用現有的資源群組。

![][5]

### <a name="choose-subscription"></a>選擇訂用帳戶

Azure IoT 中樞會自動列出使用者帳戶所連結的 Azure 訂用帳戶。 您可以選擇要與 IoT 中樞相關聯的 Azure 訂用帳戶。

### <a name="choose-the-location"></a>選擇位置

[位置] 選項提供可在其中使用 IoT 中樞的區域清單。

### <a name="create-the-iot-hub"></a>建立 IoT 中樞

完成上述的所有步驟之後，您便可以建立 IoT 中樞。 按一下 [建立] 以啟動後端程序，並透過您選擇的選項建立和部署 IoT 中樞。

由於要在適當的位置伺服器上執行後端部署需要時間，因此建立 IoT 中樞會需要幾分鐘的時間。

## <a name="change-the-settings-of-the-iot-hub"></a>變更 IoT 中樞的設定

從 IoT 中樞刀鋒視窗建立 IoT 中樞後，您可以變更此現有 IoT 中樞的設定。

![][8]

**共用存取原則**：這些原則定義了裝置與服務連接至 IoT 中樞的權限。 您可以按一下 [一般] 之下的 [共用存取原則] 來存取這些原則。 在這個刀鋒視窗中，您可以修改現有的原則或新增原則。

### <a name="create-a-policy"></a>建立原則

* 按一下 [新增] 開啟刀鋒視窗。 您可以在此輸入新的原則名稱以及您想要與此原則產生關聯的權限，如在下一個圖中所示：

    有許多權限可與這些共用原則產生關聯。 [登錄讀取] 和 [登錄寫入] 原則會授與讀取和寫入存取權給身分識別登錄。 選擇寫入選項就會自動選擇讀取選項。

    [服務連線] 原則會授與存取服務端點的權限，例如**接收裝置到雲端**。 [裝置連線] 原則會授與使用 IoT 中樞裝置端端點傳送和接收訊息的權限。

* 按一下 [建立]  將此新建立的原則新增至現有的清單。

![][10]

## <a name="endpoints"></a>端點

按一下 [端點] 以顯示您正在修改之 IoT 中樞的端點清單。 端點有兩個類型︰IoT 中樞內建的端點，以及您在 IoT 中樞建立後新增的端點。

![][11]

### <a name="built-in-endpoints"></a>內建端點

有兩種內建端點︰**雲端到裝置回饋**和**事件**。

* **雲端到裝置回饋**設定：此設定有 2 個子設定：訊息的**雲端到裝置 TTL** (存留時間) 和**保留時間** (以小時為單位)。 當您第一次建立 IoT 中樞時，這兩個設定的預設值都是一個小時。 若要調整這些設定，可使用滑桿或輸入值。
* **事件**設定：這個設定有數個子設定，其中有些是唯讀。 下列清單說明這些設定：

  * **分割**：當 IoT 中樞建立後會設定預設值。 您可以透過此設定變更分割數目。

  * **事件中樞相容的名稱和端點**：建立 IoT 中樞時，事件中樞會在內部建立，而您在某些情況下可能需要其存取權。 您無法自訂事件中樞的相容名稱和端點值，但可以按一下 [複製] 來複製它們。

  * **保留時間**：預設為一天，但可以使用下拉式清單變更。 這是裝置到雲端設定以天為單位的值。

  * **取用者群組**：取用者群組可讓多個讀取器從 IoT 中樞獨立讀取訊息。 每個 IoT 中樞都是使用預設取用者群組建立的。 不過，您可以使用這個設定新增或刪除 IoT 中樞的取用者群組。

  > [!NOTE]
  > 預設的取用者群組無法編輯或刪除。

### <a name="custom-endpoints"></a>自訂端點

您可以使用入口網站在 IoT 中樞上新增自訂端點。 從 [端點] 刀鋒視窗，按一下頂端的 [新增] 以開啟 [新增端點] 刀鋒視窗。 輸入必要資訊，然後按一下 [確定]。 您的自訂端點現在會列在主要 [端點] 刀鋒視窗中。

![][13]

您可以在[參考 - IoT 中樞端點][lnk-devguide-endpoints]中深入了解自訂端點。

## <a name="routes"></a>路由

按一下 [路由] 以管理 IoT 中樞分派您的裝置到雲端訊息的方式。

![][14]

您可以將路由新增至 IoT 中樞，方法是按一下 [路由]* 刀鋒視窗頂端的 [新增]、輸入必要資訊，然後按一下 [確定]。 接著您的路由會列在主要 [路由] 刀鋒視窗中。 您可以在路由清單中按一下路由來編輯它。 若要啟用路由，按一下路由清單中的路由，並將 [已啟用] 切換為 [關閉]。 若要儲存變更，請按一下刀鋒視窗底部的 [確定]。

![][15]

## <a name="pricing-and-scale"></a>價格和調整

現有 IoT 中樞的價格可透過 [價格]  設定變更，但是有下列例外狀況：

* 在目前的實作中，具有免費 SKU 的 IoT 中樞無法變更為付費 SKU 層，反之亦然。
* 在 Azure 訂用帳戶中只能有一個免費層 IoT 中樞。

![][12]

該日傳送的訊息數目超過較低層級的配額時，您才能從較高層級移至較低層級。 例如，如果每天的訊息數目都超過 400,000，則 IoT 中樞的層會變更。 不過，如果您變更為 S1 層，IoT 中樞當天會進行節流。

## <a name="delete-the-iot-hub"></a>刪除 IoT 中樞

您可以藉由按一下 [瀏覽] ，然後選擇要刪除的適當中樞，即可瀏覽至您想要刪除的 IoT 中樞。 若要刪除 IoT 中樞，請按一下 IoT 中樞名稱下方的 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [大量管理 IoT 裝置][lnk-bulk]
* [IoT 中樞度量][lnk-metrics]
* [作業監視][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南][lnk-devguide]
* [使用 IoT Edge 來模擬裝置][lnk-iotedge]
* [徹底保護您的 IoT 解決方案][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
