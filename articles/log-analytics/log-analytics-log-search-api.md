---
title: "Log Analytics 記錄檔搜尋 REST API | Microsoft Docs"
description: "本指南提供基本教學課程，說明如何使用 Operations Management Suite (OMS) 中的 Log Analytics 搜尋 REST API，並提供使用命令的範例。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: bwren
ms.openlocfilehash: 5b51c6fcc69c8dff6579a1a1221e88822eccc1a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-log-search-rest-api"></a>Log Analytics 記錄檔搜尋 REST API
本指南提供基本教學課程 (包括範例)，說明如何使用 Log Analytics 搜尋 REST API。 Log Analytics 是 Operations Management Suite (OMS) 的一部分。

> [!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則您應該參閱[新版記錄搜尋 API 的文件](https://dev.loganalytics.io/)。

> [!NOTE]
> Log Analytics 在以前稱為 Operational Insights，這也是資源提供者中使用此名稱的原因。
>
>

## <a name="overview-of-the-log-search-rest-api"></a>記錄檔搜尋 REST API 概觀
Log Analytics 搜尋 API 是 RESTful，可透過 Azure Resource Manager API 來存取。 本文件提供透過 [ARMClient](https://github.com/projectkudu/ARMClient) 存取 API 的範例，這是可簡化叫用 Azure Resource Manager API 的開放原始碼命令列工具。 使用 ARMClient 是存取 Log Analytics 搜尋 API 的許多選項之一。 另一個選項是使用 OperationalInsights 的 Azure PowerShell 模組，其中包含可存取搜尋的 Cmdlet。 這些工具可讓您利用 Azure Resource Manager API 呼叫 OMS 工作區，並在其中執行搜尋命令。 API 會以 JSON 格式來輸出搜尋結果，可讓您以程式設計方式將搜尋結果用在許多不同用途上。

您可以透過 [Library for .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) 和 [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx) 來使用 Azure Resource Manager。 若要深入了解，請檢閱連結的網頁。

> [!NOTE]
> 如果您使用彙總命令，例如 `|measure count()` 或 `distinct`，則每次呼叫搜尋最多可傳回 500,000 筆記錄。 不含彙總命令的搜尋最多傳回 5,000 筆記錄。
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>基本 Log Analytics 搜尋 REST API 教學課程
### <a name="to-use-armclient"></a>使用 ARMClient
1. 安裝 [Chocolatey](https://chocolatey.org/)，這是適用於 Windows 的開放原始碼封裝管理員。 以系統管理員身分開啟命令提示字元視窗，然後執行下列命令：

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. 執行下列命令來安裝 ARMClient：

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>使用 ARMClient 執行搜尋
1. 使用您的 Microsoft 帳戶、公司帳戶或學校帳戶登入：

    ```
    armclient login
    ```

    成功登入會列出繫結至指定帳戶的所有訂用帳戶：

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. 取得 Operations Management Suite 工作區：

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    成功的 Get 呼叫會輸出繫結至訂用帳戶的所有工作區：

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 建立您的搜尋變數：

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 使用新的搜尋變數來搜尋：

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Log Analytics 搜尋 REST API 參考範例
下列範例顯示如何使用 Search API。

### <a name="search---actionread"></a>搜尋 - 動作/讀取
**範例 Url：**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**要求：**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
下表描述可用的屬性。

| **屬性** | **說明** |
| --- | --- |
| top |要傳回的結果數目上限。 |
| highlight |包含 pre 和 post 參數，通常用於反白顯示比對欄位 |
| pre |以指定字串做為比對欄位的前置詞。 |
| post |以指定字串附加至比對欄位之後。 |
| query |用來收集並傳回結果的搜尋查詢。 |
| start |您想要從其中找到結果的時間範圍開頭。 |
| end |您想要從其中找到結果的時間範圍結尾。 |

**回應：**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>搜尋/{ID} - 動作/讀取
**要求已儲存搜尋的內容：**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> 如果搜尋會傳回「擱置中」狀態，則輪詢更新的結果可以透過此 API 完成。 6 分鐘後，搜尋的結果將會從快取卸除，並將傳回 HTTP Gone。 如果初始搜尋要求立即傳回「成功」狀態，則結果不會新增至快取，導致在查詢此 API 時會傳回 HTTP Gone。 HTTP 200 結果內容的格式與初始搜尋要求相同，只是會有更新的值。
>
>

### <a name="saved-searches"></a>已儲存的搜尋
**已儲存搜尋的要求清單：**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

支援的方法：GET PUT DELETE

支援的收集方法：GET

下表描述可用的屬性。

| 屬性 | 說明 |
| --- | --- |
| 識別碼 |唯一識別碼。 |
| Etag |**修補程式的必要項目**。 每次寫入時由伺服器進行更新。 值必須等於目前儲存的值或 ‘*’ 才能進行更新。 舊值/無效值時會傳回 409。 |
| properties.query |**必要**。 搜尋查詢。 |
| properties.displayName |**必要**。 使用者定義的查詢顯示名稱。 |
| properties.category |**必要**。 使用者定義的查詢類別。 |

> [!NOTE]
> 目前，當 Log Analytics 搜尋 API 輪詢工作區的儲存搜尋時，其會傳回使用者建立的儲存搜尋。 此 API 不會傳回解決方案所提供之已儲存的搜尋。
>
>

### <a name="create-saved-searches"></a>建立已儲存的搜尋
**要求：**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> Log Analytics API 所建立並儲存的所有搜尋、排程和動作，都必須使用小寫名稱。

### <a name="delete-saved-searches"></a>刪除儲存搜尋
**要求：**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>更新儲存搜尋
 **要求：**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>中繼資料 - 僅限 JSON
以下的方法可讓您看到工作區中所收集資料的所有記錄類型欄位。 比方說，如果您想知道事件類型是否具有名為 Computer 的欄位，此查詢可作為一種檢查方法。

**欄位要求：**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**回應：**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

下表描述可用的屬性。

| **屬性** | **說明** |
| --- | --- |
| 名稱 |欄位名稱。 |
| displayName |欄位的顯示名稱。 |
| 類型 |欄位值的類型。 |
| 可面向化 |目前 ‘indexed’、‘stored ‘和 ‘facet’ 屬性的組合。 |
| 顯示 |目前 ‘display’ 屬性。 如果欄位顯示在搜尋中，則為 True。 |
| ownerType |減少至僅屬於 onboarded IP 的類型。 |

## <a name="optional-parameters"></a>選擇性參數
下列資訊描述可用的選擇性參數。

### <a name="highlighting"></a>醒目提示
“Highlight” 參數是一種選擇性參數，您可以用來要求搜尋子系統，包含其回應中的一組標記。

這些標記表示開頭與結尾的醒目提示文字，以符合您在搜尋查詢中所提供的詞彙。
您可以指定開頭與結尾的標記，讓搜尋用來包裝醒目提示的詞彙。

**範例搜尋查詢**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**範例結果：**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

請注意，上述結果包含已加上前置詞和附加詞的錯誤訊息。

## <a name="computer-groups"></a>電腦群組
電腦群組是一種特殊的已儲存搜尋，其會傳回一組電腦。  您可以在其他查詢中使用電腦群組，以將結果限制為該群組中的電腦。  電腦群組會實作為已儲存的搜尋，其含有 Group 標籤與 Computer 值。

以下是電腦群組的回應範例。

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>擷取電腦群組
若要擷取電腦群組，請使用 Get 方法搭配群組識別碼。

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>建立或更新電腦群組
若要建立電腦群組，請使用 Put 方法搭配已儲存的唯一搜尋識別碼。 如果您使用現有的電腦群組識別碼，則系統會修改它。 當您在 Log Analytics 入口網站中建立電腦群組時，將會依據群組和名稱來建立識別碼。

用於群組定義的查詢必須傳回一組電腦，群組才能正確運作。  建議您在查詢的結尾加上 `| Distinct Computer`，以確保傳回正確的資料。

已儲存搜尋的定義必須包含 Group 標籤與 Computer 值，才能將搜尋分類為電腦群組。

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>刪除電腦群組
若要刪除電腦群組，請使用 Delete 方法搭配群組識別碼。

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>後續步驟
* 了解 [記錄搜尋](log-analytics-log-searches.md) ，以使用自訂欄位作為準則來建立查詢。
