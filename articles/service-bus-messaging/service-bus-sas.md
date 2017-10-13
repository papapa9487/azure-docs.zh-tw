---
title: "使用共用存取簽章的 Azure 服務匯流排驗證 | Microsoft Docs"
description: "使用共用存取簽章的服務匯流排驗證概觀，詳細說明搭配 Azure 服務匯流排的 SAS 驗證資訊。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>使用共用存取簽章的服務匯流排驗證

*共用存取簽章* (SAS) 是服務匯流排傳訊的主要安全性機制。 本文討論 SAS、其運作方式以及如何以跨平台的方式使用它們。

SAS 驗證可讓應用程式使用在命名空間或在與特定權限相關聯的訊息實體 (佇列或主題) 上設定的存取金鑰，向服務匯流排進行驗證。 您可以接著使用此金鑰來產生 SAS 權杖，以便用戶端用來向服務匯流排進行驗證。

SAS 驗證支援包含在 Azure SDK 2.0 版或更新版本中。

## <a name="overview-of-sas"></a>SAS 的概觀

共用存取簽章是以 SHA-256 安全雜湊或 URI 為基礎的驗證機制。 SAS 是所有服務匯流排服務使用的非常強大的機制。 在實際使用中，SAS 有兩個元件：「共用存取原則」和「共用存取簽章」(通常稱為「權杖」)。

服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 用戶端會藉由出示 SAS 權杖來宣告服務匯流排資源的存取權。 此權杖包含所存取的資源 URI 以及使用設定的金鑰所簽署的有效期限。

