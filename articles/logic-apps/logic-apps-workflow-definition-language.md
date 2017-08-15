---
title: "工作流程定義語言結構描述 - Azure Logic Apps | Microsoft Docs"
description: "根據 Azure Logic Apps 的工作流程定義結構描述來定義工作流程"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 6befc5b26f2b01113f1aa813125b33eb66ad6f6a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---

# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Azure Logic Apps 的工作流程定義語言結構描述

工作流程定義會包含執行為部分邏輯應用程式的實際邏輯。 此定義包含一或多個會啟動邏輯應用程式的觸發程序，和一或多個邏輯應用程式要採取的動作。  
  
## <a name="basic-workflow-definition-structure"></a>基本工作流程定義結構

以下是工作流程定義的基本結構︰  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> [工作流程管理 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 文件具有如何建立及管理邏輯應用程式工作流程的相關資訊。
  
|元素名稱|必要|說明|  
|------------------|--------------|-----------------|  
|$schema|否|指定 JSON 結構描述檔案的位置，說明定義語言的版本。 當您從外部參考定義時，需要這個位置。 對於本文件而言，位置為︰ <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|否|指定定義版本。 當您使用定義部署工作流程時，可以使用這個值以確定使用正確的定義。|  
|參數|否|指定用來將資料輸入定義的參數。 您最多可以定義 50 個參數。|  
|觸發程序|否|指定初始化工作流程之觸發程序的資訊。 您最多可以定義 10 個觸發程序。|  
|動作|否|指定當流程執行時所採取的動作。 您最多可以定義 250 個動作。|  
|輸出|否|指定已部署資源的相關資訊。 您最多可以定義 10 個輸出。|  
  
## <a name="parameters"></a>參數

本區段會指定在部署階段於工作流程定義中使用的所有參數。 所有參數必須先在本區段中宣告，才能在定義的其他區段中使用。  
  
下列範例顯示參數定義的結構：  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|元素名稱|必要|說明|  
|------------------|--------------|-----------------|  
|類型|是|**類型**：字串 <p> **宣告**：`"parameters": {"parameter1": {"type": "string"}` <p> **規格**：`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **類型**：securestring <p> **宣告**：`"parameters": {"parameter1": {"type": "securestring"}}` <p> **規格**：`"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **類別**：int <p> **宣告**：`"parameters": {"parameter1": {"type": "int"}}` <p> **規格**：`"parameters": {"parameter1": {"value" : 5}}` <p> **類型**：bool <p> **宣告**：`"parameters": {"parameter1": {"type": "bool"}}` <p> **規格**：`"parameters": {"parameter1": { "value": true }}` <p> **類型**：陣列 <p> **宣告**：`"parameters": {"parameter1": {"type": "array"}}` <p> **規格**：`"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **類型**：物件 <p> **宣告**：`"parameters": {"parameter1": {"type": "object"}}` <p> **規格**：`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **類型**：secureobject <p> **宣告**：`"parameters": {"parameter1": {"type": "object"}}` <p> **規格**：`"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **注意：**`GET`作業中不會傳回 `securestring` 和 `secureobject` 類型。 所有密碼、金鑰和密碼都應該使用這個類型。|  
|defaultValue|否|在建立資源期間未指定任何值時，請為參數指定預設值。|  
|allowedValues|否|指定參數允許值的陣列。|  
|中繼資料|否|指定參數的其他資訊，例如可讀取描述或是 Visual Studio 或其他工具所使用的設計階段資料。|  
  
此範例顯示如何在動作的主體區段中使用參數︰  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 參數也可以在輸出中使用。  
  
## <a name="triggers-and-actions"></a>觸發程序及動作  

觸發程序和動作會指定呼叫，該呼叫可以參與工作流程執行。 如需有關此區段的詳細資訊，請參閱[工作流程動作和觸發程序](logic-apps-workflow-actions-triggers.md)。
  
## <a name="outputs"></a>輸出  

輸出會指定可以從工作流程執行傳回的資訊。 例如，如果您有想要針對每個執行追蹤的特定狀態或值，可以在執行輸出中包含該資料。 資料會出現在該執行的管理 REST API 中，以及 Azure 入口網站該執行的管理 UI 中。 您也可以將這些輸出傳送至類似 PowerBI 的其他外部系統來建立儀表板。 輸出「不是」用來回應服務 REST API 上的連入要求。 若要使用 `response` 動作類型來回應連入要求，範例如下︰
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|元素名稱|必要|說明|  
|------------------|--------------|-----------------|  
|key1|是|指定輸出的金鑰識別碼。 將 **key1** 以您想要用來識別輸出的名稱取代。|  
|value|是|指定輸出的值。|  
|類型|是|指定已指定值的類型。 可能的值類型為︰ <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>運算式  

定義中的 JSON 值可以是常值，或者是使用定義時評估的運算式。 例如：  
  
```json
"name": "value"
```

 或  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> 某些運算式可能會從執行開始時不存在的執行階段動作中取得其值。 您可以使用**函式**來協助擷取這些值。  
  
運算式可以出現在 JSON 字串值中的任何一處，並一律產生另一個 JSON 值。 當 JSON 值已經確定是運算式時，藉由移除 @ 符號來擷取運算式的主體。 如果需要的常值字串開頭為 @，字串必須使用 @@ 逸出。 下列範例顯示如何評估運算式。  
  
|JSON 值|結果|  
|----------------|------------|  
|"parameters"|傳回字元 'parameters'。|  
|"parameters[1]"|傳回字元 'parameters[1]'。|  
|"@@"|傳回包含 '@' 的 1 個字元字串。|  
|" @"|傳回包含 '@' 的 2 個字元字串。|  
  
使用「字串插補」，運算式也可以出現在字串內，其中運算式會包含在 `@{ ... }` 內。 例如： <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

結果一律是字串，使這項功能類似於 `concat` 函式。 假設您將 `myNumber` 定義為`42` 以及將 `myString` 定義為 `sampleString`：  
  
|JSON 值|結果|  
|----------------|------------|  
|"@parameters('myString')"|傳回 `sampleString` 做為字串。|  
|"@{parameters('myString')}"|傳回 `sampleString` 做為字串。|  
|"@parameters('myNumber')"|傳回 `42` 做為「編號」。|  
|"@{parameters('myNumber')}"|傳回 `42` 做為「字串」。|  
|"Answer is: @{parameters('myNumber')}"|傳回字串 `Answer is: 42`。|  
|"@concat('Answer is: ', string(parameters('myNumber')))"|傳回字串 `Answer is: 42`|  
|"Answer is: @@{parameters('myNumber')}"|傳回字串 `Answer is: @{parameters('myNumber')}`。|  
  
## <a name="operators"></a>運算子  

運算子是您可以在運算式或函式內使用的字元。 
  
|運算子|說明|  
|--------------|-----------------|  
|。|點運算子可讓您參考物件的屬性|  
|?|問號運算子可讓您參考沒有執行階段錯誤的物件 null 屬性。 例如，您可以使用此運算式來處理 null 觸發程序輸出︰ <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|單引號是包裝字串常值的唯一方法。 您無法在運算式中使用雙引號，因為這個標點符號與包裝整個運算式的 JSON 引號相衝突。|  
|[]|方括號可以用來從具有特定索引的陣列取得值。 例如，如果您有一個動作會將 `range(0,10)` 傳入至 `forEach` 函式，可以使用此函式從陣列取得項目︰  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Functions  

您也可以在運算式內呼叫函式。 下表顯示可以在運算式中使用的函式。  
  
|運算是|評估|  
|----------------|----------------|  
|"@function('Hello')"|使用常值字串 Hello 做為第一個參數來呼叫定義的函式成員。|  
|"@function('It's Cool!')"|使用常值字串 'It's Cool!' 做為第一個參數來呼叫 定義的函式成員。|  
|"@function().prop1"|傳回定義之 `myfunction` 成員的 prop1 屬性值。|  
|"@function('Hello').prop1"|使用常值字串 Hello 做為第一個參數來呼叫定義的函式成員，並且傳回物件的 prop1 屬性。|  
|"@function(parameters('Hello'))"|評估 Hello 參數，並且將值傳遞至函式|  
  
### <a name="referencing-functions"></a>參考函式  

您可以使用這些函式參考邏輯應用程式中其他動作的輸出，或邏輯應用程式建立時傳入的值。 例如，您可以參考某個步驟的資料，然後在另一個步驟中使用它。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|參數|傳回在定義中定義的參數值。 <p>`parameters('password')` <p> **參數編號**：1 <p> **名稱**：參數 <p> **描述**︰必要。 您想要其值之參數的名稱。|  
|動作|可讓運算式從其他 JSON 名稱和值組或目前執行階段動作的輸出衍生其值。 下列範例中 Propertypath 顯示的屬性是選擇性的。 如果未指定 propertyPath，參考是針對整個動作物件。 此函式僅適用於動作的內部 do-until 條件。 <p>`action().outputs.body.propertyPath`|  
|動作|可讓運算式從其他 JSON 名稱和值組或執行階段動作的輸出衍生其值。 這些運算式會明確地宣告一個動作取決於其他動作。 下列範例中 Propertypath 顯示的屬性是選擇性的。 如果未指定 propertyPath，參考是針對整個動作物件。 您可以使用此元素或條件元素指定相依性，但是不需要針對相同的相依資源使用兩者。 <p>`actions('myAction').outputs.body.propertyPath` <p> **參數編號**：1 <p> **名稱**︰動作名稱 <p> **描述**︰必要。 您想要其值之動作的名稱。 <p> 動作物件上的可用屬性包括： <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>如需這些屬性的詳細資訊，請參閱 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646)。|
|觸發程序|可讓運算式從其他 JSON 名稱和值組或執行階段觸發程序的輸出衍生其值。 下列範例中 Propertypath 顯示的屬性是選擇性的。 如果未指定 propertyPath，參考是針對整個觸發程序物件。 <p>`trigger().outputs.body.propertyPath` <p>在觸發程序的輸入內使用時，函式會傳回先前執行的輸出。 但是，在觸發程序的條件內使用時，`trigger` 函式會傳回目前執行的輸出。 <p> 觸發程序物件上的可用屬性包括： <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>如需這些屬性的詳細資訊，請參閱 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644)。|
|actionOutputs|此函式是 `actions('actionName').outputs` 的速記 <p> **參數編號**：1 <p> **名稱**︰動作名稱 <p> **描述**︰必要。 您想要其值之動作的名稱。|  
|actionBody 和內文|這些函式是 `actions('actionName').outputs.body` 的速記 <p> **參數編號**：1 <p> **名稱**︰動作名稱 <p> **描述**︰必要。 您想要其值之動作的名稱。|  
|triggerOutputs|此函式是 `trigger().outputs` 的速記|  
|triggerBody|此函式是 `trigger().outputs.body` 的速記|  
|item|在重複的動作內使用時，此函式會傳回項目，該項目在動作的這個反覆項目陣列中。 例如，如果您有動作，會針對每個項目執行訊息陣列，則可以使用此語法︰ <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>集合函式  

