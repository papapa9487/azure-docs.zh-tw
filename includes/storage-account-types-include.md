儲存體帳戶分為兩種類型：

### <a name="general-purpose-storage-accounts"></a>一般用途的儲存體帳戶
一般用途的儲存體帳戶可讓您使用單一帳戶存取 Azure 儲存體服務，例如資料表、佇列、檔案、Blob 和 Azure 虛擬機器磁碟。 這種類型的儲存體帳戶有兩個效能層︰

* 標準儲存體效能層，可讓您儲存資料表、佇列、檔案、 Blob 和 Azure 虛擬機器磁碟。
* 進階儲存體效能層，目前僅支援 Azure 虛擬機器磁碟。 如需進階儲存體的深入概觀，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../articles/virtual-machines/windows/premium-storage.md) 。

### <a name="blob-storage-accounts"></a>Blob 儲存體帳戶
Blob 儲存體帳戶是特殊的儲存體帳戶，可將非結構化資料儲存為 Azure 儲存體中的 Blob (物件)。 Blob 儲存體帳戶類似於現有的一般用途儲存體帳戶，可共用所有強大的持續性、可用性、延展性以及您現今使用的效能功能，包括區塊 Blob 和附加 Blob 的 100% API 一致性。 對於只需要封鎖或附加 Blob 儲存體的應用程式，我們建議使用 Blob 儲存體帳戶。

> [!NOTE]
> Blob 儲存體帳戶僅支援區塊和附加 Blob，不支援分頁 Blob。
> 
> 

Blob 儲存體帳戶會公開 [存取層]  屬性，該屬性可在帳戶建立期間指定，而後視需要加以修改。 根據您的資料存取模式，可以指定兩種類型的存取層︰

* [經常性存取]  層，這表示將會更頻繁地存取儲存體帳戶中的物件。 這可讓您以較低的存取成本儲存資料。
* [非經常性存取]  層，這表示將不常存取儲存體帳戶中的物件。 這可讓您以較低的資料儲存成本儲存資料。

如果您的資料使用模式有變動，您也可以隨時在這些存取層之間切換。 變更存取層可能會導致額外的費用。 如需詳細資訊，請參閱 [Blob 儲存體帳戶的價格和計費](../articles/storage/common/storage-account-options.md#pricing-and-billing) 。

如需 Blob 儲存體帳戶的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取和非經常性存取層](../articles/storage/blobs/storage-blob-storage-tiers.md)。

在建立儲存體帳戶之前，您必須擁有 Azure 訂閱，訂閱是指可讓您存取各種 Azure 服務的方案。 您可以利用 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/)來開始使用 Azure。 在決定購買訂用計畫之後，您便可以選擇各種 [購買選項](https://azure.microsoft.com/pricing/purchase-options/)。 如果您是 [MSDN 訂閱者](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，您將取得可在 Azure 服務 (包括 Azure 儲存體) 中使用的每月免費額度。 如需批量價格的詳細資訊，請參閱 [Azure 儲存體價格 ](https://azure.microsoft.com/pricing/details/storage/) 。

若要了解如何建立儲存體帳戶，請參閱 [建立儲存體帳戶](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) 以取得詳細資訊。 單一訂用帳戶可讓您建立最多 200 個唯一命名的儲存體帳戶。 如需關於儲存體帳戶限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](../articles/storage/common/storage-scalability-targets.md) 。

