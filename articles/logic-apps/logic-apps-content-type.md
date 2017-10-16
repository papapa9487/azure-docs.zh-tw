---
title: "處理內容類型 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps 在設計階段與執行階段處理內容類型的方式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="handle-content-types-in-logic-apps"></a>在邏輯應用程式中處理內容類型

許多不同類型的內容可以在整個邏輯應用程式中流動，包括 JSON、XML、一般檔案及二進位資料。 儘管 Logic Apps 引擎支援所有的內容類型，但有些是透過 Logic Apps 引擎進行原生了解。 有些則可能要視需要進行轉型或轉換。 本文說明引擎如何處理不同的內容類型，以及如何視需要正確處理這些類型。

## <a name="content-type-header"></a>Content-Type 標頭

若要從基本開始，讓我們看看下列這兩個 `Content-Types`，它們不需要轉換或轉型，就能在邏輯應用程式中使用：`application/json` 和 `text/plain`。

## <a name="applicationjson"></a>Application/JSON

工作流程引擎依賴來自 HTTP 呼叫的 `Content-Type` 標頭來決定適當的處理。 內容類型為 `application/json` 的所有要求都會以 JSON 物件的形式儲存並加以處理。 此外，JSON 內容預設就能剖析，不需要任何轉換。 

例如，在此例中使用 `@body('myAction')['foo'][0]` 之類的運算式來取得 `bar` 值，可以剖析工作流程中具有 `application/json ` 內容類型標頭的要求︰

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

不需要任何額外的轉換。 如果您正在使用格式為 JSON 的資料，但並未指定標頭，則可使用 `@json()` 函式手動將它轉換為 JSON，例如 `@json(triggerBody())['foo']`。

### <a name="schema-and-schema-generator"></a>結構描述和結構描述產生器

要求觸發程序可讓您輸入您希望接收之承載的 JSON 結構描述。 此結構描述可讓設計工具產生權杖，以便您取用要求的內容。 如果您尚未準備好結構描述，選取 [請使用範例承載產生結構描述]，如此便能從範例承載中產生 JSON 結構描述。

![結構描述](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>「剖析 JSON」動作

`Parse JSON` 動作可讓您將 JSON 內容剖析為易記權杖，以便在邏輯應用程式中使用。 此動作類似於要求觸發程序，可讓您輸入或產生所要剖析內容的 JSON 結構描述。 此工具可讓您更容易地從服務匯流排、Azure Cosmos DB 等取用資料。

![剖析 JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

類似於 `application/json`，若所收到的 HTTP 訊息中 `Content-Type` 標頭為 `text/plain`，就會以原始格式來儲存。 此外，如果這些訊息包含於後續動作中而不需轉換，這些要求就會與 `Content-Type`: `text/plain` 標頭一同送出。 例如，使用一般檔案時，您可能會收到以下 HTTP 內容做為 `text/plain`︰

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

如果在下一個動作中，您將要求做為另一個要求的內文 (`@body('flatfile')`) 來傳送，則要求必須含有 `text/plain` Content-Type 標頭。 如果您正在使用格式為純文字的資料，但並未指定標頭，則可使用 `@string()` 函式 (例如：`@string(triggerBody())`) 手動將資料轉換為文字。

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml 和 Application/octet-stream 以及轉換器函數

邏輯應用程式引擎一律會保留在 HTTP 要求或回應上接收到的 `Content-Type`。 因此，如果引擎收到 `Content-Type` 為 `application/octet-stream` 的內容，而且您在後續動作中納入未經轉型的內容，則外送要求具有 `Content-Type`: `application/octet-stream`。 如此一來，引擎就可以保證資料在整個工作流程中移動時不會遺失。 不過，當動作狀態 (輸入和輸出) 通過工作流程時，該狀態會儲存於 JSON 物件中。 所以，為了保留某些資料類型，引擎會將內容轉換為二進位 Base64 編碼的字串，其中包含可保留 `$content` 和 `$content-type` (都會自動轉換) 的適當中繼資料。 

* `@json()` - 將資料轉換為 `application/json`
* `@xml()` - 將資料轉換為 `application/xml`
* `@binary()` - 將資料轉換為 `application/octet-stream`
* `@string()` - 將資料轉換為 `text/plain`
* `@base64()` - 將內容轉換為 Base64 字串
* `@base64toString()` - 將 Base64 編碼的字串轉換為 `text/plain`
* `@base64toBinary()` - 將 Base64 編碼的字串轉換為 `application/octet-stream`
* `@encodeDataUri()` - 將字串編碼為 dataUri 位元組陣列
* `@decodeDataUri()` - 將 dataUri 解碼為位元組陣列

例如，如果您收到的 HTTP 要求具有 `Content-Type`: `application/xml`：

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

您可以轉換，並在稍後搭配像是 `@xml(triggerBody())` 的項目使用，或者在像是 `@xpath(xml(triggerBody()), '/CustomerName')` 的函式內使用。

## <a name="other-content-types"></a>其他內容類型

有其他內容類型也受到支援，而且可與邏輯應用程式一起運作，但是可能需要藉由將 `$content` 解碼來手動擷取訊息內文。 例如，假設您觸發 `application/x-www-url-formencoded` 要求，其 `$content` 是編碼為 Base64 字串的承載，可保留所有資料：

```
CustomerName=Frank&Address=123+Avenue
```

因為要求不是純文字或 JSON，所以要求會儲存在動作中，如下所示︰

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

目前沒有任何適用於 form-data 的原生函式，所以您仍可藉由使用 `@string(body('formdataAction'))` 之類的函式手動存取資料，進而在工作流程中使用此資料。 如果您希望外送要求也具有 `application/x-www-url-formencoded` 內容類型標頭，您可以直接將要求新增到動作內文，而不需使用任何像是 `@body('formdataAction')` 的轉換。 不過，唯有當內文是 `body` 輸入中的唯一參數時，才適用這種方法。 如果您嘗試在 `application/json` 要求中使用 `@body('formdataAction')`，您會因為傳送已編碼的內文而發生執行階段錯誤。

