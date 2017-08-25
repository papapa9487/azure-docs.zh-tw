當您不再需要某個連結至虛擬機器 (VM) 的資料磁碟時，可以輕鬆將它中斷連結。 當您從 VM 中斷連結磁碟時，磁碟不會從儲存體中移除。 如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連結至相同 VM 或其他 VM。  

> [!NOTE]
> Azure 中的 VM 使用不同類型的磁碟 - 作業系統磁碟、本機暫存磁碟，以及選擇性的資料磁碟。 如需詳細資訊，請參閱[有關虛擬機器的磁碟和 VHD](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 您無法將作業系統磁碟中斷連結，除非您同時刪除 VM。

## <a name="find-the-disk"></a>尋找磁碟
您必須先找出 LUN 編號 (要中斷連結之磁碟的識別碼)，才能將磁碟與 VM 中斷連結。 若要這樣做，請遵循下列步驟：

1. 開啟 Azure CLI 並 [連接至您的 Azure 訂用帳戶](../articles/xplat-cli-connect.md)。 確定處於 Azure 服務管理模式中 (`azure config mode asm`)。
2. 找出哪些磁碟已連結至您的 VM。 下列範例會列出名為 `myVM` 的 VM 的磁碟：

    ```azurecli
    azure vm disk list myVM
    ```

    輸出類似於下列範例：

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. 請記下您要卸離之磁碟的 LUN 或 **邏輯單元編號** 。

## <a name="remove-operating-system-references-to-the-disk"></a>移除磁碟的作業系統參考
在從 Linux 客體中斷連結磁碟之前，您應該先確定磁碟上的所有磁碟分割都不在使用中。 請確定作業系統沒有在重新開機之後嘗試重新掛接它們。 下列步驟可復原您在[附加](../articles/virtual-machines/linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)磁碟時可能建立的組態。

1. 使用 `lsscsi` 命令來找出磁碟識別碼。 您可透過 `yum install lsscsi` (Red Hat 式散發) 或 `apt-get install lsscsi`(Debian 式散發) 來安裝 `lsscsi`。 您可以使用 LUN 編號找到要尋找的磁碟識別碼。 在每個資料列的 Tuple 中，最後一個數字即為 LUN。 在下列範例從 `lsscsi`，LUN 0 對應至 /dev/sdc

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. 使用 `fdisk -l <disk>` 探索與要卸離之磁碟相關聯的分割區。 下列範例顯示 `/dev/sdc`的輸出：

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. 取消掛接每個列出的磁碟分割區。 下列範例會卸載 `/dev/sdc1`：

    ```bash
    sudo umount /dev/sdc1
    ```

4. 使用 `blkid` 命令來探索所有分割區的 UUID。 輸出類似於下列範例：

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. 移除 **/etc/fstab** 檔案 (與裝置路徑或要卸離之磁碟的所有分割區的 UUID 相關聯) 中的項目。  此範例中的項目可能是︰

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    或
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>卸離磁碟
找出磁碟的 LUN 編號並移除作業系統參考之後，您可以開始卸離磁碟︰

1. 執行命令 `azure vm disk detach
   <virtual-machine-name> <LUN>`，從虛擬機器卸離選取的磁碟。 下列範例會從名為 `myVM` 的 VM 卸離 LUN `0`：
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. 您可以再次執行 `azure vm disk list`，檢查是否已卸離磁碟。 下列範例會檢查名為 `myVM` 的 VM：
   
    ```azurecli
    azure vm disk list myVM
    ```

    輸出會類似下列範例，其中顯示資料磁碟再也無法附加︰

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

卸離的磁碟仍留在儲存體中，但不再連接至虛擬機器。

