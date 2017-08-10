---
title: "封存 Azure 活動記錄檔 | Microsoft Docs"
description: "了解如何封存 Azure 活動記錄檔以在儲存體帳戶中長期保存。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 195751d4c5ac4021eb929e96d39b9d0e72d3070f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="archive-the-azure-activity-log"></a>封存 Azure 活動記錄檔
在本文中，我們示範如何使用 Azure 入口網站、PowerShell Cmdlet 或跨平台 CLI 封存儲存體帳戶中的 [**Azure 活動記錄檔**](monitoring-overview-activity-logs.md)。 如果您想要保留活動記錄檔超過 90 天 (而且對保留原則有完全的控制)，以便稽核、靜態分析或備份，這個選項非常有用。 如果您只需要保留事件 90 天或更短，則不需要設定封存至儲存體帳戶，因為在不啟用封存的情況下，活動記錄檔就會在 Azure 平台保留 90 天。

## <a name="prerequisites"></a>必要條件
在開始之前，您需要 [建立儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account) ，以便將活動記錄檔封存至此。 我們強烈建議您不要使用已儲存了其他非監視資料的現有儲存體帳戶，這樣您對監視資料才能有更好的存取控制。 不過，如果您也要封存診斷記錄檔和度量至儲存體帳戶，則將同一儲存體帳戶用於活動記錄檔合情合理，因為可以將所有監視資料集中在一個位置。 您使用的儲存體帳戶必須是一般用途的儲存體帳戶，不可以是 blob 儲存體帳戶。 儲存體帳戶不一定要和訂用帳戶發出記錄檔屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。

## <a name="log-profile"></a>記錄檔設定檔
若要使用下列任一方法封存活動記錄檔，請設定訂用帳戶的 **記錄檔設定檔** 。 記錄檔的設定檔定義了要儲存或串流的事件類型以及輸出 — 儲存體帳戶和/或事件中樞。 它也定義事件儲存在儲存體帳戶中的保留原則 (保留的天數)。 如果保留原則設定為零，則會無限期地儲存事件。 否則，這可以設為介於 1 到 2147483647 之間的任何值。 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄檔將被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄檔會被刪除。 [您可以至此處閱讀記錄檔設定檔的相關資訊](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。 

## <a name="archive-the-activity-log-using-the-portal"></a>使用入口網站封存活動記錄檔
1. 在入口網站中，按一下左側導覽中的 [活動記錄檔]  連結。 如果您沒有看到活動記錄檔的連結，先按一下 [更多服務]  連結。
   
    ![瀏覽至活動記錄檔刀鋒視窗](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. 在刀鋒視窗頂端，按一下 [匯出] 。
   
    ![按一下匯出按鈕](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. 在出現的刀鋒視窗中，勾選 [匯出至儲存體帳戶]  方塊，選取儲存體帳戶。
   
    ![設定儲存體帳戶](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. 使用滑桿或文字方塊，定義活動記錄事件應該在儲存體帳戶中保留的天數。 如果您想要讓您的資料無限期保存在儲存體帳戶，將此數目設定為零。
5. 按一下 [儲存] 。

## <a name="archive-the-activity-log-via-powershell"></a>透過 PowerShell 封存活動記錄檔
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus -RetentionInDays 180 -Categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| StorageAccountId |否 |資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 您要檢視所有地區的清單，可以[瀏覽此頁面](https://azure.microsoft.com/en-us/regions)或使用 [Azure 管理 REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx)。 |
| RetentionInDays |是 |事件應保留的天數，1 到 2147483647 之間。 值為 0 會無限期地 (永遠) 儲存記錄檔。 |
| 類別 |是 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值有 Write、Delete、Action。 |

## <a name="archive-the-activity-log-via-cli"></a>透過 CLI 封存活動記錄檔
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --locations global,westus,eastus,northeurope --retentionInDays 180 –categories Write,Delete,Action
```

| 屬性 | 必要 | 說明 |
| --- | --- | --- |
| 名稱 |是 |記錄檔設定檔的名稱。 |
| storageId |否 |資源識別碼，活動記錄檔應該要儲存至此儲存體帳戶。 |
| 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 您要檢視所有地區的清單，可以[瀏覽此頁面](https://azure.microsoft.com/en-us/regions)或使用 [Azure 管理 REST API](https://msdn.microsoft.com/library/azure/gg441293.aspx)。 |
| RetentionInDays |是 |事件應保留的天數，1 到 2147483647 之間。 值為 0 會無限期地 (永遠) 儲存記錄檔。 |
| 類別 |是 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值有 Write、Delete、Action。 |

## <a name="storage-schema-of-the-activity-log"></a>活動記錄檔的儲存體結構描述
一旦您已經設定封存，只要一發生活動記錄檔事件，就會在儲存體帳戶中建立儲存體容器。 在容器內的 blob 的活動記錄檔和診斷記錄檔會遵循相同的格式。 這些 blob 的結構為：

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
> 
> 

例如，blob 名稱可能是︰

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。 分鐘值 (m = 00) 一定是 00，因為活動記錄檔事件是分成每小時的個別 blob。

在 PT1H.json 檔案中，每個事件會以下列格式儲存在 “records” 陣列︰

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
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
| category |事件的類別，例如 寫入、讀取、動作。 |
| resultType |結果的類型，例如 成功、失敗、開始 |
| resultSignature |取決於資源類型。 |
| durationMs |作業的持續時間 (以毫秒為單位) |
| callerIpAddress |已執行作業的使用者的 IP 地址，根據可用性的 UPN 宣告或 SPN 宣告。 |
| correlationId |通常是字串格式的 GUID。 具有相同 correlationId、屬於同一 uber 動作的事件。 |
| 身分識別 |描述授權和宣告的 JSON blob。 |
| 授權 |事件的 RBAC 屬性的 blob。 通常包括 action、role 和 scope 屬性。 |
| 層級 |事件的層級。 下列其中一個值：重大、錯誤、警告、資訊和詳細資訊 |
| location |在 location 發生 (或全球) 的區域。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |

> [!NOTE]
> 屬性和這些屬性的使用方式，依資源而異。
> 
> 

## <a name="next-steps"></a>後續步驟
* [下載 blob 以供分析](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [將活動記錄檔串流至事件中樞](monitoring-stream-activity-logs-event-hubs.md)
* [深入了解活動記錄檔](monitoring-overview-activity-logs.md)


