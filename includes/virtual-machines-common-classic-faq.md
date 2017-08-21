


本文可解決以傳統部署模型建立之 Azure 虛擬機器的一些使用者常見問題。

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>我是否可以將在傳統部署模型中建立的 VM 移轉到新的 Resource Manager 模型？
是。 如需有關如何移轉的指示，請參閱：

* [使用 Azure PowerShell 從傳統移轉至 Azure Resource Manager](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md)。
* [使用 Azure CLI 從傳統移轉至 Azure Resource Manager](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)。

## <a name="what-can-i-run-on-an-azure-vm"></a>我可以在 Azure VM 上執行什麼？
所有的訂閱者都可以在 Azure 虛擬機器上執行伺服器軟體。 您可以執行最新版本的 Windows Server，以及各種 Linux 散發套件。 如需支援的詳細資料，請參閱：

• 針對 Windows VM -- [Azure 虛擬機器的 Microsoft 伺服器軟體支援](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• 針對 Linux VM -- [Azure 背書之散發套件上的 Linux](http://go.microsoft.com/fwlink/p/?LinkId=393551)

針對 Windows 用戶端映像，特定版本的 Windows 7 和 Windows 8.1 可供 MSDN Azure 權益訂閱者和 MSDN 開發與測試隨用隨付訂閱者 (針對開發與測試工作) 使用。 如需詳細資訊 (包括指示和限制)，請參閱 [MSDN 訂閱者的 Windows 用戶端映像](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。

## <a name="why-are-affinity-groups-being-deprecated"></a>為何同質群組遭到取代？
同質群組是舊的概念，可依地理位置將 Azure 內的客戶雲端服務部署和儲存體帳戶進行分組。 一開始提供它們的目的在於改善早期 Azure 網路設計中的 VM 到 VM 網路效能。 它們也支援初版的虛擬網路 (VNet)，而這只限於區域中的一小組硬體。

區域內的目前 Azure 網路設計不再需要同質群組。 虛擬網路也在區域範圍內，因此使用虛擬網路時不再需要同質群組。 由於這些改善，我們不再建議客戶使用同質群組，因為它們在某些案例中可能有所限制。 使用同質群組不一定會建立 VM 與特定硬體的關聯，後者會限制您對可用 VM 大小的選擇。 如果與同質群組相關聯的特定硬體接近容量極限，它也可能在嘗試新增 VM 時造成容量相關錯誤。

在 Azure Resource Manager 部署模型和 Azure 入口網站中，同質群組功能已經遭到取代。 對於傳統 Azure 入口網站，我們將不再支援建立同質群組，以及建立釘選到同質群組的儲存體資源。 您不需要修改使用同質群組的現有雲端服務。 不過，除非 Azure 支援專家建議使用同質群組，否則您不應該使用新雲端服務的同質群組。

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>我可以使用多少的儲存體搭配虛擬機器？
每個資料磁碟最多可達 1 TB。 可使用的資料磁碟數量取決於虛擬機器的大小。 如需詳細資訊，請參閱 [虛擬機器的大小](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

Azure 儲存體帳戶提供作業系統磁碟和任何資料磁碟的儲存空間。 每個磁碟是以分頁 Blob 方式儲存的 .vhd 檔案。 如需定價的詳細資料，請參閱 [儲存體定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkId=396819)。

## <a name="which-virtual-hard-disk-types-can-i-use"></a>可以使用哪些虛擬硬碟類型？
Azure 僅支援固定的 VHD 格式虛擬硬碟。 如果您想要在 Azure 中使用 VHDX，則需要先使用「Hyper-V 管理員」或 [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) Cmdlet 進行轉換。 接著，請使用 [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet (以 [服務管理] 模式) 將 VHD 上傳到 Azure 中的儲存體帳戶，您便可以在虛擬機器上使用。

* 如需適用於 Linux 的指示，請參閱[建立及上傳含有 Linux 作業系統的虛擬硬碟](../articles/virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。
* 如需適用於 Windows 的指示，請參閱[建立及上傳 Windows Server VHD 至 Azure](../articles/virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>這些虛擬機器與 Hyper-V 虛擬機器是一樣的嗎？
在許多方面來說，它們與「第一代」Hyper-V VM 類似，但並非完全相同。 這兩種類型都提供虛擬的硬體，以及可相容 VHD 格式虛擬硬碟。 這表示您可以在 Hyper-V 和 Azure 之間移動它們。 有時讓 Hyper-V 使用者感到驚訝的三個主要差異為：

* Azure 不會提供虛擬機器的主控台存取權。 VM 要完成開機之後才可供存取。
* 多數[大小](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的 Azure VM 僅有 1 個虛擬網路介面卡，這表示它們也可能會有 1 個外部 IP 位址。 (A8 和 A9 大小會使用第二個網路介面卡，讓應用程式在有限案例中的執行個體之間進行通訊。)
* Azure VM 不支援第 2 代 Hyper-V VM 功能。 如需這些功能的詳細資料，請參閱[Hyper-V 的虛擬機器規格](http://technet.microsoft.com/library/dn592184.aspx)以及[第 2 代虛擬機器概觀](https://technet.microsoft.com/library/dn282285.aspx)。

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>這些虛擬機器可以使用我現有的內部部署網路基礎結構嗎？
對於傳統部署模型中建立的虛擬機器，您可以使用 Azure 虛擬網路來擴充現有的基礎結構。 這個方法像是在設立一個分公司。 您可以在 Azure 中佈建及管理虛擬私人網路 (VPN)，並使用內部部署 IT 基礎結構安全地連接這些網路。 如需詳細資訊，請參閱 [虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

當建立虛擬機器時，將需要指定您想要虛擬機器隸屬的網路。 您不能將現有的虛擬機器加入虛擬網路中。 然而，您可以透過從現有的虛擬機器中斷虛擬硬碟 (VHD) 連結，然後使用該虛擬硬碟建立含有您想要之網路組態的新虛擬機器以解決這個問題。

## <a name="how-can-i-access--my-virtual-machine"></a>如何存取我的虛擬機器？
您需要建立遠端連線以登入虛擬機器，針對 Windows VM，請使用遠端桌面連線；針對 Linux VM，請使用安全殼層 (SSH)。 如需相關指示，請參閱：

* [如何登入執行 Windows Server 的虛擬機器](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 最多支援 2 個並行連線，除非伺服器設定為遠端桌面服務工作階段主機。  
* [如何登入執行 Linux 的虛擬機器](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 根據預設，SSH 允許最多 10 個並行連線。 您可以編輯組態檔以增加這個數字。

如果您遇到遠端桌面或 SSH 的相關問題，請安裝並使用 [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 擴充功能來協助修正問題。

對於 Windows VM，其他選項包括：

* 在 Azure 傳統入口網站中，找出 VM，然後從命令列按一下 [重設遠端存取]  。
* 檢閱[針對以 Windows 為基礎的 Azure 虛擬機器的遠端桌面連線進行疑難排解](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 使用 Windows PowerShell 遠端功能以連線到 VM，或建立其他資源的額外端點來連線至 VM。 如需詳細資訊，請參閱[如何設定虛擬機器的端點](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

如果您熟悉 Hyper-V，您可能正在尋找類似 VMConnect 的工具。 Azure 沒有提供類似的工具，因為並不支援主控台存取虛擬機器。

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>我可以使用 D: 磁碟機 (Windows) 或 /dev/sdb1 (Linux) 來儲存資料嗎？
您不應使用 D: 磁碟機 (Windows) 或 /dev/sdb1 (Linux)。 它們僅提供暫存空間，因此您會有遺失資料且無法復原的風險。 當虛擬機器移動到不同的主機時就可能發生這種情況。 可能要移動虛擬機器的一些原因是調整虛擬機器的大小、更新主機，或主機上的硬體故障等等。

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>如何變更暫存磁碟的磁碟機代號？
在 Windows 虛擬機器上，您可以透過移動分頁檔並重新指派磁碟機代號來變更磁碟機代號，但您必須確定以特定的順序執行這些步驟。 如需相關指示，請參閱 [變更 Windows 暫存磁碟的磁碟機代號](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>如何升級客體作業系統？
升級這個詞彙通常是指移動至較新版的作業系統，但仍在相同硬體上。 對於 Azure VM，Linux 和 Windows 移動至較新版的處理程序有所不同：

* 針對 Linux VM，使用套件管理工具和適合散發的程序。
* 針對 Windows 虛擬機器，請使用 Windows Server 移轉工具來移轉伺服器。 請勿嘗試升級位於 Azure 的客體 OS。 不支援此動作是因為有失去虛擬機器存取權的風險。 如果在升級期間發生問題，您可能會無法啟動遠端桌面工作階段，而且將無法疑難排解問題。

如需移轉 Windows Server 的工具和程序的一般詳細資料，請參閱 [移轉角色與功能至 Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940)。

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>虛擬機器上的預設使用者名稱和密碼是什麼？
Azure 提供的映像沒有預先設定的使用者名稱和密碼。 當您使用這些映像的其中一個來建立虛擬機器時，您將需要提供用來登入虛擬機器的使用者名稱和密碼。

如果您已經忘記使用者名稱或密碼，而且已經安裝 VM 代理程式，您可以安裝並使用 [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 擴充功能來修正問題。

其他詳細資料：

* 針對 Linux 映像，如果您使用 Azure 傳統入口網站，則預設使用者名稱會是 ‘azureuser’，但是您可以使用 [從資源庫] 代替使用 [快速建立] 做為建立虛擬機器的方式，來變更預設使用者名稱。 使用 [從資源庫] 也可讓您決定是否要使用密碼、SSH 金鑰，或同時使用兩者來登入。 此使用者帳戶是非特殊權限使用者，但有 'sudo' 存取權限可以執行特殊權限命令。 'root' 帳戶已停用。
* 針對 Windows 映像，當您建立 VM 時需要提供使用者名稱和密碼。 帳戶會加入至系統管理員群組。

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Azure 可以在我的虛擬機器上執行防毒軟體嗎？
Azure 提供數個防毒軟體解決方案的選項，但管理則掌握在您手中。 例如，您可能需要個別訂閱反惡意程式碼軟體，且需要決定何時執行掃描和安裝更新。 當您建立 Windows 虛擬機器或在稍後的時間點，可以使用適用於 Microsoft Antimalware、Symantec Endpoint Protection，或 TrendMicro Deep Security 代理程式的 VM 延伸模組，新增防毒軟體支援。 Symantec 和 TrendMicro 延伸模組提供您使用免費的限時試用訂用帳戶或現有的企業訂用帳戶。 Microsoft Antimalware 則是免費的。 如需詳細資訊，請參閱：

* [如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [如何在 Azure VM 上安裝和設定 Trend Micro Deep Security as a Service](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [在 Azure 虛擬機器上部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>備份和復原有哪些選擇？
Azure 備份在特定地區以預覽版提供。 如需詳細資訊，請參閱 [備份 Azure 虛擬機器](../articles/backup/backup-azure-vms.md)。 來自認證合作夥伴的其他解決方案也可供使用。 若要了解目前可用的項目，請搜尋 Azure Marketplace。

其他選項是使用 Blob 儲存體的快照集功能。 若要這樣做，您需要在任何依賴 Blob 快照集的作業前關閉 VM。 這樣會儲存擱置的資料寫入，並讓檔案系統保持一致的狀態。

## <a name="how-does-azure-charge-for-my-vm"></a>Azure 如何對我的 VM 收費？
Azure 可依據 VM 的大小和作業系統，以每小時價格方式收費。 針對不足一小時的部分，Azure 只會收取使用分鐘數的費用。 如果您以包含特定預先安裝軟體的 VM 映像建立 VM，可能會收取額外的每小時軟體費用。 Azure 針對 VM 作業系統和資料磁碟的儲存體個別收費。 暫存磁碟儲存體是免費的。

當 VM 狀態為「執行中」或「已停止」都會向您收費，但當 VM 狀態為「已停止 (已取消配置)」則不會收費。 若要讓 VM 進入「已停止 (已取消配置)」狀態，請執行下列其中一項：

* 從 Azure 傳統入口網站中關閉或刪除 VM。
* 使用 Stop-AzureVM Cmdlet (在 Azure PowerShell 模組中可用)。
* 在服務管理 REST API 中使用關機角色作業，並為 PostShutdownAction 元素指定 StoppedDeallocated。

如需更多詳細資料，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/)。

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Azure 會因為維護重新啟動我的 VM 嗎？
Azure 有時會重新啟動您的 VM，這是 Azure 資料中心中定期、計劃性維護更新的一部份。

當 Azure 偵測到嚴重的硬體問題可能會影響您的 VM 時，會發生非計劃性維護事件。 對於非計劃性事件，Azure 會自動地移轉 VM 至狀況良好的主機並重新啟動 VM。

針對任何獨立的 VM (表示 VM 並非可用性集合的一部份)，Azure 在計劃性維護之前，至少每一個星期會使用電子郵件通知訂用帳戶的服務管理員，因為 VM 可能會在更新期間重新啟動。 在 VM上執行的應用程式可能會遭遇停機時間。

當因為計畫性維護而發生重新啟動時，您也可以使用 Azure 傳統入口網站或 Azure PowerShell 來檢視重新啟動記錄。 如需詳細資訊，請參閱 [檢視 VM 重新啟動記錄檔](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/)。

若要提供備援，請在相同的可用性集合中放入兩個以上同樣設定的 VM。 這有助於確保在計劃性或非計劃性的維護期間，至少有一個 VM 仍可使用。 Azure 保證此組態的 VM 可用性特定層級。 如需詳細資訊，請參閱[管理虛擬機器的可用性](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="additional-resources"></a>其他資源
[關於 Azure 虛擬機器](../articles/virtual-machines/virtual-machines-linux-about.md)

[使用 Azure CLI 來建立和管理 Linux VM](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[使用 Azure PowerShell 建立和管理 Windows VM](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

