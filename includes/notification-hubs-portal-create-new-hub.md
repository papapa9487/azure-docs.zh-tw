

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [新增] > **[Web + 行動]** > [通知中樞]。
   
      ![Azure 入口網站 - 建立通知中樞](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. 在 [通知中樞] 方塊中，輸入唯一的名稱。 選取您的 [區域]、[訂用帳戶] 和 [資源群組] \(如果您已經有的話)。 
   
    如果您已經有想要在其中建立中樞的服務匯流排命名空間，請執行下列作業：

    a. 在 [命名空間] 區域中，選取 [選取現有] 連結。 
   
    b. 選取 [ **建立**]。

    如果您還沒有服務匯流排命名空間，您可以使用預設名稱，根據中樞名稱建立此預設名稱 (如果命名空間名稱可用)。
   
      ![Azure 入口網站 - 設定通知中樞屬性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

    建立命名空間和通知中樞之後，Azure 入口網站隨即開啟。 
   
      ![Azure 入口網站 - 通知中樞入口網站頁面](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)

4. 選取 [設定] > [存取原則]。 請記下您可使用的兩個連接字串。 您稍後需要用到這些連接字串來處理推播通知。
   
      ![Azure 入口網站 - 通知中樞連接字串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

