---
title: "用於複寫至 Azure 的 Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajanaki
ms.openlocfilehash: b4bb049577feb416f498a428bae80ecea418180e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>用於從內部部署複寫至 Azure 的 Azure Site Recovery 支援矩陣


本文摘要說明在複寫和復原到 Azure 時 Azure Site Recovery 支援的組態和元件。 如需 Azure Site Recovery 需求的相關資訊，請參閱[必要條件](site-recovery-prereq.md)。

> [!NOTE]
> 請務必將站台復原提供者和代理程式更新為最新版本，以透過支援矩陣中的更新實現相容性。


## <a name="support-for-deployment-options"></a>支援部署選項

**部署** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)** |
--- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至 Azure 儲存體，使用 Azure Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 型或傳統 VM。 | 內部部署 Hyper-V VM 至 Azure 儲存體，使用 Resource Manager 或傳統儲存體和網路。<br/><br/> 容錯移轉至 Resource Manager 型或傳統 VM。
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式。
**PowerShell** | 目前不支援。 | 支援


## <a name="support-for-datacenter-management-servers"></a>支援資料中心管理伺服器

### <a name="virtualization-management-entities"></a>虛擬化管理實體

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vCenter 6.5、6.0 或 5.5
**Hyper-V (有 Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 和 System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 System Center Virtual Machine Manager 2016 雲端。
  > 目前不支援包含將現有 SCVMM 2012 R2 升級至 2016 的設定。
### <a name="host-servers"></a>主機伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vSphere 6.5、6.0、5.5
**Hyper-V (含/不含 Virtual Machine Manager)** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2。<br></br>Windows Server 2016 主機應由 SCVMM 2016 所管理 (若使用 SCVMM)。


  >[!Note]
  >目前不支援混用執行 Windows Server 2016 和 2012 R2 之主機的 Hyper-V 網站。 目前不支援將 Windows Server 2016 主機上的 VM 復原到替代位置。

## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本

複寫至 Azure 時，受保護的虛擬機器必須符合 [Azure 需求](#failed-over-azure-vm-requirements)。
下表摘要說明使用 Azure Site Recovery 時各種部署案例中的複寫作業系統支援。 這項支援適用於在上述 OS 中執行的任何工作負載。

 **VMware/實體伺服器** | **Hyper-V (含/不含 VMM)** |
--- | --- |
64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/>*Windows Server 2016* - VMware 虛擬機器和實體伺服器目前不支援。 <br/><br/> Red Hat Enterprise Linux：5.2 至 5.11、6.1 至 6.9、7.0 至 7.3 <br/><br/>CentOS：5.2 至 5.11、6.1 至 6.9、7.0 至 7.3 <br/><br/>Ubuntu 14.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(不支援 SLES 11 SP3 至 SLES 11 SP4 的複寫電腦升級。 若已將複寫電腦從 SLES 11SP3 升級至 SLES 11 SP4，則您必須停用複寫以在升級後重新提供電腦防護。） | 任何 [Hyper-V 支援的](https://technet.microsoft.com/library/cc794868.aspx)的客體 OS


>[!IMPORTANT]
>(適用於複寫至 Azure 的 VMware/實體伺服器)
>
> 在 Red Hat Enterprise Linux Server 7+ 和 CentOS 7+ 伺服器上，自 Azure Site Recovery 行動服務 9.8 版本起開始支援 3.10.0-514 核心版本。<br/><br/>
> 若客戶使用行動服務版本低於 9.8 版的 3.10.0-514 核心版本，則必須停用複寫、將行動服務版本更新至 9.8 版，然後再啟用複寫。


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>VMware/實體伺服器支援的 Ubuntu 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic 至 3.13.0-117-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic 至 3.13.0-121-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 至 3.13.0-128-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-91-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 至 4.4.0-81-generic、<br/>4.8.0-34-generic 至 4.8.0-56-generic、<br/>4.10.0-14-generic 至 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 至 4.4.0-91-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-32-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Linux 上的支援檔案系統與客體儲存體組態 (VMware/實體伺服器)

執行 VMware 或實體伺服器的 Linux 伺服器，支援下列檔案系統與儲存體組態：
* 檔案系統：ext3、ext4、ReiserFS (僅 Suse Linux Enterprise Server)、XFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式

不支援並行虛擬存放裝置 (由並行虛擬驅動程式匯出的裝置)。<br/>
不支援多佇列區塊 IO 裝置。<br/>
不支援使用 HP CCISS 儲存體控制器的實體伺服器。<br/>

>[!Note]
> 在 Linux 伺服器上，下列目錄必須一律位於來源伺服器的同個磁碟 (OS 磁碟) (若設為獨立資料分割/檔案系統)：/ (root)、/boot、/usr、/usr/local、/var、/ 等等<br/><br/>
> 從「行動服務」9.10 版開始，支援 XFS 檔案系統上的 XFSv5 功能 (例如中繼資料總和檢查碼)。 如果您使用 XFSv5 功能，請確定正在執行行動服務 9.10 版或更新版本。 您可使用 xfs_info 公用程式來檢查資料分割的 XFS 超級區塊。 若 ftype 設為 1，則會使用 XFSv5 功能。
>


## <a name="support-for-network-configuration"></a>支援網路組態
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 的各種部署案例中的網路組態支援。

### <a name="host-network-configuration"></a>主機網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
NIC Teaming | 是<br/><br/>複寫實體機器時不支援| 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vm-network-configuration"></a>客體 VM 網路組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
NIC Teaming | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
靜態 IP (Windows) | 是 | 是
靜態 IP (Linux) | 是 <br/><br/>虛擬機器設定為在容錯回復時使用 DHCP  | 否
多個 NIC | 是 | 是

### <a name="failed-over-azure-vm-network-configuration"></a>容錯移轉的 Azure VM 網路組態

**Azure 網路功能** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | 是 | 是
ILB | 是 | 是
ELB | 是 | 是
流量管理員 | 是 | 是
多個 NIC | 是 | 是
保留的 IP | 是 | 是
IPv4 | 是 | 是
保留來源 IP | 是 | 是


## <a name="support-for-storage"></a>儲存體的支援
下表摘要說明使用 Azure Site Recovery 複寫至 Azure 的各種部署案例中的儲存體組態支援。

### <a name="host-storage-configuration"></a>主機儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | --- | ---
NFS | VMware 為是<br/><br/> 實體伺服器為否 | N/A
SMB 3.0 | N/A | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO)<br></br>測試標的︰Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | 是 | 是

### <a name="guest-or-physical-server-storage-configuration"></a>客體或實體伺服器儲存體組態

**組態** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
VMDK | 是 | N/A
VHD/VHDX | N/A | 是
第 2 代 VM | N/A | 是
EFI/UEFI| 否 | 是
共用叢集磁碟 | 否 | 否
已加密磁碟 | 否 | 否
NFS | 否 | N/A
SMB 3.0 | 否 | 否
RDM | 是<br/><br/> 實體伺服器為 N/A | N/A
磁碟 > 1 TB | 是<br/><br/>最大 4095 GB | 是<br/><br/>最大 4095 GB
具有 4K 磁區大小的磁碟 | 是 | 是，支援第 1 代 VM<br/><br/>不支援第 2 代 VM。
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM 邏輯磁碟區管理 | 是 | 是
儲存空間 | 否 | 是
熱新增/移除磁碟 | 否 | 否
排除磁碟 | 是 | 是
多重路徑 (MPIO) | N/A | 是

**Azure 儲存體** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
LRS | 是 | 是
GRS | 是 | 是
RA-GRS | 是 | 是
非經常性儲存體 | 否 | 否
經常性存取儲存體| 否 | 否
待用加密 (SSE)| 是 | 是
進階儲存體 | 是 | 是
匯入/匯出服務 | 否 | 否


## <a name="support-for-azure-compute-configuration"></a>支援 Azure 計算設定

**計算功能** | **VMware/實體伺服器** | **Hyper-V (含/不含 Virtual Machine Manager)**
--- | --- | ---
可用性設定組 | 是 | 是
中樞 | 是 | 是  
受控磁碟 | 是 | 是<br/><br/>目前不支援從具有受控磁碟的 Azure VM 容錯回復至內部部署。

## <a name="failed-over-azure-vm-requirements"></a>容錯移轉的 Azure VM 需求

您可以部署 Site Recovery 以複寫執行受 Azure 支援的任何作業系統的虛擬機器和實體伺服器。 這包括大部分的 Windows 和 Linux 版本。 複寫至 Azure 時，您想要複寫的內部部署 VM 必須符合下列 Azure 需求。

**實體** | **需求** | **詳細資料**
--- | --- | ---
**客體作業系統** | 從 Hyper-V 複寫到 Azure：Site Recovery 支援 [Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)的所有作業系統。 <br/><br/> 針對 VMware 和實體伺服器複寫：請檢查 Windows 和 Linux [必要條件](site-recovery-vmware-to-azure-classic.md) | 如果不支援，則先決條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟大小** | 如果您要將 **VMware VM 或實體伺服器複寫至 Azure**，則最多 2048 GB。<br/><br/>對於 **Hyper-V 第 1 代** VM，最多 2048 GB。<br/><br/>對於 **Hyper-V 第 2 代 VM**，最多 300 GB。  | 如果不支援，則先決條件檢查會失敗
**作業系統磁碟計數** | 1 | 如果不支援，則先決條件檢查會失敗。
**資料磁碟計數** | 如果您要複寫 **VMware VM 至 Azure**，則為 64 或低於 64；如果您要複寫 **Hyper-V VM 至 Azure**，則為 16 或低於 16 | 如果不支援，則先決條件檢查會失敗
**資料磁碟 VHD 大小** | 最多 4095 GB | 如果不支援，則先決條件檢查會失敗
**網路介面卡** | 支援多個介面卡 |
**共用 VHD** | 不支援 | 如果不支援，則先決條件檢查會失敗
**FC 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗
**硬碟格式** | VHD  <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援 | 必須停用 Bitlocker，才能保護虛擬機器。
**VM 名稱** | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。 | 更新 Site Recovery 中虛擬機器屬性的值。
**VM type** | 第 1 代<br/><br/> 第 2 代 -- Windows | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>支援復原服務保存庫動作

**動作** | **VMware/實體伺服器** | **Hyper-V (無 Virtual Machine Manager)** | **Hyper-V (有 Virtual Machine Manager)**
--- | --- | --- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 | 否


## <a name="support-for-provider-and-agent"></a>支援提供者和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 如果沒有 Virtual Machine Manager 伺服器，安裝在內部部署 Virtual Machine Manager 伺服器或 Hyper-V 伺服器上 | 5.1.19 ([可從入口網站取得](http://aka.ms/downloaddra)) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 整合安裝 (VMware 到 Azure)** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9.3.4246.1 (可從入口網站取得) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | N/A (可從入口網站取得) | N/A
**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> 安裝在內部部署 Hyper-V 伺服器上 (無論是否有 Virtual Machine Manager 伺服器) | 最新的代理程式 ([可從入口網站取得](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>後續步驟
[檢查必要條件](site-recovery-prereq.md)
