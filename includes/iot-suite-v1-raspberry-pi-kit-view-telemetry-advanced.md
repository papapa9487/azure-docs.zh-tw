## <a name="view-the-telemetry"></a>檢視遙測資料

Raspberry Pi 現在會將遙測資料傳送至遠端監視解決方案。 您可以在解決方案儀表板上檢視遙測資料。 您也可以從解決方案儀表板將訊息傳送至 Raspberry Pi。

- 瀏覽至解決方案儀表板。
- 在 [要檢視的裝置] 下拉式清單中選取您的裝置。
- 來自 Raspberry Pi 的遙測資料會顯示在儀表板上。

![顯示來自 Raspberry Pi 的遙測資料][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>起始韌體更新

韌體更新程序會在 Raspberry Pi 上下載及安裝已更新的裝置用戶端應用程式版本。 如需有關韌體更新程序的詳細資訊，請參閱[使用 IoT 中樞管理裝置的概觀][lnk-update-pattern]中的韌體更新模式說明。

您可以在裝置上叫用方法，以起始韌體更新程序。 這個方法是非同步的，並會在更新程序開始時盡快傳回資料。 裝置會使用報告的屬性來通知解決方案有關更新的進度。

您可從解決方案儀表板在 Raspberry Pi 上叫用方法。 當 Raspberry Pi 第一次連線到遠端監視解決方案時，它會傳送所支援方法的相關資訊。 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
