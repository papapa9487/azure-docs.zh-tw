## <a name="prepare-your-intel-nuc"></a>準備您的 Intel NUC

若要完成硬體設定，您需要︰

- 將 Intel NUC 連線至套件內含的電源供應器。
- 使用乙太網路纜線將 Intel NUC 連線至您的網路。

您現在已完成 Intel NUC 閘道裝置的硬體設定。

### <a name="sign-in-and-access-the-terminal"></a>登入及存取終端機

您有兩種方式可存取 Intel NUC 上的終端機環境︰

- 如果您有連線至 Intel NUC 的鍵盤和監視器，就可以直接存取殼層。 預設認證是使用者名稱 **root** 和密碼 **root**。

- 使用桌上型電腦的 SSH 來存取 Intel NUC 上的殼層。

#### <a name="sign-in-with-ssh"></a>使用 SSH 登入

若要使用 SSH 登入，您需要 Intel NUC 的 IP 位址。 如果您有連線至 Intel NUC 的鍵盤和監視器，請使用 `ifconfig` 命令來尋找 IP 位址。 或者，連線至您的路由器，將您網路上的裝置位址列出。

以使用者名稱 **root** 和密碼 **root** 登入。

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>選擇性︰共用 Intel NUC 上的資料夾

您可以與桌上型電腦環境共用 Intel NUC 上的資料夾。 共用資料夾可讓您使用慣用的桌上型電腦文字編輯器 (例如 [Visual Studio Code](https://code.visualstudio.com/) 或 [Sublime Text](http://www.sublimetext.com/)) 來編輯 Intel NUC 上的檔案，而不是使用 `nano` 或 `vi`。

若要與 Windows 共用資料夾，請在 Intel NUC 上設定 Samba 伺服器。 或者，使用 Intel NUC 上的 SFTP 伺服器搭配桌上型電腦上的 SFTP 用戶端。