您可以在服務匯流排[轉送](service-bus-fundamentals-hybrid-solutions.md#relays)、[佇列](service-bus-fundamentals-hybrid-solutions.md#queues)和[主題](service-bus-fundamentals-hybrid-solutions.md#topics)上設定共用存取簽章授權規則。

SAS 驗證會使用下列元素︰

* [共用存取授權規則](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)︰Base64 表示法中的 256 位元主要密碼編譯金鑰、選用的次要金鑰以及金鑰名稱和相關權限 (接聽、傳送或管理權限的集合)。
* [共用存取簽章](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 權杖︰使用資源字串的 HMAC-SHA256 所產生，包含所存取資源的 URI 和有效期限以及密碼編譯金鑰。 下列各節所述的簽章和其他元素都會格式化為字串，以構成 SAS 權杖。

## <a name="shared-access-policy"></a>共用存取原則

對 SAS 應了解的一項重點是它從原則開始。 針對每個原則，您會決定三段資訊：**名稱**、**範圍**和**權限**。 **名稱** 只是該範圍內的唯一名稱。 範圍也很簡單：它是發生問題之資源的 URI。 針對服務匯流排命名空間，範圍是完整的網域名稱 (FQDN)，例如 `https://<yournamespace>.servicebus.windows.net/`。

原則的可用權限大部分都易於理解：

* 傳送
* 接聽
* 管理

建立原則之後，它會獲指派「主索引鍵」和「次要索引鍵」。 這些是密碼編譯增強式金鑰。 請勿遺失或洩漏金鑰 - 它們永遠都可在 [Azure 入口網站][Azure portal]取得。 您可以使用其中一個產生的金鑰，以及您可以隨時重新產生它們。 不過，如果您重新產生或變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

建立服務匯流排命名空間時，將會自動為整個命名空間建立稱為 **RootManageSharedAccessKey** 的原則，而且此原則會具備所有權限。 您未以 **root** 登入，所以除非有適合的理由，請勿使用此原則。 您可以在入口網站命名空間的 [設定] 索引標籤中建立額外的原則。 請務必注意服務匯流排中單一樹狀目錄的層級 (命名空間、佇列等) 最多只能附加 12 個原則。

## <a name="configuration-for-shared-access-signature-authentication"></a>共用存取簽章驗證的設定
您可以在服務匯流排命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則。 目前不支援在服務匯流排訂用帳戶上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) ，但是您可以使用在命名空間或主題上設定的規則來保護訂用帳戶的存取安全。 如需可說明此程序的實用範例，請參閱 [搭配使用共用存取簽章 (SAS) 驗證與服務匯流排訂用帳戶](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) 範例。

在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類規則。 在服務匯流排命名空間上設定的規則可套用到該命名空間中的所有實體。

![SAS](./media/service-bus-sas/service-bus-namespace.png)

在此圖中，*manageRuleNS*、*sendRuleNS* 和 *listenRuleNS* 授權規則套用至佇列 Q1 和主題 T1，而 *listenRuleQ* 和 *sendRuleQ* 僅套用至佇列 Q1 以及 *sendRuleT* 僅套用至主題 T1。

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 的主要參數如下所示︰

| 參數 | 說明 |
| --- | --- |
| *KeyName* |用以描述授權規則的字串。 |
| *PrimaryKey* |用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元主要金鑰。 |
| *SecondaryKey* |用來簽署和驗證 SAS 權杖的 Base64 編碼 256 位元次要金鑰。 |
| *AccessRights* |授權規則所授與的存取權限清單。 這些權限可以是任何接聽、傳送和管理權限的集合。 |

佈建完服務匯流排命名空間後，預設會建立一個 [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) 設為 **RootManageSharedAccessKey** 的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。

## <a name="generate-a-shared-access-signature-token"></a>產生共用存取簽章 (權杖)

原則本身不是服務匯流排的存取權杖。 它是來自使用主要或次要索引鍵產生的存取權杖的物件。 任何有權存取在共用存取授權規則中指定的簽署金鑰的用戶端都可以產生 SAS 權杖。 權杖是以下列格式仔細編寫字串而產生：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

其中，`signature-string` 是權杖範圍的 SHA-256 雜湊 (如前一節中所述的**範圍**) 並附加 CRLF 與到期時間 (自紀元起以秒為單位：1970 年 1 月 1 日 `00:00:00 UTC`)。 

> [!NOTE]
> 為了避免過短的權杖到期時間，建議您將到期時間值編碼為至少 32 位元的不帶正負號整數，或最好為長 (64 位元) 整數。  
> 
> 

雜湊看起來類似下列虛擬程式碼，並傳回 32 個位元組。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非雜湊值位在 **SharedAccessSignature** 字串中，如此收件者可以使用相同的參數計算雜湊，以確保它會傳回相同的結果。 URI 會指定範圍，而金鑰名稱會識別要用來計算雜湊的原則。 從安全性的觀點而言，這很重要。 如果簽章與收件者 (服務匯流排) 所計算的不符，則會拒絕存取。 此時您可以確定寄件者可存取金鑰，並且應該被授與原則中指定的權限。

請注意，您應該使用編碼的資源 URI 進行此操作。 資源 URI 是宣告其存取權之服務匯流排資源的完整 URI。 例如，`http://<namespace>.servicebus.windows.net/<entityPath>` 或 `sb://<namespace>.servicebus.windows.net/<entityPath>`；也就是 `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`。

用於簽署的共用存取授權規則必須設定於此 URI 或其中一個階層式上層所指定的實體。 例如，先前範例中的 `http://contoso.servicebus.windows.net/contosoTopics/T1` 或 `http://contoso.servicebus.windows.net`。

SAS 權杖適用於 `signature-string` 中所用 `<resourceURI>` 底下的所有資源。

SAS 權杖中的 [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) 是指用來產生權杖之共用存取授權規則的 **keyName** 。

*URL-encoded-resourceURI* 必須與簽章預算期間 string-to-sign 中所用的 URI 相同。 它應該是 [百分比編碼](https://msdn.microsoft.com/library/4fkewx0t.aspx)。

建議您定期重新產生用於 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件的金鑰。 應用程式通常應該使用 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) 來產生 SAS 權杖。 重新產生金鑰，您應該以舊的主要金鑰取代 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) ，並產生新金鑰做為新的主要金鑰。 如此一來，您即可繼續使用以舊的主要金鑰簽發但尚未到期的權杖進行授權。

如果金鑰受到危害而必須撤銷金鑰，您可以重新產生 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 的 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) 和 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey)，並取代成新的金鑰。 此程序會讓所有以舊金鑰簽章的權杖失效。

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>如何搭配使用共用存取簽章驗證與服務匯流排

下列案例包括授權規則的設定、SAS 權杖的產生以及用戶端授權。

如需服務匯流排應用程式的完整工作範例，以便了解相關設定及使用 SAS 授權，請參閱 [共用存取簽章驗證與服務匯流排](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)。 以下提供相關的範例，說明如何使用在命名空間或主題上設定的 SAS 授權規則來保護服務匯流排訂用帳戶︰ [使用共用存取簽章 (SAS) 驗證與服務匯流排訂用帳](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)。

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>存取命名空間上的共用存取授權規則

服務匯流排命名空間根目錄上的作業需要憑證驗證。 您必須針對您的 Azure 訂用帳戶上傳管理憑證。 若要上傳管理憑證，請使用 [Azure 入口網站][Azure portal]，並遵循[此處](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)的步驟。 如需有關 Azure 管理憑證的詳細資訊，請參閱 [Azure 憑證概觀](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates)。

