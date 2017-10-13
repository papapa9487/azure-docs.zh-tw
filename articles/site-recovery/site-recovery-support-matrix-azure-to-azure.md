---
title: "用於從 Azure 複寫至 Azure 的 Azure Site Recovery 支援矩陣 | Microsoft Docs"
description: "摘要說明支援的作業系統，以及 Azure 虛擬機器 (VM) 的 Azure Site Recovery 複寫設定 (對於嚴重損壞修復 (DR) 需求，從一個區域複寫至另一個區域)。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: f52520f88d36914d4cad7c8aea4fb21e44b3cc9d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>用於從 Azure 複寫至 Azure 的 Azure Site Recovery 支援矩陣


>[!NOTE]
>
> Azure 虛擬機器的 Site Recovery 複寫目前為預覽狀態。

本文摘要說明在從一個區域將 Azure 虛擬機器複寫和復原另一個區域時， Azure Site Recovery 支援的組態和元件。

## <a name="user-interface-options"></a>使用者介面選項

**使用者介面** |  **支援 / 不支援**
--- | ---
**Azure 入口網站** | 支援
**傳統入口網站** | 不支援
**PowerShell** | 目前不支援
**REST API** | 目前不支援
**CLI** | 目前不支援


## <a name="resource-move-support"></a>資源移動支援

**資源移動類型** | **支援 / 不支援** | **備註**  
--- | --- | ---
**在資源群組間移動保存庫** | 不支援 |您無法跨越資源群組移動復原服務保存庫。
**跨越資源群組移動運算、 儲存體和網路** | 不支援 |如果您在啟用複寫之後移動虛擬機器 (或其相關聯的元件，例如儲存體和網路)，則您需要對於虛擬機器停用複寫，並再次啟用複寫。



## <a name="support-for-deployment-models"></a>部署模型的支援

**部署模型** | **支援 / 不支援** | **備註**  
--- | --- | ---
**傳統** | 支援 | 您只能複寫傳統的虛擬機器，並將它復原為傳統的虛擬機器。 您無法將它復原為資源管理員虛擬機器。 不使用虛擬網路部署傳統 VM，而直接部署到 Azure 地區，是不受支援的做法。
**資源管理員** | 支援 |

>[!NOTE]
>
> 1. 若為嚴重損壞修復案例，不支援從一個訂用帳戶將 Azure 虛擬機器複寫至另一個訂用帳戶。
> 2. 不支援在訂用帳戶之間移轉 Azure 虛擬機器。
> 3. 不支援在同一個區域內移轉 Azure 虛擬機器。
> 4. 不支援從傳統部署模型將 Azure 虛擬機器移轉至資源管理員部署模型。

## <a name="support-for-replicated-machine-os-versions"></a>支援多種複寫機器作業系統版本

下列支援適用於在上述 OS 中執行的任何工作負載。

#### <a name="windows"></a>Windows

- Windows Server 2016 (伺服器核心和含有桌面體驗的伺服器)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 (至少加裝 SP1)

