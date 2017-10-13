> [!div class="op_single_selector"]
> * [Windows 上的 C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上的 C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>案例概觀
在此案例中，您會建立一個裝置，此裝置會將下列遙測資料傳送給遠端監視[預先設定解決方案][lnk-what-are-preconfig-solutions]：

* 外部溫度
* 內部溫度
* 溼度

為了簡化起見，在裝置上的程式碼會產生範例值，但我們鼓勵您將實際的感應器連接到您的裝置並傳送實際的遙測資料來擴充此範例。

此裝置同時也能夠回應從解決方案儀表板叫用的方法，以及回應解決方案儀表板中設定的所需屬性值。

若要完成此教學課程，您需要一個有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

## <a name="before-you-start"></a>開始之前
在您為裝置撰寫任何程式碼之前，您必須先佈建遠端監視預先設定解決方案，並在該解決方案中佈建新的自訂裝置。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>佈建遠端監視預先設定解決方案
您在本教學課程中建立的裝置會將資料傳送給[遠端監視][lnk-remote-monitoring]預先設定解決方案的執行個體。 如果您尚未在您的 Azure 帳戶中佈建遠端監視預先設定解決方案，請依照下列步驟執行：

1. 在 <https://www.azureiotsuite.com/> 頁面上，按一下 [**+**] 以建立解決方案。
2. 按一下 [遠端監視] 面板上的 [選取]，以建立解決方案。
3. 在 [建立遠端監視解決方案] 頁面上，輸入 您選擇的 [解決方案名稱]，選取您要部署的 [區域]，並選取想要使用的 Azure 訂用帳戶。 按一下 [建立解決方案] 。
4. 等候佈建程序完成。

> [!WARNING]
> 預先設定的解決方案使用可計費的 Azure 服務。 當您使用完預先設定的解決方案之後，請務必將它從您的訂用帳戶中移除，以避免任何不必要的費用。 您可以從訂用帳戶中完全移除預先設定的解決方案，請至 <https://www.azureiotsuite.com/> 頁面進行此操作。
> 
> 

當遠端監視解決方案的佈建程序完成之後，請按一下 [啟動]  ，以在瀏覽器中開啟解決方案儀表板。

![解決方案儀表板][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>在遠端監視方案中佈建您的裝置
> [!NOTE]
> 如果您已經在解決方案中佈建裝置，則可以略過此步驟。 建立用戶端應用程式時，您需要知道裝置認證。
> 
> 

對於連線到預先設定解決方案的裝置，該裝置必須使用有效的認證向 IoT 中樞識別自己。 您會從解決方案儀表板收到裝置認證。 稍後在本教學課程中，您會將您的裝置認證包含在您的用戶端應用程式中。

若要在您的遠端監視解決方案中新增裝置，請在解決方案儀表板中完成下列步驟：

1. 在儀表板左下角，按一下 [新增裝置] 。
   
   ![新增裝置][1]
2. 在 [自訂裝置] 面板中，按一下 [新增]。
   
   ![新增自訂裝置][2]
3. 選擇 [讓我定義自己的裝置識別碼]。 輸入裝置識別碼 (例如 **mydevice**)，按一下 檢查 ID 以確認沒有其他裝置使用該名稱，然後按一下建立 來佈建裝置。
   
   ![新增裝置識別碼][3]
4. 記下裝置認證 (裝置識別碼、IoT 中樞主機名稱及裝置金鑰)。 用戶端應用程式需要這些值，才能連接到遠端監視解決方案。 然後按一下 [完成]。
   
    ![檢視裝置認證][4]
5. 從解決方案儀表板的裝置清單中選取您的裝置。 然後，在 [裝置詳細資料] 面板中，按一下 [啟用裝置]。 裝置的狀態現在會是 [正在執行]。 遠端監視解決方案現在已可從您的裝置接收遙測資料，並在該裝置上叫用方法。

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/