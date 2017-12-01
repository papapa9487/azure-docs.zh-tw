Azure IoT Edge 的主要功能之一，是能夠從雲端將模組部署到您的 IoT Edge 裝置。 IoT Edge 模組是實作為容器的可執行檔套件。 在本節中，您部署的模組會產生模擬裝置的遙測。 

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。
1. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。
1. 選取 [新增 IoT Edge 模組]。
1. 在 [名稱] 欄位中，輸入 `tempSensor`。 
1. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。 
1. 其他設定保留不變，然後選取 [儲存]。

   ![在輸入名稱和影像 URI 之後儲存 IoT Edge 模組](./media/iot-edge-deploy-module/name-image.png)

1. 回到 [新增模組] 步驟中，選取 [下一步]。
1. 在 [指定路由] 步驟中，選取 [下一步]。
1. 在 [檢閱範本] 步驟中，選取 [提交]。
1. 返回裝置的詳細資料頁面，選取 [重新整理]。 您應該會看到新的 tempSensor 模組沿著 IoT Edge 執行階段執行。 

   ![在已部署的模組清單中檢視 tempSensor][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png