## <a name="install-the-prerequisites"></a>安裝必要條件

1. 安裝 [Visual Studio 2015 或 2017](https://www.visualstudio.com)。 如果您符合授權需求，就可以使用免費的 Community Edition。 請務必包含 Visual C++ 和 NuGet 套件管理員。

1. 安裝 [git](http://www.git-scm.com)，並確定您可以從命令列執行 git.exe。

1. 安裝 [CMake](https://cmake.org/download/)，並確定您可以從命令列執行 cmake.exe。 建議使用 CMake 3.7.2 版本或更新版本。 **.msi** 安裝程式是 Windows 上最簡單的選項。 當安裝程式提示時，請至少將 CMake 新增至目前使用者的路徑。

1. 安裝 [Python 2.7](https://www.python.org/downloads/release/python-27)。 請務必將 Python 新增至 `PATH` 環境變數。 移至 [控制台] > [系統及安全性] > [系統] > [進階系統設定] > [環境變數]。 將 `C:\Python27` 新增至您的路徑。 

1. 在命令提示字元中，執行下列命令，將 Azure IoT Edge GitHub 存放庫複製到本機電腦：

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>如何建置範例

您現在可以在本機電腦上建置 IoT Edge 執行階段和範例：

1. 根據您的版本，開啟「VS 2015 開發人員命令提示字元」或「VS 2017 開發人員命令提示字元」。

1. 瀏覽至 **iot-edge** 存放庫的本機複本中的根資料夾。

1. 執行組建指令碼，如下所示：

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

此指令碼會建立 Visual Studio 方案檔及建置方案。 您可以在 **iot-edge** 存放庫本機複本的 **build** 資料夾中找到此 Visual Studio 方案。 如果您想要建置並執行單元測試，請新增 `--run-unittests` 參數。 如果您想要建置並執行端對端測試，請新增 `--run-e2e-tests`。

> [!NOTE]
> 每次執行 **build.cmd** 指令碼時，都會先在 **iot-edge** 存放庫本機複本的根資料夾中刪除，再重新建立 **build** 資料夾。