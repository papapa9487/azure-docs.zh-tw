## <a name="overview"></a>概觀

在本教學課程中，您會完成下列步驟：

- 將遠端監視預先設定解決方案的執行個體部署至您的 Azure 訂用帳戶。 這個步驟會自動部署並設定多個 Azure 服務。
- 設定您的裝置，以便與您的電腦和遠端監視解決方案進行通訊。
- 更新範例裝置程式碼以連線到遠端監視解決方案，並傳送您可以在解決方案儀表板上檢視的模擬遙測。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

> [!NOTE]
> 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

### <a name="required-software"></a>必要的軟體

您需要透過桌上型電腦上的 SSH 用戶端，才能從遠端存取 Raspberry Pi 上的命令列。

- Windows 不包含 SSH 用戶端。 我們建議使用 [PuTTY](http://www.putty.org/)。
- 大部分的 Linux 散發套件和 Mac OS 都包含命令列 SSH 公用程式。 如需詳細資訊，請參閱[使用 Linux 或 Mac OS 的 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)。

### <a name="required-hardware"></a>必要的硬體

一部桌上型電腦，可讓您從遠端連線到 Raspberry Pi 上的命令列。

[適用於 Raspberry Pi 3 的 Microsoft IoT 入門套件][lnk-starter-kits]或對等的元件。 本教學課程會使用套件中的下列項目：

- Raspberry Pi 3
- MicroSD 卡 (具有 NOOBS)
- USB Mini 連接線
- 乙太網路連接線

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/