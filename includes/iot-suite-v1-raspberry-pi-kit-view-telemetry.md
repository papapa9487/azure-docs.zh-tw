## <a name="view-the-telemetry"></a>檢視遙測資料

Raspberry Pi 現在會將遙測資料傳送至遠端監視解決方案。 您可以在解決方案儀表板上檢視遙測資料。 您也可以從解決方案儀表板將訊息傳送至 Raspberry Pi。

- 瀏覽至解決方案儀表板。
- 在 [要檢視的裝置] 下拉式清單中選取您的裝置。
- 來自 Raspberry Pi 的遙測資料會顯示在儀表板上。

![顯示來自 Raspberry Pi 的遙測資料][img-telemetry-display]

## <a name="act-on-the-device"></a>在裝置上採取行動

從解決方案儀表板，您可以在 Raspberry Pi 上叫用方法。 當 Raspberry Pi 連線到遠端監視解決方案時，它會傳送所支援方法的相關資訊。

- 在解決方案儀表板中，按一下 [裝置] 以造訪 [裝置] 頁面。 在 [裝置清單] 中選取您的 Raspberry Pi。 然後選擇 [方法]：

    ![在儀表板中列出裝置][img-list-devices]

- 在 [叫用方法] 頁面上，選擇 [方法] 下拉式清單中的 [LightBlink]。

- 選擇 [InvokeMethod]。 連接到 Raspberry Pi 的 LED 會閃爍數次。 Raspberry Pi 上的應用程式會將通知傳回解決方案儀表板︰

    ![顯示方法歷程記錄][img-method-history]

- 您可以使用 **ChangeLightStatus** 方法並將 **LightStatusValue** 設定為 **1** (開啟) 或 **0** (關閉)，以開啟和關閉 LED。

> [!WARNING]
> 如果讓遠端監視解決方案繼續在您的 Azure 帳戶中執行，您需支付其執行期間的費用。 如需將遠端監視解決方案執行時的耗用量降低的詳細資訊，請參閱[設定 Azure IoT 套件預先設定的解決方案以供示範][lnk-demo-config]。 使用完畢時，從您的 Azure 帳戶刪除預先設定的解決方案。


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md