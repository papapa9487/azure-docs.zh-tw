---
title: "開始使用 Azure 服務匯流排主題和訂用帳戶 | Microsoft Docs"
description: "撰寫使用服務匯流排傳訊主題和訂用帳戶的 C# 主控台應用程式。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 43064ddbdfec3e2ddae8de8c69f6c03dc7f8f45e
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="get-started-with-service-bus-topics"></a>開始使用服務匯流排主題

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本教學課程涵蓋下列步驟：

1. 使用 Azure 入口網站建立服務匯流排命名空間。
2. 使用 Azure 入口網站建立服務匯流排主題。
3. 使用 Azure 入口網站，針對該主題建立服務匯流排訂用帳戶。
4. 撰寫主控台應用程式以傳送訊息至主題。
5. 撰寫主控台應用程式以從訂用帳戶接收該訊息。

## <a name="prerequisites"></a>必要條件

1. [Visual Studio 2015 或更新版本](http://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2017。
2. Azure 訂用帳戶。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 入口網站建立命名空間

如果您已建立服務匯流排傳訊命名空間，請跳至[使用 Azure 入口網站建立主題](#2-create-a-topic-using-the-azure-portal)一節。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2.使用 Azure 入口網站建立主題

1. 登入 [Azure 入口網站][azure-portal]。
2. 在入口網站的左方瀏覽窗格中，按一下 [服務匯流排] \(如果您未看見 [服務匯流排]，請按一下 [更多服務]，或按一下 [所有資源])。
3. 按一下要在其中建立主題的命名空間。 命名空間概觀的刀鋒視窗即會出現：
   
    ![建立主題][createtopic1]
4. 在 [服務匯流排命名空間] 刀鋒視窗中，按一下 [主題]，然後按一下 [新增主題]。
   
    ![選取主題][createtopic2]
5. 輸入主題名稱，然後取消核取 [啟用資料分割] 選項。 保留其他選項的預設值。
   
    ![選取新增][createtopic3]
6. 按一下刀鋒視窗底部的 [建立] 。

## <a name="3-create-a-subscription-to-the-topic"></a>3.針對主題建立訂用帳戶

1. 在入口網站的資源窗格中，按一下在步驟 1 所建立的命名空間，接著按一下在步驟 2 所建立的主題名稱。
2. 在概觀窗格頂端，按一下 [訂用帳戶] 旁的加號，將訂用帳戶新增至主題。

    ![建立訂用帳戶][createtopic4]

3. 輸入訂用帳戶名稱。 保留其他選項的預設值。

## <a name="4-send-messages-to-the-topic"></a>4.將訊息傳送到主題

為了將訊息傳送至主題，我們使用 Visual Studio 撰寫 C# 主控台應用程式。

### <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio，並建立新的**主控台應用程式 (.NET Framework)** 專案。

### <a name="add-the-service-bus-nuget-package"></a>新增服務匯流排 NuGet 封裝

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
2. 按一下 [瀏覽] 索引標籤，搜尋 [WindowsAzure.ServiceBus]，然後選取 [WindowsAzure.ServiceBus] 項目。 按一下 [安裝]  完成安裝作業，然後關閉此對話方塊。
   
    ![選取 NuGet 封裝][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>撰寫一些程式碼以將訊息傳送至主題

1. 在 Program.cs 檔案開頭處新增以下 `using` 陳述式。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. 將下列程式碼新增至 `Main` 方法。 將 `connectionString` 變數設定為建立命名空間時取得的連接字串，並將 `topicName` 設定為建立主題時使用的名稱。
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 執行程式，並檢查 Azure 入口網站：按一下命名空間 [概觀] 刀鋒視窗中的主題名稱。 主題 [Essentials] 刀鋒視窗即會顯示。 請注意，在靠近刀鋒視窗底部所列的訂用帳戶中，每個訂用帳戶的**訊息計數**值現在應該是 1。 每次執行傳送者應用程式而未擷取訊息 (如下一節所述)，這個值就會增加 1。 也請注意，每當應用程式新增訊息至主題/訂用帳戶，主題目前的大小就會讓 [Essentials] 刀鋒視窗上的**目前**值增加。
   
      ![訊息大小][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5.自訂用帳戶接收訊息

1. 若要接收您剛傳送的訊息，請建立新的主控台應用程式，並和上面的傳送者應用程式類似，新增服務匯流排 NuGet 套件的參考。
2. 在 Program.cs 檔案開頭處新增以下 `using` 陳述式。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. 將下列程式碼新增至 `Main` 方法。 將 `connectionString` 變數設定為建立命名空間時取得的連接字串，並將 `topicName` 設定為建立主題時使用的名稱。 另外，請務必以您在步驟 3 中建立的訂用帳戶名稱來取代 `<your subscription name>`。 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. 執行程式，並再次檢查入口網站。 請注意，**訊息計數**和**目前**值現在是 0。
   
    ![主題長度][topic-message-receive]

恭喜！ 您現在已建立主題和訂用帳戶，傳送一則訊息並接收該訊息。

## <a name="next-steps"></a>後續步驟

查看 [GitHub 存放庫以及範例](https://github.com/Azure/azure-service-bus/tree/master/samples)，其中會展示一些更進階的服務匯流排傳訊功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
