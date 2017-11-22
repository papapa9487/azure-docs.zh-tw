---
title: "部署適用於 Azure IoT Edge 的模組 | Microsoft Docs"
description: "了解如何將模組部署到 Edge 裝置"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: d8688ab2daefd400e9c0948853459dd238fa0d43
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>了解針對單一裝置或大規模部署 IoT Edge - 預覽

Azure IoT Edge 裝置會遵循類似其他 IoT 裝置類型的[裝置生命週期][ lnk-lifecycle]：

1. 佈建 IoT Edge 裝置，其中涉及利用 OS 來處理裝置映像，以及安裝 [IoT Edge 執行階段][lnk-runtime]。
1. 裝置會設定來執行 [IoT Edge 模組][lnk-modules]，然後監視其健康情況。 
1. 最後，當裝置被取代時可能就會被淘汰或變成過時。  

Azure IoT Edge 提供兩種方式來設定要在 IoT Edge 裝置上執行的模組：一個用於單一裝置 (您在 Azure IoT Edge 教學課程中所使用) 上的開發和快速反覆項目，另一個則用於管理大群 IoT Edge 裝置。 這兩種方法均可在 Azure 入口網站中以及利用程式設計方式來使用。

本文著重於多群裝置的設定和監視階段，統稱為 IoT Edge 部署。 整體部署步驟如下所示：   

1. 操作員會定義部署，其中描述一組模組以及目標裝置。 每個部署都含有反映此資訊的部署資訊清單。 
1. IoT 中樞服務會與所有目標裝置通訊，利用所需模組來設定它們。 
1. IoT 中樞服務會從 IoT Edge 裝置擷取狀態，並向要監視的操作員呈現那些狀態。  例如，操作員可在未成功設定 Edge 裝置時看見，或是查看模組在執行階段期間是否失敗。 
1. 隨時都可針對部署設定符合目標條件的新 IoT Edge 裝置。 例如，若部署的目標是在華盛頓州的所有 IoT Edge 裝置，一旦將其佈建並新增至「華盛頓州」裝置群組之後，就會自動設定新的 IoT Edge 裝置。 
 
本文將逐步解說用以設定及監視部署的每個元件。 如需建立和更新部署的逐步解說，請參閱[大規模部署和監視 IoT Edge 模組][lnk-howto]。

## <a name="deployment"></a>部署

部署會指派 IoT Edge 模組映像，在一組目標的 IoT Edge 裝置上當成執行個體來執行。 其運作方式是設定 IoT Edge 部署資訊清單，以包含具有對應初始化參數的模組清單。 您可以將部署指派到單一裝置 (通常會以裝置識別碼為根據) 或裝置群組 (以標記為根據)。 一旦 IoT Edge 裝置接收到部署資訊清單之後，就會從各自的容器存放庫下載並安裝模組容器映像，並據以設定它們。 建立部署之後，操作員就能監視部署狀態，以查看是否已正確設定目標裝置。   

裝置需要佈建為要使用部署來設定的 IoT Edge 裝置。 以下為必要條件，且不會包含於部署中：
* 基本作業系統
* Docker 
* 佈建 IoT Edge 執行階段 

### <a name="deployment-manifest"></a>部署資訊清單

部署資訊清單是一份 JSON 文件，其中描述要在目標 IoT Edge 裝置上設定的模組。 其中包含所有模組的設定中繼資料，包括必要的系統模組 (特別是 IoT Edge 代理程式和 IoT Edge 中樞)。  

每個模組的設定中繼資料都會包括： 
* 版本 
* 類型 
* 狀態 (例如，執行中或已停止) 
* 重新啟動原則 
* 映像和容器存放庫 
* 資料輸入和輸出的路由 

### <a name="target-condition"></a>目標條件

目標條件會指定 IoT Edge 裝置是否應位於部署的範圍內。 目標條件取決於裝置對應項標記。 

### <a name="priority"></a>優先順序

優先順序會定義是否應將部署套用到相對於其他部署的目標裝置。 部署優先順序為正整數，以較大的數字表示較高的優先順序。 如果有多個部署將目標設為某個 IoT Edge 裝置，則會套用優先順序最高的部署。  不會套用優先順序較低的部署，也不會將它們合併。  如果有兩個以上具有相同優先順序的部署將目標設為某個裝置，則會套用最新建立的部署 (取決於建立時間戳記)。

### <a name="labels"></a>標籤 

標籤是字串索引鍵/值組，可用來篩選和群組部署。 部署可能會有多個標籤。 標籤是選擇性的，不會對 IoT Edge 裝置的實際設定產生任何影響。 

### <a name="deployment-status"></a>部署狀態

您可以監視部署，以判斷是否已針對任何目標 IoT Edge 裝置成功套用該部署。  目標 Edge 裝置將出現在下列一或多個狀態類別中： 
* **目標**會顯示符合部署目標條件的 IoT Edge 裝置。
* **實際**會顯示另一個優先順序較高的部署並未設為目標的目標 IoT Edge 裝置。
* **健康情況**顯示的 IoT Edge 裝置已向服務回報已成功部署模組。 
* **狀況不良**顯示 IoT Edge 裝置已向服務回報未成功部署一或多個模組。 若要進一步調查此錯誤，請遠端連線到那些裝置並檢視記錄檔。
* **未知**顯示的 IoT Edge 裝置並未回報任何有關此部署的狀態。 若要進一步調查，請檢視服務資訊和記錄檔。

## <a name="phased-rollout"></a>階段式推出 

階段式推出是一個整體程序，操作員可藉以將變更部署至一組範圍廣泛的 IoT Edge 裝置。 目標是逐漸進行變更，以降低進行大規模重大變更的風險。  

階段式推出會在下列階段和步驟中執行： 
1. 建立 IoT Edge 裝置的測試環境，方法是佈建它們，並設定裝置對應項標記，例如 `tag.environment='test'`。 測試環境應該鏡像處理部署最終將設為目標的生產環境。 
1. 建立部署，其中包括所需的模組和設定。 目標條件應將目標設為測試 IoT Edge 裝置環境。   
1. 在測試環境中驗證新的模組設定。
1. 更新部署，藉由將新標記新增至目標條件，來包括生產 IoT Edge 裝置子集。 此外，確定部署的優先順序高於目前將目標設為那些裝置的其他部署。 
1. 藉由檢視部署狀態，來確認已在目標 IoT 裝置上成功部署。
1. 更新部署，將目標設為所有剩餘的生產 IoT Edge 裝置。

## <a name="rollback"></a>復原

部署可以在發生錯誤或設定錯誤的情況下復原。  由於部署會定義 IoT Edge 裝置的絕對模組設定，因此，即使目標是要移除所有模組，也必須以較低的優先順序將其他部署的目標設為同一個裝置。  

依照以下順序執行復原： 
1. 確認第二個部署也會在同一個裝置集合上設定目標。 如果復原的目標是要移除所有模組，則第二個部署不應包含任何模組。 
1. 修改或移除您想要復原以使裝置不再符合目標條件之部署的目標條件運算式。
1. 藉由檢視部署狀態來確認已成功復原。
   * 已復原的部署應該不會再顯示已復原裝置的狀態。
   * 第二個部署現在應該會包含已復原裝置的部署狀態。


## <a name="next-steps"></a>後續步驟

* 逐步解說[大規模部署和監視 IoT Edge 模組][lnk-howto]中，用來建立、更新或刪除部署的步驟。
* 深入了解其他 IoT Edge 概念，例如 [IoT Edge 執行階段][lnk-runtime]和 [IoT Edge 模組][lnk-modules]。

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md