可供存取服務匯流排命名空間上共用存取授權規則的端點如下所示：

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

若要在服務匯流排命名空間上建立 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件，請以序列化為 JSON 或 XML 的規則資訊在此端點上執行 POST 作業。 例如：

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

同樣地，在端點上使用 GET 作業以讀取在命名空間上設定的授權規則。

若要更新或刪除特定授權規則，請使用以下端點：

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>存取實體上的共用存取授權規則

您可以透過對應的 [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 或 [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) 中的 [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) 集合，存取服務匯流排佇列或主題上設定的 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件。

下列程式碼示範如何新增佇列的授權規則。

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>使用共用存取簽章授權

搭配使用 Azure .NET SDK 與服務匯流排 .NET 程式庫的應用程式可以透過 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 類別來使用 SAS 授權。 下列程式碼說明如何使用權杖提供者將訊息傳送至服務匯流排佇列。

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

應用程式在接受連接字串的方法中使用 SAS 連接字串，也可以使用 SAS 進行驗證。

請注意，若要搭配使用 SAS 授權與服務匯流排轉送，您可以使用在服務匯流排命名空間上設定的 SAS 金鑰。 如果您在命名空間 (具有 [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription) 的 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)) 物件上明確建立轉送，您可以設定該轉送的 SAS 規則。 若要搭配使用 SAS 授權與服務匯流排訂用帳戶，您可以使用在服務匯流排命名空間或主題上設定的 SAS 金鑰。

## <a name="use-the-shared-access-signature-at-http-level"></a>使用共用存取簽章 (於 HTTP 層級)

既然知道如何為服務匯流排中的任何實體建立共用存取簽章，您可準備執行 HTTP POST：

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

請記住，這適用於所有項目。 您可以為佇列、主題或訂用帳戶建立 SAS。 

如果您提供寄件者或用戶端 SAS 權杖，他們不會直接有金鑰，並且他們無法回復雜湊來取得它。 因此，您可以控制他們可以存取的項目，以及時間長度。 要記住的重點是，如果您變更原則中的主索引鍵，從其建立的任何共用存取簽章將會失效。

## <a name="use-the-shared-access-signature-at-amqp-level"></a>使用共用存取簽章 (於 AMQP 層級)

在前一節中，說明了如何使用 SAS 權杖搭配 HTTP POST 要求傳送資料到服務匯流排。 如您所了解，您可以使用進階訊息佇列通訊協定 (AMQP) 來存取服務匯流排，此通訊協定在許多案例中，都是基於效能考量而做為慣用的通訊協定。 如需使用 SAS 權杖搭配 AMQP 的用法，請參閱 [AMQP 宣告型安全性 1.0 版](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) 文件中的說明，該文件是自 2013 年開始的工作草稿，不過現在 Azure 已經提供良好的支援。

開始將資料傳送到服務匯流排之前，發行者必須在 AMQP 訊息內部將 SAS 權杖傳送至正確定義且名為 **$cbs** 的 AMQP 節點 (您可以將它視為一個由服務所使用的「特別」佇列，用來取得並驗證所有的 SAS 權杖)。 發行者必須在 AMQP 訊息中指定 **ReplyTo** 欄位；這是服務將以權杖驗證結果 (發行者與服務之間的簡單要求/回覆模式) 回覆發行者的節點所在。 此回覆節點是「動態」建立，如 AMQP 1.0 規格中所述的「動態建立遠端節點」。 檢查 SAS 權杖有效之後，發行者可以繼續並開始將資料傳送至服務。

下列步驟示範如何使用 [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) 程式庫，搭配 AMQP 通訊協定來傳送 SAS 權杖。 如果您不能使用以 C\# 開發的官方服務匯流排 SDK (例如，在 WinRT、.Net Compact Framework、.Net Micro Framework 和 Mono 上)，這就非常有用。 當然，此程式庫對於了解宣告型安全性如何在 AMQP 層級運作非常有用，如同您了解其如何在 HTTP 層級運作一樣 (使用 HTTP POST 要求以及在標頭 "Authorization" 內部傳送的 SAS 權杖)。 如果您不需要深入了解 AMQP，您可以搭配 .Net Framework 應用程式使用正式服務匯流排 SDK，其將為您做到這點。

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

