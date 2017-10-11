---
title: "規劃使用 Azure Site Recovery 將 Hyper-V 複寫至次要 VMM 站台的網路服務 | Microsoft Docs"
description: "本文討論使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要的 System Center VMM 站台的網路服務規劃。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>步驟 3：規劃將 Hyper-V VM 複寫至次要 VMM 站台的網路服務

檢閱部署的必要條件後，請閱讀本文以規劃在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 將 System Center Virtual Machine Manager (VMM) 雲端中管理的 Hyper-V 虛擬機器 (VM) 複寫至次要站台的網路服務。 

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。

## <a name="network-mapping-overview"></a>網路對應概觀

將 Hyper-V VM (在 VMM 中管理) 複寫到次要資料中心時，將使用網路對應。 網路對應會對應來源 VMM 伺服器上的 VM 網路與目標 VMM 伺服器上的 VM 網路。 對應具有下列功能：

- **網路連線**—在容錯移轉之後，將 VM 連線至適當的網路。 複本 VM 將會連線至對應到來源網路的目標網路。
- **最佳放置**—以最佳方式將複本 VM 放置在 Hyper-V 主機伺服器上。 複本 VM 會放在可以存取對應的 VM 網路的主機上。
- **無網路對應**— 如果您沒有設定網路對應，複本 VM 將不會在容錯移轉後連線至 VM 網路。


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



## <a name="prepare-for-network-mapping"></a>準備網路對應

1. 在來源和目標 VMM 伺服器上，應具備與來源和目標雲端相關聯的邏輯網路。 
2. 在來源和目標伺服器上，應具備連結至邏輯網路的 VM 網路。
3. 來源位置中 Hyper-V 主機上的 VM 應連結至來源 VM 網路。 如果您只有單一 VMM 伺服器，則可以在同一部伺服器上的 VM 網路之間設定對應。

在 Site Recovery 部署期間設定網路對應時，會發生下列情況：

- 當您設定網路對應並選取目標 VM 網路後，將會顯示使用來源 VM 網路的 VMM 來源雲端，以及目標雲端上可用的目標 VM 網路。
- - 正確設定對應並啟用複寫後，來源 VM 將連接至其來源 VM 網路，且其位於目標位置的複本將連接至其對應的 VM 網路。
- 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在的子網路名稱相同，則複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有名稱相符的目標子網路，VM 將會連線到網路中的第一個子網路。

## <a name="connect-to-vms-after-failover"></a>容錯移轉後連線到 VM

規劃您的複寫和容錯移轉策略時，其中一個重要的問題，就是如何在容錯移轉之後連線至複本。 其中有幾個選項： 

- **使用不同的 IP 位址**：您可以選擇讓複寫的 VM 使用不同的 IP 位址。 在此情況下，VM 會在容錯移轉之後取得新的 IP 位址，因此需要更新 DNS。
- **保留相同的 IP 位址**：您可能想讓複本 VM 使用相同的 IP 位址。 保留相同的 IP 位址可在容錯移轉之後減少網路相關問題，藉以簡化復原。 

## <a name="retain-ip-addresses"></a>保留 IP 位址

如果您想在容錯移轉至次要站台後保留主要站台的 IP 位址，可以執行完整的子網路容錯移轉，並更新路由以指示 IP 位址的新位置，或在主要和復原站台之間部署延伸的子網路。

### <a name="stretched-subnet"></a>延伸的子網路

在延伸的子網路中，主要站台和次要站台可同時使用子網路。 如果您將伺服器和其 IP (第 3 層) 組態移至次要站台，網路會自動將流量路由傳送至新位置。 

從第 2 層 (資料連結層) 的觀點而言，您需要可以管理延伸的 VLAN 的網路設備。 另外，透過延伸 VLAN，潛在的容錯網域會延伸到兩個站台，基本上成為單一失敗點。 雖然機率不高，但有可能發生廣播風暴，而且無法隔離。 


### <a name="subnet-failover"></a>子網路容錯移轉

