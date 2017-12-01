---
title: "Azure 活動記錄檔概觀 | Microsoft Docs"
description: "認識 Azure 活動記錄檔，並了解如何使用它來了解您的 Azure 訂用帳戶內發生的事件。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: johnkem
ms.openlocfilehash: 4a796920d5ff76d4ff4d41afe2ec14aa89ae2265
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>使用 Azure 活動記錄監視訂用帳戶活動
**Azure 活動記錄**是訂用帳戶記錄，可讓您深入探索 Azure 中發生的訂用帳戶層級事件。 所涵蓋的資料範圍從 Azure Resource Manager 作業資料到服務健康情況事件的更新。 活動記錄之前稱為「稽核記錄」或「作業記錄」，因為系統管理類別會報告訂用帳戶中控制層面的事件。 您可以使用活動記錄檔來判斷訂用帳戶中的資源上任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。 您也可以了解作業的狀態和其他相關屬性。 活動記錄不包含讀取 (GET) 作業，或是使用傳統/"RDFE" 模型之資源的作業。

![活動記錄與其他類型的記錄 ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

圖 1：活動記錄與其他類型的記錄

活動記錄不同於[診斷記錄](monitoring-overview-of-diagnostic-logs.md)。 活動記錄會提供有關外部資源作業的資料 (控制層面)。 診斷記錄是由資源所發出，會提供該資源作業的相關資訊 (資料層面)。

您可以使用 Azure 入口網站、CLI、PowerShell Cmdlet、Azure 監視器 REST API 從活動記錄檔擷取事件。


> [!WARNING]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 此記錄不會追蹤使用傳統/RDFE 模型的資源。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在傳統入口網站或 Azure Resource Manager Proxy 之外與傳統資源類型互動，則您的動作只會記錄在「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。
>
>

檢視下列簡介活動記錄的影片。
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>活動記錄中的類別
活動記錄包含數個資料類別。 如需這些類別結構描述的完整詳細資料，[請參閱這篇文章](monitoring-activity-log-schema.md)。 其中包含：
* **系統管理** - 透過 Resource Manager 執行的所有建立、更新、刪除和動作作業皆記錄在此類別中。 您可能會在此類別中看到的事件類型範例包括「建立虛擬機器」和「刪除網路安全性群組」。使用者或應用程式使用 Resource Manager 所執行的每個動作，都會成為特定資源類型上的作業模型。 如果作業類型為「寫入」、「刪除」或「動作」，則該作業的啟動及成功或失敗記錄皆會記錄在「系統管理」類別。 「系統管理」類別也包含訂用帳戶中角色型存取控制的所有變更。
* **服務健康情況** - 所有在 Azure 中發生的服務健康情況事件皆記錄在此類別中。 您可能會在此類別中看到的事件類型範例為「美國東部的 SQL Azure 發生停機事件」。 服務健康情況事件有五個種類：必要動作、協助復原、事件、維護、資訊或安全性，這些情況只會在事件可能影響訂用帳戶中的資源時顯示。
* **警示** - 此類別包含所有 Azure 警示的啟用記錄。 您可能會在此類別中看到的事件類型範例為「myVM 上的 CPU 百分比在過去 5 分鐘內已超過 80」 各種 Azure 系統都有警示概念，您可以定義某種類型的規則，並在條件符合該規則時接收通知。 每次支援的 Azure 警示類型「啟動」時，或產生通知的條件符合時，該啟用記錄會也會推送至此類別的活動記錄。
* **自動調整規模** - 所有與自動調整規模引擎 (以訂用帳戶中定義的自動調整規模設定為基礎) 作業相關的所有事件皆記錄在此類別。 您可能會在此類別中看到的事件類型範例為「自動調整規模的相應增加動作失敗」 自動調整可讓您使用自動調整設定，依據每日時間和/或負載 (計量) 資料，自動相應放大或縮小受支援資源類型中的執行個體數目。 符合相應增加或相應減少的條件時，啟動及成功或失敗事件會記錄在此類別中。
* **建議** - 此類別包含來自特定資源類型 (如網站和 SQL 伺服器) 的建議事件。 這些事件會提供如何充分運用資源的建議。 只有擁有會發出建議的資源時，您才會收到此類型的事件。
* **安全性** - 此類別包含「Azure 資訊安全中心」所產生之任何警示的記錄。 其中一個您會在此類別中看到的事件類型範例為「執行疑似雙重附檔名的檔案」。
* **原則和資源健康狀態** - 這些類別不包含任何事件；僅保留以供未來使用。

## <a name="event-schema-per-category"></a>每個類別的事件結構描述
[請參閱這篇文章以了解每一類活動記錄事件的結構描述。](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Azure 活動記錄檔的用途
以下是您可以利用活動記錄檔進行的事：

![Azure 活動記錄](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* 在 **Azure 入口網站**中查詢和檢視活動記錄檔。
* [根據活動記錄事件建立警示。](monitoring-activity-log-alerts.md)
* [將活動記錄檔串流至**事件中樞**](monitoring-stream-activity-logs-event-hubs.md)，以利第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。
* 使用 [**PowerBI 內容套件**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)在 PowerBI 中分析活動記錄檔。
* [將活動記錄檔儲存到**儲存體帳戶**以供封存或手動檢查](monitoring-archive-activity-log.md)。 您可以使用**記錄設定檔**指定保留時間 (以天為單位)。
* 透過 PowerShell Cmdlet、CLI 或 REST API 查詢活動記錄。

## <a name="query-the-activity-log-in-the-azure-portal"></a>在 Azure 入口網站中查詢活動記錄
在 Azure 入口網站中，您可以從幾個地方檢視活動記錄：
* [活動記錄] 刀鋒視窗，您可以在左側瀏覽窗格的 [更多服務] 下搜尋活動記錄來存取此刀鋒視窗。
* [監視器] 刀鋒視窗，預設會顯示在左側瀏覽窗格中。 [活動記錄] 會是此 [Azure 監視器] 刀鋒視窗的一個區段。
* 任何資源的**資源刀鋒視窗**，例如虛擬機器的設定刀鋒視窗。 [活動記錄] 會是大多數這類資源刀鋒視窗中的其中一個區段，按一下即可將事件自動篩選為與該特定資源相關的事件。

在 Azure 入口網站中，您可以依下列欄位篩選活動記錄：
* 時間範圍 - 事件的開始和結束時間。
* 類別 - 如上所述的事件類別。
* 訂用帳戶 - 一或多個 Azure 訂用帳戶名稱。
* 資源群組 - 這些訂用帳戶中的一或多個資源群組。
* 資源 (名稱) - 特定資源的名稱。
* 資源類型 - 資源的類型，例如 Microsoft.Compute/virtualmachines。
* 作業名稱 - Azure Resource Manager 作業的名稱，例如 Microsoft.SQL/servers/Write。
* 嚴重性 - 事件的嚴重性層級 (資訊、警告、錯誤、重大)。
* 事件起始者 -「呼叫者」或執行作業的使用者。
* 開啟搜尋 - 這是開啟文字搜尋方塊，可在所有事件的所有欄位中搜尋該字串。

定義一組篩選之後，如果未來需要再次套用這些篩選以執行相同的查詢，您可以將它儲存為在工作階段間保存的查詢。 您也可以將查詢釘選到 Azure 儀表板，以持續留意特定事件。

按一下 [套用] 執行您的查詢，並顯示所有相符的事件。 按一下清單中的任何事件即可顯示該事件的摘要，以及該事件的完整原始 JSON。

若要取得更強大的功能，您可以按一下**記錄搜尋**圖示，以顯示 [Log Analytics 活動記錄分析解決方案](../log-analytics/log-analytics-activity.md)中的活動記錄資料。 [活動記錄] 刀鋒視窗提供記錄的基本篩選/瀏覽體驗，而 Log Analytics 則可讓您以更強大的方式對資料進行樞紐分析、查詢和視覺化。

## <a name="export-the-activity-log-with-a-log-profile"></a>使用記錄設定檔匯出活動記錄
**記錄檔設定檔** 控制活動記錄檔的匯出方式。 使用記錄檔設定檔，您可以設定︰

* 活動記錄檔應該要傳送至何處 (儲存體帳戶或事件中樞)
* 應該要傳送何種事件分類 (Write、Delete、Action)。 *「類別」一詞在記錄設定檔和活動記錄事件中的意義並不相同。在記錄設定檔中，「類別」代表作業類型 (寫入、刪除、動作)。在活動記錄事件中，「類別」屬性代表事件的來源或類型 (例如，管理、ServiceHealth、警示等等)。*
* 應該要匯出哪一個區域 (位置)。 請務必包含「全域」，因為活動記錄中的許多事件都是全域事件。
* 活動記錄應該在儲存體帳戶中保留多久。
    - 保留期為 0 天表示會永遠保留記錄。 否則，此值可以是 1 到 2147483647 之間的任意天數。
    - 如果有設定保留原則，但將儲存體帳戶的記錄檔儲存停用 (例如，如果只選取事件中樞或 OMS 選項)，保留原則不會有任何作用。
    - 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄會被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄檔會被刪除。

您可以使用並非發出記錄的同一個訂用帳戶中的儲存體帳戶或事件中樞命名空間。 進行此設定的使用者必須具有這兩個訂用帳戶的適當 RBAC 存取權。

這些設定可透過入口網站中 [活動記錄檔] 刀鋒視窗中的 [匯出] 選項來設定。 也可以[使用 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell Cmdlet 或 CLI 以程式設計方式設定。 一個訂用帳戶只能有一個記錄檔的設定檔。

### <a name="configure-log-profiles-using-the-azure-portal"></a>使用 Azure 入口網站設定記錄檔設定檔
您可以將活動記錄檔串流至事件中樞，或在 Azure 入口網站中使用 [匯出] 選項將它們儲存在儲存體帳戶。

1. 使用入口網站左側的功能表，瀏覽至 [活動記錄檔]  刀鋒視窗。

    ![在入口網站中瀏覽至活動記錄檔](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下刀鋒視窗頂端的 [匯出]  按鈕。

    ![入口網站中的匯出按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現的刀鋒視窗中，您可以選取︰  
  * 您要匯出事件的區域
  * 您要儲存事件的儲存體帳戶
  * 您想要在儲存體中保留這些事件的天數。 如果設定為 0 天會永遠保留記錄檔。
  * 服務匯流排命名空間，您要在其中建立事件中樞以對這些事件進行串流處理。

     ![匯出活動記錄檔刀鋒視窗](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存]  來儲存這些設定。 您的訂用帳戶時會立即套用設定。

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>使用 Azure PowerShell Cmdlet 設定記錄檔設定檔
#### <a name="get-existing-log-profile"></a>取得現有的記錄檔設定檔
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>新增記錄檔設定檔
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| Name |是 |記錄檔設定檔的名稱。 |
| StorageAccountId |否 |資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| serviceBusRuleId |否 |服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。 將會是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
| RetentionInDays |是 |事件應保留的天數，1 到 2147483647 之間。 值為 0 會無限期地 (永遠) 儲存記錄檔。 |
| 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值有 Write、Delete、Action。 |

#### <a name="remove-a-log-profile"></a>移除記錄檔設定檔
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>使用 Azure 跨平台 CLI 設定記錄檔設定檔
#### <a name="get-existing-log-profile"></a>取得現有的記錄檔設定檔
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` 屬性應該是您的記錄檔設定檔的名稱。

#### <a name="add-a-log-profile"></a>新增記錄檔設定檔
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 名稱 |是 |記錄檔設定檔的名稱。 |
| storageId |否 |資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| serviceBusRuleId |否 |服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。 將會是此格式的字串︰`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
| RetentionInDays |是 |事件應保留的天數，1 到 2147483647 之間。 值為 0 會無限期地 (永遠) 儲存記錄檔。 |
| 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值有 Write、Delete、Action。 |

#### <a name="remove-a-log-profile"></a>移除記錄檔設定檔
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>後續步驟
* [深入了解活動記錄檔 (之前的稽核記錄檔)](../azure-resource-manager/resource-group-audit.md)
* [將 Azure 活動記錄檔串流至事件中樞](monitoring-stream-activity-logs-event-hubs.md)
