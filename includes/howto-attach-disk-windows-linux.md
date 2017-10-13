


## <a name="attach-an-empty-disk"></a>連接空的磁碟
連結空磁碟是一個新增資料磁碟的簡易方式，因為 Azure 會為您建立 .vhd 檔案並將它儲存在儲存體帳戶中。

1. 按一下 [虛擬機器 (傳統)]，然後選取適當的 VM。

2. 在 [設定] 功能表中，按一下 [磁碟]。

   ![連接新的空磁碟](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. 按一下命令列上的 [連結新項目]。  
    [連結新磁碟] 對話方塊隨即出現。

    ![附加新的磁碟](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    填寫下列資訊︰
    - 在 [檔案名稱] 中，接受預設名稱，或是為 .vhd 檔案輸入另一個名稱。 資料磁碟會使用自動產生的名稱，即使您為 .vhd 檔案輸入另一個名稱亦然。
    - 選取資料磁碟的 [類型]。 所有虛擬機器皆支援標準磁碟。 許多虛擬機器也支援進階磁碟。
    - 選取資料磁碟的 [大小 (GB)]。
    - 在 [主機快取] 中選擇 [無] 或 [唯讀]。
    - 按一下 [確定] 以完成。

4. 建立並連結資料磁碟之後，它就會列在 VM 的 [磁碟] 區段中。

   ![成功連結新的空白資料磁碟](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> 新增資料磁碟之後，您將必須登入 VM 並將磁碟初始化，才能使用該磁碟。

## <a name="how-to-attach-an-existing-disk"></a>做法：連接現有磁碟
連接現有磁碟要求您在儲存體帳戶中需要有可用的 .vhd。 請使用 [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet，將 .vhd 檔案上傳至儲存體帳戶。 在建立並上傳 .vhd 檔案之後，您就可將它連結至 VM。

1. 按一下 [虛擬機器 (傳統)]，然後選取適當的虛擬機器。

2. 在 [設定] 功能表中，按一下 [磁碟]。

3. 按一下命令列上的 [連結現有項目]。

    ![連接資料磁碟](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. 按一下 [位置]。 隨即會顯示可用的儲存體帳戶。 接下來，從所列帳戶中選取適當的儲存體帳戶。

    ![提供磁碟儲存體帳戶](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. **儲存體帳戶**會保有一或多個包含磁碟機 (VHD) 的容器。 從所列容器中選取適當的容器。

    ![提供 virtual-machines-windows 的容器](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. [VHD] 面板會列出容器中保有的磁碟機。 對其中一個磁碟按一下，然後按一下選取。

    ![提供 virtual-machines-windows 的磁碟映像](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. [連結現有磁碟] 面板會再次顯示，其中所列出的位置會包含要新增至虛擬機器的儲存體帳戶、容器和選取的硬碟 (VHD)。

  將 [主機快取] 設定為 [無] 或 [唯讀]，然後按一下 [確定]。

    ![成功連接資料磁碟](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
