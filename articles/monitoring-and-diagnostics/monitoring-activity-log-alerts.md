---
title: "建立活動記錄警示 | Microsoft Docs"
description: "活動記錄中發生特定事件時，透過 SMS、Webhook 及電子郵件收到通知。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts"></a>建立活動記錄警示

## <a name="overview"></a>概觀
活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。 它們是 Azure 資源，因此可以使用 Azure Resource Manager 範本來加以建立。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 本文介紹活動記錄警示背後的概念。 然後將說明如何使用 Azure 入口網站來設定活動記錄事件的警示。

通常，您在下列情況要建立活動記錄警示來接收通知：

* 在 Azure 訂用帳戶中的資源發生特定變更時，通常會將範圍限定在特定資源群組或資源。 例如，在 myProductionResourceGroup 中的任何虛擬機器被刪除時，您可能需要收到通知。 或者，您需要在將任何新角色指派給訂用帳戶中的使用者時收到通知。
* 隨即發生服務健康情況事件。 服務健康狀態事件包括適用於訂用帳戶中資源的事件 (incident) 和維護事件 (event) 通知。

在任一情況下，活動記錄警示只會監視建立警示所在之訂用帳戶中的事件。

您可以針對活動記錄事件，以 JSON 物件中任何最上層屬性作為基礎設定活動記錄警示。 不過，入口網站會顯示最常用的選項：

- **類別**：系統管理、服務健康狀態、自動調整規模及建議。 如需詳細資訊，請參閱 [Azure 活動記錄概觀](./monitoring-overview-activity-logs.md#categories-in-the-activity-log)。 若要深入了解服務健康情況事件，請參閱[在服務通知上接收活動記錄警示](./monitoring-activity-log-alerts-on-service-notifications.md)。
- **資源群組**
- **Resource**
- **資源類型**
- **作業名稱**：Resource Manager 角色型存取控制作業名稱。
- **層級**：事件的安全性層級 (詳細資訊、資訊、警告、錯誤或重大)。
- **狀態**：事件的狀態，通常為「已啟動」、「失敗」或「成功」。
- **事件起始者**：也稱為「呼叫者」。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

>[!NOTE]
>您必須在您的警示中指定至少兩個上述準則，其中一個是類別。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
>
>

當活動記錄警示啟動時，會使用動作群組來產生動作或通知。 動作群組是一組可重複使用的通知接收者，例如電子郵件地址、Webhook URL 或 SMS 電話號碼。 可以從多個警示參考接收者，將通知通道集中管理並群組。 當您定義活動記錄警示時，會有兩個選項。 您可以：

* 在您的活動記錄警示中使用現有的動作群組。 
* 建立新的動作群組。 

若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](monitoring-action-groups.md)。

若要深入了解服務健康情況通知，請參閱[在服務健康情況通知上接收活動記錄警示](monitoring-activity-log-alerts-on-service-notifications.md)。

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站為新動作群組建立活動記錄事件的警示
1. 在 [入口網站](https://portal.azure.com) 中，選取 [監視]。

    ![監視」服務](./media/monitoring-activity-log-alerts/home-monitor.png)
2. 在 [活動記錄] 區段中，選取 [警示]。

    ![「警示」索引標籤](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. 選取 [新增活動記錄警示]，並填寫各欄位。

4. 在 [活動記錄警示名稱] 方塊中輸入名稱，並選取 [描述]。

    ![[新增活動記錄警示] 命令](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。 警示資源會部署到此訂用帳戶，並從中監視活動記錄事件。

    ![[新增活動記錄警示] 對話方塊](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. 選取將在其中建立警示資源的 [資源群組]。 這不是由警示所監視的資源群組。 相反地，這是警示資源所在的資源群組。

7. 選擇性地選取 [事件類別]，從而修改所顯示的額外篩選條件。 在系統管理事件中，篩選條件包括 [資源群組]、[資源]、[資源類型]、作業名稱、層級、狀態 和 [事件初始化方式]。 這些值會識別此警示應該監視的事件。

    >[!NOTE]
    >您必須在您的警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
    >
    >

8. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

9.  透過提供動作的下列各項來定義動作的清單：

    a. **名稱**：輸入動作的名稱、別名或識別碼。

    b. **動作類型**：選取 SMS、電子郵件或 Webhook。

    c. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址或 Webhook URI。

10. 選取 [確定] 可建立警示。

警示需要花幾分鐘時間完全傳播，然後就會變成作用中。 新的事件符合警示的準則時，就會將它觸發。

如需詳細資訊，請參閱[了解活動記錄警示中使用的 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)。

>[!NOTE]
>在這些步驟中定義的動作群組，會以現有動作群組的形式提供所有未來的警示定義重複使用。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站為現有動作群組建立活動記錄事件的警示
1. 請遵循上一節的步驟 1 到 7，以建立您的活動記錄警示。

2. 在 [通知媒介] 下，選取 [現有] 動作群組按鈕。 從清單中選取現有的動作群組。

3. 選取 [確定] 可建立警示。

警示需要花幾分鐘時間完全傳播，然後就會變成作用中。 新的事件符合警示的準則時，就會將它觸發。

## <a name="manage-your-alerts"></a>管理您的警示

建立警示之後，它會顯示在 [監視] 刀鋒視窗的 [警示] 區段中。 選取您要管理的警示：

* 進行編輯。
* 進行刪除。
* 如果您需要暫時停止或恢復接收警示的通知，可以將警示停用或啟用。

## <a name="next-steps"></a>後續步驟
- 取得[警示概觀](monitoring-overview-alerts.md)。
- 深入了解[通知速率限制](monitoring-alerts-rate-limiting.md)。
- 檢閱[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)。
- 深入了解[動作群組](monitoring-action-groups.md)。  
- 深入了解[服務健康狀態通知](monitoring-service-notifications.md)。
- [建立活動記錄警示以監視訂用帳戶的所有自動調整引擎作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)。
- [建立活動記錄警示以監視訂用帳戶中所有失敗的相應縮小/相應放大自動調整作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)。
