## <a name="view-device-telemetry"></a>檢視裝置遙測資料

您可以在解決方案中，檢視從您 [裝置] 頁面上之裝置所傳送的遙測。

1. 在 [裝置] 頁面上選取裝置清單中佈建的裝置。 面板會顯示您裝置的相關資訊，包括裝置遙測繪圖：

    ![請參閱裝置詳細資料](media/iot-suite-visualize-connecting/devicesdetail.png)

1. 選擇 [壓力] 以變更遙測顯示器：

    ![檢視壓力遙測](media/iot-suite-visualize-connecting/devicespressure.png)

1. 若要檢視有關您裝置的診斷資訊，請向下捲動至 [診斷]：

    ![檢視裝置診斷](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>在裝置上採取行動

若要在您的裝置上叫用方法，請使用遠端監視解決方案中的 [裝置] 頁面。 例如，在遠端監視解決方案中，**Chiller** 裝置會實作 **Reboot** 方法。

1. 選擇 [裝置] 以瀏覽至解決方案中的 [裝置] 頁面。

1. 在 [裝置] 頁面上選取裝置清單中佈建的裝置：

    ![選取您的實體裝置](media/iot-suite-visualize-connecting/devicesselect.png)

1. 若要顯示您可以在裝置上呼叫的方法清單，選擇 [排程]。 若要排程可在多個裝置上執行的方法，您可以在清單中選取多個裝置。 [排程] 面板會顯示所有您選取之裝置的通用方法。

1. 選擇 [重新開機]，將作業名稱設定為 **RebootPhysicalChiller**，然後選擇 [套用]：

    ![排程重新開機](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 當裝置處理該方法時，會在執行裝置程式碼的主控台中顯示訊息。

> [!NOTE]
> 若要在解決方案中追蹤作業的狀態，請選擇 [檢視]。

## <a name="next-steps"></a>後續步驟

[自訂遠端監視預先設定解決方案](../articles/iot-suite/iot-suite-remote-monitoring-customize.md)文章中說明一些自訂預先設定解決方案的方法。