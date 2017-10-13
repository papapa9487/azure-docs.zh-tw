


可用性設定組可協助您的虛擬機器在停機期間 (例如維護期間) 仍然保持可用狀態。 在可用性設定組中置入二或多個類似設定的虛擬機器，將可針對虛擬機器所執行的應用程式或服務，建立維護其可用性所需的備援。 如需其運作方式的詳細資訊，請參閱[管理虛擬機器的可用性][Manage the availability of virtual machines]。

同時使用可用性設定組和負載平衡端點，是協助確保您的應用程式一直可用並有效率執行的最佳做法。 如需負載平衡端點的詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡][Load balancing for Azure infrastructure services]。

您可以使用下列兩個選項的其中之一，將傳統虛擬機器加入可用性設定組：

* [選項 1︰同時建立虛擬機器和可用性設定組][Option 1: Create a virtual machine and an availability set at the same time]。 然後，將新的虛擬機器加入您在建立這些虛擬機器時的設定組。
* [選項 2︰將現有的虛擬機器新增至可用性設定組][Option 2: Add an existing virtual machine to an availability set]。

> [!NOTE]
> 在傳統模型中，您要放入相同可用性設定組的虛擬機器必須隸屬於相同的雲端服務。
> 
> 

## <a id="createset"> </a>選項 1︰同時建立虛擬機器和可用性設定組
您可以使用 Azure 入口網站或 Azure PowerShell 命令來執行此作業。

使用 Azure 入口網站：

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com)，請先登入。
2. 在 中樞 功能表上按一下 +新增，然後按一下虛擬機器。
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. 選取您想要使用的 Marketplace 虛擬機器映像。 您可以選擇建立 Linux 或 Windows 虛擬機器。
4. 確認所選虛擬機器的部署模型設為 傳統，然後按一下建立
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. 輸入虛擬機器名稱、使用者名稱和密碼 (Windows 機器) 或 SSH 公開金鑰 (Linux 機器)。 
6. 選擇 VM 大小，然後按一下選取  以繼續。
7. 選擇 [選擇性組態] > [可用性設定組]，並選取您想要加入虛擬機器的可用性設定組。
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. 檢閱組態設定。 完成之後，請按一下 [建立] 。
9. 當 Azure 建立虛擬機器時，您可以在 [中樞] 功能表的 [虛擬機器]  下追蹤進度。

若要使用 Azure PowerShell 命令建立 Azure 虛擬機器，並將它新增至新的或現有的可用性設定組，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>選項 2︰將現有的虛擬機器新增至可用性設定組
在 Azure 入口網站中，您可以將現有的傳統虛擬機器新增至現有的可用性設定組，或為其建立一個新的可用性設定組。 (請注意，相同可用性設定組中的虛擬機器必須隸屬於相同的雲端服務。)步驟幾乎完全相同。 有了 Azure PowerShell，您可以將虛擬機器加入現有的可用性設定組。

1. 如果您尚未登入 [Azure 入口網站](https://portal.azure.com)，請先登入。
2. 在 [中樞] 功能表上，按一下 [虛擬機器 (傳統)] 。
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. 從虛擬機器的清單中，選取您想要加入至集合的虛擬機器名稱。
4. 從虛擬機器的 [設定] 中選擇 [可用性設定組]。
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. 選取您想要加入虛擬機器的可用性設定組。 虛擬機器必須和可用性設定組隸屬於相同的雲端服務。
   
    ![替代映像文字](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. 按一下 [儲存] 。

若要使用 Azure PowerShell 命令，請開啟系統管理員層級的 Azure PowerShell 工作階段並執行下列命令。 在預留位置 (例如 &lt;VmCloudServiceName&gt;) 中，以正確的名稱取代括號中的所有內容，包括 < and > 字元。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> 系統可能必須重新啟動虛擬機器，以結束將它加入至可用性集合的作業。
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

