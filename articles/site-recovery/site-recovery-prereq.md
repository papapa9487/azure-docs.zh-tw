---
title: "使用 Azure Site Recovery 複寫至 Azure 的必要條件 | Microsoft Docs"
description: "深入了解使用 Azure Site Recovery 服務將虛擬機器和實體機器複寫至 Azure 的必要條件。"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 490833c14b6856cdaf6f6bfd2f67ce54fb0414a2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/19/2017

---

#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>使用 Site Recovery 從內部部署複寫至 Azure 的必要條件

> [!div class="op_single_selector"]
> * [從 Azure 複寫至 Azure](site-recovery-azure-to-azure-prereq.md)
> * [從內部部署複寫至 Azure](site-recovery-prereq.md)

Azure Site Recovery 可藉由協調 Azure 虛擬機器 (VM) 至其他 Azure 區域的複寫，支援您的商務持續性與災害復原 (BCDR) 策略。 Site Recovery 也會將內部部署實體伺服器與虛擬機器複寫至雲端 (Azure) 或複寫至次要資料中心。 如果主要位置運作中斷，您可以容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當主要位置恢復正常作業時，您即可容錯回復至主要位置。 如需 Site Recovery 的詳細資訊，請參閱[什麼是 Site Recovery？](site-recovery-overview.md)。

在本文中，我們將摘要說明開始從內部部署機器到 Azure 的 Site Recovery 複寫所需的必要條件。

若有任何意見，可以在文末留言評論。 您也可以在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。

## <a name="azure-requirements"></a>Azure 需求

