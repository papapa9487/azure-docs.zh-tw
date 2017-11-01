| 資源 | 預設限制 |
| --- | --- |
| 每一訂用帳戶的儲存體帳戶數目 | 200<sup>1</sup> |
| 儲存體帳戶容量上限 | 500 TiB<sup>2</sup> |
| 每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限 | 沒有限制 |
| 每一儲存體帳戶的輸入上限<sup>3</sup> (美國地區) | 如果啟用 GRS/ZRS<sup>4</sup>，則為 10 Gbps，LRS 為 20 Gbps<sup>2</sup> |
| 每一儲存體帳戶的輸出上限<sup>3</sup> (美國地區) | 如果啟用 RA-GRS/GRS/ZRS<sup>4</sup>，則為 20 Gbps，LRS 為 30 Gbps<sup>2</sup> |
| 每一儲存體帳戶的輸入上限<sup>3</sup> (非美國地區) | 如果啟用 GRS/ZRS<sup>4</sup>，則為 5 Gbps，LRS 為 10 Gbps<sup>2</sup> |
| 每一儲存體帳戶的輸出上限<sup>3</sup> (非美國地區) | 如果啟用 RA-GRS/GRS/ZRS<sup>4</sup>，則為 10 Gbps，LRS 為 15 Gbps<sup>2</sup> |

<sup>1</sup>同時包括標準和進階儲存體帳戶。 如果您需要超過 200 個儲存體帳戶，請透過 [Azure 支援](https://azure.microsoft.com/support/faq/)提出要求。 Azure 儲存體團隊將會檢閱您的商務案例，而且可以核准多達 250 個儲存體帳戶。 

<sup>2</sup> 若要取得標準儲存體帳戶，才能成長超越容量、輸入/輸出和要求速率方面的公告限制，請透過 [Azure 支援](https://azure.microsoft.com/support/faq/)提出要求。 Azure 儲存體團隊會檢閱要求，並可能依照個案核准較高的限制。

<sup>3</sup>只受限於帳戶的輸入/輸出限制。 *輸入*是指傳送至某個儲存體帳戶的所有資料 (要求)。 *輸出* 是指從某個儲存體帳戶接收的所有資料 (回應)。  

<sup>4</sup>Azure 儲存體備援選項包括：
* **RA-GRS**：讀取權限異地備援儲存體。 如果已啟用 RA-GRS，次要位置的輸出目標會與主要位置的輸出目標完全相同。
* **GRS**：異地備援儲存體。 
* **ZRS**：區域備援儲存體。 僅適用於區塊 Blob。 
* **LRS**：本地備援儲存體。 