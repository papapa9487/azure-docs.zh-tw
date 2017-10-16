---
title: "Azure API 管理原則中的錯誤處理 | Microsoft Docs"
description: "了解如何回應在處理 Azure API 管理要求期間可能會發生的錯誤狀況。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: e8837b8d62bb8caeee9460661438368c2d11697a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="error-handling-in-api-management-policies"></a>API 管理原則中的錯誤處理
Azure API 管理可讓發佈者回應在處理 Proxy 要求期間可能會發生的錯誤狀況，其方法是藉由提供 `ProxyError` 物件。 `ProxyError` 物件是透過 [context.LastError](api-management-policy-expressions.md#ContextVariables) 屬性來存取，並可供 `on-error` 原則區段中的原則使用。 本主題提供 Azure API 管理中錯誤處理功能的參考。  
  
## <a name="error-handling-in-api-management"></a>API 管理中的錯誤處理  
 Azure API 管理中的原則分為 `inbound`、`backend`、`outbound` 和 `on-error` 區段，如下列範例所示。  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 在處理要求期間，會執行內建步驟以及屬於要求範圍內的任何原則。 如果發生錯誤，處理作業會立即跳到 `on-error` 原則區段。 `on-error` 原則區段可用在任何範圍內，而且 API 發佈者可以設定自訂行為，例如將錯誤記錄到事件中樞，或建立要傳回給呼叫者的新回應。  
  
> [!NOTE]
>  依預設，原則中不會有 `on-error` 區段。 若要在原則中新增 `on-error` 區段，請在原則編輯器中瀏覽至所要的原則，然後予以新增。 如需如何設定原則的詳細資訊，請參閱 [API 管理中的原則](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)。  
>   
>  如果沒有 `on-error` 區段，在發生錯誤狀況時，呼叫端將會收到 400 或 500 HTTP 回應訊息。  
  
### <a name="policies-allowed-in-on-error"></a>on-error 中允許的原則  
 下列原則可用於 `on-error` 原則區段。  
  
-   [choose](api-management-advanced-policies.md#choose)  
  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 當發生錯誤且控制項跳至 `on-error` 原則區段時，該錯誤就會儲存在 [context.LastError](api-management-policy-expressions.md#ContextVariables) 屬性中，此屬性可供 `on-error` 區段中的原則存取，並具有下列屬性。  
  
|名稱|類型|說明|必要|  
|----------|----------|-----------------|--------------|  
|來源|字串|發生錯誤之元素的名稱。 可能是原則或內建的管線步驟名稱。|是|  
|原因|字串|方便電腦理解的錯誤碼，可用於處理錯誤。|否|  
|訊息|字串|人類可以看懂的錯誤描述。|是|  
|Scope|字串|發生錯誤之範圍的名稱，此名稱可為「全域」、「產品」、「API」或「作業」其中之一|否|  
|區段|字串|發生錯誤的區段名稱，此名稱可為「輸入」、「後端」、「輸出」或「錯誤」其中之一。|否|  
|Path|字串|指定巢狀原則，例如「choose[3]/when[2]」。|否|  
|PolicyId|字串|發生錯誤之原則上 `id` 屬性的值 (如果客戶有指定)|否|  
  
> [!NOTE]
>  所有原則都有可以新增至原則根元素的選擇性 `id` 屬性。 如果在錯誤狀況發生時，原則中有這個屬性，此屬性的值就能使用 `context.LastError.PolicyId` 屬性來加以擷取。  
  
## <a name="predefined-errors-for-built-in-steps"></a>內建步驟的預先定義錯誤  
 針對在內建處理步驟評估期間可能會發生的錯誤狀況，系統預先定義了下列錯誤。  
  
|來源|條件|原因|訊息|  
|------------|---------------|------------|-------------|  
|組態|Uri 不符合任何 Api 或作業|OperationNotFound|連入要求與作業無法匹配。|  
|授權|未提供訂用帳戶金鑰|SubscriptionKeyNotFound|拒絕存取，因為找不到訂用帳戶金鑰。 在對這個 API 提出要求時，請務必包含訂用帳戶金鑰。|  
|授權|訂用帳戶金鑰值無效|SubscriptionKeyInvalid|拒絕存取，因為訂用帳戶金鑰無效。 請務必提供適用於作用中訂用帳戶的有效金鑰。|  
  
## <a name="predefined-errors-for-policies"></a>原則的預先定義錯誤  
 針對在原則評估期間可能會發生的錯誤狀況，系統預先定義了下列錯誤。  
  
|來源|條件|原因|訊息|  
|------------|---------------|------------|-------------|  
|rate-limit|超過了速率限制|RateLimitExceeded|超過速率限制|  
|quota|超過配額|QuotaExceeded|呼叫量配額不足。 會在 xx:xx:xx 中補充配額。 -或- 頻寬配額不足。 會在 xx:xx:xx 中補充配額。|  
|jsonp|回呼參數值無效 (包含錯誤的字元)|CallbackParameterInvalid|回呼參數 {callback-parameter-name} 的值不是有效的 JavaScript 識別碼。|  
|ip-filter|無法從要求中剖析呼叫端 IP|FailedToParseCallerIP|無法建立呼叫端的 IP 位址。 拒絕存取。|  
|ip-filter|呼叫端 IP 不在允許清單中|CallerIpNotAllowed|不允許呼叫端 IP 位址 {ip-address}。 拒絕存取。|  
|ip-filter|呼叫端 IP 位於封鎖清單中|CallerIpBlocked|呼叫端 IP 位址遭到封鎖。 拒絕存取。|  
|check-header|所需標頭不存在或找不到值|HeaderNotFound|在要求中找不到標頭 {header-name}。 拒絕存取。|  
|check-header|所需標頭不存在或找不到值|HeaderValueNotAllowed|不允許標頭 {header-name} 值 {header-value}。 拒絕存取。|  
|validate-jwt|要求中沒有 Jwt 權杖|TokenNotFound|在要求中找不到 JWT。 拒絕存取。|  
|validate-jwt|簽章驗證失敗|TokenSignatureInvalid|<來自 jwt 程式庫的訊息\>。 拒絕存取。|  
|validate-jwt|無效的對象|TokenAudienceNotAllowed|<來自 jwt 程式庫的訊息\>。 拒絕存取。|  
|validate-jwt|無效的簽發者|TokenIssuerNotAllowed|<來自 jwt 程式庫的訊息\>。 拒絕存取。|  
|validate-jwt|權杖過期|TokenExpired|<來自 jwt 程式庫的訊息\>。 拒絕存取。|  
|validate-jwt|識別碼未解析簽章金鑰|TokenSignatureKeyNotFound|<來自 jwt 程式庫的訊息\>。 拒絕存取。|  
|validate-jwt|權杖中沒有必要的宣告|TokenClaimNotFound|下列宣告中沒有 JWT 權杖︰<c1\>, <c2\>, … 拒絕存取。|  
|validate-jwt|宣告值不相符|TokenClaimValueNotAllowed|不允許宣告 {claim-name} 值 {claim-value}。 拒絕存取。|  
|validate-jwt|其他驗證失敗|JwtInvalid|<來自 jwt 程式庫的訊息\>|

## <a name="next-steps"></a>後續步驟
如需有關使用原則的詳細資訊，請參閱 [API 管理中的原則](api-management-howto-policies.md)。  