>[!NOTE]
>
> 不支援 \* Windows Server 2016 Nano Server。

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7、6.8、6.9、7.0、7.1、7.2、7.3
- CentOS 6.5、6.6、6.7、6.8、6.9、7.0、7.1、7.2、7.3
- Ubuntu 14.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(不支援 SLES 11 SP3 至 SLES 11 SP4 的複寫電腦升級。 若已將複寫電腦從 SLES 11SP3 升級至 SLES 11 SP4，則您必須停用複寫以在升級後重新提供電腦防護。）

>[!NOTE]
>
> 使用密碼型驗證和登入並使用雲端 init 封裝來設定雲端虛擬機器的 Ubuntu 伺服器，可能對容錯移轉停用密碼型登入 (取決於 cloudinit 組態)。從 Azure 入口網站上容錯移轉的虛擬機器本身的設定功能表 (在 [支援 + 疑難排解] 區段下) 重設密碼，即可重新啟用密碼型登入。

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 虛擬機器支援的 Ubuntu 核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic 至 3.13.0-117-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic 至 3.13.0-121-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 至 3.13.0-125-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-83-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 至 4.4.0-81-generic、<br/>4.8.0-34-generic 至 4.8.0-56-generic、<br/>4.10.0-14-generic 至 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 至 4.4.0-83-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Azure 虛擬機器 (執行 Linux OS) 上的支援檔案系統與客體儲存體組態

* 檔案系統：ext3、ext4、ReiserFS (僅 Suse Linux Enterprise Server)、XFS
* 磁碟區管理員：LVM2
* 多重路徑軟體：裝置對應程式

## <a name="region-support"></a>區域支援

您可以複製和復原相同的地理叢集內任何兩個區域之間的 VM。

**地理叢集** | **Azure 區域**
-- | --
美洲 | 加拿大東部、加拿大中部、美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2、美國中部、美國中北部
歐洲 | 英國西部、英國南部、北歐、西歐
亞洲 | 印度南部、印度中部、東南亞、東亞、日本東部、日本西部、韓國中部、韓國南部
澳大利亞   | 澳大利亞東部、澳大利亞東南部

>[!NOTE]
>
> 對於巴西南部區域，您只能複寫及容錯移轉至美國中南部、美國中西部、美國東部、美國東部 2、美國西部、美國西部 2 和美國中北部區域的其中一個區域，並進行容錯回復。


## <a name="support-for-compute-configuration"></a>計算設定的支援

**組態** | **支援/不支援** | **備註**
--- | --- | ---
大小 | 至少 2 顆 CPU 核心和 1 GB RAM 的任何 Azure VM 大小 | 請參閱 [Azure 虛擬機器大小](../virtual-machines/windows/sizes.md)
可用性設定組 | 支援 | 您在入口網站中進行「啟用複寫」步驟期間，如果使用預設選項，將根據來源地區設定自動建立可用性設定組。 您可以隨時變更 [複寫項目] > [設定] > [計算與網路] > [可用性設定組] 中的目標可用性設定組。
Hybrid Use Benefit | 支援 | 如果來源 VM 已啟用 HUB 授權，測試容錯移轉或容錯移轉 VM 也會使用 HUB 授權。
虛擬機器擴展集 | 不支援 |
Azure 資源庫映像 - Microsoft 發行 | 支援 | 只要 VM 在 Site Recovery 支援的作業系統上執行即支援
Azure 資源庫映像 - 第三方發行 | 支援 | 只要 VM 在 Site Recovery 支援的作業系統上執行即支援。
自訂映像 - 第三方發行 | 支援 | 只要 VM 在 Site Recovery 支援的作業系統上執行即支援。
使用 Site Recovery 移轉 VM | 支援 | 如果使用 Site Recovery 將 VMware/實體機器移轉到 Azure，您需要解除安裝舊版的行動服務，然後重新啟動電腦，再複寫到另一個 Azure 區域。

## <a name="support-for-storage-configuration"></a>儲存體設定的支援

**組態** | **支援/不支援** | **備註**
--- | --- | ---
最大的作業系統磁碟大小 | 1023 GB | 請參閱 [VM 使用的磁碟](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)。
最大的資料磁碟大小 | 1023 GB | 請參閱 [VM 使用的磁碟](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)。
資料磁碟數量 | 特定 Azure VM 大小支援多達 64 個 | 請參閱 [Azure 虛擬機器大小](../virtual-machines/windows/sizes.md)
暫存磁碟 | 一律排除在複寫之外 | 暫存磁碟排除在複寫之外。 根據 Azure 指引，您不應該在暫存磁碟上放置任何持續性資料。 如需詳細資訊，請參閱 [Azure VM 上的暫存磁碟](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk)。
磁碟上的資料變更率 | 各磁碟資料變更率最高達 6 MBps | 如果磁碟的平均資料變更率持續超過 6 MBps，複寫將趕不上進度。 不過，如果是偶爾出現的資料高載，而且資料變更率有時超過 6 MBps 後便下降，則複寫會趕上進度。 在此情況下，您可能會發現復原點稍微延遲。
標準儲存體帳戶上的磁碟 | 支援 |
進階儲存體帳戶上的磁碟 | 支援 | 如果 VM 的磁碟分散於進階和標準儲存體帳戶，您可以對於各個磁碟選取不同的目標儲存體帳戶，以確保目標區域有相同的儲存體設定
標準受控磁碟 | 不支援 |  
進階受控磁碟 | 不支援 |
儲存體空間 | 支援 |         
待用加密 (SSE) | 支援 | 對於快取和目標儲存體帳戶，您可以選取 SSE 啟用的儲存體帳戶。     
Azure 磁碟加密 (ADE) | 不支援 |
熱新增/移除磁碟 | 不支援 | 如果您在 VM 上新增或移除資料磁碟，需要停用複寫，然後再次為 VM 啟用複寫。
排除磁碟 | 不支援|   預設排除暫存磁碟。
LRS | 支援 |
GRS | 支援 |
RA-GRS | 支援 |
ZRS | 不支援 |  
非經常性和經常性儲存體 | 不支援 | 非經常性和經常性儲存體不支援虛擬機器磁碟

>[!IMPORTANT]
> 請確定您遵循來源 Azure 虛擬機器的[儲存體指引](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，以避免任何效能問題。 如果您遵循預設設定，Site Recovery 會根據來源組態建立所需的儲存體帳戶。 如果您自訂並選取您自己的設定，請確定您維持 (../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) 作為來源 VM。

## <a name="support-for-network-configuration"></a>網路組態的支援
**組態** | **支援/不支援** | **備註**
--- | --- | ---
網路介面 (NIC) | 特定 Azure VM 大小支援的 NIC 數目上限 | 在測試容錯移轉或容錯移轉作業中建立 VM 時，會建立 NIC。 VM 容錯移轉的 NIC 數目取決於啟用複寫時來源 VM 具有的 NIC 數量。 啟用複寫後，如果您新增/移除 NIC，不會影響容錯移轉 VM 上的 NIC 計數。
網際網路負載平衡器 | 支援 | 您需要使用復原方案中的 azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
內部負載平衡器 | 支援 | 您需要使用復原方案中的 azure 自動化指令碼，使預先設定的負載平衡器產生關聯。
公用 IP| 支援 | 您需要使現有公用 IP 與 NIC 產生關聯，或建立一個 IP 並使用復原方案中的 azure 自動化指令碼與 NIC 產生關聯。
NIC 上的 NSG (資源管理員)| 支援 | 您需要使用復原方案中的 azure 自動化指令碼，使 NSG 與 NIC 產生關聯。  
子網路上的 NSG (資源管理員與傳統)| 支援 | 您需要使用復原方案中的 azure 自動化指令碼，使 NSG 與 NIC 產生關聯。
VM 上的 NSG (傳統)| 支援 | 您需要使用復原方案中的 azure 自動化指令碼，使 NSG 與 NIC 產生關聯。
保留的 IP (靜態 IP) / 保留來源 IP | 支援 | 如果來源 VM 上的 NIC 有靜態 IP 組態，而目標子網路有相同的 IP，則會將它指派給容錯移轉 VM。 如果目標子網路沒有相同的 IP，子網路中一個可用的 IP 會被保留供 VM 使用。 您可以在 [複寫項目] > [設定] > [計算與網路] > [網路介面] 中指定選擇的固定 IP。 您可以選取 NIC，並指定您選擇的子網路和 IP。
動態 IP| 支援 | 如果來源 VM 上的 NIC 有動態 IP 組態，則容錯移轉 VM 上的 NIC 預設也是動態。 您可以在 [複寫項目] > [設定] > [計算與網路] > [網路介面] 中指定選擇的固定 IP。 您可以選取 NIC，並指定您選擇的子網路和 IP。
流量管理員整合 | 支援 | 您可以預先設定流量管理員，定期將流量傳輸到來源區域中的端點，如果發生容錯移轉，則傳輸到目標區域中的端點。
Azure 受管理 DNS | 支援 |
自訂 DNS  | 支援 |    
未經驗證的 Proxy | 支援 | 請參閱[網路指引文件](site-recovery-azure-to-azure-networking-guidance.md)。    
經驗證的 Proxy | 不支援 | 如果 VM 對於輸出連線能力使用經驗證的 Proxy，則無法使用 Azure Site Recovery 加以複寫。    
內部部署的網站對網站 VPN 與 (不論是否有 ExpressRoute)| 支援 | 請確定設定 UDR 和 NSG 時，站台復原流量不是傳送到內部部署的裝置。 請參閱[網路指引文件](site-recovery-azure-to-azure-networking-guidance.md)。  
VNET 對 VNET 連線 | 支援 | 請參閱[網路指引文件](site-recovery-azure-to-azure-networking-guidance.md)。  


## <a name="next-steps"></a>後續步驟
- 深入了解[複寫 Azure VM 的網路指引](site-recovery-azure-to-azure-networking-guidance.md)
- [複寫 Azure VM](site-recovery-azure-to-azure.md) 開始保護您的工作負載