**需求** | **詳細資料**
--- | ---
**Azure 帳戶** | [Microsoft Azure 帳戶](http://azure.microsoft.com/)。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
**Site Recovery 服務** | 如需 Azure Site Recovery 服務的詳細資訊，請參閱 [Site Recovery 價格](https://azure.microsoft.com/pricing/details/site-recovery/)。 |
**Azure 儲存體** | 您需要 Azure 儲存體帳戶來儲存複寫的資料。 儲存體帳戶必須位於與 Azure 復原服務保存庫相同的區域中。 發生容錯移轉時，會建立 Azure VM。<br/><br/> 依您想要對於 Azure VM 容錯移轉使用的資源模型而定，可以使用 [Azure Resource Manager 部署模型](../storage/common/storage-create-storage-account.md)或[傳統部署模型](../storage/common/storage-create-storage-account.md)設定帳戶。<br/><br/>您可以使用[異地備援儲存體](../storage/common/storage-redundancy.md#geo-redundant-storage)或本地備援儲存體。 建議使用異地備援儲存體。 使用異地備援儲存體時，如果發生區域性停電或無法復原主要區域，就能夠恢復資料。<br/><br/> 您可以使用標準的 Azure 儲存體帳戶，或使用 Azure 進階儲存體。 [進階儲存體](https://docs.microsoft.com/azure/storage/storage-premium-storage)通常用於需要持續有高 I/O 效能和低延遲的虛擬機器。 使用進階儲存體，虛擬機器即可裝載大量 I/O 的工作負載。 如果您使用進階儲存體來存放複寫的資料，您也需要標準儲存體帳戶。 標準儲存體帳戶可儲存複寫記錄，這些記錄會擷取內部部署資料不斷發生的變更。<br/><br/>
**儲存體限制** | 您無法移動不同資源群組的 Site Recovery 中使用的儲存體帳戶，也無法移至或使用其他訂用帳戶。<br/><br/> 目前，印度中部與印度南部無法複寫至進階儲存體帳戶。
**Azure 網路** | 您需要 Azure 網路，供 Azure VM 在容錯移轉之後連線。 此 Azure 網路必須位於與復原服務保存庫相同的區域中。<br/><br/> 在 Azure 入口網站，您可以使用[資源管理員部署模型](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[傳統部署模型](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)建立 Azure 網路。<br/><br/> 如果您將從 System Center Virtual Machine Manager (VMM) 複寫至 Azure 時，必須設定 VMM VM 網路與 Azure 網路之間的網路對應。 這可確保 Azure VM 在容錯移轉之後連線到適當的網路。
**網路限制** | 您無法將在 Site Recovery 中使用的網路帳戶移至不同的資源群組，也無法移至或使用其他訂用帳戶。
**網路對應** | 如果您要複寫 VMM 雲端中的 Microsoft Hyper-V VM，必須設定網路對應。 這可確保建立的 Azure VM 在容錯移轉之後連線到適當的網路。

> [!NOTE]
> 以下各節說明客戶環境中各種元件的必要條件。 如需有關支援特定設定的詳細資訊，請參閱[支援矩陣](site-recovery-support-matrix.md)。
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>VMware VM 或實體 Windows 或 Linux 伺服器至 Azure 的災害復原
VMware VM 或實體 Windows 或 Linux 伺服器的災害復原需要下列元件。 這些是除了 [Azure 需求](#azure-requirements)所述之外的元件。


### <a name="configuration-server-or-additional-process-server"></a>組態伺服器或其他處理伺服器

設定內部部署機器做為組態伺服器，協調內部部署網站與 Azure 之間的通訊。 下表討論可設定為組態伺服器之虛擬機器的系統和軟體需求。

> [!IMPORTANT]
> 部署用於保護 VMWare 虛擬機器的組態伺服器時，我們建議您將其部署為**高可用 (HA)** 虛擬機器。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>VMWare vCenter 或 vSphere 主機必要條件

| **元件** | **需求** |
| --- | --- |
| **vSphere** | 您必須有一個或多個 VMware vSphere Hypervisor。<br/><br/>Hypervisor 必須執行 vSphere 6.0、5.5 或 5.1 版 (含最新更新)。<br/><br/>我們建議 vSphere 主機與 vCenter 伺服器兩者位於和處理序伺服器相同的網路中。 除非您已設定專用的處理序伺服器，否則這是組態伺服器所在的網路。 |
| **vCenter** | 建議您部署 VMware vCenter 伺服器來管理您的 vSphere 主機。 它必須執行 vCenter 6.0 或 5.5 版 (含最新更新)。<br/><br/>**限制**：Site Recovery 不支援 vCenter vMotion 執行個體之間的複寫。 在重新保護作業之後，主要目標虛擬機器亦不支援儲存體 DRS 和 Storage vMotion。|

### <a name="replicated-machine-prerequisites"></a>複寫之機器的必要條件

| **元件** | **需求** |
| --- | --- |
| **內部部署機器** (VMware VM) | 複寫的虛擬機器必須安裝並執行 VMware 工具。<br/><br/> 虛擬機器必須符合[Azure 的必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)用於建立 Azure VM。<br/><br/>每部受保護的電腦上的磁碟容量不可超過 1,023 GB。 <br/><br/>安裝磁碟機上必須至少有 2 GB 的可用空間來進行元件安裝。<br/><br/>如果您想要啟用多部 VM 虛擬機器一致性，必須在虛擬機器本機防火牆上開啟連接埠 20004。<br/><br/>機器名稱的長度必須介於 1 到 63 個字元 (可使用字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 <br/><br/>您可以在為機器啟用複寫後修改 Azure 名稱。<br/><br/> |
| **Windows 機器** (實體或 VMware) | 機器必須執行下列其中一個受支援的 64 位元作業系統： <br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>- Windows Server 2008 R2 SP1 或更新版本<br/><br/> 作業系統必須安裝在 C 磁碟機上。作業系統磁碟必須是 Windows 基本磁碟而非動態磁碟。 資料磁碟可以為動態。<br/><br/>|
| **Linux 機器** (實體或 VMware) | 機器必須執行下列其中一個受支援的 64 位元作業系統： <br/>- Red Hat Enterprise Linux 5.2 至 5.11、6.1 至 6.9、7.0 至 7.3<br/>- CentOS 5.2 至 5.11、6.1 至 6.9、7.0 至 7.3<br/>- Ubuntu 14.04 LTS 伺服器 (如需支援的 Ubuntu 核心版本清單，請參閱[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Ubuntu 16.04 LTS 伺服器 (如需支援的 Ubuntu 核心版本清單，請參閱[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-  Debian 7 或 Debian 8<br/>-  Oracle Enterprise Linux 6.5 或 6.4，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)<br/>- SUSE Linux Enterprise Server 11 SP4 或 SUSE Linux Enterprise Server 11 SP3<br/><br/>受保護機器上您的 /etc/hosts 檔案必須有將本機主機名稱對應到所有網路介面卡相關聯 IP 位址的項目。<br/><br/>容錯移轉之後，如果您想要連線到使用安全殼層 (SSH) 用戶端執行 Linux 的 Azure VM，請確定受保護的機器上的 SSH 服務已設定為系統啟動時自動啟動。 也請確定防火牆規則允許對於受保護的機器進行的 SSH 連線。<br/><br/>主機名稱、掛接點、裝置名稱和 Linux 系統路徑和檔案名稱 (例如 /etc/ 和 /usr) 必須僅使用英文。<br/><br/>下列目錄必須一律位於來源伺服器的同個磁碟 (OS 磁碟) (若設為獨立資料分割/檔案系統)：<br/>- / (根)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>目前，XFS v5 功能 (例如，中繼資料總和檢查碼) 不支援 XFS 檔案系統的 Site Recovery。 請確定 XFS 檔案系統未使用任何 v5 功能。 您可使用 xfs_info 公用程式來檢查資料分割的 XFS 超級區塊。 若 **ftype** 設為 **1**，則會使用 XFS v5 功能。<br/><br/>在 Red Hat Enterprise Linux 7 和 CentOS 7 伺服器上，必須安裝和提供可用的 lsof 公用程式。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Hyper-V 虛擬機器至 Azure (無 VMM) 的災害復原

VMM 雲端之中 Hyper-V VM 的災害復原需要下列元件。 這些是除了 [Azure 需求](#azure-requirements)所述之外的元件。

| **必要條件** | **詳細資料** |
| --- | --- |
| **Hyper-V 主機** |一部或多部內部部署伺服器必須執行含最新更新且已啟用 Hyper-V 角色的 Windows Server 2012 R2，或執行 Microsoft Hyper-V Server 2012 R2。<br/><br/>Hyper-V 伺服器必須有一部或多部虛擬機器。<br/><br/>Hyper-V 伺服器必須直接或透過 Proxy 連接到網際網路。<br/><br/>Hyper-V 伺服器必須安裝知識庫文章 [2961977](https://support.microsoft.com/kb/2961977) 所述的修正。
|**Provider 和代理程式**| 在 Site Recovery 部署期間，您會安裝 Azure Site Recovery Provider。 Provider 安裝也會在有要保護的虛擬機器正在執行的每部 Hyper-V 伺服器上安裝 Azure 復原服務代理程式。 <br/><br/>Site Recovery 保存庫中的所有 Hyper-V 伺服器必須有相同版本的提供者和代理程式。<br/><br/>Provider 需要透過網際網路連線到 Site Recovery。 可以直接或透過 Proxy 傳送流量。 不支援 HTTPS 型 Proxy。 Proxy 伺服器必須允許存取下列 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>如果您在伺服器上有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/><br/> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (連接埠 443)。<br/><br/> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM 雲端中的 Hyper-V 虛擬機器至 Azure 的災害復原

VMM 雲端之中 Hyper-V VM 的災害復原需要下列元件。 這些是除了 [Azure 需求](#azure-requirements)所述之外的元件。

| **必要條件** | **詳細資料** |
| --- | --- |
| **Virtual Machine Manager** |在 System Center 2012 R2 或更高版本上必須有一部或多部 VMM 伺服器正在執行。 每部 VMM 伺服器必須有一個或多個雲端。 <br/><br/>雲端必須有：<br/>- 一或多個 VMM 主機群組。<br/>- 每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。<br/><br/>如需設定 VMM 雲端的詳細資訊，請參閱[如何在 Virtual Machine Manager 2012 中建立雲端](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx)。 |
| **Hyper-V** |Hyper-V 主機伺服器必須至少執行 Windows Server 2012 R2 (已啟用 Hyper-V 角色) 或 Microsoft Hyper-V Server 2012 R2。 必須安裝最新的更新。<br/><br/> Hyper-V 伺服器必須有一部或多部虛擬機器。<br/><br/> 您想要複寫的虛擬機器包含在其中的 Hyper-V 主機伺服器或叢集必須在 VMM 雲端中受管理。<br/><br/>Hyper-V 伺服器必須直接或透過 Proxy 連接到網際網路。<br/><br/>Hyper-V 伺服器必須安裝知識庫文章 [2961977](https://support.microsoft.com/kb/2961977) 所述的修正。<br/><br/>Hyper-V 主機伺服器需要網際網路存取權以將資料複寫至 Azure。 |
| **Provider 和代理程式** |在 Azure Site Recovery 部署期間，在 VMM 伺服器上安裝 Azure Site Recovery Provider。 在 Hyper-V 主機上安裝 Recovery Services 代理程式。 Provider 和代理程式必須直接透過網際網路或透過 Proxy 連線至 Azure。 不支援 HTTPS 型 Proxy。 VMM 伺服器上的 Proxy 伺服器和 Hyper-V 主機必須允許存取︰ <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>如果您在 VMM 伺服器上有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/><br/> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (連接埠 443)。<br/><br/>允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>複寫之機器的必要條件

| **元件** | **詳細資料** |
| --- | --- |
| **受保護的 VM** | Site Recovery 支援 [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 支援的所有作業系統。<br/><br/>虛擬機器必須符合[Azure 的必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)用於建立 Azure VM。 機器名稱的長度必須介於 1 到 63 個字元 (可使用字母、數字和連字號)。 名稱必須以字母或數字開頭，並以字母或數字結尾。 <br/><br/>您啟用虛擬機器的複寫後，可以修改虛擬機器名稱。 <br/><br/> 每部受保護的電腦上的磁碟容量不可超過 1,023 GB。 VM 最多可以有 16 個磁碟 (最多可達 16 TB)。<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>VMM 雲端中的 Hyper-V 虛擬機器至客戶自有網站的災害復原

VMM 雲端之中對於 Hyper-V VM 向客戶擁有的網站進行的災害復原需要下列元件。 這些是除了 [Azure 需求](#azure-requirements)所述之外的元件。

| **元件** | **詳細資料** |
| --- | --- |
| **Virtual Machine Manager** |  建議您在主要站台與次要站台各部署一部 VMM 伺服器。<br/><br/> 您可以[在單一 VMM 伺服器上的雲端之間進行複寫](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)。 若要在單一 VMM 伺服器上於兩個雲端之間複寫，您需要在 VMM 伺服器上設定至少兩個雲端。<br/><br/> VMM 伺服器至少必須執行含有最新更新的 System Center 2012 SP1。<br/><br/> 每個 VMM 伺服器必須有一個或多個雲端。 所有的雲端都必須設定 Hyper-V 容量設定檔。 <br/><br/>雲端必須有一個或多個 VMM 主機群組。 如需設定 VMM 雲端的詳細資訊，請參閱 [Azure Site Recovery 部署的準備](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)。 |
| **Hyper-V** | Hyper-V 伺服器必須至少執行已啟用 Hyper-V 角色並已安裝最新更新的 Windows Server 2012。<br/><br/> Hyper-V 伺服器必須有一部或多部虛擬機器。<br/><br/>  Hyper-V 主機伺服器必須位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，建議您安裝知識庫文章 [2961977](https://support.microsoft.com/kb/2961977) 中所述的更新。<br/><br/> 如果您是在 Windows Server 2012 上的叢集中執行 Hyper-V，且您的叢集是靜態 IP 位址型叢集，則不會自動建立叢集訊息代理程式。 您必須手動設定叢集訊息代理程式。 如需叢集訊息代理程式的詳細資訊，請參閱[設定叢集對叢集複寫的複本訊息代理程式角色](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。 |
| **提供者** | 在 Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 Provider 會透過 HTTPS (連接埠 443) 與 Site Recovery 通訊來協調複寫。 資料複寫是透過 LAN 或透過 VPN 連線在主要和次要 Hyper-V 伺服器之間進行。<br/><br/> VMM 伺服器上執行的 Provider 需要存取下列 URL：<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Site Recovery Provider 必須允許從 VMM 伺服器至 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火牆通訊，並允許 HTTPS (連接埠 443) 通訊協定。 |


## <a name="url-access"></a>URL 存取
這些 URL 必須可透過 VMware、VMM 和 Hyper-V 主機伺服器提供：

|**URL** | **VMM 至 VMM** | **VMM 至 Azure** | **Hyper-V 至 Azure** | **VMware 至 Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | 允許 | 允許 | 允許 | 允許 |
|``*.backup.windowsazure.com`` | 不需要 | 允許 | 允許 | 允許 |
|``*.hypervrecoverymanager.windowsazure.com`` | 允許 | 允許 | 允許 | 允許 |
|``*.store.core.windows.net`` | 允許 | 允許 | 允許 | 允許 |
|``*.blob.core.windows.net`` | 不需要 | 允許 | 允許 | 允許 |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | 不需要 | 不需要 | 不需要 | 允許 SQL 下載 |
|``time.windows.com`` | 允許 | 允許 | 允許 | 允許|
|``time.nist.gov`` | 允許 | 允許 | 允許 | 允許 |