上述 `PutCbsToken()` 方法會接收代表服務之 TCP 連線的 *connection* ([AMQP .NET Lite 程式庫](https://github.com/Azure/amqpnetlite)所提供的 AMQP 連接類別執行個體) 以及要做為 SAS 權杖傳送的 sasToken 參數。 

> [!NOTE]
> 請務必以 **設為 EXTERNAL 的 SASL 驗證機制** (而非當您不需要傳送 SAS 權杖時所使用且包含使用者名稱與密碼的預設 PLAIN) 建立連線。
> 
> 

接下來，發行者會建立兩個 AMQP 連結來傳送 SAS 權杖，並接受來自服務的回覆 (權杖驗證結果)。

AMQP 訊息包含眾多屬性，以及比簡單訊息更多的資訊。 SAS 權杖是訊息的內文 (使用其建構函式)。 **"ReplyTo"** 屬性設定為在接收者連結上接收驗證結果的節點名稱 (您可以視需要變更它的名稱，而服務將會動態建立它)。 最後三個應用程式/自訂屬性是由服務所使用，以指出它必須執行何種類型的作業。 如 CBS 草稿規格所述，它們必須是**作業名稱** (像是 "put-token")、**權杖類型** (在本案例中為 "servicebus.windows.net:sastoken")，以及套用權杖之**對象的 "name"** (完整實體)。

在寄件者連結上傳送 SAS 權杖之後，發行者必須在接收者連結上讀取回覆。 回覆是包含名稱為 **"status-code"** 之應用程式屬性的簡單 AMQP 訊息，可用來包含做為 HTTP 狀態碼的相同值

## <a name="rights-required-for-service-bus-operations"></a>服務匯流排作業所需的權限

下表顯示服務匯流排資源上各種作業所需的存取權限。

| 作業 | 所需的宣告 | 宣告範圍 |
| --- | --- | --- |
| **命名空間** | | |
| 在命名空間上設定授權規則 |管理 |任何命名空間位址 |
| **服務登錄** | | |
| 列舉私人原則 |管理 |任何命名空間位址 |
| 開始在命名空間上接聽 |接聽 |任何命名空間位址 |
| 將訊息傳送至命名空間上的接聽程式 |傳送 |任何命名空間位址 |
| **佇列** | | |
| 建立佇列 |管理 |任何命名空間位址 |
| 刪除佇列 |管理 |任何有效的佇列位址 |
| 列舉佇列 |管理 |/$Resources/Queues |
| 取得佇列描述 |管理 |任何有效的佇列位址 |
| 設定佇列的授權規則 |管理 |任何有效的佇列位址 |
| 傳送到佇列中 |傳送 |任何有效的佇列位址 |
| 從佇列接收訊息 |接聽 |任何有效的佇列位址 |
| 在 peek-lock 模式中接收訊息後放棄或完成訊息 |接聽 |任何有效的佇列位址 |
| 延遲訊息以便稍後擷取 |接聽 |任何有效的佇列位址 |
| 讓訊息寄不出去 |接聽 |任何有效的佇列位址 |
| 取得與訊息佇列工作階段相關聯的狀態 |接聽 |任何有效的佇列位址 |
| 設定與訊息佇列工作階段相關聯的狀態 |接聽 |任何有效的佇列位址 |
| **主題** | | |
| 建立主題 |管理 |任何命名空間位址 |
| 刪除主題 |管理 |任何有效的主題位址 |
| 列舉主題 |管理 |/$Resources/Topics |
| 取得主題描述 |管理 |任何有效的主題位址 |
| 設定主題的授權規則 |管理 |任何有效的主題位址 |
| 傳送至主題 |傳送 |任何有效的主題位址 |
| **訂用帳戶** | | |
| 建立訂閱 |管理 |任何命名空間位址 |
| 刪除訂用帳戶 |管理 |../myTopic/Subscriptions/mySubscription |
| 列舉訂用帳戶 |管理 |../myTopic/Subscriptions |
| 取得訂用帳戶描述 |管理 |../myTopic/Subscriptions/mySubscription |
| 在 peek-lock 模式中接收訊息後放棄或完成訊息 |接聽 |../myTopic/Subscriptions/mySubscription |
| 延遲訊息以便稍後擷取 |接聽 |../myTopic/Subscriptions/mySubscription |
| 讓訊息寄不出去 |接聽 |../myTopic/Subscriptions/mySubscription |
| 取得與主題工作階段相關聯的狀態 |接聽 |../myTopic/Subscriptions/mySubscription |
| 設定與主題工作階段相關聯的狀態 |接聽 |../myTopic/Subscriptions/mySubscription |
| **規則** | | |
| 建立規則 |管理 |../myTopic/Subscriptions/mySubscription |
| 刪除規則 |管理 |../myTopic/Subscriptions/mySubscription |
| 列舉規則 |管理或接聽 |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排訊息，請參閱下列主題。

* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com