---
title: "Azure Site Recovery：常見問題集 | Microsoft Docs"
description: "本文討論 Azure Site Recovery 的相關熱門問題。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 95e31d0ca5983e0946ad6fb993e7a89a6a63d2c3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery：常見問題集 (FAQ)
本文包含有關 Azure Site Recovery 的常見問題集。 如果您在閱讀本文後有問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。

## <a name="general"></a>一般
### <a name="what-does-site-recovery-do"></a>Site Recovery 的功能是什麼？
Site Recovery 可協調並自動執行區域、內部部署虛擬機器和實體伺服器之間至 Azure 的 Azure VM 複寫；協調並自動執行內部部署機器至次要資料中心的複寫，藉此保障您的商務持續性與災害復原 (BCDR) 策略。 [深入了解](site-recovery-overview.md)。

### <a name="what-can-site-recovery-protect"></a>Site Recovery 可以保護什麼？
* **Azure VM**：Site Recovery 可複寫在支援的 Azure VM 上執行的任何工作負載。
* **Hyper-V 虛擬機器**：Site Recovery 可以保護 Hyper-V VM 上執行的任何工作負載。
* **實體伺服器**：Site Recovery 可以保護執行 Windows 或 Linux 的實體伺服器。
* **VMware 虛擬機器**：Site Recovery 可以保護 VMware VM 上執行的任何工作負載。

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Site Recovery 是否支援 Azure Resource Manager？
Azure 入口網站提供的 Site Recovery 可支援 Resource Manager。 Site Recovery 支援 Azure 傳統入口網站的舊版部署。 您無法在傳統入口網站中建立新的保存庫，其亦不支援新功能。

### <a name="can-i-replicate-azure-vms"></a>我可以複寫 Azure VM 嗎？
是，您可以在 Azure 區域之間複寫支援的 Azure VM。 [深入了解](site-recovery-azure-to-azure.md)。

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>在 Hyper-V 中，我需要什麼，才能使用 Site Recovery 來協調複寫？
對於 Hyper-V 主機伺服器，您的需求視部署案例而定。 請查看下列主題中的 Hyper-V 先決條件：

