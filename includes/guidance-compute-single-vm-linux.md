本文概述一組已經實證的做法，以便在 Azure 上執行 Linux 虛擬機器 (VM)，並注意延展性、可用性、管理性和安全性。 Azure 支援執行各種受歡迎的 Linux 散發套件，包括 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 FreeBSD。 如需詳細資訊，請參閱 [Azure 和 Linux][azure-linux]。

> [!NOTE]
> Azure 有兩個不同的部署模型：[Resource Manager][resource-manager-overview] 和傳統。 本文使用 Microsoft 建議用於新部署的資源管理員。
> 
> 

我們不建議用使用單一 VM 進行關鍵任務工作負載，因為它會建立單一失敗點。 若要擁有較高的可用性，您必須在[可用性設定組][availability-set]中部署多個 VM。 如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。 

## <a name="architecture-diagram"></a>架構圖表
在 Azure 中佈建 VM 牽涉的移動組建比 VM 本身更多。 您需要考量一些計算、網路及儲存體元素。

> 包含此架構圖表的 Visio 文件可在 [Microsoft 下載中心][visio-download]提供下載。 此圖表位於「計算 - 單一 VM」頁面。
> 
> 

![[0]][0]

* **資源群組。** [*資源群組*][resource-manager-overview]是保存相關資源的容器。 建立資源群組以保存此 VM 的資源。
* **VM**。 您可以從已發佈的映像清單，或從您上傳至 Azure Blob 儲存體的虛擬硬碟 (VHD) 檔案佈建 VM。
* **作業系統磁碟。** 作業系統磁碟是儲存在 [Azure 儲存體][azure-storage]中的 VHD。 這表示即使主機電腦關閉，它仍持續存在。 作業系統磁碟為 `/dev/sda1`。
* **暫存磁碟。** VM 是使用暫存磁碟來建立。 此磁碟會儲存在主機電腦的實體磁碟機上。 不會  儲存在 Azure 儲存體中，而且可能在重新開機期間和其他 VM 生命週期事件中刪除。 僅將此磁碟使用於暫存資料，例如分頁檔或交換檔。 暫存磁碟為 `/dev/sdb1` 且掛接於 `/mnt/resource` 或 `/mnt`。
* **資料磁碟。** [資料磁碟][data-disk]是用於應用程式資料的持續性 VHD。 資料磁碟會儲存在 Azure 儲存體中，例如作業系統磁碟。
* **虛擬網路 (VNet) 和子網路。** 在 Azure 中的每個 VM 都會部署到 VNet 中，而虛擬網路會進一步分成數個子網路。
* **公用 IP 位址。** 必須要有公用 IP 位址才能與 VM 進行通訊 &mdash; 例如透過 SSH。
* **網路介面 (NIC)**。 NIC 可讓 VM 與虛擬網路通訊。
* **網路安全性群組 (NSG)**。 [NSG][nsg] 是用來允許/拒絕對子網路的網路流量。 您可以將 NSG 與獨立的 NIC 或子網路關聯。 如果您將它與子網路關聯，該 NSG 規則會套用至子網路中的所有 VM。
* **診斷。** 診斷記錄對於管理和針對 VM 進行疑難排解十分重要。

## <a name="recommendations"></a>建議

下列建議適用於大部分的案例。 除非您有特定的需求會覆寫它們，否則請遵循下列建議。 

### <a name="vm-recommendations"></a>VM 建議

Azure 提供許多不同的虛擬機器大小，但是我們建議使用 DS 和 GS 系列，因為這些機器大小支援[進階儲存體][premium-storage]。 選取其中一個機器大小，除非您有高效能運算等特殊工作負載。 如需詳細資訊，請參閱[虛擬機器的大小][virtual-machine-sizes]。

如果您將現有的工作負載移至 Azure，則從最符合您內部部署伺服器的 VM 大小開始。 然後根據 CPU、記憶體和每秒的磁碟輸入/輸出作業 (IOPS) 測量您的實際工作負載效能，並視需要調整大小。 如果您的 VM 需要多個 NIC，請注意 NIC 的最大數目是 [VM 大小][vm-size-tables]的函式。

當您佈建 VM 和其他資源時，必須指定一個區域。 一般而言，選擇最接近您的內部使用者或客戶的區域。 不過，並非所有 VM 大小都可使用於所有區域。 如需詳細資訊，請參閱[依區域提供的服務][services-by-region]。 若要列出指定區域可用的 VM 大小，請執行下列 Azure 命令列介面 (CLI) 命令：

```
azure vm sizes --location <location>
```

如需選擇已發佈 VM 映像的相關資訊，請參閱[透過 Azure CLI 選取 Linux VM 映像][select-vm-image]。

### <a name="disk-and-storage-recommendations"></a>磁碟和儲存體建議

