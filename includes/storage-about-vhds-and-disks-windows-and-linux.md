
## <a name="about-vhds"></a>關於 VHD

Azure 中使用的 VHD 是以分頁 Blob 儲存在 Azure 標準或進階儲存體帳戶中的 .vhd 檔案。 如需分頁 Blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/)。 如需進階儲存體的詳細資訊，請參閱[高效能的進階儲存體和 Azure VM](../articles/storage/common/storage-premium-storage.md)。

Azure 支援 VHD 格式的固定磁碟。 固定格式會線性地陳列檔案內部的邏輯磁碟，因此磁碟位移 X 會儲存於 Blob 位移 X。Blob 最後的頁尾將說明 VHD 屬性。 因為大多數的磁碟內部會有大型的未用範圍，因此固定格式通常會浪費空間。 不過，Azure 會以疏鬆格式來儲存 .vhd 檔案，因此您可同時享有固定和動態磁碟的好處。 如需詳細資訊，請參閱[開始使用虛擬硬碟](https://technet.microsoft.com/library/dd979539.aspx)。

Azure 中所有您想要做為來源以建立磁碟或映像的 .vhd 檔案，均為唯讀。 Azure 會在您建立磁碟或映像製作 .vhd 檔案的複本。 取決於您使用 VHD 的方式，這些複本可為唯讀或讀取及寫入。

當您從映像建立虛擬機器時，Azure 會以來源 .vhd 檔案複本為虛擬機器建立磁碟。 若要防止意外刪除，Azure 會在任何用來建立映像、作業系統磁碟或資料磁碟的來源 .vhd 檔案上加上租用。

您必須先刪除磁碟或映像來移除租約，才能刪除來源 .vhd 檔案。 若要刪除虛擬機器做為作業系統磁碟使用的 .vhd 檔案，您只要刪除虛擬機器並刪除所有關聯的磁碟，就可以一次刪除虛擬機器、作業系統磁碟和來源 .vhd 檔案。 不過，刪除做為資料磁碟來源的 .vhd 檔案檔案需要依設定的順序執行幾個步驟。 首先，您需將磁碟與虛擬機器中斷連結，接著刪除磁碟，然後再刪除 .vhd 檔案。

> [!WARNING]
> 如果您刪除儲存體中的來源 .vhd 檔案從或刪除您的儲存體帳戶，Microsoft 便無法為您復原該資料。
> 

## <a name="types-of-disks"></a>磁碟類型 

Azure 磁碟設計成確保可用性達 99.999%。 Azure 磁碟一直提供企業級持久性，其年度失敗率為零，領先業界。

在建立磁碟時，您有兩個可供選擇的儲存體效能層級，分別是標準儲存體和進階儲存體。 此外，磁碟也有兩種類型，即非受控和受控，這兩種類型可以位於任一個效能層級。


### <a name="standard-storage"></a>標準儲存體 

標準儲存體是以 HDD 為後盾，既可提供符合成本效益的儲存體，又可保有效能。 標準儲存體可複寫到一個資料中心的本機位置，或是利用主要和次要資料中心提供異地備援。 如需儲存體複寫的詳細資訊，請參閱 [Azure 儲存體複寫](../articles/storage/common/storage-redundancy.md)。 

如需搭配使用標準儲存體與 VM 磁碟的詳細資訊，請參閱[標準儲存體和磁碟](../articles/storage/common/storage-standard-storage.md)。

### <a name="premium-storage"></a>進階儲存體 

進階儲存體是以 SSD 為後盾，可針對執行時需要大量 I/O 之工作負載的 VM 提供高效能、低延遲的磁碟支援。 進階儲存體可搭配 DS、DSv2、GS、Ls 或 FS 系列的 Azure VM 使用。 如需詳細資訊，請參閱[進階儲存體](../articles/storage/common/storage-premium-storage.md)。

### <a name="unmanaged-disks"></a>非受控磁碟

非受控磁碟是 VM 已在使用的傳統磁碟類型。 利用這些磁碟，您可以建立自己的儲存體帳戶，並在建立磁碟時指定該儲存體帳戶。 您必須確保相同儲存體帳戶中未放入過多磁碟，否則您可能會超出儲存體帳戶的[延展性目標](../articles/storage/common/storage-scalability-targets.md) (例如，20,000 IOPS)，而導致 VM 遭到節流。 使用非受控磁碟時，您必須了解如何充分運用一或多個儲存體帳戶，以獲得最佳的 VM 效能。

### <a name="managed-disks"></a>受控磁碟 

受控磁碟可在背景中為您處理儲存體帳戶的建立/管理作業，確保您不需要擔心儲存體帳戶的延展性限制。 您只需指定磁碟大小和效能層級 (標準/進階)，Azure 就會為您建立和管理磁碟。 即便是新增磁碟或相應增加和減少 VM，您都不必擔心所使用的儲存體。 

您也可以在每個 Azure 區域中使用單一儲存體帳戶管理自訂映像，並使用映像在相同訂用帳戶中建立數百個 VM。 如需受控磁碟的詳細資訊，請參閱[受控磁碟概觀](../articles/virtual-machines/windows/managed-disks-overview.md)。

建議您對新 VM 使用 Azure 受控磁碟，並將先前建立的未受控磁碟轉換成受控磁碟，以充分利用受控磁碟中提供的許多功能。

### <a name="disk-comparison"></a>磁碟比較

下表會比較進階儲存體和標準儲存體的非受控磁碟和受控磁碟，以協助您決定要使用何者。

|    | Azure 進階磁碟 | Azure 標準磁碟 |
|--- | ------------------ | ------------------- |
| 磁碟類型 | 固態硬碟 (SSD) | 硬碟 (HDD)  |
| 概觀  | 以 SSD 為基礎，針對執行時需要大量 I/O 之工作負載的 VM 或裝載任務關鍵性生產環境的 VM，提供高效能、低延遲的磁碟支援 | 以 HDD 為基礎、符合成本效益的磁碟支援，適用於開發/測試 VM 案例 |
| 案例  | 生產環境和重視效能的工作負載 | 開發/測試、非關鍵性 <br>不常存取 |
| 磁碟大小 | P4: 32 GB (僅限受控磁碟)<br>P6: 64 GB (僅限受控磁碟)<br>P10：128 GB<br>P20：512 GB<br>P30：1024 GB<br>P40：2048 GB<br>P50：4095 GB | 非受控磁碟：1 GB – 4 TB (4095 GB) <br><br>受控磁碟：<br> S4：32 GB <br>S6：64 GB <br>S10：128 GB <br>S20：512 GB <br>S30：1024 GB <br>S40：2048 GB<br>S50：4095 GB| 
| 每一磁碟的輸送量上限 | 250 MB/秒 | 60 MB/秒 | 
| 每一磁碟的 IOPS 上限 | 7500 IOPS | 500 IOPS | 

