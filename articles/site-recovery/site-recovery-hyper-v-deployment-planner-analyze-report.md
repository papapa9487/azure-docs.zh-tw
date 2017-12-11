---
title: "Hyper-V 到 Azure 的 Azure Site Recovery 部署規劃工具 | Microsoft Docs"
description: "本文說說明 Hyper-V 到 Azure 的 Azure Site Recovery 部署規劃工具案例所產生報告的分析。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 714c2074f643d2b168c054c5af467b550f57daba
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>分析 Azure Site Recovery 部署規劃工具報告
產生的 Microsoft Excel 報告包含下列工作表：

## <a name="on-premises-summary"></a>內部部署摘要
內部部署摘要工作表會提供已剖析 Hyper-V 環境的摘要工作表![內部部署摘要](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

[開始日期] 和 [結束日期]：考慮用於產生報告之剖析資料的開始和結束日期。 根據預設，開始日期是剖析開始的日期，而結束日期是剖析停止的日期。 如果使用這些參數產生報告，這可以是 'StartDate' 和 'EndDate' 值。

**剖析總天數**：為其產生報告之開始和結束日期之間的剖析總天數。

**相同的虛擬機器數目**：相容的 VM 總數，系統會計算其所需的網路頻寬、所需的儲存體帳戶、Microsoft Azure 核心的數目。

**所有相容虛擬機器的磁碟總數**：所有相容 VM 中的磁碟總數。

**每部相容虛擬機器的平均磁碟數**：在所有相容 VM 中計算的平均磁碟數。

**平均磁碟大小 (GB)**：在所有相容 VM 中計算的平均磁碟大小。

**所需的 RPO (分鐘)**：預設復原點目標，或在產生報告時針對 'DesiredRPO' 參數所傳遞的值，用來估計所需的頻寬。

**所需的頻寬 (Mbps)**：在產生報告時針對 ‘Bandwidth’ 參數所傳遞的值，用來估計可用的 RPO。

**每日觀察到的典型資料變換 (GB)**：在所有剖析天數中觀察到的平均資料變換。

## <a name="recommendations"></a>建議  
根據選取的所需 RPO，Hyper-V 到 Azure 報告的建議工作表包含下列詳細資料：

![Hyper-V 到 Azure 報告的建議](media/site-recovery-hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>分析資料
![分析資料](media/site-recovery-hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**剖析的資料期間**：執行剖析的期間。 根據預設，此工具會在計算中包含所有已分析的資料。 如果您已在報告產生中使用 StartDate 和 EndDate 選項，它會針對特定期間產生報告。 

**已剖析的 Hyper-V 伺服器數目**：已產生其 VM 報告的 Hyper-V 伺服器數目。 按一下此數字以檢視 Hyper-V 伺服器的名稱。 這會開啟「內部部署儲存體需求」工作表，其中列出所有的伺服器及其儲存體需求。    

**所需的 RPO**：您部署的復原點目標。 預設會計算 RPO 值 15、30 和 60 分鐘所需的網路頻寬。 根據選取項目，在工作表上更新受影響的值。 如果您在產生報告時使用了 DesiredRPOinMin 參數，該值會顯示於所需 RPO 結果中。

### <a name="profiling-overview"></a>剖析概觀
![剖析概觀](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**剖析的虛擬機器總數**：可取得其剖析資料的 VM 總數。 如果 VMListFile 包含任何未剖析的 VM 名稱，則這些 VM 不會納入報告產生考量並從剖析的 VM 總計數中排除。

**相容的虛擬機器**：可以使用 Azure Site Recovery 在 Azure 中保護的 VM 數目。 這是相容的 VM 總數，系統會計算其所需的網路頻寬、儲存體帳戶數目、Azure 核心數目。 在 [相容 VM] 區段中可取得每部相容 VM 的詳細資料。

**不相容的虛擬機器**：可使用 Azure Site recovery 保護之不相容的已剖析 VM 數目。 「不相容的 VM」一節會提到不相容的原因。 如果 VMListFile 包含任何未剖析 VM 的名稱，則這些 VM 會從不相容的 VM 計數中排除。 這些 VM 會在「不相容的 VM」區段結尾列為「找不到資料」。

**所需 RPO**：以分鐘為單位的所需復原點目標。 此報告會針對三個 RPO 值而產生：15 (預設值)、30 和 60 分鐘。 報告中的頻寬建議會隨著您在工作表右上方提供的 [所需的 RPO] 下拉式清單中選取的項目變更。 如果您已使用 -DesiredRPO 參數以自訂值產生報告，這個自訂值會顯示為 [所需的 RPO] 下拉式清單中的預設值。

### <a name="required-network-bandwidth-mbps"></a>所需的網路頻寬 (Mbps)
![所需的網路頻寬](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**符合當時的 100% RPO︰**所要配置的建議頻寬 (以 Mbps 為單位)，以符合當時所需的 100% RPO。 此頻寬量必須專用於您所有相容 VM 的穩定狀態差異複寫，以避免任何 RPO 違規。

**符合當時的 90% RPO**︰如果因為寬頻價格或任何其他原因，您無法設定符合當時所需 100% RPO 所需的頻寬，您可以選擇設定較低的頻寬，以符合當時所需的 90% RPO。 若要了解設定此低頻寬的含意，報告提供了可預期之 RPO 違規次數和持續時間的假設分析。

**達成的輸送量**：來自您已對儲存體帳戶所在 Azure 區域執行 GetThroughput 命令之伺服器的輸送量。 此輸送量指出當您使用 Azure Site Recovery 保護相容 VM 時可達成的預估等級，前提是 Hyper-V 伺服器儲存體和網路特性仍與您執行此工具的伺服器相同。

對於所有企業 Azure Site Recovery 部署，建議使用 [ExpressRoute](https://aka.ms/expressroute)。

### <a name="required-storage-accounts"></a>所需的儲存體帳戶
下列圖表顯示保護所有相容 VM 所需的儲存體帳戶 (標準和進階) 總數。 若要了解每個 VM 所要使用的儲存體帳戶，請參閱「VM 儲存體放置」一節。

![所需的儲存體帳戶](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>所需的 Azure 核心數目
此結果是在所有相容 VM 的容錯移轉或測試容錯移轉之前所要設定的核心總數。 如果訂用帳戶中可用的核心太少，Azure Site Recovery 便無法在測試容錯移轉或容錯移轉時建立 VM。
![所需的 Azure 核心數目](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>其外內部部署儲存體需求

Hyper-V 伺服器上成功初始複寫和差異複寫以確保 VM 複寫所需的總可用儲存體，將不會導致生產應用程式發生任何非預期的停機。 在[內部部署儲存體需求](#on-premises-storage-requirement)中可取得每個磁碟區需求的詳細資料。 

若要了解複寫為何需要可用空間，請參閱[內部部署儲存體需求](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication)一節。

![內部部署儲存體需求和複製頻率](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>複製頻率上限
必須為複寫設定建議的複製頻率上限，以達成所需的 RPO。 預設值為 5 分鐘。 您可以設定 30 秒的複製頻率，以達到更佳的 RPO。

### <a name="what-if-analysis"></a>假設分析
![假設分析](media/site-recovery-hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) 此分析概述當您設定較低的頻寬，讓所需的 RPO 只符合當時的 90% 時，在分析期間可發生多少次違規。 任何指定的日期都可能發生一或多個 RPO 違規。 此圖會顯示當天的尖峰 RPO。 根據這項分析，您可以判斷指定的較低頻寬是否可接受所有天數的 RPO 違規次數和每日的尖峰 RPO 目標達成。 如果可接受，您可以配置較低的頻寬進行複寫，否則依照建議配置較高的頻寬，以符合當時所需的 100% RPO。 

### <a name="recommendation-for-successful-initial-replication"></a>成功初始複寫的建議
在這一節中，我們會建議要保護 VM 的批次數目，以及成功完成初始複寫 (IR) 所需的頻寬下限。 

![IR 批次處理](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

您必須依照指定的批次順序保護 VM。 每個批次有特定的 VM 清單。 第 1 批 VM 必須在第 2 批之前受到保護。 第 2 批 VM 必須在第 3 批之前受到保護，依此類推。 完成第 1 批 VM 的初始複寫之後，您可以啟用第 2 批 VM 的複寫。 同樣地，完成第 2 批 VM 的初始複寫之後，您可以啟用第 3 批 VM 的複寫，依此類推。 如果未遵照批次順序，稍後保護的 VM 可能沒有足夠的頻寬可用於初始複寫。 結果是，VM 永遠無法完成初始複寫，或一些受保護的 VM 可能會進入重新同步模式。 所選 RPO 工作表的 IR 批次處理有關於每個批次應納入哪些 VM 的詳細資訊。

這裡的圖表會依指定的批次順序顯示各批次的初始複寫和差異複寫的頻寬分配。 當您保護第一批 VM 時，初始複寫可以使用完整頻寬。 第一批的初始複寫結束後，差異複寫將會需要部分的頻寬。 剩餘頻寬可供第二批 VM 的初始複寫使用。 第 2 批資料列會顯示第 1 批 VM 所需的差異複寫頻寬，以及第 2 批 VM 可用於初始複寫的頻寬。 同樣地，第 3 批資料列會顯示先前批次所需的差異複寫頻寬，以及第 3 批可用於初始複寫的頻寬，依此類推。  所有批次的初始複寫結束後，最後一列會顯示所有受保護 VM 的差異複寫所需的頻寬。 

**我為什麼需要初始複寫批次處理？**
初始複寫的完成時間會以 VM 磁碟大小、已用的磁碟空間和可用的網路輸送量為基礎。 在所選 RPO 工作表的 IR 批次處理中可取得詳細資料。

### <a name="cost-estimation"></a>成本估計
此圖形會就您所選的目標區域和您針對報告產生所指定的貨幣，顯示災害復原 (DR) 至 Azure 之估計總成本的摘要檢視。
![成本估計摘要](media/site-recovery-hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png) 當您使用 Azure Site Recovery 在 Azure 中保護所有相容 VM 時，此摘要可協助您了解您需要支付的儲存體、計算、網路和授權成本。 成本是針對相容的 VM，而不是針對所有已分析的 VM 計算。  
 
您可以檢視每月或每年的成本。 深入了解[支援的目標區域](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)和[支援的貨幣](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies)。

**依元件的成本** DR 總成本分成四個元件：計算、儲存體、網路和 Azure Site Recovery 授權成本。 此成本是根據在複寫期間和在 DR 演練時產生的耗用量來計算，其中涵蓋計算、儲存體 (進階和標準)、在內部部署網站與 Azure 之間設定的 ExpressRoute/VPN，以及 Azure Site Recovery 授權。

**依狀態的成本** 災害復原 (DR) 的總成本是根據兩個不同的狀態來分類 - 複寫和 DR 演練。 

**複寫成本**：在複寫期間產生的成本。 其中涵蓋儲存體、網路和 Azure Site Recovery 授權的成本。 

**DR 演練成本**：在測試容錯移轉期間產生的成本。 Azure Site Recovery 會在測試容錯移轉期間啟動 VM。 DR 演練成本涵蓋執行 VM 的計算和儲存成本。 

**每月/年的 Azure 儲存成本** 它會顯示將會針對複寫及 DR 演練的進階和標準儲存體產生的總儲存成本。
您可以在[成本估計](site-recovery-hyper-v-deployment-planner-cost-estimation.md)工作表中檢視每個 VM 的詳細成本分析。

### <a name="growth-factor-and-percentile-values-used"></a>使用的成長因子和百分位數值
位於工作表底部的這個區段會顯示用於剖析 VM 之所有效能計數器的百分位數值 (預設值為第 95 個百分位數)，以及所有計算中使用的成長因子 (預設值為 30%)。

![使用的成長因子和百分位數值](media/site-recovery-hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>以可用頻寬做為輸入的建議
![分析概觀與頻寬輸入](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

您可能會遇到一下情況：您知道無法針對 Azure Site Recovery 複寫設定超過 x Mbps 的頻寬。 此工具可讓您輸入可用的頻寬 (在報告產生期間使用 -Bandwidth 參數)，並取得可達成的 RPO (以分鐘為單位)。 使用此可達成的 RPO 值，您可以決定是否需要佈建額外的頻寬，或可接受具有此 RPO 的災害復原解決方案。

![可達成的 RPO](media/site-recovery-hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>VM 儲存體放置建議 

![VM 儲存體放置](media/site-recovery-hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**磁碟儲存體類型**：標準或進階儲存體帳戶，用來複寫 [要放置的 VM] 資料行中提到的所有對應 VM。

**建議的前置詞**：建議的三個字元前置詞，可用來命名儲存體帳戶。 您可以使用自己的前置詞，但工具的建議會遵循[儲存體帳戶的磁碟分割命名慣例](https://aka.ms/storage-performance-checklist)。

**建議的帳戶名稱**：在您包含建議前置詞後的儲存體帳戶名稱。 以您的自訂輸入取代括弧內的名稱 (< 和 >)。

**記錄儲存體帳戶**︰所有複寫記錄都會儲存在標準儲存體帳戶中。 對於複寫至進階儲存體帳戶的 VM，請針對記錄儲存體設定額外的標準儲存體帳戶。 多個進階複寫儲存體帳戶可以使用單一標準記錄儲存體帳戶。 複寫到標準儲存體帳戶的 VM 會對記錄使用相同的儲存體帳戶。

**建議的記錄帳戶名稱**：在您包含建議前置詞後的儲存體記錄帳戶名稱。 以您的自訂輸入取代括弧內的名稱 (< 和 >)。

**放置摘要**：提供在複寫和測試容錯移轉或容錯移轉時，儲存體帳戶上載入的 VM 總數摘要。 其中包含對應到儲存體帳戶的 VM 總數、置於此儲存體帳戶中所有 VM 的讀/寫 IOPS 總計、寫入 (複寫) IOPS 總計、所有磁碟的設定大小總計，以及磁碟總數。

**要放置的虛擬機器**：應置於指定之儲存體帳戶的所有 VM 清單，以提供最佳效能和使用情況。

## <a name="compatible-vms"></a>相容的 VM
Azure Site Recovery 部署規劃工具所產生的 Microsoft Excel 報告會在「相容的 VM」工作表中，提供所有相容的 VM 詳細資料。

![相容的 VM](media/site-recovery-hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM 名稱**：產生報告時，使用於 VMListFile 的 VM 名稱。 此資料行也會列出連結至 VM 的磁碟 (VHD)。 名稱包含此工具在分析期間探索到 VM 時其所在位置的 Hyper-V 主機名稱。

**VM 相容性**：值為 [是] 和 [是\*]。 **[是\*]** 適用於 VM 適合於 [Azure 進階儲存體](https://aka.ms/premium-storage-workload)的情況。 相較於對應到磁碟的大小，這裡剖析的高變換或 IOPS 磁碟適合較大的進階磁碟大小。 儲存體帳戶會根據磁碟大小，決定磁碟所要對應至的進階儲存體大小類型。 
* <128 GB 為 P10。
* 128 GB 至 256 GB 為 P15。
* 256 GB 至 512 GB 為 P20。
* 512 GB 至 1024 GB 為 P30。
* 1025 GB 至 2048 GB 為 P40。
* 2049 GB 至 4095 GB 為 P50。

比方說，如果磁碟的工作負載特性讓它放在 P20 或 P30 類別中，但大小會將它對應到較低的進階儲存體磁碟類型，此工具會將該 VM 標示為 [是]\*。 此工具也建議您變更來源磁碟大小，以符合建議的進階儲存體磁碟類型，或變更容錯移轉後的目標磁碟類型。

**儲存體類型**：標準或進階。

**建議的前置詞**：三個字元的儲存體帳戶前置詞。

**儲存體帳戶**︰使用建議儲存體帳戶前置詞的名稱。

**尖峰 R/W IOPS (含成長因子)**：磁碟上的尖峰工作負載讀/寫 IOPS (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的讀/寫 IOPS 總計不一定是 VM 個別磁碟的讀/寫 IOPS 總和，因為 VM 的尖峰讀/寫 IOPS 是其個別磁碟在分析期間內每一分鐘之讀/寫 IOPS 總和的尖峰。

**尖峰資料變換 (MBps) (含成長因子)**：磁碟上的尖峰變換率 (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的資料變換總計不一定是 VM 個別磁碟的資料變換總和，因為 VM 的尖峰資料變換是其個別磁碟在剖析期間每一分鐘之變換總和的尖峰。

**Azure VM 大小**：此內部部署 VM 理想的對應 Azure 雲端服務虛擬機器大小。 對應是以內部部署 VM 的記憶體、磁碟/核心/NIC 數目和讀/寫 IOPS 為基礎。 建議一律是符合所有內部部署 VM 特性的最低 Azure VM 大小。

**磁碟數目**：VM 上的虛擬機器磁碟 (VHD) 總數。

**磁碟大小 (GB)**：VM 之所有磁碟的總大小。 此工具也會顯示 VM 中個別磁碟的磁碟大小。

**核心**：VM 上的 CPU 核心數目。

**記憶體 (MB)**：VM 上的 RAM。

**NIC**：VM 上的 NIC 數目。

**開機類型**︰VM 的開機類型。 可以是 BIOS 或 EFI。

## <a name="incompatible-vms"></a>不相容的 VM
Azure Site Recovery 部署規劃工具所產生的 Microsoft Excel 報告會在「不相容的 VM」工作表中，提供所有不相容的 VM 詳細資料。

![不相容的 VM](media/site-recovery-hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM 名稱**：產生報告時，使用於 VMListFile 的 VM 名稱。 此資料行也會列出連結至 VM 的磁碟 (VHD)。 名稱包含此工具在分析期間探索到 VM 時其所在位置的 Hyper-V 主機名稱。

**VM 相容性**：指出為何指定的 VM 不適合與 Azure Site Recovery 搭配使用。 相關原因會針對 VM 的每個不相容磁碟進行說明，且根據發佈的[儲存體限制](https://aka.ms/azure-storage-scalbility-performance)，原因可能是下列其中一項：

* 磁碟大小 > 4095 GB。 Azure 儲存體目前不支援大於 4095 GB 的資料磁碟大小。

* 第 1 代 (BIOS 開機類型) VM 的 OS 磁碟 > 2047 GB。  Azure Site Recovery 對於第 1 代 VM 不支援大於 2047 GB 的 OS 磁碟大小。

* 第 2 代 (EFI 開機類型) VM 的 OS 磁碟 > 300 GB。 Azure Site Recovery 對於第 2 代 VM 不支援大於 300 GB 的 OS 磁碟大小。

* VM 名稱包含下列任一字元 “” [] `。  此工具無法取得名稱中含有上述任何字元之 VM 的剖析資料。 

* VHD 由兩個或更多 VM 共用。 Azure 不支援具有共用 VHD 的 VM。

* 不支援具有虛擬光纖通道的 VM。 Azure Site Recovery 不支援具有虛擬光纖通道的 VM。

* Hyper-V 叢集不包含複寫代理程式。 如果未針對叢集設定 Hyper-V 複本代理程式，則 Azure Site Recovery 不支援 Hyper-V 叢集中的 VM。

* VM 不具高可用性。 Azure Site Recovery 不支援 VHD 儲存在本機磁碟 (而不是儲存在叢集磁碟) 的 Hyper-V 叢集節點 VM。 

* VM 大小總計 (複寫 + TFO) 超過支援的進階儲存體帳戶大小限制 (35 TB)。 當 VM 中單一磁碟的效能特性超過標準儲存體支援的最大 Azure 或 Azure Site Recovery 限制，通常會發生此不相容情況。 這類情況會將 VM 推送到進階儲存體區域中。 不過，進階儲存體帳戶支援的大小上限為 35 TB，而單一的受保護 VM 無法跨多個儲存體帳戶受到保護。 也請注意，在受保護的 VM 上執行測試容錯移轉時，如果已針對測試容錯移轉設定非受控磁碟，它會在正在進行複寫的相同儲存體帳戶中執行。 在此執行個體中，需要與複寫相同的額外儲存體數量。 這確保進行複寫並以平行方式繼續進行測試容錯移轉。 若已針對測試容錯移轉設定受控磁碟，則不需要為容錯移轉 VM 準備任何額外的空間。

* 來源 IOPS 超過支援的儲存體 IOPS 限制 (每個磁碟 7500)。

* 來源 IOPS 超過支援的儲存體 IOPS 限制 (每個 VM 80,000)。

* 平均資料變換超出磁碟平均 IO 大小支援的 Azure Site Recovery 資料變換限制 10 MBps。

* 磁碟的平均有效寫入 IOPS 超過支援的 Azure Site Recovery IOPS 限制 840。

* 計算的快照集儲存體超過支援的快照集儲存體限制 10 TB。

**尖峰 R/W IOPS (含成長因子)**：磁碟上的尖峰工作負載 IOPS (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的讀/寫 IOPS 總計不一定是 VM 個別磁碟的讀/寫 IOPS 總和，因為 VM 的尖峰讀/寫 IOPS 是其個別磁碟在剖析期間內每一分鐘之讀/寫 IOPS 總和的尖峰。

**尖峰資料變換 (MBps) (含成長因子)**：磁碟上的尖峰變換率 (預設值為第 95 個百分位數)，包括未來的成長因子 (預設值為 30%)。 請注意，VM 的資料變換總計不一定是 VM 個別磁碟的資料變換總和，因為 VM 的尖峰資料變換是其個別磁碟在剖析期間每一分鐘之變換總和的尖峰。

**磁碟數目**：VM 上的 VHD 總數。

**磁碟大小 (GB)**：VM 之所有磁碟的總設定大小。 此工具也會顯示 VM 中個別磁碟的磁碟大小。

**核心**：VM 上的 CPU 核心數目。

**記憶體 (MB)**：VM 上的 RAM 數量。

**NIC**：VM 上的 NIC 數目。

**開機類型**︰VM 的開機類型。 可以是 BIOS 或 EFI。

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供 Azure Site Recovery 限制。 上述限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式 I/O 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。 為了獲得最佳結果，即使在部署規劃之後，仍一律建議發出測試容錯移轉來執行廣泛的應用程式測試，以了解應用程式真正的效能情況。
 
**複寫儲存體目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB | 2 MBps | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB  | 2 MBps | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MBps |  每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MBps | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MBps | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |10 MBps | 每個磁碟 842 GB

這些限制是採用 30% I/O 重疊時的平均數字。 Azure Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。 先前數字採用大約五分鐘的典型積壓。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

## <a name="on-premises-storage-requirement"></a>內部部署儲存體需求

此工作表提供 Hyper-V 伺服器 (VHD 所在位置) 的每個磁碟區要成功初始複寫和差異複寫的總可用儲存空間需求。 在啟用複寫之前，在磁碟區上新增所需的儲存空間，以便確保複寫不會對生產應用程式造成任何非預期的停機。 

Azure Site Recovery 部署規劃工具會根據用於複寫的 VHD 大小和網路頻寬，識別最佳的儲存空間需求。

![內部部署儲存體需求](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>在用於複寫的 Hyper-V 伺服器上為什麼需要可用的空間？
* 當您啟用 VM 的複寫時，Azure Site Recovery 會製作 VM 的每個 VHD 的快照集，以便進行初始複寫 (IR)。 正在進行初始複寫時，應用程式會將新的變更寫入磁碟中。 Azure Site Recovery 會在記錄檔中追蹤這些差異變更，而記錄檔需要額外的儲存空間。  直到初始複寫完成，記錄檔才會儲存在本機。 如果記錄檔和快照集 (AVHDX) 沒有足夠的可用空間，複寫就會進入重新同步模式，且永遠無法完成複寫。 在最壞的情況下，您會需要 VHD 大小的 100% 額外可用空間，以便進行初始複寫。
* 初始複寫結束後，就會開始差異複寫。 Azure Site Recovery 會在記錄檔中追蹤這些差異變更，而記錄檔會儲存在 VM 的 VHD 所在的磁碟區上。 這些記錄檔會以設定的複製頻率複寫至 Azure。 根據可用的網路頻寬，記錄檔會需要一些時間才會複寫至 Azure。 如果沒有足夠的空間可用於儲存記錄檔，複寫將會暫停，且 VM 的複寫狀態會進入所需的重新同步。
* 如果網路頻寬不足以將記錄檔推送至 Azure，則記錄檔會堆放在磁碟區上。 在最糟的情況下，當記錄檔大小增加至 VHD 大小的 50% 時，VM 的複寫將會進入所需的重新同步。 在最壞的情況下，您會需要 VHD 大小的 50% 額外可用空間，以便進行差異複寫。

**Hyper-V 主機**：已剖析的 Hyper-V 伺服器清單。 如果伺服器是屬於 Hyper-V 叢集，則所有叢集節點都會群組在一起。

**V磁碟區 (VHD 路徑)**：存在 VHD/VHDX 之 Hyper-V 主機的每個磁碟區。 

**可用空間 (GB)**：磁碟區上的可用空間。

**磁碟區上所需的總儲存空間 (GB)**：磁碟區上要成功初始複寫和差異複寫所需的可用儲存空間總計。 

**為了成功複寫而要在磁碟區上佈建的額外儲存體總量**：建議為了成功初始複寫和差異複寫，而必須在磁碟區上佈建的額外空間總量。

## <a name="initial-replication-batching"></a>初始複寫批次處理 

### <a name="why-do-i-need-initial-replication-ir-batching"></a>我為什麼需要初始複寫 (IR) 批次處理？
如果所有 VM 同時受到保護，則可用的儲存體需求會高出許多，而如果沒有足夠的儲存體可用，VM 的複寫就會進入重新同步模式。 此外，網路頻寬需求會高出許多，以便一起成功完成所有 VM 的初始複寫。 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>所選 RPO 的初始複寫批次處理
此工作表會提供每個初始複寫 (IR) 批次的詳細資料檢視。 針對每個 RPO，建立個別的 IR 批次處理工作表。 

遵循每個磁碟區的內部部署儲存體需求建議之後，您需要複寫的主要資訊是可以平行方式保護的 VM 清單。 這些 VM 會組成一個批次。 可以有多個批次。 您必須依照指定的批次順序保護 VM。 先保護第 1 批 VM，初始複寫完成後，再保護第 2 批 VM，依此類推。 您可從以下工作表取得批次和對應 VM 清單。 

![IR 批次處理詳細資料1](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)
![IR 批次處理詳細資料2](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>每個批次會提供以下資訊： 
**Hyper-V 主機**：要保護之 VM 的 Hyper-V 主機。
虛擬機器：要保護的 VM。 

**註解**：如果需要對 VM 的特定磁碟區採取任何動作，此處會提供註解。  比方說，如果磁碟區上沒有足夠的可用空間，註解會指示新增額外的儲存體來保護此 VM。

**磁碟區 (VHD 路徑)**：VM 的 VHD 所在的磁碟區名稱。 磁碟區上的可用空間 (GB)：VM 磁碟區上的可用磁碟空間。 在計算磁碟區上的可用空間時，這會考量 VHD 位於相同磁碟區的前幾批 VM 用於差異複寫的磁碟空間。  

例如，VM1、VM2 和 VM3 位於磁碟區 E:\VHDpath 上。 複寫之前，磁碟區上的可用空間為 500 GB。 VM1 屬於第 1 批、 VM2 屬於第 2 批，而 VM3 屬於第 3 批。  若為 VM1，可用空間為 500 GB。 若為 VM2，可用空間會是 500 – VM1 差異複寫所需的磁碟空間。  假設 VM1 需要 300 GB 的空間進行差異複寫，則 VM2 的可用空間會是 500 GB – 300 GB = 200 GB。  同樣地，VM2 需要 300 GB 進行差異複寫。 VM3 的可用空間會是 200 GB - 300 GB = -100 GB。

**磁碟區上為了進行初始複寫所需的儲存體 (GB)**：VM 磁碟區上為了進行初始複寫所需的可用儲存空間。

**磁碟區上為了進行差異複寫所需的儲存體 (GB)**：VM 磁碟區上為了進行差異複寫所需的可用儲存空間。

**為了避免複寫失敗所需的額外儲存體 (以不足額為基礎) (GB)**：VM 磁碟區上所需的額外的儲存空間。  這是初始複寫和差異複寫的儲存空間需求上限 - 磁碟區上的可用空間。

**初始複寫所需的最小頻寬 (Mbps)**：VM 的初始複寫所需的最小頻寬。

**差異複寫所需的最小頻寬 (Mbps)**：VM 的差異複寫所需的最小頻寬。

### <a name="network-utilization-details-for-each-batch"></a>每個批次的網路使用量詳細資料 
每個批次資料表都會提供批次的網路使用量摘要。

**批次的可用頻寬**：在考量前一批的差異複寫頻寬後，批次可用的頻寬。

**可用於批次初始複寫的大約頻寬**：可用於 VM 批次初始複寫的頻寬。 

**批次差異複寫所耗用的大約頻寬**：VM 批次差異複寫所需的頻寬。 

**批次的估計初始複寫時間 (HH:MM)**：以小時：分鐘表示的估計初始複寫時間。

## <a name="cost-estimation"></a>成本估計
深入了解[成本估計](site-recovery-hyper-v-deployment-planner-cost-estimation.md)。 

## <a name="next-steps"></a>後續步驟
深入了解[成本估計](site-recovery-hyper-v-deployment-planner-cost-estimation.md)。