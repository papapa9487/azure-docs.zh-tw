如果 Azure 中的虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文說明如何使用 Azure 入口網站將虛擬硬碟連接至另一個 VM，以修正任何錯誤，然後重新建立原始 VM。


## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 刪除遇到問題的 VM，但保留虛擬硬碟。
2. 將虛擬硬碟連結和裝載至另一個 VM，以進行疑難排解。
3. 連接至疑難排解 VM。 編輯檔案或執行工具來修正原始虛擬硬碟的錯誤。
4. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
5. 使用原始虛擬硬碟建立 VM。

## <a name="delete-the-original-vm"></a>刪除原始的 VM
虛擬硬碟和 VM 在 Azure 中是兩個不同的資源。 虛擬硬碟中儲存作業系統、應用程式和設定。 VM 只是中繼資料，可定義大小或位置，還可參考資源，例如虛擬硬碟或虛擬網路介面卡 (NIC)。 每個虛擬硬碟在連結至 VM 時會獲派租用。 雖然即使 VM 正在執行時也可以連結和中斷連結資料磁碟，但除非刪除 VM 資源，否則無法中斷連結 OS 磁碟。 即使 VM 處於已停止和解除配置的狀態，租用仍會持續讓 OS 磁碟與 VM 產生關聯。

復原 VM 的第一個步驟是刪除 VM 資源本身。 刪除 VM 時，虛擬硬碟會留在儲存體帳戶中。 刪除 VM 之後，您可以將虛擬硬碟連結至另一個 VM，以進行疑難排解並解決錯誤。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在左側功能表中，按一下 [虛擬機器 (傳統)]。
3. 選取發生問題的 VM，按一下 [磁碟]，然後識別虛擬硬碟的名稱。 
4. 選取 OS 虛擬硬碟，並檢查 [位置] 來識別包含該虛擬硬碟的儲存體帳戶。 在下列範例中，".blob.core.windows.net" 前面緊鄰的字串是儲存體帳戶名稱。

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![VM 位置的相關影像](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. 以滑鼠右鍵按一下 VM，然後選取 [刪除]。 請確定當您刪除 VM 時未選取磁碟。
6. 建立新的復原 VM。 此 VM 必須與問題 VM 位於相同的區域和資源群組 (雲端服務)。
7. 選取復原 VM，然後選取 [磁碟] > [連結現有項目]。
8. 若要選取您現有的虛擬硬碟，請按一下 [VHD 檔案]：

    ![瀏覽現有 VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. 選取儲存體帳戶 > VHD 容器 > 虛擬硬碟，按一下 [選取] 按鈕，以確認您的選擇。

    ![選取現有 VHD](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. 現在已選取 VHD，請選取 [確定] 以連結現有的虛擬硬碟。
11. 幾秒鐘後，VM 的 [磁碟] 窗格將會顯示現有的虛擬硬碟已連線為資料磁碟︰

    ![現有虛擬硬碟已連結為資料磁碟](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>修正原始虛擬硬碟的問題
裝載現有的虛擬硬碟之後，您現在可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>卸載並中斷連結原始虛擬硬碟
一旦解決任何錯誤，請從疑難排解 VM 卸載並中斷連結現有的虛擬硬碟。 直到釋放將虛擬硬碟連結至疑難排解 VM 的租用之後，您才能將虛擬硬碟和任何 VM 一起使用。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 在左側功能表中，選取 [虛擬機器 (傳統)]。
3. 找出復原 VM。 選取 [磁碟]，以滑鼠右鍵按一下磁碟，然後選取 [中斷連結]。

## <a name="create-a-vm-from-the-original-hard-disk"></a>從原始硬碟建立 VM

若要從原始虛擬硬碟建立 VM，請使用 [Azure 入口網站](https://portal.azure.com)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站左上方，選取 [新增] > [計算] > [虛擬機器] > [從資源庫]。
3. 在 [選擇映像] 區段中，選取 [我的磁碟]，然後選取原始虛擬硬碟。 檢查位置資訊。 這是必須部署 VM 的區域。 選取下一步按鈕。
4. 在 [虛擬機器設定] 區段中，輸入 VM 名稱並選取 VM 的大小。
