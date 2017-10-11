---
title: "開始使用 Azure 服務匯流排佇列 | Microsoft Docs"
description: "撰寫使用服務匯流排傳訊佇列的 C# 主控台應用程式。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-service-bus-queues"></a>開始使用服務匯流排佇列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>將會完成的工作
本教學課程涵蓋下列步驟：

1. 使用 Azure 入口網站建立服務匯流排命名空間。
2. 使用 Azure 入口網站建立服務匯流排佇列。
3. 撰寫主控台應用程式來傳送訊息。
4. 撰寫主控台應用程式以接收上一個步驟中傳送的訊息。

## <a name="prerequisites"></a>必要條件
1. [Visual Studio 2015 或更新版本](http://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2017。
2. Azure 訂用帳戶。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 入口網站建立命名空間
如果您已建立服務匯流排傳訊命名空間，請跳至[使用 Azure 入口網站建立佇列](#2-create-a-queue-using-the-azure-portal)一節。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2.使用 Azure 入口網站建立佇列
如果您已經建立服務匯流排佇列，請跳至[將訊息傳送至佇列](#3-send-messages-to-the-queue)一節。

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3.將訊息傳送到佇列
為了將訊息傳送到佇列，我們使用 Visual Studio 撰寫 C# 主控台應用程式。

### <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio，並建立新的**主控台應用程式 (.NET Framework)** 專案。

### <a name="add-the-service-bus-nuget-package"></a>新增服務匯流排 NuGet 封裝
1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
2. 按一下 [瀏覽] 索引標籤，搜尋 [Microsoft Azure 服務匯流排]，然後選取 [WindowsAzure.ServiceBus] 項目。 按一下 [安裝]  完成安裝作業，然後關閉此對話方塊。
   
    ![選取 NuGet 封裝][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>撰寫一些程式碼來將訊息傳送到佇列
1. 在 Program.cs 檔案開頭處新增以下 `using` 陳述式。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. 將下列程式碼新增至 `Main` 方法。 將 `connectionString` 變數設定為建立命名空間時取得的連接字串，並將 `queueName` 設定為建立佇列時使用的佇列名稱。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 檔案看起來應該會像下面這樣。
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 執行程式，並檢查 Azure 入口網站：按一下命名空間 [概觀] 刀鋒視窗中的佇列名稱。 佇列 [Essentials] 刀鋒視窗即會顯示。 請注意，**使用中訊息計數**值現在應該是 1。 每次執行傳送者應用程式而未擷取訊息時，這個值就會增加 1。 也請注意，每當應用程式在佇列中新增訊息時，佇列目前的大小就會增加。
   
      ![訊息大小][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4.從佇列接收訊息

1. 若要接收您剛傳送的訊息，請建立新的主控台應用程式，並和上面的傳送者應用程式類似，新增服務匯流排 NuGet 套件的參考。
2. 在 Program.cs 檔案開頭處新增以下 `using` 陳述式。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. 將下列程式碼新增至 `Main` 方法。 將 `connectionString` 變數設定為建立命名空間時取得的連接字串，並將 `queueName` 設定為建立佇列時使用的佇列名稱。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 檔案看起來應該會像下面這樣：
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. 執行程式，並再次檢查入口網站。 請注意，**使用中訊息計數**和**目前**值現在是 0。
   
    ![佇列長度][queue-message-receive]

恭喜！ 您現已建立佇列、傳送訊息和接收訊息。

## <a name="next-steps"></a>後續步驟

查看 [GitHub 存放庫以及範例](https://github.com/Azure/azure-service-bus/tree/master/samples)，其中會展示一些更進階的服務匯流排傳訊功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
