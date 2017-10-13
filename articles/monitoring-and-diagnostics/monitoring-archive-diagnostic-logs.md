---
title: "封存 Azure 診斷記錄 | Microsoft Docs"
description: "了解如何封存 Azure 診斷記錄以在儲存體帳戶中長期保存。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.openlocfilehash: dbc5f89001dcb6cd1ab061cb0a9632e4e5d2c1c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="archive-azure-diagnostic-logs"></a>封存 Azure 診斷記錄
在本文中，我們會示範如何使用 Azure 入口網站、PowerShell Cmdlet、CLI 或 REST API 來封存儲存體帳戶中的 [Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)。 如果您想要使用適用於稽核、靜態分析或備份的選用保留原則來保留診斷記錄，這個選項非常有用。 儲存體帳戶不一定要和資源發出記錄檔屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。

## <a name="prerequisites"></a>必要條件
在開始之前，您需要[建立儲存體帳戶](../storage/storage-create-storage-account.md)，以便將診斷記錄封存至其中。 我們強烈建議您不要使用已儲存了其他非監視資料的現有儲存體帳戶，這樣您對監視資料才能有更好的存取控制。 不過，如果您也要封存活動記錄和診斷度量至儲存體帳戶，則將同一儲存體帳戶用於診斷記錄合情合理，因為可以將所有監視資料集中在一個位置。 您使用的儲存體帳戶必須是一般用途的儲存體帳戶，不可以是 blob 儲存體帳戶。

## <a name="diagnostic-settings"></a>診斷設定
若要使用下列任何方法封存診斷記錄，您必須為特定資源設定**診斷設定**。 資源的診斷設定會定義要傳送至目的地 (儲存體帳戶、事件中樞命名空間或 Log Analytics) 的記錄類別與計量資料。 它也會定義儲存在儲存體帳戶中每個記錄類別之事件和計量資料的保留原則 (保留的天數)。 如果保留原則設定為零，則會無限期地 (亦即永遠) 儲存該記錄類別的事件。 否則，保留原則可以是 1 到 2147483647 之間的任何天數。 [您可以在此深入了解診斷設定](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)。 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄檔將被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄檔會被刪除

## <a name="archive-diagnostic-logs-using-the-portal"></a>使用入口網站封存診斷記錄
1. 在入口網站中，瀏覽至 Azure 監視器，然後按一下 [診斷設定]

    ![Azure 監視器的監視區段](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. 選擇性地依資源群組或資源類型篩選清單，然後按一下您要設定診斷設定的資源。

3. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「開啟診斷」。

   ![新增診斷設定 - 無現有的設定](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   如果資源上已有設定，您將會看此資源上已設定的設定清單。 按一下「新增診斷設定」。

   ![新增診斷設定 - 現有的設定](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. 為您的設定提供名稱，並勾選 [匯出到儲存體帳戶] 核取方塊，然後選取儲存體帳戶。 使用 [保留 (天)]  滑桿選擇性地設定這些記錄的保留天數。 保留天數為 0 會無限期地儲存記錄。
   
   ![新增診斷設定 - 現有的設定](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)
    
4. 按一下 [儲存] 。

過了幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且每次產生新的事件資料，都會將診斷記錄封存至該儲存體帳戶。

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>透過 Azure PowerShell 封存診斷記錄
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| ResourceId |是 |要對其設定診斷設定之資源的資源識別碼。 |
| StorageAccountId |否 |資源識別碼，診斷記錄應該要儲存至此儲存體帳戶。 |
| 類別 |否 |要啟用之記錄類別的逗號分隔清單。 |
| 已啟用 |是 |布林值，表示要對資源啟用還是停用診斷。 |
| RetentionEnabled |否 |布林值，表示此資源是否啟用保留原則。 |
| RetentionInDays |否 |事件應保留的天數，1 到 2147483647 之間。 值為 0 會無限期地儲存記錄檔。 |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>透過跨平台 CLI 封存診斷記錄
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| ResourceId |是 |要對其設定診斷設定之資源的資源識別碼。 |
| storageId |否 |資源識別碼，診斷記錄應該要儲存至此儲存體帳戶。 |
| 類別 |否 |要啟用之記錄類別的逗號分隔清單。 |
| 已啟用 |是 |布林值，表示要對資源啟用還是停用診斷。 |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>透過 REST API 封存診斷記錄
[請參閱本文件](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings)，以取得如何使用 Azure 監視器 REST API 設定診斷設定的資訊。

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>儲存體帳戶中的診斷記錄結構描述
設定了封存之後，便會在已啟用的其中一個記錄類別發生事件時，立即於儲存體帳戶中建立儲存體容器。 容器內的 Blob 的診斷記錄和活動記錄會遵循相同的格式。 這些 blob 的結構為：

> insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
> 
> 

或者，形式更簡單，

> insights-logs-{log category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
> 
> 

例如，blob 名稱可能是︰

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。 分鐘值 (m = 00) 一定是 00，因為診斷記錄事件是分成每小時的個別 blob。

在 PT1H.json 檔案中，每個事件會以下列格式儲存在 “records” 陣列︰

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| 元素名稱 | 說明 |
| --- | --- |
| 分析 |處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| ResourceId |受影響資源的資源識別碼。 |
| operationName |作業名稱。 |
| category |事件的記錄類別。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |

> [!NOTE]
> 屬性和這些屬性的使用方式，依資源而異。
> 
> 

## <a name="next-steps"></a>後續步驟
* [下載 blob 以供分析](../storage/storage-dotnet-how-to-use-blobs.md)
* [將診斷記錄串流至事件中樞命名空間](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [深入了解診斷記錄](monitoring-overview-of-diagnostic-logs.md)
