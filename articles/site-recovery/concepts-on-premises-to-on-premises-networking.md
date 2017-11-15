---
title: "使用 Azure Site Recovery 容錯移轉至次要網站之後連線至 VM | Microsoft Docs"
description: "使用 Azure Site Recovery 容錯移轉至次要網站之後，可供連線到 Azure VM 的網路服務指引。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: pratshar
ms.openlocfilehash: ce33a90a303a10de8ff198754e8e39c334035537
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="connecting-to-vms-after-failover-to-a-secondary-site"></a>在容錯移轉至次要網站之後連線至 VM

檢閱部署的必要條件後，請閱讀本文以規劃在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 將 System Center Virtual Machine Manager (VMM) 雲端中管理的 Hyper-V 虛擬機器 (VM) 複寫至次要站台的網路服務。 

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。

## <a name="overview"></a>概觀

規劃您的複寫和容錯移轉策略時，其中一個重要的問題，就是如何在容錯移轉之後連線至複本。 其中有幾個選項： 

- **使用不同的 IP 位址**：您可以選擇讓複寫的 VM 使用不同的 IP 位址。 在此情況下，VM 會在容錯移轉之後取得新的 IP 位址，因此需要更新 DNS。
- **保留相同的 IP 位址**：您可能想讓複本 VM 使用相同的 IP 位址。 保留相同的 IP 位址可在容錯移轉之後減少網路相關問題，藉以簡化復原。 

## <a name="retaining-ip-addresses"></a>保留 IP 位址

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




