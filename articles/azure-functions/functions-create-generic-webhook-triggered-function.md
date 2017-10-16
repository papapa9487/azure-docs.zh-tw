---
title: "在 Azure 中建立由泛型 Webhook 所觸發的函式 | Microsoft Docs"
description: "使用 Azure Functions 在 Azure 中建立 Webhook 所叫用的無伺服器函式。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: fafc10c0-84da-4404-b4fa-eea03c7bf2b1
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f283f8d79c5ae5fb6a72c84c9e9edb7bb8de4a83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-a-generic-webhook"></a>建立由泛型 Webhook 所觸發的函式

Azure Functions 可讓您在無伺服器環境中執行程式碼，而不需要先建立 VM 或發佈 Web 應用程式。 例如，您可以設定由 Azure 監視器引發之警示所觸發的函式。 本主題示範如何在將資源群組新增至訂用帳戶時執行 C# 程式碼。   

![Azure 入口網站中由泛型 Webhook 所觸發的函式](./media/functions-create-generic-webhook-triggered-function/function-completed.png)

## <a name="prerequisites"></a>必要條件 

若要完成本教學課程：

+ 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>建立 Azure 函數應用程式

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

接下來，您要在新的函式應用程式中建立函式。

## <a name="create-function"></a>建立由泛型 Webhook 所觸發的函式

1. 展開函式應用程式，然後按一下 [Functions] 旁的 [+] 按鈕。 如果這個函式是您函式應用程式中的第一個函式，請選取 [自訂函式]。 這會顯示一組完整的函式範本。

    ![Azure 入口網站中的 Functions 快速入門](./media/functions-create-generic-webhook-triggered-function/add-first-function.png)

2. 選取**泛型 WebHook - C#** 範本。 鍵入您 C# 函式的名稱，然後選取 [建立]。

     ![在 Azure 入口網站中建立由泛型 Webhook 所觸發的函式](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger.png) 

2. 在您的新函式中，按一下 [取得函式 URL]，然後複製並儲存值。 您可以使用此值來設定 Webhook。 

    ![檢閱函式程式碼](./media/functions-create-generic-webhook-triggered-function/functions-copy-function-url.png)
         
接下來，您可以在 Azure 監視器的活動記錄警示中建立 Webhook 端點。 

## <a name="create-an-activity-log-alert"></a>建立活動記錄警示

1. 在 Azure 入口網站中，巡覽至 [監視器] 服務，選取 [警示]，然後按一下 [新增活動記錄警示]。   

    ![監視](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert.png)

2. 使用表格中所指定的設定︰

    ![建立活動記錄警示](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings.png)

    | 設定      |  建議的值   | 說明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **活動記錄警示名稱** | resource-group-create-alert | 活動記錄警示的名稱。 |
    | **訂用帳戶** | 您的訂用帳戶 | 您將在本教學課程中使用的訂用帳戶。 | 
    |  **資源群組** | myResourceGroup | 警示資源所要部署的目標資源群組。 使用相同的資源群組作為您的函式應用程式，就可以輕鬆地讓您在完成教學課程之後予以清除。 |
    | **事件類別目錄** | 管理 | 此類別目錄包含對 Azure 資源所做的變更。  |
    | **資源類型** | 資源群組 | 將警示篩選至資源群組活動。 |
    | **資源群組**<br/>和**資源** | 全部 | 監視所有資源。 |
    | **作業名稱** | 建立資源群組 | 將警示篩選至建立作業。 |
    | **Level** | 資訊 | 包含資訊層級警示。 | 
    | **狀態** | Succeeded | 將警示篩選至已順利完成的動作。 |
    | **動作群組** | 新增 | 建立新的動作群組，以定義引發警示時所採取的動作。 |
    | **動作群組名稱** | function-webhook | 用於識別動作群組的名稱。  | 
    | **簡短名稱** | funcwebhook | 動作群組的簡短名稱。 |  

3. 在 [動作] 中，使用如下表中所指定的設定來新增動作： 

    ![新增動作群組](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings-2.png)

    | 設定      |  建議的值   | 說明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | CallFunctionWebhook | 動作的名稱。 |
    | **動作類型** | Webhook | 警示的回應是呼叫 Webhook URL。 |
    | **詳細資料** | 函式 URL | 貼上您之前複製之函式的 Webhook URL。 |v

4. 按一下 [確定] 以建立警示和動作群組。  

現在，當您在訂用帳戶中建立資源群組時，即會呼叫此 Webhook。 接下來，您要更新函式中的程式碼，以處理要求主體中的 JSON 記錄資料。   

## <a name="update-the-function-code"></a>更新函式程式碼

1. 在入口網站中巡覽回到您的函式應用程式，然後展開您的函式。 

2. 在入口網站中使用下列程式碼取代函式中的 C# 指令碼：

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"Webhook was triggered!");
    
        // Get the activityLog object from the JSON in the message body.
        string jsonContent = await req.Content.ReadAsStringAsync();
        JToken activityLog = JObject.Parse(jsonContent.ToString())
            .SelectToken("data.context.activityLog");
    
        // Return an error if the resource in the activity log isn't a resource group. 
        if (activityLog == null || !string.Equals((string)activityLog["resourceType"], 
            "Microsoft.Resources/subscriptions/resourcegroups"))
        {
            log.Error("An error occured");
            return req.CreateResponse(HttpStatusCode.BadRequest, new
            {
                error = "Unexpected message payload or wrong alert received."
            });
        }
    
        // Write information about the created resource group to the streaming log.
        log.Info(string.Format("Resource group '{0}' was {1} on {2}.",
            (string)activityLog["resourceGroupName"],
            ((string)activityLog["subStatus"]).ToLower(), 
            (DateTime)activityLog["submissionTimestamp"]));
    
        return req.CreateResponse(HttpStatusCode.OK);    
    }
    ```

現在，您可以在訂用帳戶中建立新的資源群組，來測試此函式。

## <a name="test-the-function"></a>測試函式

1. 按一下 Azure 入口網站左側的資源群組圖示，選取 [+ 新增]，鍵入**資源群組名稱**，然後選取 [建立] 以建立空的資源群組。
    
    ![建立資源群組。](./media/functions-create-generic-webhook-triggered-function/functions-create-resource-group.png)

2. 返回您的函式並展開 [記錄] 視窗。 建立資源群組之後，活動記錄警示會觸發 Webhook 並執行函式。 您會看到新資源群組的名稱已寫入記錄。  

    ![新增測試應用程式設定。](./media/functions-create-generic-webhook-triggered-function/function-view-logs.png)

3. (選擇性) 請返回並刪除您所建立的資源群組。 請注意，此活動不會觸發函式。 這是因為警示已篩選出刪除作業。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已建立函式，此函式會在收到泛型 Webhook 所提出的要求時開始執行。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

如需 Webhook 觸發程序的詳細資訊，請參閱 [Azure Functions HTTP 和 Webhook 繫結](functions-bindings-http-webhook.md)。 若要深入了解如何使用 C# 開發函式，請參閱 [Azure Functions C# 指令碼開發人員參考](functions-reference-csharp.md)。

