---
title: "在 Azure 入口網站中建立和管理動作群組 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中建立和管理動作群組。"
author: anirudhcavale
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
ms.date: 05/15/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: ea15705bf02d9773507c6cb59f2da4c1dd0f9d77
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>在 Azure 入口網站中建立和管理動作群組
## <a name="overview"></a>概觀 ##
本文將說明如何在 Azure 入口網站中建立和管理動作群組。

您可以設定包含動作群組的動作清單。 接著，當您定義活動記錄警示時，可以使用這些群組。 然後您所定義的每個活動記錄警示就可以使用這些群組，確保在每次觸發活動記錄警示時皆採取相同的動作。

動作群組的每個動作類型可以有多達 10 個。 每個動作是由下列屬性所組成：

* **名稱**：動作群組內的唯一識別碼。  
* **動作類型**：傳送 SMS、傳送電子郵件或呼叫 Webhook。  
* **詳細資料**：對應的電話號碼、電子郵件地址或 Webhook URI。

如需如何使用 Azure Resource Manager 範本設定動作群組的資訊，請參閱[動作群組 Resource Manager 範本](monitoring-create-action-group-with-resource-manager-template.md)。

## <a name="create-an-action-group-by-using-the-azure-portal"></a>使用 Azure 入口網站建立動作群組 ##
1. 在 [入口網站][](https://portal.azure.com) 中，選取 [監視]。 [監視] 刀鋒視窗會將所有監視設定和資料合併在一個檢視中。

    ![「監視」服務](./media/monitoring-action-groups/home-monitor.png)
2. 在 [活動記錄] 區段中，選取 [動作群組]。

    ![使用 [動作群組] 索引標籤](./media/monitoring-action-groups/action-groups-blade.png)
3. 選取 [新增動作群組]，並填寫各欄位。

    ![「新增動作群組」命令](./media/monitoring-action-groups/add-action-group.png)
4. 在 [動作群組名稱] 方塊中輸入名稱，然後在 [簡短名稱] 方塊中，輸入名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

      ![「新增動作群組」對話方塊](./media/monitoring-action-groups/action-group-define.png)

5. [訂用帳戶] 方塊會自動填入您目前的訂用帳戶。 訂用帳戶是要儲存動作群組的位置。

6. 選取要在其中儲存動作群組的 [資源群組]。

7. 針對每個動作提供下列資訊，來定義動作的清單：

    a. **名稱**：輸入此動作的唯一識別碼。

    b. **動作類型**：選取 SMS、電子郵件或 Webhook。

    c. **詳細資料**：根據動作類型，輸入電話號碼、電子郵件地址或 Webhook URI。

8. 選取 [確定] 來建立動作群組。

## <a name="manage-your-action-groups"></a>管理您的動作群組 ##
建立動作群組之後，將會在 [監視] 刀鋒視窗的 [動作群組] 區段中顯示。 選取您要管理的動作群組：

* 新增、編輯或移除動作。
* 刪除動作群組。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)。  
* 了解[活動記錄警示 Webhook 結構描述](monitoring-activity-log-alerts-webhook.md)。  
* 深入了解警示的[速率限制](monitoring-alerts-rate-limiting.md)。 
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。

