<!--author=alkohli last changed: 09/01/16-->

#### <a name="to-download-hotfixes"></a>下載 Hofix
請執行下列步驟，從 Microsoft Update Catalog 下載軟體更新。

1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。
2. 如果這是您在此電腦上第一次使用 Microsoft Update Catalog，請在系統提示您安裝 Microsoft Update Catalog 附加元件時，按一下 [安裝]  。
    ![安裝目錄](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. 在 Microsoft Update Catalog 的搜尋方塊中，輸入您要下載的 Hotfix 知識庫 (KB) 編號 (例如 **3186843**)，然後按一下搜尋。
   
    Hotfix 清單隨即出現，例如 **適用於 StorSimple 8000 系列的累積軟體套件組合更新 3.0**。
   
    ![搜尋目錄](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. 按一下 [新增] 。 更新便會新增到購物籃中。
5. 搜尋上表中所列的其他任何 Hotfix (**3186859**)，然後將每個都加入購物籃。
6. 按一下 [ **檢視購物籃**]。
7. 按一下 [下載] 。 指定或「瀏覽」  至您想要儲存下載項目的本機位置。 更新便會下載到指定的位置，並放在與更新名稱相同的子資料夾中。 資料夾也可以複製到裝置可連線的網路共用位置。

> [!NOTE]
> Hotfix 必須可同時從兩個控制器存取，以偵測來自對等控制器的任何潛在錯誤訊息。
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>安裝及驗證一般模式 Hotfix
執行下列步驟來安裝及驗證一般模式 Hotfix。 如果您已使用 Azure 入口網站安裝這些 Hotfix，請直接跳到 [安裝及驗證維護模式 Hotfix](#to-install-and-verify-maintenance-mode-hotfixes)。

1. 若要安裝 Hotfix，請存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。 請依照[使用 PuTTy 連接到序列主控台](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的詳細指示執行作業。 在命令提示字元中，按 **Enter**鍵。
2. 選取 [ **選項 1** ] 以使用完整的存取權限登入裝置。 建議您先在被動控制站上安裝此 Hotfix。
3. 若要安裝 Hotfix，請在命令提示字元中輸入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    在上述命令的共用路徑中使用 IP 而不是 DNS。 只有當您要存取已驗證的共用位置時，才會用到認證參數。
   
    我們建議您使用認證參數來存取共用項目。 即使是開放給「所有人」的共用項目，通常也不會開放給未經驗證的使用者。
   
    在系統提示時提供密碼。
   
    下方顯示一項範例輸出。
   
        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ````
4. 當系統提示您確認 Hotfix 安裝時，請輸入 **Y** 。
5. 使用 `Get-HcsUpdateStatus` Cmdlet 來監視更新。 會先在被動控制站上完成更新。 更新被動控制器之後，將進行容錯移轉，然後更新將套用到另一個控制器。 兩個控制器皆更新後，即更新完畢。
   
    下列範例輸出顯示更新進行中。 更新正在進行中時，`RunInprogress` 會是 `True`。

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 8/29/2016 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     下列範例輸出指出更新已完成。 更新完成時，`RunInProgress` 將會是 `False`。
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 8/30/2016 9:15:55 AM
    LastUpdateTimestamp : 8/30/2016 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE] 
    > 有時在更新進行期間，Cmdlet 會回報 `False`。 若要確保此 Hotfix 已完成，請等待幾分鐘的時間、重新執行此命令並確認 `RunInProgress` 為 `False`。 如果的確為 False 的話，則 Hotfix 已完成。

1. 軟體更新完成後，請確認系統軟體版本。 輸入：
   
    `Get-HcsSystem`
   
    您應該會看見下列版本：
   
   * `HcsSoftwareVersion: 6.3.9600.17759`
   * `CisAgentVersion:  1.0.9343.0`
   * `MdsAgentVersion: 30.0.4698.16`
     
     如果在套用更新後版本號碼並未變更，則表示此 Hotfix 未成功套用。 若您看到這種情況，請連絡 [Microsoft 支援](../articles/storsimple/storsimple-contact-microsoft-support.md)以取得進一步的協助。
     
     > [!IMPORTANT]
     > 您必須先透過 `Restart-HcsController` Cmdlet 重新啟動主動控制器，再套用其餘的更新。 
     > 
     > 
2. 重複步驟 3-5 來安裝 LSI 驅動程式及韌體 Hotfix **KB3186859**。 安裝 Hotfix 之後，請使用 `Get-HcsSystem` Cmdlet。 LSI 版本應為：
   
   * `Lsisas2Version: 2.0.78.00`
3. 重複步驟 3-5 來安裝 Storport 和 Spaceport 更新 **KB3121261**。
4. 如果您要從 Update 2 或之前版本進行更新，也必須下載：
   
   * 使用 KB3146621 的 iSCSI 修正程式
   * 使用 KB3103616 的 WMI 修正程式

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>安裝及驗證維護模式 Hotfix
請使用 KB3121899 來安裝磁碟韌體更新。 這些是干擾性更新，且需要約 30 分鐘來完成。 您可以藉由連接至裝置序列主控台，以選擇在預計的維護視窗中安裝這些更新。

請注意，如果您的磁碟韌體已是最新版本，便不需要安裝這些更新。 從裝置序列主控台執行 `Get-HcsUpdateAvailability` Cmdlet，以檢查是否有可用的更新，以及更新是干擾性 (維護模式) 還是非干擾性 (一般模式) 更新。

若要安裝磁碟韌體更新，請依照下面的指示執行。

1. 使裝置處於維護模式。 請注意，連線至處於維護模式的裝置時，您不應該使用 Windows PowerShell 遠端執行功能。 透過裝置序列主控台連線時，請在裝置控制器上執行此 Cmdlet。 輸入：
   
    `Enter-HcsMaintenanceMode`
   
    下方顯示一項範例輸出。
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76673
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    接著，兩個控制器就會重新啟動以進入維護模式。
2. 若要安裝磁碟韌體更新，請輸入：
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    下方顯示一項範例輸出。
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. 使用 `Get-HcsUpdateStatus` 命令監視安裝進度。 當 `RunInProgress` 變成 `False` 時，代表更新完成。
4. 安裝完成之後，維護模式 Hotfix 安裝所在的控制器將會重新開機。 以具有完整存取權的選項 1 登入，並驗證磁碟韌體版本。 輸入：
   
   `Get-HcsFirmwareVersion`
   
   預期的磁碟韌體版本為：
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   下方顯示一項範例輸出。
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17705
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    在第二個控制站上執行 `Get-HcsFirmwareVersion` 命令來驗證軟體版本已經更新。 然後您就可以結束維護模式。 若要這麼做，請針對每個裝置控制器輸入以下命令：
   
   `Exit-HcsMaintenanceMode`
5. 當您離開維護模式時，控制器便會重新啟動。 在磁碟韌體更新已成功套用且裝置已結束維護模式後，返回 Azure 傳統入口網站。 請注意，入口網站可能有 24 小時的時間不會顯示您已安裝維護模式更新。

