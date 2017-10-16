---
title: "了解 Azure IoT 中樞查詢語言 | Microsoft Docs"
description: "開發人員指南 - 說明類似 SQL 的 IoT 中樞查詢語言，用於從 IoT 中樞擷取裝置對應項和作業的相關資訊。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/17
ms.author: elioda
ms.openlocfilehash: a7650104eda58923558892f6f0f6666d16dbce28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="reference---iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>參考 - 裝置對應項、作業和訊息路由的 IoT 中樞查詢語言

IoT 中樞提供功能強大、類似 SQL 的語言，來擷取有關[裝置對應項][lnk-twins]、[作業][lnk-jobs]和[訊息路由][lnk-devguide-messaging-routes]的資訊。 本文提供︰

* IoT 中樞查詢語言主要功能的簡介，以及
* 語言的詳細說明。

## <a name="get-started-with-device-twin-queries"></a>開始使用裝置對應項查詢
[裝置對應項][lnk-twins]可以包含標籤和屬性形式的任意 JSON 物件。 IoT 中樞可讓您以包含所有裝置對應項資訊的單一 JSON 文件形式查詢裝置對應項。
比方說，假設您的 IoT 中樞裝置對應項有下列結構︰

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT 中樞可以將裝置對應項公開為稱為**裝置**的文件集合。
因此，下列查詢會擷取整組裝置對應項︰

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 支援將大型結果分頁。

IoT 中樞允許擷取使用任意條件進行的裝置對應項篩選。 例如，

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

會擷取 **location.region** 標籤設為 **US**的裝置對應項。
布林運算子和算術比較也受到支援，例如

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

會擷取所有位於 US、且設定為遙測傳送頻率比每分鐘還低的裝置對應項。 為了方便起見，您也可以使用陣列常數搭配 **IN** 和 **NIN** (不在) 運算子。 例如，

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

會擷取所有報告使用 WiFi 或有線連線能力的裝置對應項。 您通常必須識別包含特定屬性的所有裝置對應項。 為了實現此目的，IoT 中樞支援 `is_defined()` 函式。 例如，

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

擷取可定義 `connectivity` 報告屬性的所有裝置對應項。 請參閱 [WHERE 子句][lnk-query-where]一節，以取得篩選功能的完整參考。

群組和彙總也受到支援。 例如，

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

會傳回每個遙測組態狀態中的裝置計數。

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

上述範例說明了如下情況：三個裝置回報設定成功、兩個仍在套用組態，一個回報發生錯誤。

