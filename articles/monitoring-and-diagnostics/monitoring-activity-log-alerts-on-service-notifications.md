---
title: "接收 Azure 服務通知的活動記錄警示 | Microsoft Docs"
description: "在 Azure 服務發生時透過 SMS、電子郵件或 Webhook 獲得通知。"
author: johnkemnetz
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 6e011ea3d9d8f8453068d43e390cfba46dfb3277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>建立服務通知的活動記錄警示
## <a name="overview"></a>概觀
本文將說明如何使用 Azure 入口網站為服務健康情況通知設定活動記錄警示。  

您可以在 Azure 傳送服務健康狀態通知到您的 Azure 訂用帳戶時接收警示。 您可以針對下列設定警示：

- 服務健康情況通知的類別 (服務問題、計劃性維護或健康情況摘要報告)。
- 受影響的訂閱。
- 受影響的服務。
- 受影響的區域。

您也可以設定應傳送警示的對象：

- 選取現有的動作群組。
- 建立新動作群組 (可用於未來的警示)。

若要深入了解動作群組，請參閱[建立及管理動作群組](monitoring-action-groups.md)。

若要了解如何使用 Azure Resource Manager 範本來設定服務健康情況通知警示，請參閱 [Resource Manager 範本](monitoring-create-activity-log-alerts-with-resource-manager-template.md)。

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站為新動作群組建立服務健康情況通知的警示
1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]。

    ![「服務健康情況」服務](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. 在 [警示] 區段中，選取 [健康情況警示]。

    ![[健康情況警示] 索引標籤](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. 選取 [建立服務健康情況警示] 並填入欄位。

    ![「建立服務健康情況警示」命令](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. 在 [活動記錄警示名稱] 方塊中輸入名稱，並提供 [描述]。

    ![「新增活動記錄警示」對話方塊](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group-sh.png)

5. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 此訂用帳戶會用來儲存活動記錄警示。 警示資源會部署到此訂用帳戶，並從中監視活動記錄事件。

6. 選取將在其中建立警示資源的 [資源群組]。 這不是由警示所監視的資源群組， 而是警示資源所在的資源群組。

7. [事件類別目錄] 方塊會自動設為 [服務健康情況]。 或是選擇您想要接收服務健康情況通知的 [服務]、[區域] 和 [類型]。

8. 在 [Alert via]\(警示媒介\) 下，選取 [新增] 動作群組按鈕。 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 當此警示發動時，通知中會引用該簡短名稱。

9. 請提供接收者的下列各項，以定義接收者的清單：

    a. **名稱**：輸入接收者名稱、別名或識別碼。

    b. **動作類型**：選取簡訊、電子郵件、Webhook 和 Azure 應用程式等。

    c. **詳細資料**：根據選擇的動作類型，輸入電話號碼、電子郵件地址或 Webhook URI 等。

10. 選取 [確定] 可建立警示。

在幾分鐘之內會啟用警示，開始根據建立時所指定的條件觸發警示。

了解如何[設定現有問題管理系統的 Webhook 通知](../service-health/service-health-alert-webhook-guide.md)。 如需活動記錄警示之 Webhook 結構描述的資訊，請參閱 [Azure 活動記錄警示的 Webhook](monitoring-activity-log-alerts-webhook.md)。

>[!NOTE]
>在這些步驟中定義的動作群組，會以現有動作群組的形式提供給所有未來的警示定義重複使用。
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站，為現有動作群組建立服務健康情況通知的警示

1. 請遵循上一節的步驟 1 到 7，以建立您的服務健康情況通知。 

2. 在 [Alert via]\(警示媒介\) 下，選取 [現有] 動作群組按鈕。 選取適當的動作群組。

3. 選取 [確定] 可建立警示。

在幾分鐘之內會啟用警示，開始根據建立時所指定的條件觸發警示。

## <a name="manage-your-alerts"></a>管理警示

警示建立之後，會顯示在 [監視] 的 [警示] 區段中。 選取您要管理的警示：

* 進行編輯。
* 進行刪除。
* 如果您需要暫時停止或恢復接收警示的通知，可以將警示停用或啟用。

## <a name="next-steps"></a>後續步驟
- 了解如何[設定現有問題管理系統的 Webhook 通知](../service-health/service-health-alert-webhook-guide.md)。
- 深入了解[服務健康狀態通知](monitoring-service-notifications.md)。
- 深入了解[通知速率限制](monitoring-alerts-rate-limiting.md)。
- 檢閱[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)。
- 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。 
- 深入了解[動作群組](monitoring-action-groups.md)。
