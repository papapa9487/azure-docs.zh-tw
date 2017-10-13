---
title: "使用 Azure Site Recovery 規劃 Hyper-V (不含 System Center VMM) 至 Azure 複寫的網路服務 | Microsoft Docs"
description: "本文討論將 Hyper-V VM (不含 VMM) 複寫至 Azure 時所需的網路規劃"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5ca12254-975d-48e8-a84d-422825dac9b2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: 100b9d8a55c2c163e7a04680f0f7d7963315ee73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-plan-networking-for-hyper-v-to-azure-replication"></a>步驟 4：為 Hyper-V 至 Azure 複寫規劃網路服務

本文摘要說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署 Hyper-V VM (不含 System Center VMM) 複寫至 Azure 時的網路規劃考量。

請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。


## <a name="connecting-to-replica-vms-after-failover"></a>在容錯移轉之後連線到複本 VM

規劃您的複寫和容錯移轉策略時，其中一項重要的問題，是如何在容錯移轉之後連線至 Azure VM。 設計複本 Azure VM 的網路策略時，有兩種選擇：

- **不同的 IP 位址**：您可以選取針對複寫的 Azure VM 網路使用不同的 IP 位址範圍。 在此情節中，VM 會在容錯移轉之後取得新的 IP 位址，因此需要 DNS 更新。 [深入了解](site-recovery-test-failover-vmm-to-vmm.md#prepare-the-infrastructure-for-test-failover)
- **相同的 IP 位址**：您可能會想要在容錯移轉之後，將與您主要內部部署網路中相同的 IP 位址範圍用於 Azure 網路。  保留相同的 IP 位址可在容錯移轉之後減少網路相關問題，藉以簡化復原。 不過，當您複寫到 Azure 時，將需要在容錯移轉後，以新的 IP 位址位置更新路由。


## <a name="retain-ip-addresses"></a>保留 IP 位址

Site Recovery 提供可在容錯移轉至 Azure (藉由子網路容錯移轉) 時保留固定 IP 位址的功能。

藉由子網路容錯移轉，特定的子網路會存在於站台 1 或站台 2 中，但絕不會同時存在於這兩個站台中。 為了在發生容錯移轉時維持 IP 位址空間，您需以程式設計方式為路由器基礎結構進行安排，將子網路從一個站台移到另一個站台。 在容錯移轉期間，子網路會隨著關聯的受保護 VM 一起移動。 主要的缺點在於，當發生失敗時，您必須移動整個子網路。


### <a name="failover-example"></a>容錯移轉範例

讓我們看看容錯移轉至 Azure 的範例。

- Woodgrove Bank 是一家虛構的公司，擁有裝載其商務應用程式的內部部署基礎結構。 它們的行動應用程式裝載在 Azure 上。
- Azure 中 Woodgrove Bank VM 與內部部署伺服器之間的連線，是由內部部署邊緣網路與 Azure 虛擬網路之間的站對站 (VPN) 連線所提供的。
- 此 VPN 意謂著此公司在 Azure 中的虛擬網路會顯示為其內部部署網路的延伸。
- Woodgrove 想要使用 Site Recovery 將內部部署工作負載複寫到 Azure。
 - Woodgrove 必須處理依存於硬式編碼 IP 位址的應用程式和組態，因此必須在容錯移轉至 Azure 之後，保留其應用程式的 IP 位址。
 - Woodgrove 已將來自範圍 172.16.1.0/24、172.16.2.0/24 的 IP 位址指派給其在 Azure 中執行的資源。


Woodgrove 若要既能將其 VM 複寫至 Azure，同時又保留 IP 位址，就必須執行下列操作：

1. 建立 Azure 虛擬網路。 這應該為內部部署網路的延伸，以便讓應用程式可以順暢地容錯移轉。
2. 除了點對站連線能力之外，Azure 還可讓您將站對站 VPN 連線能力新增到 Azure 中建立的虛擬網路。
3. 當設定站對站連線時，在 Azure 網路中，只有當 IP 位址範圍不同於內部部署 IP 位址範圍時，您才能將流量路由傳送到內部部署位置 (本機網路)。
    - 這是因為 Azure 不支援延伸的子網路。 因此，如果您的內部部署環境中有子網路 192.168.1.0/24，您便無法在 Azure 網路中新增本機網路 192.168.1.0/24。
    - 這是預期行為，因為 Azure 並不知道子網路中沒有任何作用中的 VM，也不知道子網路是僅針對災害復原而建立的。
    - 若要能夠從 Azure 網路正確地路由傳送網路流量，網路和本機網路中的子網路便不得發生衝突。

![子網路容錯移轉之前](./media/hyper-v-site-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>容錯移轉之前

1. 建立額外的網路 (例如「復原網路」)。 這是要作為容錯移轉 VM 建立位置的網路。
2. 為了確保在容錯移轉後會保留 VM 的 IP 位址，請在 VM 屬性 > [設定] 中，指定與 VM 在內部部署環境中所用相同的 IP 位址，然後按一下 [儲存]。
3. 當 VM 容錯移轉時，Azure Site Recovery 會將提供的 IP 位址指派給它。

    ![網路屬性](./media/hyper-v-site-walkthrough-network/network-design8.png)

4. 容錯移轉觸發，並使用必要的 IP 位址在 Azure 中建立 VM 之後，您可以使用 [Vnet 對 Vnet 連線](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)來連線到網路。 此動作可以編寫指令碼。
5. 路由需要經過適當地修改，以反映 192.168.1.0/24 現已移至 Azure。

    ![子網路容錯移轉之後](./media/hyper-v-site-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>容錯移轉之後

如果您還沒有如上所示的 Azure 網路，可以在容錯移轉之後，建立主要站台與 Azure 之間的站對站 VPN 連線。

## <a name="change-ip-addresses"></a>變更 IP 位址

這篇[部落格文章](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)說明當您不需要在容錯移轉之後保留 IP 位址時，如何設定 Azure 網路基礎結構。 它是以應用程式描述作為開頭、研究如何在內部部署環境及 Azure 中設定網路，然後以關於執行容錯移轉的資訊作為總結。  

## <a name="next-steps"></a>後續步驟

移至[步驟 5：準備 Azure](hyper-v-site-walkthrough-prepare-azure.md)
