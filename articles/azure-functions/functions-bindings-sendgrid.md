---
title: "Azure Functions SendGrid 繫結 | Microsoft Docs"
description: "Azure Functions SendGrid 繫結參考"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: bcdbb6aee49d230a4cb0ba08d836facacb64de7f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 繫結

此文章說明如何在 Azure Functions 中設定及使用 SendGrid 繫結。 透過 SendGrid，您可以使用 Azure Functions 以程式設計方式傳送自訂的電子郵件。

此文章是適用於 Azure Functions 開發人員的參考資訊。 如果您不熟悉 Azure Functions，請從下列資源開始︰

[建立您的第一個 Azure 函式](functions-create-first-azure-function.md)。 
[C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md) 或 [Node](functions-reference-node.md) 開發人員參考。

## <a name="functionjson-for-sendgrid-bindings"></a>適用於 SendGrid 繫結的 function.json

Azure Functions 提供適用於 SendGrid 的輸出繫結。 SendGrid 輸出繫結可讓您以程式設計方式建立並傳送電子郵件。 

SendGrid 繫結支援下列屬性︰

|屬性  |說明  |
|---------|---------|
|**name**| 必要項目 - 函式程式碼中用於要求或要求主體的變數名稱。 當只有一個傳回值時，此值為 ```$return```。 |
|**type**| 必要項目 - 必須設定為 `sendGrid`。|
|**direction**| 必要項目 - 必須設定為 `out`。|
|**apiKey**| 必要項目 - 必須設定為函數應用程式的應用程式設定中儲存的 API 金鑰名稱。 |
|**to**| 收件者的電子郵件地址。 |
|**from**| 寄件者的電子郵件地址。 |
|**subject**| 電子郵件主旨。 |
|**text**| 電子郵件內容。 |

**function.json** 範例：

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions 將會您的連接資訊和 API 金鑰儲存為應用程式設定，使得不會將讓它們簽入至您的原始檔控制儲存機制。 這個動作會保護您的機密資訊。
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>SendGrid 輸出繫結的 C# 範例

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>SendGrid 輸出繫結的 Node 範例

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>後續步驟
如需 Azure Functions 其他繫結與觸發程序的相關資訊，請參閱 
- [Azure Functions 觸發程序和繫結開發人員參考](functions-triggers-bindings.md)

- [Azure Functions 的最佳做法](functions-best-practices.md)列出建立 Azure 函式時可採用的一些最佳做法。

- [Azure Functions 開發人員參考](functions-reference.md) 可供程式設計人員撰寫函式及定義觸發程序和繫結時參考。

