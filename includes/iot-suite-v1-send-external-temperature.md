## <a name="configure-the-nodejs-simulated-device"></a>設定 Node.js 模擬裝置
1. 在遠端監視儀表板上，按一下 [+ 新增裝置]，然後新增*自訂裝置*。 記下 IoT 中樞主機名稱、裝置識別碼和裝置金鑰。 當您在本教學課程稍後準備 remote_monitoring.js 裝置用戶端應用程式時，需要這些資料。
2. 請確定 Node.js 0.12.x 版或更新版本已經安裝在您的開發電腦上。 在命令提示字元或殼層中執行 `node --version` 來檢查版本。 如需使用套件管理員在 Linux 上安裝 Node.js 的資訊，請參閱 [Installing Node.js via package manager (透過套件管理員安裝 Node.js)][node-linux]。
3. 安裝 Node.js 之後，請將最新版本的 [azure-iot-sdk-node][lnk-github-repo] 儲存機制複製到您的開發電腦。 一律使用 **master** 分支取得最新版本的程式庫和範例。
4. 從您的本機複本 [azure-iot-sdk-node][lnk-github-repo] 儲存機制，將下列兩個檔案從 node/device/samples 資料夾複製到您開發電腦的空白資料夾中：
   
   * packages.json
   * remote_monitoring.js
5. 開啟 remote_monitoring.js 檔案並尋找下列變數定義：
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. 以您裝置的連接字串取代 **[IoT 中樞裝置連接字串]** 。 使用您在步驟 1 記下的 IoT 中樞主機名稱、裝置識別碼，以及裝置金鑰的值。 裝置連接字串使用的格式如下：
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    如果 IoT 中樞主機名稱是 **contoso** 且裝置識別碼是 **mydevice**，連接字串看起來如下片段：
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. 儲存檔案。 在殼層或命令提示字元中，於包含這些檔案的資料夾，執行下列命令以安裝必要的套件，然後執行範例應用程式：
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>觀察運作中的動態遙測
儀表板會顯示現有模擬裝置的溫度與濕度遙測：

![預設儀表板][image1]

如果您選取在上一節執行的 Node.js 模擬裝置，您會看到溫度、濕度與外部溫度遙測：

![新增外部溫度到儀表板][image2]

遠端監視解決方案會自動偵測其他的外部溫度遙測類型，並將它新增到儀表板的圖表上。

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png