# <a name="azure-managed-disks-overview"></a>Azure 受控磁碟概觀

Azure 受控磁碟會管理 VM 磁碟相關的[儲存體帳戶](../articles/storage/common/storage-introduction.md)，簡化 Azure IaaS VM 的磁碟管理。 您只需要指定類型 ([進階](../articles/virtual-machines/windows/premium-storage.md)或[標準](../articles/virtual-machines/windows/standard-storage.md))，還有您需要的磁碟大小，Azure 就會替您建立並管理磁碟。

## <a name="benefits-of-managed-disks"></a>受控磁碟的好處

讓我們看看使用受控磁碟能夠獲得哪些優點，請從下列 Channel 9 影片開始：[透過受控磁碟得到更佳的Azure VM 復原效果](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)。
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>簡單且可調整的 VM 部署

受控磁碟會自動為您管理儲存體。 以前，您必須建立儲存體帳戶，以保存 Azure VM 的磁碟 (VHD 檔案)。 相應增加時，您必須確定已建立其他儲存體帳戶，而不會超過任何磁碟的儲存體 IOPS 限制。 由於受控磁碟會處理儲存體，您不再受限於儲存體帳戶限制 (例如 20,000 IOPS / 帳戶)。 您也不再需要將自訂映像 (VHD 檔案) 複製到多個儲存體帳戶。 您可以集中管理它們 (每個 Azure 區域一個儲存體帳戶)，並利用它們在一個訂用帳戶中建立數百個 VM。

