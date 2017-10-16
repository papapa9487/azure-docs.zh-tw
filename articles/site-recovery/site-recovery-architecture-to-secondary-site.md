---
title: "在 Azure Site Recovery 中將內部部署機器複寫至次要內部部署網站如何運作？ | Microsoft Docs"
description: "本文提供使用 Azure Site Recovery 服務將內部部署 VM 和實體伺服器複寫至次要網站時所用元件和架構的概觀。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>在 Site Recovery 中將內部部署機器複寫至次要網站如何運作？

本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署虛擬機器和實體伺服器複寫至 Azure 時的相關元件和程序。

您可以將下列項目複寫至次要內部部署網站：
- Hyper-V 叢集和獨立主機上在 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V VM。
- 內部部署 VMware VM 和 Windows/Linux 實體伺服器。 在此案例中，複寫是由 Scout 管理。

如有任何意見，請張貼於這篇文章下方或 [Azure 復原服務論壇 (英文)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中。

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>將 Hyper-V VM 複寫至次要內部部署網站


### <a name="architectural-components"></a>架構元件

以下是要將 Hyper-V VM 複寫到次要網站的所需項目。

**元件** | <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> | **詳細資料**
--- | --- | ---
**Azure** | 您需要 Microsoft 帳戶。 |
**VMM 伺服器** | 我們建議主要網站與次要網站中各要有一部 VMM 伺服器 | 每部 VMM 伺服器應連線至網際網路。<br/><br/> 每一部伺服器都應該有至少一個 VMM 私人雲端，並設定好 Hyper-V 功能設定檔。<br/><br/> 您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 此提供者會透過網際網路與 Site Recovery 服務協調進行複寫。 Provider 和 Azure 之間的通訊都是安全且加密的。
**Hyper-V 伺服器** |  在主要和次要 VMM 雲端中，有一或多部 Hyper-V 主機伺服器。<br/><br/> 伺服器應連線到網際網路。<br/><br/> 在主要和次要 Hyper-V 主機伺服器之間，使用 Kerberos 或憑證驗證透過 LAN 或 VPN 來複寫資料。  
**Hyper-V VM** | 位於來源 Hyper-V 主機伺服器。 | 來源主機伺服器應該至少有一個您想要複寫的 VM。

### <a name="replication-process"></a>複寫程序

1. 您要設定 Azure 帳戶。
2. 您要建立用於 Site Recovery 的複寫服務保存庫，並設定保存庫設定，包括︰

    - 複寫來源和目標 (主要和次要網站)。
    - 安裝 Azure Site Recovery 提供者和 Microsoft Azure 復原服務代理程式。 提供者安裝在 VMM 伺服器上，代理程式則安裝在每一部 Hyper-V 主機上。
    - 您要建立來源 VMM 雲端的複寫原則。 此原則會套用至位於雲端主機上的所有 VM。
    - 您要啟用 Hyper-V VM 的複寫。 初始複寫會根據複寫原則設定來進行。
4. 資料變更會受到追蹤，而在初始複寫完成之後，就會開始複寫差異變更。 項目的追蹤變更會保存在 .hrl 檔案中。
5. 您要執行測試容錯移轉，以確定一切都沒問題。

**圖 1：VMM 至 VMM 的複寫**

![內部部署至內部部署](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>容錯移轉和容錯回復程序

1. 您可以在內部部署網站間執行計劃性或非計劃性的[容錯移轉](site-recovery-failover.md)。 如果您執行計劃性容錯移轉，則來源 VM 會關閉以確保不會遺失資料。
2. 您可以容錯移轉單一機器，或建立[復原計劃](site-recovery-create-recovery-plans.md)來協調多部機器的容錯移轉。
4. 如果您執行非計劃性容錯移轉到次要網站，則在容錯移轉之後，次要位置中的容錯移轉機器不會啟用保護或複寫。 如果您執行了計劃性容錯移轉，則在容錯移轉之後，次要位置中的容錯移轉機器會受到保護。
5. 然後，您要認可讓容錯移轉開始存取來自複本 VM 的工作負載。
6. 當主要網站恢復可用狀態時，您就可以起始從次要網站到主要網站的反向複寫作業。 反向複寫會讓虛擬機器進入受保護的狀態，但是次要資料中心仍是使用中位置。
7. 若要讓主要網站再次成為使用中位置，您需要起始從次要網站到主要網站的計劃性容錯移轉，然後再進行另一個反向複寫。




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>將 VMware VM/實體伺服器複寫至次要網站

您可以使用 InMage Scout 及下列架構元件，將 VMware VM 或實體伺服器複寫至次要網站：


### <a name="architectural-components"></a>架構元件

**元件** | <bpt id="p1">**</bpt>Location<ept id="p1">**</ept> | **詳細資料**
--- | --- | ---
**Azure** | InMage Scout。 | 若要取得 InMage Scout，您必須要有 Azure 訂用帳戶。<br/><br/> 建立復原服務保存庫之後，您可下載 InMage Scout 並安裝最新的更新，以設定部署。
**處理序伺服器** | 位於主要網站 | 您部署處理序伺服器來處理快取、壓縮和資料最佳化。<br/><br/> 它也會處理您想要保護的機器的整合代理程式推入安裝。
**組態伺服器** | 位於次要網站 | 組態伺服器會使用管理網站或 vContinuum 主控台來管理、設定和監視您的部署。
**vContinuum 伺服器** | 選用。 與組態伺服器安裝在相同的位置。 | 它會提供主控台來管理及監視您的受保護的環境。
**主要目標伺服器** | 位於次要網站 | 主要目標伺服器保留複製的資料。 它會從處理序伺服器接收資料，在次要網站中建立複本機器，並且保存資料保留點。<br/><br/> 您需要的主要目標伺服器的數目取決於您要保護的機器數目。<br/><br/> 如果您想要容錯回復到主要網站，您也需要主要目標伺服器。 此伺服器上會安裝整合代理程式。
**VMware ESX/ESXi 和 vCenter 伺服器** |  VM 裝載於 ESX/ESXi 主機。 主機是使用 vCenter 伺服器進行管理 | 您需要 VMware 基礎結構以便複寫 VMware VM。
**VM/實體伺服器** |  安裝於 VMware VM 上的整合代理程式和您想要複寫的實體伺服器 | 此代理程式會做為所有元件之間的通訊提供者。


### <a name="replication-process"></a>複寫程序

1. 您會在每個網站 (組態、處理序、主要目標) 中設定元件伺服器，並在您要複寫的機器上安裝整合代理程式。
2. 初始複寫之後，每部機器上的代理程式會將差異複寫變更傳送至處理序伺服器。
3. 處理序伺服器會最佳化此資料，並且將其傳輸至次要網站上的主要目標伺服器。 設定伺服器會管理複寫程序。

**圖 2：VMware 到 VMware 的複寫**

![VMware 至 VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>後續步驟

檢閱[支援矩陣](site-recovery-support-matrix-to-sec-site.md)