* [將 Hyper-V VM (不使用 VMM) 複寫至 Azure](site-recovery-hyper-v-site-to-azure.md)
* [將 Hyper-V VM (使用 VMM) 複寫至 Azure](site-recovery-vmm-to-azure.md)
* [將 Hyper-V VM 複寫至次要資料中心](site-recovery-vmm-to-vmm.md)
* 如果您要複寫至次要資料中心，請參閱 [支援的 Hyper-V VM 客體作業系統](https://technet.microsoft.com/library/mt126277.aspx)。
* 如果是覆寫至 Azure，則 Site Recovery 支援 [Azure 支援的](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)所有客體作業系統。

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>當 Hyper-V 在用戶端作業系統上執行時，我可以保護 VM 嗎？
不能，VM 所在的 Hyper-V 主機伺服器必須在支援的 Windows 伺服器機器上執行。 如果您需要保護用戶端電腦，您可以用實體機器的形式將它複寫至 [Azure](site-recovery-vmware-to-azure.md) 或[次要資料中心](site-recovery-vmware-to-vmware.md)。

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>我可以使用 Site Recovery 來保護哪些工作負載？
您可以使用 Site Recovery 來保護在支援的 VM 或實體伺服器上執行的大多數工作負載。 Site Recovery 支援應用程式感知複寫，可讓應用程式復原為智慧型狀態。 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，它還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。 [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V 主機是否必須在 VMM 雲端中？
如果您想要複寫至次要資料中心，Hyper-V VM 就必須在位於 VMM 雲端中的 Hyper-V 主機伺服器上。 如果您想要複寫至 Azure，則不論 Hyper-V 主機伺服器是否使用 VMM 雲端，您都可以複寫這些主機伺服器上的 VM。 [閱讀更多資訊](site-recovery-hyper-v-site-to-azure.md)。

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>如果我只有一部 VMM 伺服器，可以部署 Site Recovery 搭配 VMM 嗎？

是。 您可以將 VMM 雲端 Hyper-V 伺服器中的 VM 複寫至 Azure，或是在同一部伺服器上的 VMM 雲端之間進行複寫。 若要在內部部署環境之間進行複寫，建議您在主要站台與次要站台都要有 VMM 伺服器。  

### <a name="what-physical-servers-can-i-protect"></a>我可以保護哪些實體伺服器？
您可以將執行 Windows 和 Linux 的實體伺服器複寫至 Azure 或次要站台。 [了解](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) 作業系統需求。  不論是將實體伺服器複寫至 Azure 還是次要站台，都適用相同的需求。


請注意，如果您的內部部署伺服器當機，實體伺服器將會在 Azure 中以 VM 的身分執行。 目前不支援針對內部部署實體伺服器進行容錯回復。 針對受實體保護的機器，您只能針對 VMware 虛擬機器進行容錯回復。

### <a name="what-vmware-vms-can-i-protect"></a>我可以保護哪些 VMware VM？

若要保護 VMware VM，您需要一個 vSphere Hypervisor 以及幾個執行 VMware 工具的虛擬機器。 此外，建議您擁有 VMware vCenter 伺服器以便管理 Hypervisor。 [深入了解](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)將 VMware 伺服器和 VM 複寫至 Azure 或次要站台的確切需求。


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>我可以使用 Site Recovery 來管理分公司的災害復原嗎？
是。 當您使用 Site Recovery 來協調分公司中的複寫與容錯移轉時，會為您集中提供所有分公司工作負載的整合協調與檢視。 您不需要造訪分公司，就可以從總公司輕鬆執行所有分公司的容錯移轉及管理災害復原。

## <a name="pricing"></a>價格

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>使用 Azure Site Recovery 時有哪些費用？
使用 Site Recovery 時，會產生 Site Recovery 授權、Azure 儲存體、儲存體交易和輸出資料傳輸的費用。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery)。

Site Recovery 授權是根據受保護的執行個體，其中的執行個體是 VM 或實體伺服器。

- 如果 VM 磁碟複寫到標準儲存體帳戶，Azure 儲存體會依據儲存體使用量收費。 例如，如果來源磁碟大小是 1 TB，使用量是 400 GB，Site Recovery 會在 Azure 中建立 1 TB 的 VHD，但要收費的儲存體是 400 GB (加上複寫記錄檔使用的儲存體空間量)。
- 如果 VM 磁碟複寫到進階儲存體帳戶，Azure 儲存體會依據佈建的儲存體大小收費，四捨五入到最接近的進階儲存體磁碟選項。 例如，如果來源磁碟大小是 50GB，Site Recovery 會在 Azure 中建立 50 GB 的磁碟，而 Azure 會將此對應到最接近的進階儲存體磁碟 (P10)。  成本是依據 P10 計算，而非 50 GB 的磁碟大小。  [深入了解](https://aka.ms/premium-storage-pricing)。  如果您使用進階儲存體，也需要複寫記錄所需的標準儲存體帳戶，這些記錄檔使用的標準儲存體空間量也會計費。
- 在測試容錯移轉或容錯移轉發生之前，不會建立任何磁碟。 在複寫狀態下，系統會依據[儲存體定價計算機](https://azure.microsoft.com/en-in/pricing/calculator/)產生「分頁 Blob 和磁碟」類別的儲存體費用。 這些費用會依據進階/標準的儲存體類型和資料備援類型 (LRS、GRS、RA-GRS 等) 而定。
- 如果您選取要在容錯移轉時使用受控磁碟的選項，即會在容錯移轉/測試容錯移轉之後套用[受控磁碟的費用](https://azure.microsoft.com/en-in/pricing/details/managed-disks/)。 在複寫期間不會套用受控磁碟的費用。
- 如果您未選取要在容錯移轉時使用受控磁碟的選項，系統會在容錯移轉之後依據[儲存體定價計算機](https://azure.microsoft.com/en-in/pricing/calculator/)產生「分頁 Blob 和磁碟」類別的儲存體費用。 這些費用會依據進階/標準的儲存體類型和資料備援類型 (LRS、GRS、RA-GRS 等) 而定。
- 在穩定狀態複寫期間，以及容錯移轉/測試容錯移轉之後的一般 VM 操作，均會收取儲存體交易費用。 不過，這些費用微乎其微。

測試容錯移轉期間也會產生成本，將適用 VM、儲存體、輸出和儲存體交易成本。



## <a name="security"></a>安全性
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>複寫資料會傳送到 Site Recovery 服務嗎？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您虛擬機器或實體伺服器上執行哪些項目的資訊。
內部部署 Hyper-V 主機、VMware Hypervisor 或實體伺服器，會與 Azure 儲存體或次要站台交換複寫資料。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>為了遵循法規，甚至我們的內部部署中繼資料也必須保留在相同的地理區域內。 Site Recovery 可以幫助我們嗎？
是。 當您在某個區域中建立 Site Recovery 保存庫時，我們會確保我們啟用及協調複寫與容錯移轉時所需的一切中繼資料都會保留在該區域地理界限內。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
就虛擬機器和實體伺服器而言，在內部部署站台之間進行複寫時，支援傳輸中加密。 在將虛擬機器和實體伺服器複寫至 Azure 時，則同時支援傳輸中加密和[靜態加密 (在 Azure 中)](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption)。

## <a name="replication"></a>複寫

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>我可以透過站對站 VPN 複寫至 Azure 嗎？
Azure Site Recovery 會透過公用端點，將資料複製到 Azure 儲存體帳戶。 複寫不是透過站對站 VPN。 您可以透過 Azure 虛擬網路建立站對站 VPN。 這並不會影響 Site Recovery 複寫。

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>可以使用 ExpressRoute 將虛擬機器複寫到 Azure 嗎？
是的，ExpressRoute 可用來將虛擬機器複寫至 Azure。 Azure Site Recovery 會透過公用端點，將資料複製到 Azure 儲存體帳戶。 您必須設定[公用對等](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)以使用 Site Recovery 複寫的 ExpressRoute。 在虛擬機器容錯移轉到 Azure 虛擬網路之後，您可以 Azure 虛擬網路使用[私人對等互連](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)安裝來存取這些虛擬機器。

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>將虛擬機器複寫至 Azure 有任何先決條件嗎？
您想要複寫至 Azure 的虛擬機器應該要符合 [Azure 需求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>我可以將 Hyper-V 第 2 代虛擬機器複寫至 Azure 嗎？
是。 Site Recovery 會在容錯移轉時從第 2 代轉換成第 1 代。 在容錯回復時，機器會轉換回第 2 代。 [閱讀更多資訊](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>如果複寫至 Azure，我要如何支付 Azure VM 費用？
在一般複寫期間，就會將資料複寫至異地備援的 Azure 儲存體，您不需要支付任何 Azure IaaS 虛擬機器費用 (這是一項重要的優點)。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器，之後就會針對您在 Azure 中取用的運算資源進行計費。

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>我是否可以透過 SDK 自動化 Site Recovery 案例？
是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。 針對使用 PowerShell 來部署 Site Recovery，目前支援的案例包括︰

* [將 VMM 雲端中的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [將不使用 VMM 的 Hyper-V VM 複寫至 Azure PowerShell Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>如果要複寫至 Azure，我需要哪一種儲存體帳戶？
* **Azure 傳統入口網站**︰如果您要在 Azure 傳統入口網站中部署 Site Recovery，您將需要一個[標準的異地備援儲存體帳戶](../storage/common/storage-redundancy.md#geo-redundant-storage)。 目前不支援進階儲存體。 此帳戶必須位於與 Site Recovery 保存庫相同的區域中。
* **Azure 入口網站**︰如果您要在 Azure 入口網站中部署 Site Recovery，您將需要一個 LRS 或 GRS 儲存體帳戶。 我們建議使用 GRS，以便在發生區域性停電或無法復原主要區域時，能夠恢復資料。 此帳戶必須位於與復原服務保存庫相同的區域中。 當您在 Azure 入口網站部署 Site Recovery 時，進階儲存體現在支援 VMware VM、Hyper-V VM 和實體伺服器複寫。

### <a name="how-often-can-i-replicate-data"></a>我可以多久複寫一次資料？
* **Hyper-V：**可以每隔 30 秒、5 分鐘或 15 分鐘複寫一次 Hyper-V VM (進階儲存體除外)。 如果您已設定 SAN 複寫，則複寫會是同步的。
* **VMware 與實體伺服器：** 複寫頻率在此處並不相關。 複寫是連續的。

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>我可以將複寫從現有的復原網站延伸到另一個第三網站嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)中提出這項功能的要求。

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>我可以在第一次複寫至 Azure 時進行離線複寫嗎？
不支援此做法。 請在 [意見反應論壇](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-specific-disks-from-replication"></a>我可以從複寫中排除特定的磁碟嗎？
如果您是使用 Azure 入口網站[複寫 VMware VM 和 Hyper-V VM](site-recovery-exclude-disk.md) 至 Azure，就支援此做法。

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>我可以使用動態磁碟來複寫虛擬機器嗎？
複寫 Hyper-V 虛擬機器時，支援使用動態磁碟。 將 VMware VM 和實體機器複寫至 Azure 時，也支援使用這些磁碟。 作業系統磁碟必須是基本磁碟。

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>我是否可以將新機器新增到現有的複寫群組？
支援將新機器新增到現有的複寫群組。 若要這樣做，請選取複寫群組 (從 [複寫的項目] 刀鋒視窗)，在複寫群組上按一下滑鼠右鍵/選取操作功能表，然後選取適當的選項。

![新增到複寫群組](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>我可以調節為 Hyper-V 複寫流量配置的頻寬嗎？
是。 您可以從部署文章深入了解如何將頻寬節流︰

* [適用於複寫 VMware VM 和實體伺服器的容量規劃](site-recovery-plan-capacity-vmware.md)
* [適用於複寫 VMM 雲端中 Hyper-V VM 的容量規劃](site-recovery-vmm-to-azure.md#capacity-planning)
* [適用於複寫不使用 VMM 之 Hyper-V VM 的容量規劃](site-recovery-hyper-v-site-to-azure.md)

## <a name="failover"></a>容錯移轉
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>如果容錯移轉到 Azure，在容錯移轉之後，我要如何存取存取 Azure 虛擬機器？
您可以透過安全的網際網路連線、透過站台對站台 VPN 或透過 Azure ExpressRoute 存取 Azure VM。 您必須做好一些準備才能連線。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>如果我容錯移轉到 Azure，Azure 如何確定我的資料具有復原能力？
Azure 是針對復原能力而設計的。 Site Recovery 已經設計成可根據 Azure SLA，在需要時容錯移轉至次要 Azure 資料中心。 如果發生這種情況，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>如果我在兩個資料中心之間進行複寫，當我的主要資料中心發生意外中斷時，會發生什麼情況？
您可以從次要站台觸發非計劃性容錯移轉。 Site Recovery 不需要來自主要站台的連線即可執行容錯移轉。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？
容錯移轉並非自動發生。 您可以在入口網站中按一下某個按鈕來起始容錯移轉，或是使用 [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) 來觸發容錯移轉。 容錯回復在 Site Recovery 入口網站中是一個簡單的動作。

若要進行自動化，您可以使用內部部署 Orchestrator 或 Operations Manager 來偵測虛擬機器失敗，然後使用 SDK 來觸發容錯移轉。

* [閱讀更多](site-recovery-create-recovery-plans.md) 復原方案的相關資訊。
* [深入了解](site-recovery-failover.md) 容錯移轉。
* [深入了解](site-recovery-failback-azure-to-vmware.md) 如何容錯回復 VMware VM 和實體伺服器

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>如果我的內部部署主機沒有回應或當機，我是否能針對不同的主機進行容錯回復？
是，您可以使用替代位置復原從 Azure 針對不同的主機進行容錯回復。 請透過下列針對 VMware 和 Hyper-V 虛擬機器的連結，深入了解各種選項。

* [針對 VMware 虛擬機器](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [針對 Hyper-V 虛擬機器](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>服務提供者
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>我是服務提供者。 Site Recovery 是否適用於專用和共用基礎結構模型？
是，Site Recovery 同時支援專用與共用的基礎結構模型。

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>對服務提供者而言，租用戶的身分識別是否會與 Site Recovery 服務共用？
否。 租用戶身分識別會保持匿名。 您的租用戶不需要存取 Site Recovery 入口網站。 只有服務提供者系統管理員會與入口網站互動。

### <a name="will-tenant-application-data-ever-go-to-azure"></a>租用戶應用程式資料是否會傳送到 Azure？
在服務提供者擁有的站台之間進行複寫時，永遠不會將應用程式資料傳送到 Azure。 資料會在傳輸中加密，並且會在服務提供者站台之間直接進行複寫。

如果您是複寫至 Azure，應用程式資料就會傳送到 Azure 儲存體而不是 Site Recovery 服務。 資料會在傳輸中加密並在 Azure 中繼續維持加密狀態。

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>我的租用戶會收到來自 Azure 服務的帳單嗎？
否。 Azure 的計費關係是直接與服務提供者相關。 服務提供者負責為其租用戶產生特定的帳單。

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>如果我複寫至 Azure，我們需要在 Azure 中隨時執行虛擬機器嗎？
否，資料會複寫至您訂用帳戶中的 Azure 儲存體帳戶。 當您執行測試容錯移轉 (DR 演練) 或實際容錯移轉時，Site Recovery 會自動在您的訂用帳戶中建立虛擬機器。

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>當我複寫至 Azure 時，您會確保提供租用戶層級的隔離嗎？
是。

### <a name="what-platforms-do-you-currently-support"></a>目前支援哪些平台？
我們支援「Azure 套件」、「雲端平台系統」及 System Center 架構 (2012 及更新版本) 的部署。 [深入了解](https://technet.microsoft.com/library/dn850370.aspx) 「Azure 套件」和 Site Recovery 整合

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>您支援單一 Azure 套件與單一 VMM 伺服器部署嗎？
是，您可以將 Hyper-V 虛擬機器複寫至 Azure，或是在服務提供者網站之間進行複寫。  請注意，如果您是在服務提供者站台之間進行複寫，將無法使用 Azure Runbook 整合。

## <a name="next-steps"></a>後續步驟
* 參閱 [Site Recovery 概觀](site-recovery-overview.md)
* 了解 [Site Recovery 架構](site-recovery-components.md)  

