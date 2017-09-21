
在本節中，您會從 .NET 主控台應用程式將即時新聞以加註標記的範本通知形式傳送。

如果您使用 Microsoft Azure App Service 的 Mobile Apps 功能，請參閱[新增 Mobile Apps 的推播通知]教學課程，並在頂端選取您的平台。

如果您想要使用 Java 或 PHP，請參閱[如何從 Java 或 PHP 使用通知中樞]。 您可以使用[通知中樞 REST 介面]，從任何後端傳送通知。

如果您在完成[開始使用通知中樞]時建立了主控台應用程式以供傳送通知，請略過步驟 1-3。

1. 在 Visual Studio 中，建立新的 Visual C# 主控台應用程式：
   
      ![主控台應用程式連結][13]

2. 在 Visual Studio 主功能表上，選取 [工具] > [Library Package Manager] >  [Package Manager Console]，然後在主控台視窗中輸入下列字串：
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. 選取 [輸入]。  
    此動作會使用 [Microsoft.Azure.Notification Hubs NuGet 套件]來新增對 Azure 通知中樞 SDK 的參考。

4. 開啟 Program.cs 檔案，並新增下列 `using` 陳述式：
   
        using Microsoft.Azure.NotificationHubs;

5. 在 `Program` 類別中，新增或取代 (如果方法已存在) 下列方法：
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    此程式碼會分別將範本通知傳送給字串陣列中的六個標籤。 使用標籤可確保裝置只會收到已登錄類別的通知。

5. 在上述程式碼中，請使用您的通知中樞名稱及通知中樞儀表板的 *DefaultFullSharedAccessSignature* 連接字串，來取代 `<hub name>` 和 `<connection string with full access>` 預留位置。

6. 在 [主要] 方法中新增下列程式碼行：
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. 建置主控台應用程式。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[開始使用通知中樞]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[通知中樞 REST 介面]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[新增 Mobile Apps 的推播通知]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[如何從 Java 或 PHP 使用通知中樞]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet 套件]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
