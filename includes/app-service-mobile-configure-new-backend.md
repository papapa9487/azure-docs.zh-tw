
1. 按一下**應用程式服務**按鈕，選取您的行動應用程式後端，選取**快速入門**，然後選取您用戶端平台 (iOS、 Android、 Xamarin、 Cordova)。

    ![Azure 入口網站以反白顯示的行動應用程式快速入門][quickstart]

2. 如果未設定資料庫連接，建立一個由執行下列動作：

    ![Azure 入口網站中使用行動應用程式連接資料庫][connect]

    a. 建立新的 SQL 資料庫和伺服器。

    ![與行動應用程式的 azure 入口網站建立新的資料庫和伺服器][server]

    b. 等到成功建立資料連線為止。

    ![成功建立資料連接的 azure 入口網站的通知][notification]

    c. 資料連線必須成功。

    ![Azure 入口網站的通知，「 您已經有資料連接 」][already-connection]

3. 在 **2.建立資料表 API** 之下，選取 Node.js 作為**後端語言**。 
 
4. 接受通知，然後選取 **建立 TodoItem 資料表**。  
    這個動作會在資料庫中建立新的待辦項目資料表。 

    >[!IMPORTANT]
    > 現有的後端切換到 Node.js，就會覆寫所有內容。 若要改為建立的.NET 後端，請參閱[搭配行動應用程式的.NET 後端伺服器 SDK][instructions]。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