### <a name="c-example"></a>C# 範例
查詢功能由 [C# 服務 SDK][lnk-hub-sdks] 在 **RegistryManager** 類別中公開。
以下是簡單查詢的範例︰

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

請注意**查詢**物件如何以頁面大小 (最多 1000) 具現化，然後藉由呼叫 **GetNextAsTwinAsync** 方法多次擷取多個頁面。
請注意，查詢物件會公開多個 **Next\*** (視查詢所需的還原序列化選項，例如裝置對應項或作業物件，或使用投影時要使用的一般 JSON 而定)。

### <a name="nodejs-example"></a>Node.js 範例
查詢功能由[適用於 Node.js 的 Azure IoT 服務 SDK][lnk-hub-sdks] 在 **Registry** 物件中公開。
以下是簡單查詢的範例︰

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

請注意**查詢**物件如何以頁面大小 (最多 1000) 具現化，然後藉由呼叫 **nextAsTwin** 方法多次擷取多個頁面。
請注意，查詢物件會公開多個 **Next\*** (視查詢所需的還原序列化選項，例如裝置對應項或作業物件，或使用投影時要使用的一般 JSON 而定)。

### <a name="limitations"></a>限制
> [!IMPORTANT]
> 根據裝置對應項中的最新值，查詢結果可能會有數分鐘的延遲。 若依識別碼查詢個別裝置對應項，建議您一律使用抓取裝置對應項 API，這一律包含最新的值，而且有較高的節流處理限制。

目前僅支援在基本類型 (沒有物件) 之間進行比較，例如 `... WHERE properties.desired.config = properties.reported.config` 只會在這些屬性具有基本值時才受到支援。

## <a name="get-started-with-jobs-queries"></a>開始使用作業查詢
[作業][lnk-jobs]可提供方法來對裝置組執行作業。 每個裝置對應項皆包含屬於 **jobs** 集合一部分之作業的資訊。
在邏輯上，

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

目前，此集合可在 IoT 中樞查詢語言中以 **devices.jobs** 的形式來查詢。

> [!IMPORTANT]
> 在查詢裝置對應項 (也就是含有「FROM 裝置」的查詢) 時，目前永遠不會傳回作業屬性。 此屬性只能使用 `FROM devices.jobs` 直接透過查詢來存取。
>
>

例如，若要取得影響單一裝置的所有作業 (過去的和排程的)，您可以使用下列查詢︰

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

請注意這個查詢如何為每個傳回的作業提供裝置特定的狀態 (可能的話還會提供直接方法回應)。
您也可以在 **devices.jobs** 集合的所有物件屬性上，使用任意布林值條件進行篩選。
例如下列查詢：

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

會針對在 2016 年 9 月之後所建立的裝置 **myDeviceId** 擷取所有已完成的裝置對應項更新作業。

您也可以擷取單一作業的每一裝置結果。

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>限制
**devices.jobs** 上的查詢目前不支援︰

* 投影，因此只有 `SELECT *` 是可行的。
* 參照裝置對應項 (作業屬性除外) 的條件 (請參閱上一節)。
* 執行彙總，例如計數、平均、分組依據。

## <a name="get-started-with-device-to-cloud-message-routes-query-expressions"></a>開始使用裝置對雲端訊息路由查詢運算式

使用[裝置對雲端路由][lnk-devguide-messaging-routes]，您可以設定 IoT 中樞，以根據對個別訊息評估的運算式，將裝置對雲端訊息分派至不同的端點。

路由[條件][lnk-query-expressions]會使用相同的 IoT 中樞查詢語言做為對應項和作業查詢中的條件。 路由條件會依據訊息標頭和內文進行評估。 您的路由查詢運算式可能只涉及訊息標頭、只涉及訊息內文，或同時涉及訊息標頭和訊息內文。 IoT 中樞假設標頭和訊息內文有特定結構描述才能路由傳送訊息，下列各節將說明讓 IoT 中樞正確路由所需的項目：

### <a name="routing-on-message-headers"></a>依據訊息標頭進行路由

IoT 中樞假設訊息路由的訊息標頭採用下列 JSON 表示法：

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

訊息系統屬性前面會加上 `'$'` 符號。
使用者屬性則一律透過其名稱來存取。 若使用者屬性名稱剛好與系統屬性 (例如 `$to`) 相同，將使用 `$to` 運算式擷取使用者屬性。
您一律可以使用括弧 `{}` 來存取系統屬性：例如，您可以使用運算式 `{$to}` 來存取系統屬性 `to`。 以括弧括住的屬性名稱一律會擷取對應的系統屬性。

請記住，屬性名稱不區分大小寫。

> [!NOTE]
> 所有屬性皆為字串。 系統屬性 (如[開發人員指南][lnk-devguide-messaging-format]所述) 目前無法使用於查詢中。
>

例如，如果您使用 `messageType` 屬性，您可能想要將所有遙測都路由傳送至一個端點，以及將所有警示路由傳送至另一個端點。 您可以撰寫下列運算式來路由傳送遙測資料︰

```sql
messageType = 'telemetry'
```

以及撰寫下列運算式來路由傳送警示訊息︰

```sql
messageType = 'alert'
```

也支援布林運算式和函式。 這項功能可讓您區分嚴重性層級，例如︰

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

請參閱[運算式和條件][lnk-query-expressions]一節，以取得支援的完整運算子和函式清單。

### <a name="routing-on-message-bodies"></a>依據訊息內文進行路由

IoT 中樞只有在訊息內文是以 UTF-8、UTF-16 或 UTF-32 編碼的正確格式 JSON 時，才能依據訊息內文的內容進行路由。 您必須將訊息的內容類型設定為 `application/json`，並將內容編碼設定為訊息標頭支援的其中一個 UTF 編碼，IoT 中樞才能依據內文的內容路由傳送訊息。 如果未指定任一標頭，IoT 中樞將不會嘗試對訊息評估任何涉及內文的查詢運算式。 如果您的訊息不是 JSON 訊息，或者如果訊息未指定內容類型和內容編碼，您仍然可以使用訊息路由來依據訊息標頭路由傳送訊息。

您可以在查詢運算式中使用 `$body` 來路由傳送訊息。 您可以在查詢運算式中使用簡單內文參考、內文陣列參考或多個內文參考。 您的查詢運算式也可以將內文參考與訊息標頭參考合併。 例如，以下是所有有效的查詢運算式：

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>IoT 中樞查詢的基本概念
每一個 IoT 中樞查詢都包含 SELECT 和 FROM 子句，以及選擇性的 WHERE 和 GROUP BY 子句。 每個查詢都會在 JSON 文件的集合上執行，例如裝置對應項。 FROM 子句會指出要在其上反覆運算的文件集合 (**devices** 或 **devices.jobs**)。 然後，會套用 WHERE 子句中的篩選。 若為彙總，此步驟的結果會依照 GROUP BY 子句中所指定的方式針對每個群組進行分組，並依 SELECT 子句中所指定的方式產生一個資料列。

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM 子句
**FROM <from_specification>** 子句只能採用兩個值︰**FROM devices** (用來查詢裝置對應項) 或 **FROM devices.jobs** (用來查詢作業的每一裝置詳細資料)。

## <a name="where-clause"></a>WHERE 子句
**WHERE <filter_condition>** 子句是選擇性的。 它會指定一或多個條件，而且 FROM 集合中的 JSON 文件必須滿足這些條件，才能納入為結果的一部分。 任何 JSON 文件都必須將指定的條件評估為 "true"，才能併入結果。

[運算式和條件][lnk-query-expressions]一節中會說明允許的條件。

## <a name="select-clause"></a>SELECT 子句
SELECT 子句 (**SELECT <select_list>**) 是必要子句，並可指定要從查詢擷取的值。 它會指定用來產生新 JSON 物件的 JSON 值。
針對 FROM 集合已經過篩選 (及選擇性分組) 之子集的每個項目，投影階段會產生新的 JSON 物件 (以 SELECT 子句中指定的值所建構)。

以下是 SELECT 子句的文法︰

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

其中 **attribute_name** 指的是 FROM 集合中 JSON 文件的任何屬性。 您可以在[開始使用裝置對應項查詢][lnk-query-getstarted]一節中找到一些 SELECT 子句範例。

與 **SELECT \*** 不同的選取範圍子句目前只支援在裝置對應項的彙總查詢中使用。

## <a name="group-by-clause"></a>GROUP BY 子句
**GROUP BY <group_specification>** 子句是選擇性步驟，可在 WHERE 子句中指定的篩選之後、SELECT 中指定的投影之前執行。 它會根據屬性值來分組文件。 這些群組可用來產生 SELECT 子句中所指定的彙總值。

使用 GROUP BY 的查詢範例如下︰

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

GROUP BY 的正式語法如下︰

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

其中 **attribute_name** 指的是 FROM 集合中 JSON 文件的任何屬性。

目前，GROUP BY 子句只支援在查詢裝置對應項時使用。

## <a name="expressions-and-conditions"></a>運算式和條件
概括而言，運算式：

* 會評估為 JSON 類型 (例如布林值、數字、字串、陣列或物件) 的執行個體，以及
* 定義方式是使用內建運算子和函式處理來自裝置 JSON 文件和常數的資料。

「條件」是評估為布林值的運算式。 任何不同於布林值 **true** 的常數會被視為 **false** (包括 **null**、**undefined**、任何物件或陣列執行個體、任何字串，以及顯然是布林值 **false**)。

運算式的語法如下︰

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

其中：

| 符號 | 定義 |
| --- | --- |
| attribute_name | **FROM** 集合中 JSON 文件的任何屬性。 |
| binary_operator | [運算子](#operators)一節中所列的任何二元運算子。 |
| function_name| [函式](#functions)一節中所列的任何函式。 |
| decimal_literal |以小數點標記法表示的浮點數。 |
| hexadecimal_literal |以字串 '0x' 後面接著十六進位數字的字串所表示的數字。 |
| string_literal |字串常值是由零個或多個 Unicode 字元序列或逸出序列所表示的 Unicode 字串。 字串常值會以單引號 (所有格符號：') 或雙引號 (引號：") 括起來。 允許的逸出︰`\'`、`\"`、`\\`、`\uXXXX` (適用於由 4 個十六進位數字所定義的 Unicode 字元)。 |

### <a name="operators"></a>運算子
支援下列運算子：

| 系列 | 運算子 |
| --- | --- |
| 算術 |+、-、*、/、% |
| 邏輯 |AND、OR、NOT |
| 比較 |=、!=、<、>、<=、>=、<> |

### <a name="functions"></a>Functions
查詢對應項和作業時唯一支援的函式為：

| 函式 | 說明 |
| -------- | ----------- |
| IS_DEFINED(property) | 傳回布林值，表示屬性是否已經指派值 (包含 `null`)。 |

在路由條件中，支援下列比對函式：

| 函式 | 說明 |
| -------- | ----------- |
| ABS(x) | 傳回指定之數值運算式的絕對 (正) 值。 |
| EXP(x) | 傳回指定之數值運算式 (e^x) 的指數值。 |
| POWER(x,y) | 將指定之運算式的值傳回給指定的乘冪 (x^y)。|
| SQUARE(x) | 傳回指定之數值的平方。 |
| CEILING(x) | 傳回大於或等於指定之數值運算式的最小整數值。 |
| FLOOR(x) | 傳回小於或等於指定之數值運算式的最大整數。 |
| SIGN(x) | 傳回指定之數值運算式的正數 (+1)、零 (0) 或負數 (-1) 符號。|
| SQRT(x) | 傳回指定之數值的平方。 |

在路由條件中，支援下列類型檢查和轉換函式：

| 函式 | 說明 |
| -------- | ----------- |
| AS_NUMBER | 將輸入字串轉換為數字。 如果輸入是一個數字則為 `noop`；如果字串不是數字則為 `Undefined`。|
| IS_ARRAY | 傳回布林值，表示指定之運算式的類型為陣列。 |
| IS_BOOL | 傳回布林值，表示指定之運算式的類型為布林值。 |
| IS_DEFINED | 傳回布林值，表示屬性是否已經指派值。 |
| IS_NULL | 傳回布林值，表示指定之運算式的類型為 null。 |
| IS_NUMBER | 傳回布林值，表示指定之運算式的類型為數字。 |
| IS_OBJECT | 傳回布林值，表示指定之運算式的類型為 JSON 物件。 |
| IS_PRIMITIVE | 傳回布林值，表示指定之運算式的類型為基本類型 (字串、布林值、數值或 `null`)。 |
| IS_STRING | 傳回布林值，表示指定之運算式的類型為字串。 |

在路由條件中，支援下列字串函式：

| 函式 | 說明 |
| -------- | ----------- |
| CONCAT(x, …) | 傳回字串，該字串是串連兩個或多個字串值的結果。 |
| LENGTH(x) | 傳回指定字串運算式的字元數目。|
| LOWER(x) | 傳回將大寫字元資料轉換成小寫之後的字串運算式。 |
| UPPER(x) | 傳回將小寫字元資料轉換成大寫之後的字串運算式。 |
| SUBSTRING(string, start [, length]) | 傳回字串運算式的部分，從指定字元以零為起始的位置開始，直到指定的長度，或直到字串的結尾。 |
| INDEX_OF(string, fragment) | 傳回第一個指定的字串運算式中，第二個字串運算式第一次出現的開始位置，或者如果找不到字串，則為 -1。|
| STARTS_WITH(x, y) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。 |
| ENDS_WITH(x, y) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。 |
| CONTAINS(x,y) | 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。 |

## <a name="next-steps"></a>後續步驟
了解如何使用 [Azure IoT SDK][lnk-hub-sdks] 在應用程式中執行查詢。

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
