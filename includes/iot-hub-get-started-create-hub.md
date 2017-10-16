## <a name="create-an-iot-hub"></a>建立 IoT 中樞
建立 IoT 中樞以供您的模擬裝置應用程式連接。 下列步驟顯示如何使用 Azure 入口網站完成這項工作。

1. 登入 [Azure 入口網站][lnk-portal]。
1. 在 Jumpbar 中，按一下 [新增]  >  [物聯網]  >  [IoT 中樞]。
   
    ![Azure 入口網站 Jumpbar][1]
1. 在 [IoT 中樞] 視窗中，選擇 IoT 中樞的組態。
   
    ![IoT 中樞視窗][2]
   
   1. 在 [名稱] 方塊中，輸入 IoT 中樞的名稱。 如果**名稱**有效且可用，[名稱] 方塊中會出現綠色的核取記號。
    [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]
   
   1. 選取一個[定價和調整層][lnk-pricing]。 本教學課程不需要特定層。 在本教學課程中，使用免費的 F1 層。
   1. 在 [資源群組]中，建立資源群組，或選取現有的資源群組。 如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源][lnk-resource-groups]。
   1. 在 [位置] 中，選取要裝載您的 IoT 中樞的位置。 在本教學課程中，選擇最接近您的位置。
1. 選擇好 IoT 中樞組態選項時，按一下 [建立] 。  Azure 建立您的 IoT 中樞可能需要幾分鐘的時間。 若要檢查狀態，您可以在「開始面板」或 [通知] 面板中監視進度。
   
1. 成功建立 IoT 中樞時，請按一下 Azure 入口網站中您的 IoT 中樞的新圖格，以開啟新 IoT 中樞的屬性視窗。 請記下**主機名稱**，然後按一下共用存取原則。
   
    ![新的 IoT 中樞視窗][4]
1. 在 [共用存取原則] 中，按一下 [iothubowner] 原則，然後複製並記下 [iothubowner] 視窗中的 IoT 中樞連接字串。 如需詳細資訊，請參閱《IoT 中樞開發人員指南》中的[存取控制][lnk-access-control]。
   
    ![共用存取原則][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
