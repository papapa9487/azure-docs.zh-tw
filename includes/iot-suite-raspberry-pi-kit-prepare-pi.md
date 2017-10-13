## <a name="prepare-your-raspberry-pi"></a>準備您的 Raspberry Pi

### <a name="install-raspbian"></a>安裝 Raspbian

如果這是您第一次使用 Raspberry Pi，您需要使用套件內含 SD 卡上的 NOOBS 來安裝 Raspbian 作業系統。 [Raspberry Pi 軟體指南][lnk-install-raspbian]說明如何在 Raspberry Pi 上安裝作業系統。 本教學課程假設您已在 Raspberry Pi 上安裝 Raspbian 作業系統。

> [!NOTE]
> [適用於 Raspberry Pi 3 的 Microsoft Azure IoT 入門套件][lnk-starter-kits]內含的 SD 卡已安裝 NOOBS。 您可以從這張卡啟動 Raspberry Pi，並選擇安裝 Raspbian OS。

### <a name="set-up-the-hardware"></a>設定工具

本教學課程使用[適用於 Raspberry Pi 3 的 Microsoft Azure IoT 入門套件][lnk-starter-kits]內含的 BME280 感應器來產生遙測資料。 其使用 LED 來指出 Raspberry Pi 何時從解決方案儀表板處理方法引動過程。

試驗電路板上的元件如下︰

- 紅色 LED
- 220-Ohm 電阻器 (紅色、紅色、棕色)
- BME280 感應器

下圖顯示連接硬體的方式：

![Raspberry Pi 的硬體設定][img-connection-diagram]

下表摘要說明如何從 Raspberry Pi 連接到試驗電路板上的元件︰

| Raspberry Pi            | 試驗電路板             |色彩         |
| ----------------------- | ---------------------- | ------------- |
| GND (針腳 14)            | LED -ve 針腳 (18A)      | 紫色          |
| GPCLK0 (針腳 7)          | 電阻器 (25A)         | 橙色          |
| SPI_CE0 (針腳 24)        | CS (39A)               | 藍色          |
| SPI_SCLK (針腳 23)       | SCK (36A)              | 黃色        |
| SPI_MISO (針腳 21)       | SDO (37A)              | 白色         |
| SPI_MOSI (針腳 19)       | SDI (38A)              | 綠色         |
| GND (針腳 6)             | GND (35A)              | 黑色         |
| 3.3 V (針腳 1)           | 3Vo (34A)              | 紅色           |

若要完成硬體設定，您需要︰

- 將 Raspberry Pi 連線至套件內含的電源供應器。
- 使用套件內含的乙太網路連接線，將 Raspberry Pi 連線至您的網路。 或者，您可以為 Raspberry Pi 設定[無線連線能力][lnk-pi-wireless]。

您現在已完成 Raspberry Pi 的硬體設定。

### <a name="sign-in-and-access-the-terminal"></a>登入及存取終端機

您有兩個選項可存取 Raspberry Pi 上的終端機環境︰

- 如果您的 Raspberry Pi 已連接鍵盤與監視器，您可以使用 Raspbian GUI 來存取終端機視窗。

- 使用 SSH 從您的桌上型電腦存取 Raspberry Pi 上的命令列。

#### <a name="use-a-terminal-window-in-the-gui"></a>在 GUI 中使用終端機視窗

Raspbian 的預設認證是使用者名稱 **pi** 和密碼 **raspberry**。 在 GUI 的工作列中，您可以使用看似監視器的圖示來啟動 **Terminal** 公用程式。

#### <a name="sign-in-with-ssh"></a>使用 SSH 登入

您可以使用命令列的 SSH 存取 Raspberry Pi。 [SSH (安全殼層)][lnk-pi-ssh] 一文說明如何在 Raspberry Pi 上設定 SSH，以及如何從 [Windows][lnk-ssh-windows] 或 [Linux 和 Mac OS][lnk-ssh-linux] 連接。

以使用者名稱 **pi** 和密碼 **raspberry** 登入。

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>選擇性︰共用 Raspberry Pi 上的資料夾

選擇性地，您可能想要與您的桌上型電腦環境共用 Raspberry Pi 上的資料夾。 共用資料夾可讓您使用慣用的桌上型電腦文字編輯器 (例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Sublime Text](http://www.sublimetext.com/)) 來編輯 Raspberry Pi 上的檔案，而不是使用 `nano` 或 `vi`。

若要與 Windows 共用資料夾，請在 Raspberry Pi 上設定 Samba 伺服器。 或者，使用內建 [SFTP](https://www.raspberrypi.org/documentation/remote-access/) 伺服器搭配桌上型電腦上的 SFTP 用戶端。

### <a name="enable-spi"></a>啟用 SPI

您必須先在 Raspberry Pi 上啟用串列周邊介面 (SPI) 匯流排，才可以執行範例應用程式。 Raspberry Pi 會透過 SPI 匯流排與 BME280 感應器裝置通訊。 使用下列命令來編輯組態檔︰

```sh
sudo nano /boot/config.txt
```

請尋找以下這行︰

`#dtparam=spi=on`

- 若要取消註解這行，請刪除開頭的 `#`。
- 儲存您的變更 (**Ctrl-O**、**Enter**) 並結束編輯器 (**Ctrl-X**)。
- 若要啟用 SPI，請重新啟動 Raspberry Pi。 重新啟動會中斷終端機的連線，您必須在 Raspberry Pi 重新啟動時再次登入︰

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/