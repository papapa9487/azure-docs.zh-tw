Azure 現在提供兩個偵錯功能的支援︰Azure 虛擬機器 Resource Manager 部署模型的主控台輸出和螢幕擷取畫面支援。 

將自己的映像送至 Azure 或甚至啟動其中一個平台映像時，虛擬機器進入不可開機狀態的原因有很多。 這些功能可讓您輕鬆地診斷及復原開機失敗的虛擬機器。

若為 Linux 虛擬機器，您可以從入口網站輕鬆地檢視主控台記錄的輸出︰

![Azure 入口網站](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
不過，若為 Windows 和 Linux 虛擬機器，Azure 也可讓您從 Hypervisor 查看 VM 的螢幕擷取畫面︰

![錯誤](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

所有區域中的 Azure 虛擬機器都支援這兩項功能。 請注意，螢幕擷取畫面和輸出最多可能需要 10 分鐘的時間才會出現在您的儲存體帳戶中。

## <a name="common-boot-errors"></a>常見的開機錯誤

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [找不到作業系統](https://support.microsoft.com/help/4010142)
- [開機失敗或 INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>在新的虛擬機器上啟用診斷
1. 從預覽入口網站建立新的虛擬機器時，從部署模型下拉式清單中選取 [Azure Resource Manager]︰
 
    ![資源管理員](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. 設定 [監視] 選項來選取您想要放置這些診斷檔案的儲存體帳戶。
 
    ![建立 VM](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. 如果您正從 Azure Resource Manager 範本進行部署，請瀏覽至您的虛擬機器的資源並附加診斷設定檔區段。 請記得使用 “2015-06-15” API 版本標頭。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. 診斷設定檔可讓您選取想要放置這些記錄的儲存體帳戶。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

若要在已啟用開機診斷的情況下部署範例虛擬機器，請在此查看我們的存放庫。

## <a name="update-an-existing-virtual-machine"></a>更新現有的虛擬機器 ##

若要透過入口網站啟用開機診斷，您也可以透過入口網站更新現有的虛擬機器。 選取 [開機診斷] 選項和 [儲存]。 重新啟動 VM 才會生效。

![更新現有的 VM](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

