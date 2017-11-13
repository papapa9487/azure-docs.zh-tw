## <a name="create-an-iot-hub"></a>建立 IoT 中樞
建立 IoT 中樞以供您的模擬裝置應用程式連接。 下列步驟顯示如何使用 Azure 入口網站完成這項工作。

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

既然您已經建立 IoT 中樞，請找出您用來將裝置和應用程式與 IoT 中樞連線的重要資訊。 

1. 成功建立 IoT 中樞時，請按一下 Azure 入口網站中您的 IoT 中樞的新圖格，以開啟新 IoT 中樞的屬性視窗。 請記下**主機名稱**，然後按一下 [共用存取原則]。
   
    ![新的 IoT 中樞視窗][4]
1. 在 [共用存取原則] 中，按一下 [iothubowner] 原則，然後複製並記下 [iothubowner] 視窗中的 IoT 中樞連接字串。 如需詳細資訊，請參閱《IoT 中樞開發人員指南》中的[存取控制][lnk-access-control]。
   
    ![共用存取原則][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
