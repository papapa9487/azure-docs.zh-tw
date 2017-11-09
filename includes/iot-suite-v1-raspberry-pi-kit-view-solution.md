## <a name="view-the-solution-dashboard"></a>檢視解決方案儀表板

解決方案儀表板可讓您管理部署的解決方案。 例如，您可以檢視遙測、新增裝置及叫用方法。

1. 當佈建完成且預先設定解決方案的圖格指示 [就緒] 時，選擇 [啟動] 以在新的索引標籤中開啟遠端監視解決方案入口網站。

    ![啟動預先設定的解決方案][img-launch-solution]

1. 根據預設，此解決方案入口網站會顯示儀表板。 您可以使用頁面左側的功能表，瀏覽至解決方案入口網站的其他區域。

    ![遠端監視預先設定解決方案儀表板][img-menu]

## <a name="add-a-device"></a>新增裝置

對於連線到預先設定解決方案的裝置，該裝置必須使用有效的認證向 IoT 中樞識別自己。 您會從解決方案儀表板收到裝置認證。 稍後在本教學課程中，您會將您的裝置認證包含在您的用戶端應用程式中。

如果您尚未這麼做，請將自訂裝置新增至您的遠端監視解決方案。 在解決方案儀表板中完成下列步驟︰

1. 在儀表板左下角，按一下 [新增裝置] 。

   ![新增裝置][1]

1. 在 [自訂裝置] 面板中，按一下 [新增]。

   ![新增自訂裝置][2]

1. 選擇 [讓我定義自己的裝置識別碼]。 輸入 [裝置識別碼]，例如 **rasppi**，按一下 [檢查識別碼] 以確認您尚未在解決方案中使用此名稱，然後按一下 [建立] 來佈建裝置。

   ![新增裝置識別碼][3]

1. 記下裝置認證 ([裝置識別碼]、[IoT 中樞主機名稱] 及 [裝置金鑰])。 Raspberry Pi 上的用戶端應用程式需要這些值，才能連線到遠端監視解決方案。 然後按一下 [ **完成**]。

    ![檢視裝置認證][4]

1. 從解決方案儀表板的裝置清單中選取您的裝置。 然後，在 [裝置詳細資料] 面板中，按一下 [啟用裝置]。 裝置的狀態現在會是 [正在執行]。 遠端監視解決方案現在已可從您的裝置接收遙測資料，並在該裝置上叫用方法。

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png