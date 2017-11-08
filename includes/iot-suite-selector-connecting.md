> [!div class="op_single_selector"]
> * [Windows 上的 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (一般)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Raspberry Pi 上的 Node.js](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [Raspberry Pi 上的 C](../articles/iot-suite/iot-suite-connecting-pi-c.md)

在此教學課程中，您會實作一個 **Chiller** 裝置，此裝置會將下列遙測資料傳送給遠端監視[預先設定解決方案](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md)：

* 溫度
* 壓力
* 溼度

為了簡單起見，程式碼會產生 **Chiller** 的範例遙測值。 您可以將實際感應器連線到您的裝置並傳送實際的遙測來擴充範例。

範例裝置也會：

* 將中繼資料傳送至解決方案來描述其功能。
* 回應從解決方案中的**裝置**頁面觸發的動作。
* 回應從解決方案中的**裝置**頁面傳送的設定變更。

若要完成此教學課程，您需要一個有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="before-you-start"></a>開始之前

在您為裝置撰寫任何程式碼之前，您必須先佈建遠端監視預先設定解決方案，並在該解決方案中佈建新的自訂裝置。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>佈建遠端監視預先設定解決方案

您在本教學課程中建立的 **Chiller** 裝置會將資料傳送給[遠端監視](../articles/iot-suite/iot-suite-remote-monitoring-explore.md)預先設定解決方案的執行個體。 如果您尚未在您的 Azure 帳戶中佈建遠端監視預先設定解決方案，請參閱[部署遠端監視預先設定的解決方案](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

當遠端監視解決方案的佈建程序完成之後，請按一下 [啟動]  ，以在瀏覽器中開啟解決方案儀表板。

![解決方案儀表板](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>在遠端監視方案中佈建您的裝置

> [!NOTE]
> 如果您已經在解決方案中佈建裝置，則可以略過此步驟。 建立用戶端應用程式時，您需要裝置認證。

對於連線到預先設定解決方案的裝置，該裝置必須使用有效的認證向 IoT 中樞識別自己。 您會從解決方案**裝置**頁面收到裝置認證。 稍後在本教學課程中，您會將您的裝置認證包含在您的用戶端應用程式中。

若要在您的遠端監視解決方案中新增裝置，請在解決方案**裝置**頁面中完成下列步驟：

1. 選擇 [佈建]，然後選擇 [實體]作為 [裝置類型]：

    ![佈建實體裝置](media/iot-suite-selector-connecting/devicesprovision.png)

1. 輸入 **Physical-chiller** 作為裝置識別碼。 選擇 [對稱金鑰] 和 [自動產生金鑰] 選項：

    ![選擇裝置選項](media/iot-suite-selector-connecting/devicesoptions.png)

若要找出認證，必須使用您的裝置連線到預先設定的解決方案，在瀏覽器中瀏覽至您的 Azure 入口網站。 登入您的訂用帳戶。

1. 找出資源群組，當中包含遠端監視解決方案所使用的 Azure 服務。 資源群組與您佈建的遠端監視解決方案具有相同名稱。

1. 瀏覽到此資源群組中的 IoT 中樞。 然後選擇 [裝置總管]：

    ![裝置總管](media/iot-suite-selector-connecting/deviceexplorer.png)

1. 在遠端監視解決方案中，選擇您在 [裝置]頁面上建立的 [裝置識別碼]。

1. 記下**裝置識別碼**和**主要金鑰**值。 當您新增程式碼以將裝置連線到解決方案時，可以使用這些值。

您現在已在遠端監視預先設定的解決方案中佈建實體裝置。 在下列章節中，您可以實作使用裝置認證來連線到您解決方案的用戶端應用程式。

用戶端應用程式會實作內建 **Chiller** 裝置型號。 預先設定的解決方案裝置型號會指定下列相關裝置資訊：

* 裝置回報至解決方案的屬性。 例如，**Chiller** 裝置會報告其韌體和位置的相關資訊。
* 裝置傳送至解決方案的遙測類型。 例如，**Chiller** 裝置傳會送溫度、溼度和壓力值。
* 您可以從解決方案中排程以在裝置上執行的方法。 例如，**Chiller** 裝置必須實作 **Reboot**、**FirmwareUpdate**、**EmergencyValveRelease** 和 **IncreasePressuree** 方法。