受控磁碟可讓您在一個訂用帳戶中建立最多 10,000 個 VM **磁碟**，這可讓您在單一訂用帳戶中建立數千個 **VM**。 這項功能也可讓您使用 Marketplace 映像，在一個 VMSS 中建立最多一千個 VM，進一步增加[虛擬機器擴展集 (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) 的延展性。

### <a name="better-reliability-for-availability-sets"></a>可用性設定組有更高的可靠性

受控磁碟可確保[可用性設定組中的 VM](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 磁碟彼此充分隔離，以避免單一失敗點，為可用性設定組提供更高的可靠性。 它的作法是自動以不同的儲存體縮放單位 (戳記) 來放置磁碟。 如果因為硬體或軟體失敗造成戳記失敗，則只有磁碟在這些戳記上的 VM 執行個體才會失敗。 例如，假設您的應用程式在五個 VM 上執行，且這些 VM 位於可用性設定組中。 這些磁碟的 VM 不會全部以相同的戳記儲存，因此，如果有一個戳記失效，應用程式的其他執行個體會繼續執行。

### <a name="highly-durable-and-available"></a>高耐久性及可用性

Azure 磁碟設計成確保可用性達 99.999%。 得知有三個資料複本，因此有高持久性，這可讓您更安心。 如果一個或甚至兩個複本遇到問題，其餘複本有助於確保資料的持續性，以及遇到失敗時的高容錯力。 此結構讓 Azure 針對 IaaS 磁碟穩定地展現企業級持久性，提供領先界業的年度零失敗率。 

### <a name="granular-access-control"></a>細微的存取控制

您可以使用 [Azure 角色型存取控制 (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) 將受控磁碟的特定權限指派給一個或多個使用者。 受控磁碟公開各種不同的作業，包括讀取、寫入 (建立/更新)、刪除和擷取磁碟的[共用存取簽章 (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)。 您可以授權某人只能存取他份內工作所需的作業。 例如，如果您不想讓某人將受控磁碟複製到儲存體帳戶，您可以選擇不要授權存取該受控磁碟的匯出動作。 同樣地，如果您不想讓某人使用 SAS URI 來複製受控磁碟，您可以選擇不要授與有關受控磁碟的這種權限。

### <a name="azure-backup-service-support"></a>Azure 備份服務支援
搭配受控磁碟使用 Azure 備份服務，以建立具有以時間為基礎的備份、簡易 VM 還原，以及備份保留原則的備份作業。 受控磁碟僅支援本地備援儲存體 (LRS) 做為複寫選項，這表示它在單一區域內會保留三份資料。 針對區域性災害復原，您必須使用 [Azure 備份服務](../articles/backup/backup-introduction-to-azure-backup.md)來備份位於不同區域的 VM 磁碟，並以 GRS 儲存體帳戶作為備份保存庫。 Azure 備份目前支援用於備份的資料磁碟大小最大為 1TB。 如需深入了解，請參閱[針對具有受控磁碟的 VM 使用 Azure 備份服務](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)。

## <a name="pricing-and-billing"></a>價格和計費

使用受控磁碟時，需考量下列計費資訊：
* 儲存體類型

* 磁碟大小

* 交易數

* 輸出資料傳輸

* 受控磁碟快照集 (完整磁碟複製)

讓我們仔細看看這些資訊。

**儲存體類型︰**受控磁碟提供 2 個效能層級︰[進階](../articles/virtual-machines/windows/premium-storage.md) (以 SSD 為基礎) 和[標準](../articles/virtual-machines/windows/standard-storage.md) (以 HDD 為基礎)。 受控磁碟的計費取決於您為磁碟選取的儲存體類型。


**磁碟大小**：受控磁碟的計費取決於磁碟的佈建大小。 Azure 會將佈建大小對應 (無條件進位) 至下表中指定的最接近受控磁碟選項。 每一個受控磁碟對應至其中一個支援的佈建大小，並據此計費。 例如，如果您建立標準受控磁碟，並指定佈建大小 200 GB，則會依據 S20 磁碟類型的價格向您收費。

以下是進階受控磁碟可用的磁碟大小：

| **進階受控<br>磁碟類型** | **P4** | **P6** |**P10** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|----------------|----------------|----------------|  
| 磁碟大小        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


以下是標準受控磁碟可用的磁碟大小：

| **標準受控<br>磁碟類型** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| 磁碟大小        | 32 GB   | 64 GB   | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


**交易數**︰依據您在標準受控磁碟上執行的交易數向您收費。 進階受控磁碟沒有交易成本。

**輸出資料傳輸**： [輸出資料傳輸](https://azure.microsoft.com/pricing/details/data-transfers/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

如需受控磁碟價格的詳細資訊，請參閱[受控磁碟價格](https://azure.microsoft.com/pricing/details/managed-disks)。


## <a name="managed-disk-snapshots"></a>受控磁碟快照集

受控快照集是受控磁碟的完整唯讀複本，預設會儲存為標準受控磁碟。 快照集可讓您在任何時間點備份受控磁碟。 這些快照集可在來源磁碟外獨立存在，還能用來建立新的受控磁碟。 它們是根據使用的大小來計費。 例如，如果建立佈建容量為 64 GB 的受控磁碟快照集，而實際使用資料大小為 10 GB，則只會對已使用的 10 GB 資料大小收取快照集費用。  

受控磁碟目前不支援[增量快照集](../articles/virtual-machines/windows/incremental-snapshots.md)，但未來會支援。

若要深入了解如何建立受控磁碟的快照集，請參閱下列資源︰

* [在 Windows 中建立 VHD 複本並儲存為受控磁碟](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [在 Linux 中建立 VHD 複本並儲存為受控磁碟](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>映像

受控磁碟也支援建立受管理的自訂映像。 您可以從儲存體帳戶中的自訂 VHD 或直接從一般化 (系統預備的) VM 建立映像。 這會在單一映像中擷取與 VM 相關聯的所有受控磁碟，包括 OS 和資料磁碟。 這可讓您使用自訂映像建立數百個 VM，而不需要複製或管理任何儲存體帳戶。

如需建立映像的相關資訊，請參閱下列文章︰
* [在 Azure 中如何擷取一般化 VM 的受管理映像](../articles/virtual-machines/windows/capture-image-resource.md)
* [如何使用 Azure CLI 2.0 來一般化和擷取 Linux 虛擬機器](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>映像與快照集的比較

您常常看到 VM 使用「映像」這個詞，現在也看到「快照集」。 請務必了解它們之間的差異。 受控磁碟可讓您為已解除配置的一般化 VM 建立映像。 此映像包含所有附加至 VM 的磁碟。 您可以使用此映像建立新的 VM，它會包含所有磁碟。

快照集是在建立時的磁碟複本。 它只適用於一個磁碟。 如果您的 VM 只有一個磁碟 (OS)，您可以建立它的快照集或映像，然後從快照集或映像建立 VM。

如果 VM 有五個磁碟，且都已等量分配，情形又如何？ 您可以建立每個磁碟的快照集，但 VM 內對各磁碟的狀態一無所知 – 快照集只知道那個磁碟的情形。 在此情況下，快照集可能需要彼此協調，但目前不支援這樣做。

## <a name="managed-disks-and-encryption"></a>受控磁碟與加密

針對受控磁碟，有兩種加密需要討論。 第一種是「儲存體服務加密」(SSE)，這會由儲存體服務執行。 第二種是「Azure 磁碟加密」，您可以在您 VM 的作業系統和資料磁碟上啟用它。

### <a name="storage-service-encryption-sse"></a>儲存體服務加密 (SSE)

[Azure 儲存體服務加密](../articles/storage/common/storage-service-encryption.md)提供待用加密，並保護資料安全，以符合組織安全性和合規性承諾。 在所有可用受控磁碟的區域中，所有受控磁碟、快照集和映像預設都會啟用 SSE。 從 2017 年 6 月 10 日開始，所有新受控磁碟/快照集/映像和寫入至現有受控磁碟的新資料都會使用 Microsoft 所管理的金鑰進行自動待用加密。  如需詳細資訊，請造訪[受控磁碟常見問題頁面](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)。


### <a name="azure-disk-encryption-ade"></a>Azure 磁碟加密 (ADE)

Azure 磁碟加密可讓您加密由 IaaS 虛擬機器所使用的作業系統和資料磁碟。 這包括受控磁碟。 對於 Windows，磁碟機是使用業界標準的 BitLocker 加密技術來加密。 對於 Linux，磁碟是使用 DM-Crypt 技術來加密。 這會與 Azure 金鑰保存庫整合，可讓您控制和管理磁碟加密金鑰。 如需詳細資訊，請參閱 [Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](../articles/security/azure-security-disk-encryption.md)。

## <a name="next-steps"></a>後續步驟

如需受控磁碟的詳細資訊，請參閱下列文章。

### <a name="get-started-with-managed-disks"></a>開始使用受管理的磁碟

* [使用 Resource Manager 和 PowerShell 建立 VM](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [使用 Azure CLI 2.0 來建立 Linux VM](../articles/virtual-machines/linux/quick-create-cli.md)

* [使用 PowerShell 將受控資料磁碟附加至 Windows VM](../articles/virtual-machines/windows/attach-disk-ps.md)

* [在 Linux VM 中新增受控磁碟](../articles/virtual-machines/linux/add-disk.md)

* [受控磁碟的 PowerShell 範例指令碼](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [在 Azure Resource Manager 範本中使用受控磁碟](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>比較受控磁碟儲存體選項

* [進階儲存體和磁碟](../articles/virtual-machines/windows/premium-storage.md)

* [標準儲存體和磁碟](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>作業指引

* [從 AWS 和其他平台移轉至 Azure 中的受控磁碟](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [將 Azure VM 轉換成 Azure 中的受控磁碟](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
