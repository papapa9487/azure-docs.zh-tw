
---
title: "使用 Azure Site Recovery 複寫至次要網站的支援矩陣 | Microsoft Docs"
description: "摘要說明 Azure Site Recovery 支援的作業系統和元件"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 69c5d09b6608484210870e1a69c51b112b497810
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>使用 Azure Site Recovery 複寫至次要網站的支援矩陣

本文將摘要說明使用 Azure Site Recovery 複寫至次要內部部署網站所支援的項目。

## <a name="deployment-options"></a>部署選項

**部署** | **VMware/實體伺服器** | **Hyper-V (含/不含 SCVMM)**
--- | --- | --- | ---
**Azure 入口網站** | 內部部署 VMware VM 至次要 VMware 網站。<br/><br/> 下載 [InMage Scout 使用者指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) (Azure 入口網站不提供)。 | VMM 雲端中的內部部署 Hyper-V VM 至次要 VMM 雲端。<br></br> 不支援沒有 VMM 的情況  <br/><br/> 僅限標準 Hyper-V 複寫。 不支援 SAN。
**傳統入口網站** | 僅限使用維護模式。 無法建立新的保存庫。 | 僅限使用維護模式<br></br> 不支援沒有 SCVMM 的情況
**PowerShell** | 不支援 | 支援<br></br> 不支援沒有 SCVMM 的情況

## <a name="on-premises-servers"></a>內部部署伺服器

### <a name="virtualization-servers"></a>虛擬化伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vSphere 6.0、5.5 或 5.1 (含最新更新)
**Hyper-V (含 VMM)** | VMM 2016 和 VMM 2012 R2

  >[!Note]
  > 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 VMM 2016 雲端。
  > 目前不支援包含將現有 SCVMM 2012 R2 升級至 2016 的設定。
### <a name="host-servers"></a>主機伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vCenter 5.5 或 6.0 (僅支援 5.5 功能)
**Hyper-V (無 VMM)** | 不是複寫至次要網站所支援的設定
**Hyper-V (含 VMM)** | 具有最新更新的 Windows Server 2016 和 Windows Server 2012 R2。<br/><br/> Windows Server 2016 主機應由 VMM 2016 所管理。

## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本
下表摘要說明使用 Azure Site Recovery 時所遇到各種部署案例中的作業系統支援。 這項支援適用於在上述 OS 中執行的任何工作負載。

**VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
64 位元的 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4 或 6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V 所支援的](https://technet.microsoft.com/library/mt126277.aspx)任何一種客體作業系統

>[!Note]
>只有具有下列儲存體的 Linux 機器可以複寫：檔案系統 (EXT3、ETX4、ReiserFS、XFS)；多重路徑軟體裝置對應工具；磁碟區管理員 (LVM2)。
>不支援使用 HP CCISS 控制站儲存體的實體伺服器。
>只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。

## <a name="network-configuration"></a>網路組態

### <a name="hosts"></a>主機

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 是 | 是
VLAN | 是 | 是
IPv4 | 是 | 是
IPv6 | 否 | 否

### <a name="guest-vms"></a>客體 VM

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NIC Teaming | 否 | 否
IPv4 | 是 | 是
IPv6 | 否 | 否
靜態 IP (Windows) | 是 | 是
靜態 IP (Linux) | 是 | 是
多個 NIC | 是 | 是


## <a name="storage"></a>儲存體

### <a name="host-storage"></a>主機儲存體

**儲存體 (主機)** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
NFS | 是 | N/A
SMB 3.0 | N/A | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO) | 是 | 是

### <a name="guest-or-physical-server-storage"></a>客體或實體伺服器儲存體

**組態** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
VMDK | 是 | N/A
VHD/VHDX | N/A | 是 (最多 16 個磁碟)
第 2 代 VM | N/A | 是
共用叢集磁碟 | 是  | 否
已加密磁碟 | 否 | 否
UEFI| 否 | N/A
NFS | 否 | 否
SMB 3.0 | 否 | 否
RDM | 是 | N/A
磁碟 > 1 TB | 否 | 是
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM | 是 | 是
儲存空間 | 否 | 是
熱新增/移除磁碟 | 否 | 否
排除磁碟 | 否 | 是
多重路徑 (MPIO) | N/A | 是

## <a name="vaults"></a>保存庫

**動作** | **VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | --- | ---
跨資源群組間移動保存庫 (在訂用帳戶之內或跨訂用帳戶) | 否 | 否
跨資源群組間移動儲存體、網路、Azure VM (在訂用帳戶之內或跨訂用帳戶) | 否 | 否

## <a name="provider-and-agent"></a>Provider 和代理程式

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 安裝於內部部署 VMM 伺服器上，或 Hyper-V 伺服器上 (若沒有 VMM 伺服器) | 5.1.19 ([可從入口網站取得](http://aka.ms/downloaddra)) | [最新功能和修正](https://support.microsoft.com/kb/3155002)
**行動服務** | 協調內部部署 VMware 伺服器或實體伺服器和次要網站之間的複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上  | N/A (可從入口網站取得) | N/A


## <a name="next-steps"></a>後續步驟

- [將 VMM 雲端中的 Hyper-V VM 複寫至次要網站](site-recovery-vmm-to-vmm.md)
- [將 VMware VM 和實體伺服器複寫至次要網站](site-recovery-vmware-to-vmware.md)