為了達到最佳的磁碟 I/O 效能，我們建議使用[進階儲存體][premium-storage]，這會將資料儲存在固態硬碟 (SSD)。 成本是依佈建的磁碟大小而定。 IOPS 和輸送量 (亦即，資料傳輸速率) 也取決於磁碟大小，因此當您佈建磁碟時，請考慮以下三個因素 (容量、IOPS 和輸送量)。 

針對每個 VM 建立個別的 Azure 儲存體帳戶來存放虛擬硬碟 (VHD)，以避免達到儲存體帳戶的 IOPS 限制。 

新增一或多個資料磁碟。 當您建立 VHD 時，它仍未格式化。 登入 VM 來格式化磁碟。 在 Linux 殼層中，資料磁碟會顯示為`/dev/sdc``/dev/sdd` 等等。 您可以執行 `lsblk` 以列出區塊裝置，包括磁碟。 若要使用資料磁碟，請建立磁碟分割和檔案系統，並掛接該磁碟。 例如：

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

如果您有大量的資料磁碟，請注意儲存體帳戶的總 I/O 限制。 如需詳細資訊，請參閱[虛擬機器磁碟限制][vm-disk-limits]。

當您新增資料磁碟時，會指派邏輯單元編號 (LUN) 識別碼給磁碟。 或者，您可以指定 LUN 識別碼 &mdash; 例如，如果您要更換磁碟，而且想要保留相同的 LUN 識別碼，或者您有會查看特定 LUN 識別碼的應用程式。 不過，請記住，每個磁碟的 LUN 識別碼不能重複。

您可能會想變更 I/O 排程器以最佳化 SSD 的效能，因為具有進階儲存體帳戶的 VM 磁碟為 SSD。 一般建議是使用適用於 SSD 的 NOOP 排程器，但您應該使用 [iostat] 之類的工具，來監視特定工作負載的磁碟 I/O 效能。

為了達到最佳效能，請建立個別的儲存體帳戶來保存診斷記錄。 標準本地備援儲存體 (LRS) 帳戶已足以保存診斷記錄。

### <a name="network-recommendations"></a>網路建議

此公用 IP 位址可以是動態或靜態。 預設值為動態。

* 如果您需要一個不會變更的固定 IP 位址 &mdash;(例如，如果您需要在 DNS 中建立一個 A 記錄，或需要將 IP 位址加入安全清單)，請保留一個[靜態 IP 位址][static-ip]。
* 您也可以建立 IP 位址的完整網域名稱 (FQDN)。 然後您可以在 DNS 中註冊指向該 FQDN 的 [CNAME 記錄][cname-record]。 如需詳細資訊，請參閱[在 Azure 入口網站中建立完整網域名稱][fqdn]。

所有 NSG 都包含一組[預設規則][nsg-default-rules]，包括一個封鎖所有網際網路輸入流量的規則。 預設的規則不能刪除，但其他規則可以覆寫它們。 若要啟用網際網路流量，請建立允許輸入流量輸入特定連接埠的規則 &mdash; 例如，允許連接埠 80 用於 HTTP。  

若要啟用 SSH，請新增一個規則，以允許將輸入流量輸入至 TCP 連接埠 22。

## <a name="scalability-considerations"></a>延展性考量

若要相應增加或相應減少，請[變更 VM 大小][vm-resize]。 

若要水平相應放大，請將兩個以上的 VM 置於附載平衡器後方的可用性設定組中。 如需詳細資料，請參閱[在 Azure 上執行多個 VM][multi-vm]。

## <a name="availability-considerations"></a>可用性考量

若要擁有較高的可用性，您必須在可用性設定組中部署多個 VM。 這也會提供較高的[服務等級協定][vm-sla] (SLA)。 

您的 VM 可能會受到[計劃性維護][planned-maintenance]或[非計劃性維護][manage-vm-availability]影響。 您可以使用 [VM 重新啟動記錄檔][reboot-logs]來判斷 VM 重新啟動是否是因為計劃性維護所造成。

VHD 是儲存在 [Azure 儲存體][azure-storage]，而系統會複寫 Azure 儲存體來提供持久性和可用性。

為了防止在正常作業期間意外遺失資料 (例如，因使用者錯誤而造成)，您也應該使用 [Blob 快照][blob-snapshot]或其他工具來實作時間點備份。

## <a name="manageability-considerations"></a>管理性考量

**資源群組。** 將關係密切且共用相同生命週期的資源置於同一個[資源群組][resource-manager-overview]。 資源群組可讓您以群組為單位來部署和監視資源，並根據資源群組列出帳單成本。 您也可以刪除整組資源，這對於測試部署非常有用。 為資源提供有意義的名稱。 這樣能更容易找到特定資源及了解其角色。 請參閱 [Azure 資源的建議命名慣例][naming conventions]。

