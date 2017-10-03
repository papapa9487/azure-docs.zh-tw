---
title: "Azure 儲存體中的資料複寫 | Microsoft Docs"
description: "系統會 複製Microsoft Azure 儲存體帳戶中的資料，以維持持久性和高可用性。 複寫選項包括本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS)。"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 09c6f525bb608d95c60ba7907aae4b4e70923544
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="azure-storage-replication"></a>Azure 儲存體複寫
Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性。 複寫會將資料複製於相同資料中心內，或複製到第二個資料中心，依您所選的複寫選項而定。 複寫會保護您的資料，並在暫時性硬體故障時保留您應用程式的正常運作時間。 如果您的資料複寫至第二個資料中心，就能避免資料在主要位置發生嚴重失敗。

複寫可確保您的儲存體帳戶符合[儲存體的服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) 即使遇到失敗時也一樣。 請參閱 SLA 以取得 Azure 儲存體持續性和可用性保證的相關資訊。

建立儲存體帳戶時，您可以選取下列其中一個複寫選項：

* [本地備援儲存體 (LRS)](#locally-redundant-storage)
* [區域備援儲存體 (ZRS)](#zone-redundant-storage)
* [異地備援儲存體 (GRS)](#geo-redundant-storage)
* [讀取權限異地備援儲存體 (RA-GRS)](#read-access-geo-redundant-storage)

在建立儲存體帳戶時，讀取權限異地備援儲存體 (RA-GRS) 是預設選項。

下表提供 LRS、ZRS、GRS 和 RA-GRS 之間差異的快速概觀，而後續各節將詳細說明每種複寫類型。

| 複寫策略 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 可跨多個資料中心複寫資料。 |否 |是 |是 |是 |
| 可從次要位置及主要位置讀取資料。 |否 |否 |否 |是 |
| 可在不同的節點上維護的資料副本數量。 |3 |3 |6 |6 |

如需不同備援選項的定價資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/) 。

> [!NOTE]
> 進階儲存體僅支援本地備援儲存體 (LRS)。 如需進階儲存體的相關資訊，請參閱 [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../storage-premium-storage.md)。
>

## <a name="locally-redundant-storage"></a>本地備援儲存體
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>區域備援儲存體
區域備援儲存體 (ZRS) 跨一或兩個區域內的資料中心以非同步的方式複寫資料，此外，儲存三個類似 LRS 的複本，因此，它提供比 LRS 更高的持久性。 ZRS 中儲存的資料有耐久性，即使主要資料中心無法使用或無法復原。
計劃使用 ZRS 的客戶應該要注意︰

* ZRS 只適用於一般用途儲存體帳戶中的區塊 Blob，且僅支援儲存體服務版本 2014-02-14 及更新版本。
* 由於非同步方式複寫會涉及到延遲，在發生本機災難的情況下，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要區域的變更。
* 除非 Microsoft 起始容錯移轉至次要區域，否則複本可能無法使用。
* ZRS 帳戶稍後無法轉換成 LRS 或 GRS。 同樣地，現有 LRS 或 GRS 帳戶無法轉換為 ZRS 帳戶。
* ZRS 帳戶並沒有度量或記錄功能。

## <a name="geo-redundant-storage"></a>異地備援儲存體
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>讀取權限異地備援儲存體
除了 GRS 所提供的跨兩個區域複寫之外，讀取權限異地備援儲存體 (RA-GRS) 會透過提供次要位置資料的唯讀權限，最大化儲存體帳戶的可用性。

啟用次要區域資料的唯讀權限時，除了儲存體帳戶的主要端點以外，您的資料還可以在次要端點中使用。 次要端點與主要端點類似，但會在帳戶名稱中附加尾碼 `–secondary` 。 例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點會有相同的儲存體帳戶存取金鑰。

考量：

* 您的應用程式必須管理在使用 RA-GRS 時，與哪一個端點進行互動。
* 由於非同步方式複寫會涉及到延遲，在發生地區性災難的情況下，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要地區的變更。
* 如果 Microsoft 可起始容錯移轉到次要區域，您將會在完成容錯移轉之後具有該資料的讀取和寫入權限。 如需詳細資訊，請參閱[災害復原指南](../storage-disaster-recovery-guidance.md)。 
* RA-GRS 適用於高可用性目的。 如需擴充性的指引，請檢閱[效能檢查清單](../storage-performance-checklist.md)。

## <a name="frequently-asked-questions"></a>常見問題集

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1.我要如何變更儲存體帳戶的異地複寫類型？

   您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure Powershell](storage-powershell-guide-full.md) 或以程式設計方式使用我們的多個儲存體用戶端程式庫之一，將儲存體帳戶的異地複寫類型變更為 LRS、GRS 或 RA-GRS。 請注意，ZRS 帳戶無法轉換成 LRS 或 GRS。 同樣地，現有 LRS 或 GRS 帳戶無法轉換為 ZRS 帳戶。

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2.若我變更儲存體帳戶的複寫類型，是否會有任何停機時間？

   否，不會有任何停機時間。

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>3.若我變更儲存體帳戶的複寫類型，是否會產生任何額外費用？

   是。 如果您將儲存體帳戶從 LRS 變更為 GRS (或 RA-GRS)，對於涉及將現有資料從主要位置複製到次要位置的輸出，會產生額外費用。 複製初始資料之後，將資料從主要位置異地複寫到次要位置將不會產生額外的輸出費用。 有關頻寬費用的詳細資料，可在 [Azure 儲存體定價頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)找到。 如果您要從 GRS 變更為 LRS，將沒有額外費用，但會從次要位置刪除資料。

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4.RA-GRS 可以給我什麼協助？
   
   GRS 儲存體可將資料從主要區域複寫到與主要區域距離數百英哩的次要區域。 在此案例中，即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。 RA-GRS 儲存體除了包含此功能之外，還能讓您從次要位置讀取資料。 如需有關如何利用此功能的一些概念，請參閱[使用 RA-GRS 儲存體設計高可用性應用程式](../storage-designing-ha-apps-with-ragrs.md)。 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5.是否有辦法可以讓我知道將資料從主要區域複寫到次要區域所需花費的時間？
   
   如果您使用 RA-GRS 儲存體，可查閱儲存體帳戶的上次同步處理時間。 上次同步處理時間是 GMT 日期/時間值；在上次同步處理時間之前完成的所有主要位置寫入都已成功寫入到次要位置，這表示現在已經可以從次要位置讀取這些資料。 在上次同步處理時間之後完成的主要位置寫入可能已可讀取，也可能無法讀取。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)，或以程式設計方式使用 REST API 或其中一個儲存體用戶端程式庫查詢這個值。 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6.如果主要區域發生中斷，我要如何改為次要區域？
   
   如需詳細資訊，請參閱文章[如果 Azure 儲存體發生中斷應該怎麼辦](../storage-disaster-recovery-guidance.md)。

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7.RPO 和使用 GRS 的 RTO 為何？
   
   復原點目標 (RPO)：在 GRS 和 RA-GRS 中，儲存體服務會以非同步方式將資料從主要位置異地複寫到次要位置。 如果發生重大區域性災害而必須執行容錯移轉，則可能會遺失尚未進行異地複寫的最近差異變更。 可能資料遺失的分鐘數稱為 RPO (這是指資料可以復原的時間點)。 我們的 RPO 通常低於 15 分鐘，但目前並沒有關於異地複寫時間長短的 SLA。

   復原時間目標 (RTO)：這是測量我們完成容錯移轉並使儲存體帳戶恢復上線 (如果必須進行容錯移轉) 的標準。 執行容錯移轉的時間包括下列各項：
    * 我們用來調查及判斷是否可在主要位置復原資料，或者我們是否需要進行容錯移轉所花費的時間。
    * 變更主要 DNS 項目以指向次要位置來容錯移轉帳戶。

   我們對於保存您的資料採取非常認真負責的態度，所以如果有任何機會能夠復原資料，我們都會延後執行容錯移轉，而專注於復原主要位置的資料。 未來，我們打算提供 API 來讓您從帳戶層級觸發容錯移轉，使您更夠自行控制 RTO，但此目標尚未實現。
   
## <a name="next-steps"></a>後續步驟
* [使用 RA-GRS 儲存體設計高可用性應用程式](../storage-designing-ha-apps-with-ragrs.md)
* [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)
* [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)
* [Azure 儲存體的延展性與效能目標](storage-scalability-targets.md)
* [Microsoft Azure 儲存體備援選項和讀取權限異地備援儲存體 ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP 文件：Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


