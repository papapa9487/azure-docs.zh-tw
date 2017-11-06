## <a name="build-iot-edge"></a>建置 IoT Edge

本教學課程使用使用自訂 IoT Edge 模組來與遠端監視預先設定的解決方案通訊。 因此，您必須從自訂來源程式碼建置 IoT Edge 模組。 下列各節說明如何安裝 IoT Edge 和建置自訂IoT Edge 模組。

### <a name="install-iot-edge"></a>安裝 IoT Edge

下列步驟說明如何在 Intel NUC 上安裝預先編譯的 IoT Edge 軟體︰

1. 在 Intel NUC 上執行下列命令，以設定必要的智慧套件存放庫︰

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    當命令提示您 [包括此通道?] 時，請輸入 `y`。

1. 執行下列命令來更新智慧套件管理員︰

    ```bash
    smart update
    ```

1. 執行下列命令來安裝 Azure IoT Edge 套件：

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. 執行 "Hello world" 範例來驗證安裝。 這個範例會每隔五秒將 hello world 訊息寫入 log.txT 檔案。 下列命令會執行 "Hello world" 範例︰

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    當您停止範例時，請忽略任何**無效的引數**訊息。

    使用下列命令來檢視記錄檔的內容︰

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>疑難排解

如果您收到「沒有套件提供 util-linux-dev」錯誤，請嘗試將 Intel NUC 重新開機。
