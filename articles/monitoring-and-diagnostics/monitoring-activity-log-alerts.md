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
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>建立活動記錄警示

## <a name="overview"></a>概觀
活動記錄警示是發生符合警示中指定條件的新活動記錄事件時啟動的警示。 這些是 Azure 資源，因此可使用 Resource Manager 範本加以建立，並在 Azure 入口網站中加以建立、更新或刪除。 本文介紹活動記錄警示背後的概念，並接著說明如何使用 Azure 入口網站來設定活動記錄事件的警示。

您通常會針對下列兩個目的之一建立活動記錄警示：
1. 若要在 Azure 訂用帳戶中的資源發生特定變更時收到通知 (通常會將範圍限定在特定資源群組或資源)。 例如，您可能想要在 myProductionResourceGroup 中的任何虛擬機器被刪除時收到通知。 或者，您可能想要在將任何新角色指派給訂用帳戶中的使用者時收到通知。
2. 若要在發生服務健康狀態事件時收到通知。 服務健康狀態事件包括適用於訂用帳戶中資源的事件 (incident) 和維護事件 (event) 通知。

在任一情況下，活動記錄警示只會監視警示建立所在之訂用帳戶中的事件。

您可以針對活動記錄事件，設定依據 JSON 物件中任何最上層屬性的活動記錄警示，但入口網站只會顯示最常見的選項：
- **類別** - 系統管理、服務健康狀態、自動調整規模及建議的其中之一。 [請參閱這篇文章以取得活動記錄類別的資訊](./monitoring-overview-activity-logs.md#categories-in-the-activity-log)，並[在這裡深入了解服務健康狀態事件](./monitoring-activity-log-alerts-on-service-notifications.md)。
- **資源群組**
- **Resource**
- **資源類型**
- **作業名稱** - Resource Manager RBAC 作業名稱。
- **層級** - 事件的安全性層級 (詳細資訊、資訊、警告、錯誤、重大)
- **狀態** - 事件的狀態，通常包括「已啟動」、「失敗」或「成功」
- **事件起始者** - 也稱為「呼叫者」。 執行作業之使用者的電子郵件地址或 Active Directory 識別項。

>[!NOTE]
>您必須在您的警示中指定至少兩個上述準則，其中一個是類別。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
>
>

當活動記錄警示啟動之後，會使用動作群組來產生動作或通知。 動作群組是可從多個警示參考之一組可重複使用的通知接收者 (電子郵件地址、Webhook URL 或簡訊電話號碼)，以便集中管理並分組您的通知通道。 您可以在活動記錄警示中使用現有的動作群組，或在定義活動記錄警示時建立新的動作群組。 您可以在[這裡](monitoring-action-groups.md)深入了解動作群組

您可以在[這裡](monitoring-activity-log-alerts-on-service-notifications.md)深入了解服務健康狀態通知的活動記錄警示

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>使用 Azure 入口網站為新動作群組建立活動記錄事件的警示
1.  在[入口網站](https://portal.azure.com)中，巡覽至 [監視] 刀鋒視窗。

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄] 刀鋒視窗。

3.  現在按一下 [警示] 刀鋒視窗。

    ![Alerts](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  選取 [新增活動記錄警示] 命令，並填寫各欄位。

5.  為活動記錄警示**命名**，並選擇**描述**。

    ![新增警示](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  選取的 [訂用帳戶] 是要儲存活動記錄警示的位置。 系統會在您目前工作的訂用帳戶中自動填入資訊。 這是警示資源部署所在及從中監視活動記錄事件的訂用帳戶。

    ![新增警示新動作群組](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  選擇將在其中建立此警示資源的 [資源群組]。 請注意，這不是「受到」警示監視的資源群組，而是「警示資源本身部署所在」的資源群組。

8.  選擇性地選取 [事件類別]，這會修改所顯示的額外篩選集合。 若是系統管理事件，這會包括依 [資源群組]、[資源]、[資源類型]、[作業名稱]、[層級]、[狀態] 和 [事件起始者] 值篩選的選項，以識別此警示應監視的事件。

>[!NOTE]
>您必須在您的警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
>
>

9.  為動作群組提供**名稱**和**簡短名稱**，以建立**新的**動作群組；系統會在啟動這個警示時，在傳送的通知中參考此簡短名稱。

10. 然後，透過提供動作的下列各項來定義動作的清單

    a. **名稱：**動作的名稱、別名或識別項。

    b. **動作類型：**選擇動作類型：簡訊、電子郵件或 Webhook

    c. **詳細資料：**根據選擇的動作類型，提供電話號碼、電子郵件地址或 Webhook URI。

11. 完成後選取 [確定]  建立警示。

該警示將需要幾分鐘的時間才能完全傳播，之後會變成作用中，並在新的事件符合警示的準則時觸發。

[如需活動記錄警示 Webhook 結構描述的詳細資料，請參閱此文件](monitoring-activity-log-alerts-webhook.md)。

>[!NOTE]
>在這些步驟中定義的動作群組，會以現有的動作群組的形式提供所有未來的警示定義重複使用。
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>使用 Azure 入口網站為現有動作群組建立活動記錄事件的警示
1.  在[入口網站](https://portal.azure.com)中，巡覽至 [監視] 刀鋒視窗。

    ![監視](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  按一下 [監視] 選項來開啟 [監視] 刀鋒視窗。 它會先開啟 [活動記錄檔]  區段。

3.  現在按一下 [警示] 一節。

    ![Alerts](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  選取 [新增活動記錄警示] 命令，並填寫各欄位。

5.  為活動記錄警示**命名**，並選擇**描述**。 當此警示發動時，這些會出現在傳送的通知中。

    ![新增警示](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  選取的 [訂用帳戶] 是要儲存活動記錄警示的位置。 系統會在您目前工作的訂用帳戶中自動填入資訊。 這是警示資源部署所在及從中監視活動記錄事件的訂用帳戶。

    ![新增警示現有動作群組](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  選擇將在其中建立此警示資源的 [資源群組]。 請注意，這不是「受到」警示監視的資源群組，而是「警示資源本身部署所在」的資源群組。

8.  選擇性地選取 [事件類別]，這會修改所顯示的額外篩選集合。 若是系統管理事件，這會包括依 [資源群組]、[資源]、[資源類型]、[作業名稱]、[層級]、[狀態] 和 [事件起始者] 值篩選的選項，以識別此警示應監視的事件。

9.  針對 [透過下列方式通知] 選擇 [現有的動作群組]。 從清單中選取現有的動作群組。

10. 完成後選取 [確定]  建立警示。

該警示將需要幾分鐘的時間才能完全傳播，之後會變成作用中，並在新的事件符合警示的準則時觸發。

## <a name="managing-your-alerts"></a>管理警示

一旦您已建立警示，它會顯示在 [監視器] 刀鋒視窗的 [警示] 區段中。 選取您想要管理的警示，而且您能夠：
* **編輯**它。
* **刪除**它。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。

## <a name="next-steps"></a>後續步驟
- 取得[警示概觀](monitoring-overview-alerts.md)
- 深入了解[通知速率限制](monitoring-alerts-rate-limiting.md)
- 檢閱[活動記錄警示 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
- 深入了解[動作群組](monitoring-action-groups.md)  
- 深入了解[服務健康狀態通知](monitoring-service-notifications.md)
- [建立活動記錄警示以監視訂用帳戶的所有自動調整引擎作業。](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [建立活動記錄警示以監視訂用帳戶中所有失敗的相應縮小/相應放大自動調整作業](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

