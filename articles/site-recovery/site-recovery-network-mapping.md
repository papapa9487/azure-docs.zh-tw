---
title: "使用 Site Recovery 規劃 Hyper-V VM 複寫的網路對應 | Microsoft Docs"
description: "設定網路對應以用來將 Hyper-V 虛擬機器從內部部署資料中心複寫到 Azure 或次要網站。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: tysonn
ms.assetid: fcaa2f52-489d-4c1c-865f-9e78e000b351
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 91d6d0466789daa662162c60bc3c97ba6115e7eb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="plan-network-mapping-for-hyper-v-vm-replication-with-site-recovery"></a>使用 Site Recovery 規劃 Hyper-V VM 複寫的網路對應



本文可協助您瞭解和規劃在使用 [Azure Site Recovery 服務](site-recovery-overview.md)將 Hyper-V VM 複寫到 Azure 或次要網站時的網路對應。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。

## <a name="network-mapping-for-replication-to-azure"></a>Azure 複寫的網路對應

將 Hyper-V VM (在 VMM 中管理) 複寫到 Azure 時，將使用網路對應。 網路對應會對應來源 VMM 伺服器上的 VM 網路與目標 Azure 網路。 對應具有下列功能：

- **網路連線**—確定複寫的 Azure VM 連線到對應的網路。 即使相同網路上容錯移轉的所有機器在不同的復原計畫中容錯移轉，這些機器也都可以彼此連接。
- **網路閘道**— 如果目標 Azure 網路上已設定網路閘道，則 VM 可以連線到其他內部部署虛擬機器。

請注意：

- 您將來源 VMM VM 網路對應到 Azure 虛擬網路。
- 來源網路中的容錯移轉 Azure VM 將連線到對應的目標虛擬網路。
- 新增到來源 VM 網路的新 VM 會在發生複寫時連線到對應的 Azure 網路。
- 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器會在容錯移轉之後連線到該目標子網路。
- 如果沒有目標子網路具有相符的名稱，虛擬機器會連線到網路中的第一個子網路。


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>次要資料中心複寫的網路對應

將 Hyper-V VM (在 System Center Virtual Machine Manager (VMM) 中管理) 複寫到次要資料中心時，會使用網路對應。 網路對應會對應來源 VMM 伺服器上的 VM 網路與目標 VMM 伺服器上的 VM 網路。 對應具有下列功能：

- **網路連線**—在容錯移轉之後，將 VM 連線至適當的網路。 複本 VM 將會連線至對應到來源網路的目標網路。
- **最佳放置**—以最佳方式將複本 VM 放置在 Hyper-V 主機伺服器上。 複本 VM 會放在可以存取對應的 VM 網路的主機上。
- **無網路對應**— 如果您沒有設定網路對應，複本 VM 將不會在容錯移轉後連線至 VM 網路。

請注意：

- 您可以在兩部 VMM 伺服器的 VM 網路之間設定網路對應，如果兩個站台由相同的伺服器管理，則可以在單一 VMM 伺服器上設定網路對應。
- 正確設定對應並啟用複寫時，位於主要位置的 VM 將會連接至網路，且其位於目標位置的複本將會連接至其對應的網路。
-
- 如果網路已在 VMM 中正確設定，當您在網路對應期間選取目標 VM 網路時，將會顯示使用來源 VM 網路的 VMM 來源雲端，以及用於保護的目標雲端上的可用目標 VM 網路。
- 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在的子網路名稱相同，則複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。



### <a name="example"></a>範例

以下是說明這項機制的範例。 讓我們以具有兩個位置 (紐約和芝加哥) 的組織為例。

**位置** | **VMM 伺服器** | **VM 網路** | **對應至**
---|---|---|---
紐約 | VMM-NewYork| VMNetwork1-NewYork | 對應至 VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | 未對應
芝加哥 | VMM-Chicago| VMNetwork1-Chicago | 對應至 VMNetwork1-NewYork
 | | VMNetwork1-Chicago | 未對應

在此範例中：

- 為連線至 VMNetwork1-NewYork 的任何虛擬機器建立複本虛擬機器時，它將會連線至 VMNetwork1-Chicago。
- 為 VMNetwork2-NewYork 或 VMNetwork2-Chicago 建立複本虛擬機器時，它將不會連線至任何網路。

以下是如何在我們的範例組織中設定 VMM 雲端，以及與雲端相關聯的邏輯網路。

#### <a name="cloud-protection-settings"></a>雲端保護設定

**受保護的雲端** | **保護雲端** | **邏輯網路 (紐約)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>邏輯和 VM 網路設定

**位置** | **邏輯網路** | **相關聯的 VM 網路**
---|---|---
紐約 | LogicalNetwork1-NewYork | VMNetwork1-NewYork
芝加哥 | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>目標網路設定

根據這些設定，當您選取目標 VM 網路時，下表會顯示可用的選擇。

**選取** | **受保護的雲端** | **保護雲端** | **可用的目標網路**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 可用
 | GoldCloud1 | GoldCloud2 | 可用
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 尚未提供
 | GoldCloud1 | GoldCloud2 | 可用


如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在的子網路名稱相同，則複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。


#### <a name="failback-behavior"></a>容錯回復行為

若要了解容錯回復 (反向複寫) 時發生的狀況，讓我們假設 VMNetwork1-NewYork 對應到 VMNetwork1-Chicago，並包含下列設定。


**虛擬機器** | **已連線至 VM 網路**
---|---
VM1 | VMNetwork1-Network
VM2 (VM1 的複本) | VMNetwork1-Chicago

讓我們使用這些設定，檢閱幾個可能的案例中發生的情況。

**案例** | **結果**
---|---
在容錯移轉之後，VM-2 的網路屬性沒有變更。 | VM 1 仍然連線至來源網路。
在容錯移轉並中斷連線之後，VM-2 的網路屬性有所變更。 | VM-1 已中斷連線。
在容錯移轉並連線至 VMNetwork2-Chicago 之後，VM-2 的網路屬性有所變更。 | 如果未對應 VMNetwork2-Chicago，將會中斷 VM-1 連線。
VMNetwork1-Chicago 的網路對應已變更。 | VM-1 現在會連線到對應至 VMNetwork1-Chicago 的網路。



## <a name="next-steps"></a>後續步驟

深入了解[規劃網路基礎結構](site-recovery-network-design.md)。
