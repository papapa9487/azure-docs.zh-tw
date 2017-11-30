---
title: "如何使用 Azure Functions 中的 SendGrid | Microsoft Docs"
description: "說明如何使用 Azure Functions 中的 SendGrid"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>如何使用 Azure Functions 中的 SendGrid

## <a name="sendgrid-overview"></a>SendGrid 概觀

Azure Functions 支援 SendGrid 輸出繫結，可讓您的函式利用幾行程式碼和一個 SendGrid 帳戶，就能傳送電子郵件訊息。

若要在 Azure 函式中使用 SendGrid API，您必須有 [SendGrid 帳戶](http://SendGrid.com)。 此外，您還必須有 SendGrid API 金鑰。 登入您的 SendGrid 帳戶，按一下 [設定]，然後按一下 [API 金鑰] 產生 API 金鑰。 請備妥此金鑰，在後續步驟中會用到。

您現在已經準備好建立 Azure 函數應用程式。

## <a name="create-an-azure-function-app"></a>建立 Azure 函數應用程式 

Azure Functions 應用程式是一或多個 Azure 函式的容器。 Azure Functions 就只是函式。 每個 Azure 函式會繫結至一個觸發程序，也就是會導致執行函式的事件。
每個函式可以包含任意數目的輸入或輸出繫結。 繫結是您可以在函式中使用的服務。 SendGrid 是可用來傳送電子郵件的輸出繫結。 

1. 登入 Azure 入口網站，[建立 Azure 函數應用程式](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)，或開啟現有的函數應用程式。 
2. 建立 Azure 函式。 為了簡單起見，請選擇手動觸發程序和 C#。 

 ![建立 Azure 函式](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>設定 SendGrid 供 Azure 函數應用程式中使用

您必須將 SendGrid API 金鑰儲存為應用程式設定，才能在函式中使用。 ApiKey 欄位不是實際的 SendGrid API 金鑰，而是您定義來代表實際 API 金鑰的應用程式設定。 如此儲存金鑰是基於安全性，因為這樣會與任何可能簽入原始檔控制的程式碼或檔案分開。

- 在函式應用程式的 [應用程式設定] 中建立 **AppSettings**。

 ![建立 Azure 函式](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>設定 SendGrid 輸出繫結

SendGrid 可做為 Azure 函式輸出繫結。 若要建立 SendGrid 輸出繫結：

1. 在 Azure 入口網站中，移至函式的 [整合] 索引標籤。
2. 按一下 [新增輸出]，建立 SendGrid 輸出繫結。
3. 填寫 [API 金鑰] 和 [訊息參數名稱] 屬性。 想要的話，您可以現在就輸入其他屬性，或改以在程式碼中撰寫。 這些設定可用來當做預設值。

 ![設定 SendGrid 輸出繫結](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

將繫結新增至函式時，函式的資料夾中會建立名為 **function.json** 的檔案。 這個檔案包含的資訊與您在 Azure 函式的 [整合] 索引標籤中看到資訊完全相同，但為 Json 格式。 設定 [ApiKey]、[訊息] 和 [寄件者] 欄位後，將會在 **function.json** 檔案中建立下列項目︰ 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

想要的話，您可以自行直接修改這個檔案。

既然您已建立並設定函數應用程式和函式，接下來可以撰寫程式碼以傳送電子郵件。

## <a name="write-code-that-creates-and-sends-email"></a>撰寫程式碼來建立和傳送電子郵件

SendGrid API 包含建立和傳送電子郵件所需的全部命令。  

- 使用下列程式碼取代函式中的程式碼：

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

請注意，第一行包含 ```#r``` 指示詞來參考 SendGrid 組件。 之後，您可以使用 ```using``` 陳述式，更輕鬆地存取該命名空間中的物件。 在程式碼中，從 SendGrid API 建立 ```Mail```、```Personalization``` 和 ```Content``` 物件 (構成電子郵件) 的執行個體。 當您傳回訊息時，SendGrid 會傳送它。 

函式的簽章也包含 ```Mail``` 類型的額外 out 參數，名為 ```message```。 輸入和輸出繫結本身在程式碼中以函式參數表示。 

2. 按一下 [測試]，在 [要求本文] 欄位中輸入訊息，然後按一下 [執行] 按鈕，以測試您的程式碼。

 ![測試您的程式碼](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. 檢查電子郵件，確認 SendGrid 已傳送電子郵件。 它應該會傳送至步驟 1 程式碼中的位址，且包含 [要求本文] 中的訊息。

## <a name="next-steps"></a>後續步驟
本文已示範如何使用 SendGrid 服務來建立和傳送電子郵件。 若要深入了解在您的應用程式中使用 Azure Functions，請參閱下列主題︰ 

- [Azure Functions 的最佳做法](functions-best-practices.md)列出建立 Azure 函式時可採用的一些最佳做法。

- [Azure Functions 開發人員參考](functions-reference.md) 可供程式設計人員撰寫函式及定義觸發程序和繫結時參考。

- [測試 Azure Functions](functions-test-a-function.md) 說明可用於測試函式的各種工具和技巧。