您可以執行子網路容錯移轉，以獲得延伸子網路的好處，而不用實際延伸。 在此解決方案中，子網路將可用於來源或目標站台，但不能同時使用於兩者。 若要在發生容錯移轉時維持 IP 位址空間，您可以程式設計方式排列路由器基礎結構，將子網路從某個站台移到另一個站台。 之後，當發生容錯移轉時，子網路會連同相關聯的 VM 一起移動。 主要的缺點在於，當發生失敗時，您必須移動整個子網路。

### <a name="example"></a>範例

以下是完整的子網路容錯移轉範例。 主要站台有在子網路 192.168.1.0/24 中執行的應用程式。 在容錯移轉時，這個子網路中的所有 VM 會容錯移轉至次要網站，並保留其 IP 位址。 您必須修改路由，以反映屬於子網路 192.168.1.0/24 的所有 VM 現在都已移至復原站台的事實。

下圖顯示容錯移轉前後的子網路：

- 在容錯移轉之前，子網路 192.168.0.1/24 的作用中環境是來源站台，在容錯移轉之後則變成次要站台。
- 主要站台和復原站台、第三個站台和主要站台、以及第三個站台和復原站台之間的路由都必須適當地修改。

**容錯移轉之前**

![容錯移轉之前](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**容錯移轉之後**

![容錯移轉之後](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

容錯移轉之後，會發生下列情況：

- 針對每個 VMM 執行個體，Site Recovery 會為 VM 上的每個網路介面配置 IP 位址 (從相關網路中的靜態 IP 位址集區)。
- 如果次要站台中的 IP 位址集區與來源站台中的相同，Site Recovery 會配置同一個 IP 位址 (來源 VM 的 IP 位址) 給複本 VM。 IP 位址會保留在 VMM 中，但不會設為 Hyper-V 主機上的容錯移轉 IP 位址。 Hyper-V 主機上的容錯移轉 IP 位址會在容錯移轉之前設定。
- 如果同一個 IP 位址無法使用，Site Recovery 會從集區配置另一個可用的 IP 位址。
- 如果 VM 使用 DHCP，Site Recovery 無法管理 IP 位址。 您必須確定次要站台上的 DHCP 伺服器可以從與來源站台相同的範圍配置位址。

### <a name="validate-the-ip-address"></a>驗證 IP 位址

啟用 VM 保護之後，您可以使用下列範例指令碼來驗證指派給 VM 的位址。 這個 IP 位址會設定為容錯移轉 IP 位址，並在容錯移轉時指派給 VM：

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>變更 IP 位址

在此案例中，容錯移轉之 VM 的 IP 位址會變更。 此解決方案的缺點是需要維護。 通常在複本 VM 啟動後會更新 DNS。 DNS 項目可能需要變更，否則在網路中會造成混亂，而快取的項目已更新。 這可能會導致停機時間。 透過下列方式可以避免停機時間：

- 對內部網路應用程式使用低 TTL 值。
- 在 Site Recovery 復原計畫中使用下列指令碼來更新 DNS 伺服器，以確保及時更新。 如果您使用動態 DNS 登錄，則不需要這個指令碼。

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>範例 

來看看您計劃在主要和復原站台使用不同 IP 位址的案例。在此範例中，主要和次要站台有不同的 IP 位址，而且有第三個站台，可以從中存取裝載於主要或復原站台上的應用程式。

- 在容錯移轉之前，應用程式是裝載於主要站台上的子網路 192.168.1.0/24，在容錯移轉之後則會設為次要站台上的子網路 172.16.1.0/24。
- 已適當地設定 VPN 連線/網路路由，讓所有的三個站台可以互相存取。
- 在容錯移轉之後，應用程式將於復原子網路中還原。 在此案例中，不需要容錯移轉整個子網路，而且不需任何變更，即可重新設定 VPN 或網路路由。 容錯移轉和某些 DNS 更新可確保應用程式可供存取。
- 如果 DNS 設定為允許動態更新，則 VM 在容錯移轉後啟動時，就會使用新的 IP 位址自行註冊。

**容錯移轉之前**

![不同的 IP - 容錯移轉之前](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**容錯移轉之後**

![不同的 IP - 容錯移轉之後](./media/vmm-to-vmm-walkthrough-network/network-design11.png)



## <a name="next-steps"></a>後續步驟

移至[步驟 4：準備 VMM 和 Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md)。