這些函式會在集合上操作，並且通常會套用至陣列、字串，有時候會套用至字典。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|contains|如果字典包含索引鍵、清單包含值，或字串包含子字串，則傳回 true。 例如，此函式會傳回 `true`： <p>`contains('abacaba','aca')` <p> **參數編號**：1 <p> **名稱**︰集合中 <p> **描述**︰必要。 在其中搜尋的集合。 <p> **參數編號**：2 <p> **名稱**︰尋找物件 <p> **描述**︰必要。 要在**集合內**尋找的物件。|  
|length|傳回陣列或字串中的元素數目。 例如，此函式會傳回 `3`：  <p>`length('abc')` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 要取得其長度的集合。|  
|empty|如果物件、陣列或字串是空的，則傳回 true。 例如，此函式會傳回 `true`： <p>`empty('')` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 集合為空白時要檢查的集合。|  
|交集|傳回單一陣列或物件，在陣列或物件之間具有傳入的通用元素。 例如，此函式會傳回 `[1, 2]`： <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>函式的參數可以是一組物件或一組陣列 (不是兩者的混合)。 如果有兩個物件具有相同名稱，具有該名稱的最後一個物件會出現在最終物件。 <p> **參數編號**：1 ... *n* <p> **名稱**：集合 *n* <p> **描述**︰必要。 要評估的集合。 物件必須在傳入以出現在結果中的所有集合中。|  
|union|傳回單一陣列或物件，具有傳遞至此函式之陣列或物件中的所有元素。 例如，此函式會傳回 `[1, 2, 3, 10, 101]`： <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>函式的參數可以是一組物件或一組陣列 (不是混合)。 如果最終輸出中有兩個物件具有相同名稱，具有該名稱的最後一個物件會出現在最終物件。 <p> **參數編號**：1 ... *n* <p> **名稱**：集合 *n* <p> **描述**︰必要。 要評估的集合。 會出現在任何集合的物件也會出現在結果中。|  
|first|傳回傳入之陣列或字串中的第一個元素。 例如，此函式會傳回 `0`： <p>`first([0,2,3])` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 要從中採用第一個物件的集合。|  
|last|傳回傳入之陣列或字串中的最後一個元素。 例如，此函式會傳回 `3`： <p>`last('0123')` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 要從中採用最後一個物件的集合。|  
|take|傳回傳入之陣列或字串中的第一個 **Count** 元素。 例如，此函式會傳回 `[1, 2]`：  <p>`take([1, 2, 3, 4], 2)` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 從其中採用第一個 **Count** 物件的集合。 <p> **參數編號**：2 <p> **名稱**：計數 <p> **描述**︰必要。 從**集合**採用的物件數目。 必須是正整數。|  
|skip|傳回從索引**計數**開始之陣列中的元素。 例如，此函式會傳回 `[3, 4]`： <p>`skip([1, 2 ,3 ,4], 2)` <p> **參數編號**：1 <p> **名稱**：集合 <p> **描述**︰必要。 略過第一個 **Count** 物件的集合。 <p> **參數編號**：2 <p> **名稱**：計數 <p> **描述**︰必要。 從**集合**前面移除的物件數目。 必須是正整數。|  
|Join|傳回字串，陣列的每個項目都使用分隔符號聯結，例如這會傳回 `"1,2,3,4"`：<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **參數編號**：1<br /><br /> **名稱**：集合<br /><br /> **描述**︰必要。 從中聯結項目的集合。<br /><br /> **參數編號**：2<br /><br /> **名稱**：分隔符號<br /><br /> **描述**︰必要。 用來分隔項目的字串。|  
  