**SSH**。 在您建立 VM 之前，先產生 2048 位元 RSA 公開-私密金鑰組。 建立 VM 的時候使用公開金鑰檔案。 如需詳細資訊，請參閱[如何在 Azure 上搭配 Linux 與 Mac 使用 SSH][ssh-linux]。

**VM 診斷。** 啟用監視和診斷，包括基本健全狀況度量、診斷基礎結構記錄檔及[開機診斷][boot-diagnostics]。 如果您的 VM 進入無法開機的狀態，開機診斷能協助您診斷開機失敗。 如需詳細資訊，請參閱[啟用監視和診斷][enable-monitoring]。  

下列 CLI 命令會啟用診斷：

```
azure vm enable-diag <resource-group> <vm-name>
```

**停止 VM。** Azure 會區分「已停止」和「已解除配置」狀態。 您需要在 VM 狀態停止時支付費用，而不是在取消配置 VM 時支付。

使用下列 CLI 命令來解除配置 VM：

```
azure vm deallocate <resource-group> <vm-name>
```

在 Azure 入口網站中，[停止] 按鈕會取消配置 VM。 不過，如果您是在登入時透過作業系統進行關閉，則會將 VM 停止但「不會」  取消配置，因此您仍需付費。

**刪除 VM。** 如果您刪除 VM，並不會刪除 VHD。 這表示您可以放心地刪除 VM，而不會遺失任何資料。 不過，您仍需支付儲存體費用。 若要刪除 VHD，請將檔案從 [Blob 儲存體][blob-storage]中刪除。

若要防止意外刪除，請使用[資源鎖定][resource-lock]來鎖定整個資源群組或鎖定個別資源 (例如 VM)。 

## <a name="security-considerations"></a>安全性考量

藉由使用 [OSPatching] VM 擴充功能，來自動執行作業系統更新。 請在您佈建 VM 的時候安裝此擴充。 您可以指定安裝修補程式的頻率，以及安裝後是否要重新開機。

使用[角色型存取控制][rbac] (RBAC) 來控制對您所部署 Azure 資源的存取。 RBAC 可讓您指派授權角色給您 DevOps 小組的成員。 例如，「讀取者」角色能檢視 Azure 資源但不能建立、管理或刪除它們。 某些角色專門用於特定的 Azure 資源類型。 例如，「虛擬機器參與者」角色能重新啟動或解除配置 VM、重設系統管理員密碼、建立 VM 等等。 其他對此參考架構可能有用的[內建 RBAC 角色][rbac-roles]包括 [DevTest Lab 使用者][rbac-devtest]和[網路參與者][rbac-network]。 

使用者可以被指派多個角色，且您可以針對更詳細的權限建立角色。

d> [!NOTE]
> RBAC 不會限制使用者登入 VM 可執行的動作。 這些權限是由客體 OS上的帳戶類型來決定。   
> 
> 

使用[稽核記錄檔][audit-logs]來查看佈建動作和其他 VM 事件。

如果您要加密作業系統和資料磁碟，請考慮使用 [Azure 磁碟加密][disk-encryption]。 

## <a name="solution-deployment"></a>解決方案部署
此參考架構的部署可在 [GitHub][github-folder] 上取得。 它包含 VNet、NSG 及單一 VM。 若要部署架構，請依照下列步驟執行： 

1. 以滑鼠右鍵按一下下方的按鈕，然後選取 [在新索引標籤中開啟連結] 或 [在新視窗開啟連結]。
   [![部署至 Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. 一旦連結已在 Azure 入口網站中開啟，您必須輸入部分設定的值： 
   
   * **資源群組**名稱已在參數檔案中定義，因此請在文字方塊中選取 [新建] 並輸入 `ra-single-vm-rg`。
   * 從 [位置] 下拉式方塊選取區域。
   * 請勿編輯 [範本的根 URI] 或 [參數根 URI] 文字方塊。
。   * 選取 [作業系統類型]* 下拉式方塊中的 [linux]*。
   * 檢閱條款和條件，然後按一下 [我同意上方所述的條款及條件] 核取方塊。
   * 按一下 [購買] 按鈕。
3. 等待部署完成。
4. 參數檔案包含硬式編碼的系統管理員使用者名稱和密碼，並強烈建議您立即變更兩者。 在 Azure 入口網站中按一下名為 `ra-single-vm0 ` 的 VM。 然後，按一下 [支援與疑難排解] 區段中的 [重設密碼]。 選取 [模式] 下拉式清單方塊中的 [重設密碼]，然後選取新的**使用者名稱**和**密碼**。 按一下 [更新] 按鈕，保存新的使用者名稱和密碼。

## <a name="next-steps"></a>後續步驟
如需較高的可用性，請在負載平衡器後面部署兩個以上的 VM。 如需詳細資訊，請參閱[在 Azure 上執行多個 VM][multi-vm]。

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/storage/common/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure 中的單一 Linux VM 架構"

