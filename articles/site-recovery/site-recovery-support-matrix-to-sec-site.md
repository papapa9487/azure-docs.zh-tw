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
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: da120d8e325867eaf9eb8b9be1ae8d9152db54c4
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>使用 Azure Site Recovery 複寫至次要網站的支援矩陣

本文將摘要說明使用 [Azure Site Recovery ](site-recovery-overview.md) 服務複寫至次要內部部署網站時所支援的項目。

## <a name="supported-scenarios"></a>支援的案例

**部署** | **詳細資料** 
--- | ---
**VMware 至 VMware** | 內部部署 VMware VM 至次要 VMware 網站的災害復原。<br/><br/> 下載 [InMage Scout 使用者指南](https://aka.ms/asr-scout-user-guide)
**Hyper-V 至 Hyper-V** | VMM 雲端中的內部部署 Hyper-V VM 至次要 VMM 雲端的災害復原。<br></br> 不支援沒有 VMM 的情況。



  

## <a name="host-servers"></a>主機伺服器

**部署** | **支援**
--- | ---
**VMware VM/實體伺服器** | vCenter 5.5、6.0 和 6.5 (僅支援 5.5 功能)
**Hyper-V (含 VMM)** | 具有最新更新的 Windows Server 2016 和 Windows Server 2012 R2。<br/><br/> Windows Server 2016 主機應由 VMM 2016 所管理。<br/><br/> 目前不支援混用 Windows Server 2016 和 2012 R2 主機的 VMM 2016 雲端。<br/><br/> 目前不支援包含將現有的 VMM 2012 R2 升級為 System Center 2016 的部署。


## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本

下表摘要說明使用 Site Recovery 複寫的機器支援的作業系統。 任何工作負載都可以在支援的作業系統上執行。

**VMware/實體伺服器** | **Hyper-V (含 VMM)**
--- | ---
64 位元的 Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少含 SP1)<br/><br/> Red Hat Enterprise Linux 6.7、6.8、6.9、7.1、7.2 <br/><br/> Centos 6.5、6.6、6.7、6.8、6.9、7.0、7.1、7.2 <br/><br/> Oracle Enterprise Linux 6.4、6.5 或 6.8，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3、11 SP4  | [Hyper-V 所支援的](https://technet.microsoft.com/library/mt126277.aspx)任何一種客體作業系統

## <a name="linux-machine-storage"></a>Linux 機器儲存體

只能複寫有下列儲存體的 Linux 機器：

- 檔案系統 (EXT3、ETX4、ReiserFS、XFS)。
- 多重路徑軟體裝置對應程式。
- 磁碟區管理員 (LVM2)。
- 不支援使用 HP CCISS 控制站儲存體的實體伺服器。
- 只有在 SUSE Linux Enterprise Server 11 SP3 上才支援 ReiserFS 檔案系統。

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
UEFI| 是 | N/A
NFS | 否 | 否
SMB 3.0 | 否 | 否
RDM | 是 | N/A
磁碟 > 1 TB | 是 | 是
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM | 是 | 是
儲存空間 | 否 | 是
熱新增/移除磁碟 | 是 | 否
排除磁碟 | 是 | 是
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

- [將 VMM 雲端中的 Hyper-V VM 複寫至次要網站](tutorial-vmm-to-vmm.md)
- [將 VMware VM 和實體伺服器複寫至次要網站](tutorial-vmware-to-vmware.md)
