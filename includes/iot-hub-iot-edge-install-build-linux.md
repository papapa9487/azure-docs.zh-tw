## <a name="install-the-prerequisites"></a>安裝必要條件

本教學課程中的步驟假設您正在執行 Ubuntu Linux。

若要安裝必要條件套件，請開啟殼層並執行下列命令︰

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

在殼層中，執行下列命令，將 Azure IoT Edge GitHub 存放庫複製到本機電腦：

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>如何建置範例

您現在可以在本機電腦上建置 IoT Edge 執行階段和範例：

1. 開啟殼層。

1. 瀏覽至 **iot-edge** 存放庫的本機複本中的根資料夾。

1. 執行組建指令碼，如下所示：

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

此指令碼使用 **cmake** 公用程式，在 **iot-edge** 存放庫本機複本的根資料夾中建立稱為 **build** 的資料夾，以及產生 Makefile。 此指令碼接著會建置方案，略過單元測試和端對端測試。 如果您想要建置並執行單元測試，請新增 `--run-unittests` 參數。 如果您想要建置並執行端對端測試，請新增 `--run-e2e-tests`。

> [!NOTE]
> 每次執行 **build.sh** 指令碼時，都會先在 **iot-edge** 存放庫本機複本的根資料夾中刪除再重新建立 **build** 資料夾。