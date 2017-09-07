---
title: "Azure API 管理原則運算式 | Microsoft Docs"
description: "了解 Azure API 管理中的原則運算式。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 33bcc51466fa0918bf4484c58fac813d07ae14da
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="api-management-policy-expressions"></a>API 管理原則運算式
原則運算式的語法是 C# 6.0。 每個運算式皆可存取以隱含方式提供的[內容](api-management-policy-expressions.md#ContextVariables)變數，以及允許的 .NET Framework 類型[子集](api-management-policy-expressions.md#CLRTypes)。  
  
> [!NOTE]
>  如需原則運算式的詳細資訊，請觀賞[原則運算式](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/)影片。  
>   
>  如需使用原則運算式來設定原則的示範，請觀賞 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)。 這段影片中包含下列原則運算式的示範。  
>   
>  -   10:30 - 了解如何在 API 層級套用原則，以使用[設定查詢字串參數](api-management-transformation-policies.md#SetQueryStringParameter)和[設定 HTTP 標頭](api-management-transformation-policies.md#SetHTTPheader)原則將內容資訊提供給後端服務。 12:10 處是在開發人員入口網站中呼叫作業的示範，您可以看到這些原則的運作。  
> -   13:50 - 了解如何使用[驗證 JWT](api-management-access-restriction-policies.md#ValidateJWT) 原則來根據權杖宣告預先授與作業的存取權。 向前快轉到 15:00 可看到在原則編輯器中設定的原則，再到 18:50 可以看到一個示範，從開發人員入口網站 (使用和不使用必要的授權權杖) 呼叫運算。  
> -   21:00 - 了解如何使用 [API 檢查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)追蹤，以查看原則的評估方式和評估結果。  
> -   25:25 - 了解如何使用原則運算式與[從快取中取得](api-management-caching-policies.md#GetFromCache)和[儲存至快取](api-management-caching-policies.md#StoreToCache)原則，來設定 API 管理回應快取持續時間，使其符合備用服務之 `Cache-Control` 指示詞所指定的後端服務回應快取。  
> -   34:30 - 了解如何使用[控制流程](api-management-advanced-policies.md#choose)和[設定本文](api-management-transformation-policies.md#SetBody)原則，移除「從後端服務收到的回應」中的資料元素，藉此執行內容篩選。 從 31:50 處開始觀賞用於本示範的 [The Dark Sky Forecast API](https://developer.forecast.io/) 概觀。  
> -   若要下載這段影片中使用的原則陳述式，請參閱 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 儲存機制。  
  
  
##  <a name="Syntax"></a>語法  
 單一陳述式的運算式會以 `@(expression)` 括住，其中 `expression` 是格式正確的 C# 運算式陳述式。  
  
 多重陳述式的運算式則會以 `@{expression}` 括住。 多重陳述式運算式內的所有程式碼路徑都必須以 `return` 陳述式結尾。  
  
##  <a name="PolicyExpressionsExamples"></a>範例  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a>使用方式  
 如果原則參考未另行指定，則可以在任何 API 管理[原則](api-management-policies.md)中，使用運算式做為屬性值或文字值。  
  
> [!IMPORTANT]
>  請注意，當您使用原則運算式時，若原則已定義，則只能對原則運算式進行有限的驗證。 由於閘道會在執行階段於輸入或輸出管線中執行運算式，由原則運算式產生的任何執行階段例外狀況都會導致 API 呼叫中發生執行階段錯誤。  
  
##  <a name="CLRTypes"></a>原則運算式中允許的 .NET Framework 類型  
 下表列出原則運算式中允許的 .NET Framework 類型和其成員。  
  
|CLR 類型|支援的方法|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|支援所有方法|  
|Newtonsoft.Json.Linq.JArray|支援所有方法|  
|Newtonsoft.Json.Linq.JConstructor|支援所有方法|  
|Newtonsoft.Json.Linq.JContainer|支援所有方法|  
|Newtonsoft.Json.Linq.JObject|支援所有方法|  
|Newtonsoft.Json.Linq.JProperty|支援所有方法|  
|Newtonsoft.Json.Linq.JRaw|支援所有方法|  
|Newtonsoft.Json.Linq.JToken|支援所有方法|  
|Newtonsoft.Json.Linq.JTokenType|支援所有方法|  
|Newtonsoft.Json.Linq.JValue|支援所有方法|  
|System.Collections.Generic.IReadOnlyCollection<T\>|全部|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|全部|  
|System.Collections.Generic.ISet<TKey, TValue>|全部|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|索引鍵、值|  
|System.Collections.Generic.List<TKey, TValue>|全部|  
|System.Collections.Generic.Queue<TKey, TValue>|全部|  
|System.Collections.Generic.Stack<TKey, TValue>|全部|  
|System.Convert|全部|  
|System.DateTime|全部|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|全部|  
|System.Decimal|全部|  
|System.Double|全部|  
|System.Guid|全部|  
|System.IEnumerable<T\>|全部|  
|System.IEnumerator<T\>|全部|  
|System.Int16|全部|  
|System.Int32|全部|  
|System.Int64|全部|  
|System.Linq.Enumerable<T\>|支援所有方法|  
|System.Math|全部|  
|System.MidpointRounding|全部|  
|System.Nullable<T\>|全部|  
|System.Random|全部|  
|System.SByte|全部|  
|System.Security.Cryptography. HMACSHA384|全部|  
|System.Security.Cryptography. HMACSHA512|全部|  
|System.Security.Cryptography.HashAlgorithm|全部|  
|System.Security.Cryptography.HMAC|全部|  
|System.Security.Cryptography.HMACMD5|全部|  
|System.Security.Cryptography.HMACSHA1|全部|  
|System.Security.Cryptography.HMACSHA256|全部|  
|System.Security.Cryptography.KeyedHashAlgorithm|全部|  
|System.Security.Cryptography.MD5|全部|  
|System.Security.Cryptography.RNGCryptoServiceProvider|全部|  
|System.Security.Cryptography.SHA1|全部|  
|System.Security.Cryptography.SHA1Managed|全部|  
|System.Security.Cryptography.SHA256|全部|  
|System.Security.Cryptography.SHA256Managed|全部|  
|System.Security.Cryptography.SHA384|全部|  
|System.Security.Cryptography.SHA384Managed|全部|  
|System.Security.Cryptography.SHA512|全部|  
|System.Security.Cryptography.SHA512Managed|全部|  
|System.Single|全部|  
|System.String|全部|  
|System.StringSplitOptions|全部|  
|System.Text.Encoding|全部|  
|System.Text.RegularExpressions.Capture|索引、長度、值|  
|System.Text.RegularExpressions.CaptureCollection|計數、項目|  
|System.Text.RegularExpressions.Group|擷取、成功|  
|System.Text.RegularExpressions.GroupCollection|計數、項目|  
|System.Text.RegularExpressions.Match|空白、群組、結果|  
|System.Text.RegularExpressions.Regex|.ctor、IsMatch、Match、Matches、Replace|  
|System.Text.RegularExpressions.RegexOptions|編譯、IgnoreCase、IgnorePatternWhitespace、Multiline、無、RightToLeft、Singleline|  
|System.TimeSpan|全部|  
|System.Tuple|全部|  
|System.UInt16|全部|  
|System.UInt32|全部|  
|System.UInt64|全部|  
|System.Uri|全部|  
|System.Xml.Linq.Extensions|支援所有方法|  
|System.Xml.Linq.XAttribute|支援所有方法|  
|System.Xml.Linq.XCData|支援所有方法|  
|System.Xml.Linq.XComment|支援所有方法|  
|System.Xml.Linq.XContainer|支援所有方法|  
|System.Xml.Linq.XDeclaration|支援所有方法|  
|System.Xml.Linq.XDocument|支援所有方法|  
|System.Xml.Linq.XDocumentType|支援所有方法|  
|System.Xml.Linq.XElement|支援所有方法|  
|System.Xml.Linq.XName|支援所有方法|  
|System.Xml.Linq.XNamespace|支援所有方法|  
|System.Xml.Linq.XNode|支援所有方法|  
|System.Xml.Linq.XNodeDocumentOrderComparer|支援所有方法|  
|System.Xml.Linq.XNodeEqualityComparer|支援所有方法|  
|System.Xml.Linq.XObject|支援所有方法|  
|System.Xml.Linq.XProcessingInstruction|支援所有方法|  
|System.Xml.Linq.XText|支援所有方法|  
|System.Xml.XmlNodeType|全部|  
  
##  <a name="ContextVariables"></a>內容變數  
 名為 `context` 的變數可在每個原則[運算式](api-management-policy-expressions.md#Syntax)中隱含地使用。 其成員會提供與 `\request` 相關的資訊。 所有 `context` 成員都是唯讀的。  
  
|內容變數|允許的方法、屬性和參數值|  
|----------------------|-------------------------------------------------------|  
|context|Api：IApi<br /><br /> 部署<br /><br /> LastError<br /><br /> 作業<br /><br /> 產品<br /><br /> 要求<br /><br /> RequestId：Guid<br /><br /> Response<br /><br /> 訂閱<br /><br /> 追蹤︰bool<br /><br /> 使用者<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(訊息：字串)|  
|context.Api|識別碼︰字串<br /><br /> 名稱︰字串<br /><br /> 路徑︰字串<br /><br /> ServiceUrl：IUrl|  
|context.Deployment|區域︰字串<br /><br /> ServiceName︰字串|  
|context.LastError|來源︰字串<br /><br /> 原因︰字串<br /><br /> 訊息︰字串<br /><br /> 範圍︰字串<br /><br /> 區段︰字串<br /><br /> 路徑︰字串<br /><br /> PolicyId︰字串<br /><br /> 如需 context.LastError 的詳細資訊，請參閱[錯誤處理](api-management-error-handling-policies.md)。|  
|context.Operation|識別碼︰字串<br /><br /> 方法︰字串<br /><br /> 名稱︰字串<br /><br /> UrlTemplate︰字串|  
|context.Product|Api：IEnumerable<IApi\><br /><br /> ApprovalRequired：bool<br /><br /> 群組︰IEnumerable<IGroup\><br /><br /> 識別碼︰字串<br /><br /> 名稱︰字串<br /><br /> 狀態︰列舉 ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit：int?<br /><br /> SubscriptionRequired：bool|  
|context.Request|內文︰IMessageBody<br /><br /> 憑證︰System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> 標頭︰IReadOnlyDictionary<string, string[]><br /><br /> IpAddress︰字串<br /><br /> MatchedParameters︰IReadOnlyDictionary<string, string><br /><br /> 方法︰字串<br /><br /> OriginalUrl︰IUrl<br /><br /> Url︰IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName︰字串、defaultValue︰字串)|headerName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的要求標頭值或 `defaultValue`。|  
|context.Response|內文︰IMessageBody<br /><br /> 標頭︰IReadOnlyDictionary<string, string[]><br /><br /> StatusCode：int<br /><br /> StatusReason︰字串|  
|string context.Response.Headers.GetValueOrDefault(headerName︰字串、defaultValue︰字串)|headerName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的回應標頭值或 `defaultValue`。|  
|context.Subscription|CreatedTime：DateTime<br /><br /> EndDate：DateTime?<br /><br /> 識別碼︰字串<br /><br /> 索引鍵︰字串<br /><br /> 名稱︰字串<br /><br /> PrimaryKey︰字串<br /><br /> SecondaryKey︰字串<br /><br /> StartDate：DateTime?|  
|context.User|電子郵件︰字串<br /><br /> FirstName︰字串<br /><br /> 群組︰IEnumerable<IGroup\><br /><br /> 識別碼︰字串<br /><br /> 身分識別︰IEnumerable<IUserIdentity\><br /><br /> LastName︰字串<br /><br /> 附註︰字串<br /><br /> RegistrationDate︰DateTime|  
|IApi|識別碼︰字串<br /><br /> 名稱︰字串<br /><br /> 路徑︰字串<br /><br /> 通訊協定︰IEnumerable<string\><br /><br /> ServiceUrl：IUrl<br /><br /> SubscriptionKeyParameterNames︰ISubscriptionKeyParameterNames|  
|IGroup|識別碼︰字串<br /><br /> 名稱︰字串|  
|IMessageBody|As<T\>(preserveContent: bool = false)：其中 T：字串、JObject、JToken、JArray、XNode、XElement、XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Response.Body.As<T>` 方法是用來讀取指定類型 `T` 的要求和回應訊息主體。 根據預設，該方法會使用原始訊息本文資料流，並使它在傳回後無法使用。 若要避免那種情況，並讓方法在本文資料流的複本上進行操作，請將 `preserveContent` 參數設定為 `true`。 請移至[這裡](api-management-transformation-policies.md#SetBody)來查看範例。|  
|IUrl|主機︰字串<br /><br /> 路徑︰字串<br /><br /> 連接埠︰int<br /><br /> 查詢：IReadOnlyDictionary<string, string[]><br /><br /> QueryString︰字串<br /><br /> 配置︰字串|  
|IUserIdentity|識別碼︰字串<br /><br /> 提供者︰字串|  
|ISubscriptionKeyParameterNames|標頭︰字串<br /><br /> 查詢︰字串|  
|string IUrl.Query.GetValueOrDefault(queryParameterName︰字串、defaultValue︰字串)|queryParameterName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到參數，則傳回以逗號分隔的查詢參數值或 `defaultValue`。|  
|T context.Variables.GetValueOrDefault<T\>(variableName︰字串、defaultValue：T)|variableName︰字串<br /><br /> defaultValue︰T<br /><br /> 如果找不到變數，則傳回轉換為 `T` 或 `defaultValue` 的變數值。<br /><br /> 如果指定的類型不符合所傳回變數的實際類型，此方法便會擲回例外狀況。|  
|BasicAuthCredentials AsBasic(輸入：此字串)|輸入︰字串<br /><br /> 如果輸入參數包含有效的 HTTP 基本驗證授權要求標頭值，則方法會傳回類型為 `BasicAuthCredentials` 的物件；否則方法會傳回 null。|  
|bool TryParseBasic(輸入：此字串、結果：out BasicAuthCredentials)|輸入︰字串<br /><br /> 結果︰out BasicAuthCredentials<br /><br /> 如果輸入參數包含有效的 HTTP 基本驗證授權要求標頭值，則方法會傳回 `true`，且結果參數包含類型為 `BasicAuthCredentials` 的值；否則方法會傳回 `false`。|  
|BasicAuthCredentials|密碼︰字串<br /><br /> UserId︰字串|  
|Jwt AsJwt(輸入：此字串)|輸入︰字串<br /><br /> 如果輸入參數包含有效的 JWT 權杖值，則方法會傳回類型為 `Jwt` 的物件；否則方法會傳回 `null`。|  
|bool TryParseJwt(輸入：此字串、結果：out Jwt)|輸入︰字串<br /><br /> 結果：out Jwt<br /><br /> 如果輸入參數包含有效的 JWT 權杖值，則方法會傳回 `true`，且結果參數包含類型為 `Jwt` 的值；否則方法會傳回 `false`。|  
|Jwt|演算法︰字串<br /><br /> 對象︰IEnumerable<string\><br /><br /> 宣告︰IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime：DateTime?<br /><br /> 識別碼︰字串<br /><br /> 簽發者︰字串<br /><br /> NotBefore︰DateTime?<br /><br /> 主旨︰字串<br /><br /> 類型：字串|  
|string Jwt.Claims.GetValueOrDefault(claimName：字串、defaultValue：字串)|claimName︰字串<br /><br /> defaultValue︰字串<br /><br /> 如果找不到標頭，則傳回以逗號分隔的宣告值或 `defaultValue`。|

## <a name="next-steps"></a>後續步驟
如需有關使用原則的詳細資訊，請參閱 [API 管理中的原則](api-management-howto-policies.md)。  

