## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

既然您已經建立 IoT 中樞，請找出您用來將裝置和應用程式與 IoT 中樞連線的重要資訊。 

1. 建立 IoT 中樞之後，請在儀表板上按一下它。 請記下**主機名稱**，然後按一下 [共用存取原則]。

   ![取得 IoT 中樞的主機名稱](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. 在 [共用存取原則] 窗格中，按一下 [iothubowner] 原則，然後複製並記下 IoT 中樞的**連接字串**。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](../articles/iot-hub/iot-hub-devguide-security.md)。

> [!NOTE] 
在此設定教學課程中，您不需要此 iothubowner 連接字串。 不過，在您完成此設定之後，您可能需要在不同 IoT 案例的一些教學課程中使用到它。

   ![取得 IoT 中樞連接字串](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>在裝置的 IoT 中樞中註冊裝置

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。

2. 按一下 [裝置總管]。
3. 在 [裝置總管] 窗格中，按一下 [新增] 將裝置新增到 IoT 中樞。 然後執行以下動作：

   **裝置識別碼**︰輸入新裝置的識別碼。 裝置識別碼會區分大小寫。

   **驗證類型**：選取 [對稱金鑰]。

   **自動產生金鑰**：選取此核取方塊。

   **將裝置連線至 IoT 中樞**：按一下 [啟用]。

   ![在 IoT 中樞的裝置總管中新增裝置](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. 按一下 [儲存] 。
5. 建立裝置之後，請在 [裝置總管] 窗格中開啟裝置。
6. 記下連接字串的主索引鍵。

   ![取得裝置連接字串](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