### <a name="string-functions"></a>字串函數

下列函式只適用於字串。 您也可以在字串上使用某些集合函式。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|concat|結合任何數目的字串。 例如，如果參數 1 為 `p1`，此函式會傳回 `somevalue-p1-somevalue`： <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **參數編號**：1 ... *n* <p> **名稱**：字串 *n* <p> **描述**︰必要。 要結合至單一字串的字串。|  
|substring|從字串傳回字元的子集。 例如，此函式會傳回 `abc`： <p>`substring('somevalue-abc-somevalue',10,3)` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 從中採用子字串的字串。 <p> **參數編號**：2 <p> **名稱**：開始索引 <p> **描述**︰必要。 子字串在參數 1 中開始的索引。 <p> **參數編號**：3 <p> **名稱**：長度 <p> **描述**︰必要。 子字串的長度。|  
|取代|以指定的字串取代字串。 例如，此函式會傳回 `the new string`： <p>`replace('the old string', 'old', 'new')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 當參數 2 在參數 1 中找到時，針對參數 2 搜尋和使用參數 3 更新的字串。 <p> **參數編號**：2 <p> **名稱**：舊字串 <p> **描述**︰必要。 在參數 1 中找到相符項目時，以參數 3 取代的字串 <p> **參數編號**：3 <p> **名稱**：新字串 <p> **描述**︰必要。 在參數 1 中找到相符項目時，用來取代參數 2 中字串的字串。|  
|GUID|此函式會產生全域唯一字串 (GUID)。 例如，此函式可以產生此 GUID：`c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **參數編號**：1 <p> **名稱**︰格式 <p> **描述**︰選擇性。 單一格式規範，指出[如何格式化這個 Guid 值](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)。 格式參數可以是 "N"、"D"、"B"、"P" 或 "X"。 如果未提供格式，則會使用 "D"。|  
|toLower|將字串轉換為小寫。 例如，此函式會傳回 `two by two is four`： <p>`toLower('Two by Two is Four')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要轉換成小寫的字串。 如果字串中的字元沒有對等小寫，字元在傳回的字串中會保持不變。|  
|toUpper|將字串轉換為大寫。 例如，此函式會傳回 `TWO BY TWO IS FOUR`： <p>`toUpper('Two by Two is Four')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要轉換成大寫的字串。 如果字串中的字元沒有對等大寫，字元在傳回的字串中會保持不變。|  
|indexof|在不區分大小寫字串內尋找值的索引。 例如，此函式會傳回 `7`： <p>`indexof('hello, world.', 'world')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 可以包含值的字串。 <p> **參數編號**：2 <p> **名稱**：字串 <p> **描述**︰必要。 要搜尋索引的值。|  
|lastindexof|在不區分大小寫字串內尋找值的最後一個索引。 例如，此函式會傳回 `3`： <p>`lastindexof('foofoo', 'foo')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 可以包含值的字串。 <p> **參數編號**：2 <p> **名稱**：字串 <p> **描述**︰必要。 要搜尋索引的值。|  
|startswith|檢查字串是否以不區分大小寫的值開頭。 例如，此函式會傳回 `true`： <p>`startswith('hello, world', 'hello')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 可以包含值的字串。 <p> **參數編號**：2 <p> **名稱**：字串 <p> **描述**︰必要。 字串可能開始的值。|  
|endswith|檢查字串是否以不區分大小寫的值結尾。 例如，此函式會傳回 `true`： <p>`endswith('hello, world', 'world')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 可以包含值的字串。 <p> **參數編號**：2 <p> **名稱**：字串 <p> **描述**︰必要。 字串可能結尾的值。|  
|split|使用分隔符號分隔字串。 例如，此函式會傳回 `["a", "b", "c"]`： <p>`split('a;b;c',';')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 分割的字串。 <p> **參數編號**：2 <p> **名稱**：字串 <p> **描述**︰必要。 分隔符號。|  
  
### <a name="logical-functions"></a>邏輯函式  

這些函式在條件內相當有用，而且可以用來評估任何類型的邏輯。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|equals|如果兩個值相等，則傳回 true。 例如，如果參數 1 為 someValue，此函式會傳回 `true`： <p>`equals(parameters('parameter1'), 'someValue')` <p> **參數編號**：1 <p> **名稱**：物件 1 <p> **描述**︰必要。 要與**物件 2** 比較的物件。 <p> **參數編號**：2 <p> **名稱**：物件 2 <p> **描述**︰必要。 要與**物件 1** 比較的物件。|  
|less|如果第一個引數小於第二個引數，則傳回 true。 請注意，值類型只能是整數、浮點數或字串。 例如，此函式會傳回 `true`： <p>`less(10,100)` <p> **參數編號**：1 <p> **名稱**：物件 1 <p> **描述**︰必要。 物件，以檢查其是否小於**物件 2**。 <p> **參數編號**：2 <p> **名稱**：物件 2 <p> **描述**︰必要。 物件，以檢查其是否大於**物件 1**。|  
|lessOrEquals|如果第一個引數小於或等於第二個引數，則傳回 true。 請注意，值類型只能是整數、浮點數或字串。 例如，此函式會傳回 `true`： <p>`lessOrEquals(10,10)` <p> **參數編號**：1 <p> **名稱**：物件 1 <p> **描述**︰必要。 物件，以檢查其是否小於或等於**物件 2**。 <p> **參數編號**：2 <p> **名稱**：物件 2 <p> **描述**︰必要。 物件，以檢查其是否大於或等於**物件 1**。|  
|greater|如果第一個引數大於第二個引數，則傳回 true。 請注意，值類型只能是整數、浮點數或字串。 例如，此函式會傳回 `false`：  <p>`greater(10,10)` <p> **參數編號**：1 <p> **名稱**：物件 1 <p> **描述**︰必要。 物件，以檢查其是否大於**物件 2**。 <p> **參數編號**：2 <p> **名稱**：物件 2 <p> **描述**︰必要。 物件，以檢查其是否小於**物件 1**。|  
|greaterOrEquals|如果第一個引數大於或等於第二個引數，則傳回 true。 請注意，值類型只能是整數、浮點數或字串。 例如，此函式會傳回 `false`： <p>`greaterOrEquals(10,100)` <p> **參數編號**：1 <p> **名稱**：物件 1 <p> **描述**︰必要。 物件，以檢查其是否大於或等於**物件 2**。 <p> **參數編號**：2 <p> **名稱**：物件 2 <p> **描述**︰必要。 物件，以檢查其是否小於或等於**物件 1**。|  
|和|如果兩個參數都為 true，則傳回 true。 兩個引數必須是布林值。 例如，此函式會傳回 `false`： <p>`and(greater(1,10),equals(0,0))` <p> **參數編號**：1 <p> **名稱**︰布林值 1 <p> **描述**︰必要。 第一個引數必須是 `true`。 <p> **參數編號**：2 <p> **名稱**︰布林值 2 <p> **描述**︰必要。 第二個引數必須是 `true`。|  
|或|如果其中一個參數為 true，則傳回 true。 兩個引數必須是布林值。 例如，此函式會傳回 `true`： <p>`or(greater(1,10),equals(0,0))` <p> **參數編號**：1 <p> **名稱**︰布林值 1 <p> **描述**︰必要。 第一個引數可能是 `true`。 <p> **參數編號**：2 <p> **名稱**︰布林值 2 <p> **描述**︰必要。 第二個引數可能是 `true`。|  
|否|如果參數為 `false`，則傳回 true。 兩個引數必須是布林值。 例如，此函式會傳回 `true`： <p>`not(contains('200 Success','Fail'))` <p> **參數編號**：1 <p> **名稱**︰布林值 <p> **描述**：如果參數為 `false`，則傳回 true。 兩個引數必須是布林值。 此函式會傳回 `true`：`not(contains('200 Success','Fail'))`|  
|if|根據運算式造成 `true` 或 `false` 傳回指定值。  例如，此函式會傳回 `"yes"`： <p>`if(equals(1, 1), 'yes', 'no')` <p> **參數編號**：1 <p> **名稱**︰運算式 <p> **描述**︰必要。 布林值，決定運算式應該傳回哪一個值。 <p> **參數編號**：2 <p> **名稱**：True <p> **描述**︰必要。 如果運算式為 `true` 時要傳回的值。 <p> **參數編號**：3 <p> **名稱**：False <p> **描述**︰必要。 如果運算式為 `false` 時要傳回的值。|  
  
### <a name="conversion-functions"></a>轉換函式  

這些函式是用來在每個原生類型語言之間轉換︰  
  
- 字串  
  
- integer  
  
- float  
  
- 布林值  
  
- 陣列  
  
- 字典  

-   表單  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|int|將參數轉換成整數。 例如，此函式會傳回 100 做為數字，而不是字串︰ <p>`int('100')` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成整數的值。|  
|string|將參數轉換成字串。 例如，此函式會傳回 `'10'`： <p>`string(10)` <p>您也可以將物件轉換成字串。 例如，如果 `myPar` 參數是具有屬性 `abc : xyz` 的物件，則此函式會傳回 `{"abc" : "xyz"}`： <p>`string(parameters('myPar'))` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成字串的值。|  
|json|將參數轉換成 JSON 類型值並且與 `string()` 相反。 例如，此函式會傳回 `[1,2,3]` 做為陣列，而不是字串︰ <p>`json('[1,2,3]')` <p>同樣地，您可以將字串轉換成物件。 例如，此函式會傳回 `{ "abc" : "xyz" }`： <p>`json('{"abc" : "xyz"}')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 轉換成原生類型值的字串。 <p>`json()` 函式也支援 XML 輸入。 例如，參數值︰ <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>轉換成此 JSON： <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|將參數引數轉換成浮點數。 例如，此函式會傳回 `10.333`： <p>`float('10.333')` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成浮點數的值。|  
|布林|將參數轉換成布林值。 例如，此函式會傳回 `false`： <p>`bool(0)` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成布林值的值。|  
|coalesce|傳回傳入的引數中第一個非 null 的物件。 **注意**︰空字串不是 null。 例如，如果未定義參數 1 和 2，此函式會傳回 `fallback`：  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **參數編號**：1 ... *n* <p> **名稱**：物件*n* <p> **描述**︰必要。 要檢查其是否有 null 的物件。|  
|base64|傳回輸入字串的 base64 表示法。 例如，此函式會傳回 `c29tZSBzdHJpbmc=`： <p>`base64('some string')` <p> **參數編號**：1 <p> **名稱**︰字串 1 <p> **描述**︰必要。 要編碼為 base64 表示法的字串。|  
|base64ToBinary|傳回 base64 編碼字串的二進位表示法。 例如，此函式會傳回 `some string` 的二進位表示法： <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 base64 編碼的字串。|  
|base64ToString|傳回 based64 編碼字串的字串表示法。 例如，此函式會傳回 `some string`： <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 base64 編碼的字串。|  
|Binary|傳回值的二進位表示法。  例如，此函式會傳回 `some string` 的二進位表示法： <p>`binary('some string')` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成二進位的值。|  
|dataUriToBinary|傳回資料 URI 的二進位表示法。 例如，此函式會傳回 `some string` 的二進位表示法： <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要轉換成二進位表示法的資料 URI。|  
|dataUriToString|傳回資料 URI 的字串表示法。 例如，此函式會傳回 `some string`： <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串<p> **描述**︰必要。 要轉換成字串表示法的資料 URI。|  
|dataUri|傳回值的資料 URI。 例如，此函式會傳回此資料 URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`： <p>`dataUri('some string')` <p> **參數編號**：1<p> **名稱**︰值<p> **描述**︰必要。 要轉換成資料 URI 的值。|  
|decodeBase64|傳回輸入 based64 字串的字串表示法。 例如，此函式會傳回 `some string`： <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**：傳回輸入 based64 字串的字串表示法。|  
|encodeUriComponent|URL 逸出傳入的字串。 例如，此函式會傳回 `You+Are%3ACool%2FAwesome`： <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要從中逸出 URL 不安全字元的字串。|  
|decodeUriComponent|非 URL 逸出傳入的字串。 例如，此函式會傳回 `You Are:Cool/Awesome`： <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要從中解碼 URL 不安全字元的字串。|  
|decodeDataUri|傳回輸入資料 URI 字串的二進位表示法。 例如，此函式會傳回 `some string` 的二進位表示法： <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **參數編號**：1 <p> **名稱**：字串 <p> **描述**︰必要。 要解碼為二進位表示法的 dataURI。|  
|uriComponent|傳回值的 URI 編碼表示法。 例如，此函式會傳回 `You+Are%3ACool%2FAwesome`： <p>`uriComponent('You Are:Cool/Awesome')` <p> **參數編號**：1<p> **名稱**：字串 <p> **描述**︰必要。 要進行 URI 編碼的字串。|  
|uriComponentToBinary|傳回 URI 編碼字串的二進位表示法。 例如，此函式會傳回 `You Are:Cool/Awesome` 的二進位表示法： <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **參數編號**：1 <p> **名稱**：字串<p> **描述**︰必要。 URI 編碼的字串。|  
|uriComponentToString|傳回 URI 編碼字串的字串表示法。 例如，此函式會傳回 `You Are:Cool/Awesome`： <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **參數編號**：1<p> **名稱**：字串<p> **描述**︰必要。 URI 編碼的字串。|  
|xml|傳回值的 XML 表示法。 例如，此函式會傳回 `'\<name>Alan\</name>'` 所表示的 XML 內容： <p>`xml('\<name>Alan\</name>')` <p>`xml()` 函式也支援 JSON 物件輸入。 例如，參數 `{ "abc": "xyz" }` 轉換成 XML 內容︰`\<abc>xyz\</abc>` <p> **參數編號**：1<p> **名稱**︰值<p> **描述**︰必要。 要轉換成 XML 的值。|  
|xpath|傳回符合值 (xpath 運算式進行評估) 之 xpath 運算式的 XML 節點陣列。 <p> **範例 1** <p>假設參數 `p1` 的值是此 XML 的字串表示法： <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>此程式碼：`xpath(xml(parameters('p1'), '/lab/robot/name')` <p>傳回 <p>`[ <name>R1</name>, <name>R2</name> ]` <p>當此程式碼： <p>`xpath(xml(parameters('p1'), ' sum(/lab/robot/parts)')` <p>傳回 <p>`13` <p> <p> **範例 2** <p>指定下列 XML 內容： <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>此程式碼：`@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>或此程式碼： <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>傳回 <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>和此程式碼：`@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>傳回 <p>``xyz`` <p> **參數編號**：1 <p> **名稱**：Xml <p> **描述**︰必要。 要評估 XPath 運算式的 XML。 <p> **參數編號**：2 <p> **名稱**：XPath <p> **描述**︰必要。 要評估的 XPath 運算式。|  
|array|將參數轉換成陣列。 例如，此函式會傳回 `["abc"]`： <p>`array('abc')` <p> **參數編號**：1 <p> **名稱**︰值 <p> **描述**︰必要。 轉換成陣列的值。|
|createArray|從參數建立陣列。 例如，此函式會傳回 `["a", "c"]`： <p>`createArray('a', 'c')` <p> **參數編號**：1 ... *n* <p> **名稱**︰任何 *n* <p> **描述**︰必要。 要結合到陣列的值。|
|triggerFormDataValue|從表單資料或表單編碼觸發程序輸出，傳回符合索引鍵名稱的單一值。  如果有多個符合項目，則會發生錯誤。  例如，以下將會傳回 `bar`：`triggerFormDataValue('foo')`<br /><br />**參數編號**：1<br /><br />**名稱**︰索引鍵名稱<br /><br />**描述**︰必要。 要傳回之表單資料值的索引鍵名稱。|
|triggerFormDataMultiValues|從表單資料或表單編碼觸發程序輸出，傳回符合索引鍵名稱的值陣列。  例如，以下將會傳回 `["bar"]`：`triggerFormDataValue('foo')`<br /><br />**參數編號**：1<br /><br />**名稱**︰索引鍵名稱<br /><br />**描述**︰必要。 要傳回之表單資料值的索引鍵名稱。|
|triggerMultipartBody|傳回觸發程序多部分輸出中部分的內文。<br /><br />**參數編號**：1<br /><br />**名稱**︰索引<br /><br />**描述**︰必要。 要擷取之部分的索引。|
|formDataValue|從表單資料或表單編碼動作輸出，傳回符合索引鍵名稱的單一值。  如果有多個符合項目，則會發生錯誤。  例如，以下將會傳回 `bar`：`formDataValue('someAction', 'foo')`<br /><br />**參數編號**：1<br /><br />**名稱**︰動作名稱<br /><br />**描述**︰必要。 具有表單資料或表單編碼回應的動作名稱。<br /><br />**參數編號**：2<br /><br />**名稱**︰索引鍵名稱<br /><br />**描述**︰必要。 要傳回之表單資料值的索引鍵名稱。|
|formDataMultiValues|從表單資料或表單編碼動作輸出，傳回符合索引鍵名稱的值陣列。  例如，以下將會傳回 `["bar"]`：`formDataMultiValues('someAction', 'foo')`<br /><br />**參數編號**：1<br /><br />**名稱**︰動作名稱<br /><br />**描述**︰必要。 具有表單資料或表單編碼回應的動作名稱。<br /><br />**參數編號**：2<br /><br />**名稱**︰索引鍵名稱<br /><br />**描述**︰必要。 要傳回之表單資料值的索引鍵名稱。|
|multipartBody|傳回動作多部分輸出中部分的內文。<br /><br />**參數編號**：1<br /><br />**名稱**︰動作名稱<br /><br />**描述**︰必要。 具有多部分回應的動作名稱。<br /><br />**參數編號**：2<br /><br />**名稱**︰索引<br /><br />**描述**︰必要。 要擷取之部分的索引。|

### <a name="math-functions"></a>數學函式  

這些函式可用於任一類型的數字︰**整數**和**浮點數**。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|新增|傳回兩個數字相加的結果。 例如，此函式會傳回 `20.333`： <p>`add(10,10.333)` <p> **參數編號**：1 <p> **名稱**：被加數 1 <p> **描述**︰必要。 要加上**被加數 2** 的數字。 <p> **參數編號**：2 <p> **名稱**：被加數 2 <p> **描述**︰必要。 要加上**被加數 1** 的數字。|  
|sub|傳回兩個數字相減的結果。 例如，此函式會傳回 `-0.333`： <p>`sub(10,10.333)` <p> **參數編號**：1 <p> **名稱**：被減數 <p> **描述**︰必要。 從**減數**移除的數字。 <p> **參數編號**：2 <p> **名稱**︰減數 <p> **描述**︰必要。 從**被減數**移除的數字。|  
|mul|傳回兩個數字相乘的結果。 例如，此函式會傳回 `103.33`： <p>`mul(10,10.333)` <p> **參數編號**：1 <p> **名稱**︰被乘數 1 <p> **描述**︰必要。 要與**被乘數 2** 相乘的數字。 <p> **參數編號**：2 <p> **名稱**︰被乘數 2 <p> **描述**︰必要。 要與**被乘數 1** 相乘的數字。|  
|div|傳回兩個數字相除的結果。 例如，此函式會傳回 `1.0333`： <p>`div(10.333,10)` <p> **參數編號**：1 <p> **名稱**︰被除數 <p> **描述**︰必要。 要除以**除數**的數字。 <p> **參數編號**：2 <p> **名稱**︰除數 <p> **描述**︰必要。 **被除數**所除的數字。|  
|mod|兩個數字 (模數) 相除之後傳回餘數。 例如，此函式會傳回 `2`： <p>`mod(10,4)` <p> **參數編號**：1 <p> **名稱**︰被除數 <p> **描述**︰必要。 要除以**除數**的數字。 <p> **參數編號**：2 <p> **名稱**︰除數 <p> **描述**︰必要。 **被除數**所除的數字。 進行除法之後，會採用餘數。|  
|Min|有兩個不同的模式可以呼叫此函式。 <p>這裡 `min` 會採用陣列，而函式會傳回 `0`： <p>`min([0,1,2])` <p>或者，此函式可以採用值的以逗號分隔清單，也傳回 `0`： <p>`min(0,1,2)` <p> **注意**︰所有值都必須是數字，如果參數是陣列，則陣列只能有數字。 <p> **參數編號**：1 <p> **名稱**：集合或值 <p> **描述**︰必要。 要尋找最小值的值陣列，或集合的第一個值。 <p> **參數編號**：2 ... *n* <p> **名稱**：值 *n* <p> **描述**︰選擇性。 如果第一個參數是一個值，則您可以傳遞其他值，然後會傳回所有傳遞值的最小值。|  
|max|有兩個不同的模式可以呼叫此函式。 <p>這裡 `max` 會採用陣列，而函式會傳回 `2`： <p>`max([0,1,2])` <p>或者，此函式可以採用值的以逗號分隔清單，也傳回 `2`： <p>`max(0,1,2)` <p> **注意**︰所有值都必須是數字，如果參數是陣列，則陣列只能有數字。 <p> **參數編號**：1 <p> **名稱**：集合或值 <p> **描述**︰必要。 要尋找最大值的值陣列，或集合的第一個值。 <p> **參數編號**：2 ... *n* <p> **名稱**：值 *n* <p> **描述**︰選擇性。 如果第一個參數是一個值，則您可以傳遞其他值，然後會傳回所有傳遞值的最大值。|  
|range|從特定數字開始產生整數的陣列。 您會定義傳回之陣列的長度。 <p>例如，此函式會傳回 `[3,4,5,6]`： <p>`range(3,4)` <p> **參數編號**：1 <p> **名稱**：開始索引 <p> **描述**︰必要。 陣列中的第一個整數。 <p> **參數編號**：2 <p> **名稱**：計數 <p> **描述**︰必要。 這個值是陣列中的整數數目。|  
|rand|在指定範圍內 (僅含開頭) 產生隨機整數。 例如，此函式可傳回 `0` 或 '1'： <p>`rand(0,2)` <p> **參數編號**：1 <p> **名稱**︰最小值 <p> **描述**︰必要。 可以傳回的最小整數。 <p> **參數編號**：2 <p> **名稱**︰最大值 <p> **描述**︰必要。 這個值是可以傳回的最大整數之下一個整數。|  
  
### <a name="date-functions"></a>日期函式  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|utcnow|傳回目前的時間戳記做為字串，例如︰`2017-03-15T13:27:36Z`： <p>`utcnow()` <p> **參數編號**：1 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|addseconds|將整數秒數新增至傳入的字串時間戳記。 秒數可以是正數或負數。 根據預設，結果會是 ISO 8601 格式 ("o") 的字串，除非有提供格式規範。 例如：`2015-03-15T13:27:00Z`： <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **參數編號**：1 <p> **名稱**︰時間戳記 <p> **描述**︰必要。 包含時間的字串。 <p> **參數編號**：2 <p> **名稱**︰秒 <p> **描述**︰必要。 新增的秒數。 可以是負數以減去秒。 <p> **參數編號**：3 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|addminutes|將整數分鐘數新增至傳入的字串時間戳記。 分鐘數可以是正數或負數。 根據預設，結果會是 ISO 8601 格式 ("o") 的字串，除非有提供格式規範。 例如：`2015-03-15T14:00:36Z`： <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **參數編號**：1 <p> **名稱**︰時間戳記 <p> **描述**︰必要。 包含時間的字串。 <p> **參數編號**：2 <p> **名稱**︰分鐘 <p> **描述**︰必要。 要新增的分鐘數。 可以是負數以減去分鐘。 <p> **參數編號**：3 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|addhours|將整數時數新增至傳入的字串時間戳記。 時數可以是正數或負數。 根據預設，結果會是 ISO 8601 格式 ("o") 的字串，除非有提供格式規範。 例如：`2015-03-16T01:27:36Z`： <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **參數編號**：1 <p> **名稱**︰時間戳記 <p> **描述**︰必要。 包含時間的字串。 <p> **參數編號**：2 <p> **名稱**︰小時 <p> **描述**︰必要。 要新增的時數。 可以是負數以減去小時。 <p> **參數編號**：3 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|adddays|將整數天數新增至傳入的字串時間戳記。 天數可以是正數或負數。 根據預設，結果會是 ISO 8601 格式 ("o") 的字串，除非有提供格式規範。 例如：`2015-02-23T13:27:36Z`： <p>`addseconds('2015-03-15T13:27:36Z', -20)` <p> **參數編號**：1 <p> **名稱**︰時間戳記 <p> **描述**︰必要。 包含時間的字串。 <p> **參數編號**：2 <p> **名稱**︰天 <p> **描述**︰必要。 要新增的天數。 可以是負數以減去天。 <p> **參數編號**：3 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|formatDateTime|以日期格式傳回字串。 根據預設，結果會是 ISO 8601 格式 ("o") 的字串，除非有提供格式規範。 例如：`2015-02-23T13:27:36Z`： <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **參數編號**：1 <p> **名稱**︰日期 <p> **描述**︰必要。 包含日期的字串。 <p> **參數編號**：2 <p> **名稱**︰格式 <p> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|startOfHour|傳回小時開始至傳入的字串時間戳記。 例如：`2017-03-15T13:00:00Z`：<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。<br /><br />**參數編號**：2<br /><br /> **名稱**︰格式<br /><br /> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。|  
|startOfDay|傳回天數開始至傳入的字串時間戳記。 例如：`2017-03-15T00:00:00Z`：<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。<br /><br />**參數編號**：2<br /><br /> **名稱**︰格式<br /><br /> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。| 
|startOfMonth|傳回月份開始至傳入的字串時間戳記。 例如：`2017-03-01T00:00:00Z`：<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。<br /><br />**參數編號**：2<br /><br /> **名稱**︰格式<br /><br /> **描述**︰選擇性。 [單一格式規範字元](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)或[自訂格式模式](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)，指出如何格式化這個時間戳記值。 如果未提供格式，則會使用 ISO 8601 格式 ("o")。| 
|dayOfWeek|傳回字串時間戳記之週中的日元件。  星期日是 0、星期一是 1，依此類推。 例如：`3`：<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。| 
|dayOfMonth|傳回字串時間戳記之月中的日元件。 例如：`15`：<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。| 
|dayOfYear|傳回字串時間戳記之年中的日元件。 例如：`74`：<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。| 
|刻度|傳回字串時間戳記的刻度屬性。 例如：`1489603019`：<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **參數編號**：1<br /><br /> **名稱**︰時間戳記<br /><br /> **描述**︰必要。 這是包含時間的字串。| 
  
### <a name="workflow-functions"></a>工作流程函式  

這些函式可以協助您取得執行階段期間工作流程本身的相關資訊。  
  
|函式名稱|說明|  
|-------------------|-----------------|  
|listCallbackUrl|傳回字串給呼叫以叫用觸發程序或動作。 <p> **注意**︰此函式只能用於 **httpWebhook** 和 **apiConnectionWebhook**，不能用於**手動**、**週期**、**http**，或**apiConnection**。 <p>例如，`listCallbackUrl()` 函式會傳回： <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|工作流程|此函式會提供您執行階段期間工作流程本身的所有詳細資料。 <p> 工作流程物件上的可用屬性包括： <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> `run` 屬性的值是具有下列屬性的物件： <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>如需這些屬性的詳細資訊，請參閱 [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617)。<p> 例如，若要取得目前執行的名稱，請使用 `"@workflow().run.name"` 運算式。 |

## <a name="next-steps"></a>後續步驟

[工作流程動作與觸發程序](logic-apps-workflow-actions-triggers.md)

