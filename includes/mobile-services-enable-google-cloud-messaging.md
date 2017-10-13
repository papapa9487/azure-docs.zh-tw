
1. 瀏覽至 [Google 雲端主控台](https://console.developers.google.com/project)，並使用您的 Google 帳戶認證登入。 
2. 按一下 [建立專案]，輸入專案名稱，接著按 [建立]。 如果有要求，請執行簡訊驗證，再重新按一下 [建立]  。
   
    ![建立新專案](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     輸入新的**專案名稱**並按一下 [建立專案]。
3. 按一下 公用程式與其他 按鈕，然後按一下專案資訊。 請記下 [專案編號] 。 您必須設定這個值做為用戶端應用程式中的 `SenderId` 變數。
   
    ![公用程式及其他資訊](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. 在專案儀表板中，按一下 [行動 API] 之下的 [Google 雲端通訊]，接著在下一頁按一下 [啟用 API] 並接受服務條款。 
   
    ![啟用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![啟用 GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. 在專案儀表板中，按一下 [憑證]  >  [建立憑證]  >  [API 金鑰]。 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. 在 [建立新的金鑰] 中，按一下 [伺服器金鑰]，輸入金鑰的名稱，然後按一下 [建立]。
7. 記下 [API 金鑰]  的值。
   
    您將使用此 API 金鑰值，讓 Azure 能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。

