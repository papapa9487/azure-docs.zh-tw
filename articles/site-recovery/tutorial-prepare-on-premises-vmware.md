---
title: "準備內部部署 VMware 伺服器以進行 VMware VM 至 Azure 的災害復原| Microsoft Docs"
description: "了解如何準備內部部署 VMware 伺服器，以使用 Azure Site Recovery 服務來進行 Azure 的災害復原。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e59a78f2c348b581155484d77e272a050da1f1d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>準備內部部署 VMware 伺服器以進行 Azure 的災害復原

本教學課程說明如何在您想要將 VMware VM 複寫至 Azure 時，準備好內部部署 VMware 基礎結構。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 vCenter Server 或 vSphere ESXi 主機上，準備一個用來將 VM 探索自動化的帳戶
> * 準備一個用來在 VMware VM 上自動安裝行動服務的帳戶
> * 檢閱 VMware 伺服器需求
> * 檢閱 VMware VM 需求

在此教學課程系列中，我們說明如何使用 Azure Site Recovery 來備份單一 VM。 如果您想要保護多個 VMware VM，您應該下載用於 VMware 複寫的[部署規劃工具](https://aka.ms/asr-deployment-planner)。 此工具可讓您收集 VM 相容性、每個 VM 的磁碟及每個磁碟的資料變換等相關資訊。 此工具也涵蓋網路頻寬需求，以及要成功複寫和測試容錯移轉所需的 Azure 基礎結構。 [深入了解](site-recovery-deployment-planner.md)如何執行此工具。

這是系列中的第二個教學課程。 確定您已如上一個教學課程中所述[設定 Azure 元件](tutorial-prepare-azure.md)。

## <a name="prepare-an-account-for-automatic-discovery"></a>準備帳戶以進行自動探索

Site Recovery 需要存取 VMware 伺服器才能：

- 自動探索 VM。 需要至少一個唯讀帳戶。
- 協調複寫、容錯移轉和容錯回復。 您需要可執行建立和移除磁碟以及開啟 VM 電源等作業的帳戶。

遵循下列方式建立此帳戶：

1. 若要使用專用帳戶，請在 vCenter 層級建立一個角色。 指定角色的名稱，例如 **Azure_Site_Recovery**。
2. 將下表摘要說明的權限指派給角色。
3. 在 vCenter Server 或 vSphere 主機上建立使用者。 將角色指派給使用者。

### <a name="vmware-account-permissions"></a>VMware 帳戶權限

**Task** | **角色/權限** | **詳細資料**
--- | --- | ---
**VM 探索** | 至少是唯讀使用者<br/><br/> 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。
**完整複寫、容錯移轉、容錯回復** |  建立具有必要權限的角色 (Azure_Site_Recovery)，然後將角色指派給 VMware 使用者或群組<br/><br/> 資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。

## <a name="prepare-an-account-for-mobility-service-installation"></a>準備一個用來安裝行動服務的帳戶

行動服務必須安裝在您要複寫的 VM 上。 當您啟用 VM 的複寫功能時，Site Recovery 會自動安裝此服務。 若要自動安裝，您必須準備一個可供 Site Recovery 用來存取 VM 的帳戶。 當您在 Azure 主控台中設定災害復原時，您會指定此帳戶。

1. 準備有權限可以在 VM 上安裝的網域或本機帳戶。
2. 若要在 Windows VM 上安裝，如果您不使用網域帳戶，請停用本機電腦上的遠端使用者存取控制。
   - 從登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，其值為 1。
3. 若要在 Linux VM 上安裝，請在來源 Linux 伺服器上準備根帳戶。


## <a name="check-vmware-server-requirements"></a>檢查 VMware 伺服器需求

確定 VMware 伺服器符合下列需求。

**元件** | **需求**
--- | ---
**vCenter Server** | vCenter 6.5、6.0 或 5.5
**vSphere 主機** | vSphere 6.5、6.0、5.5

## <a name="check-vmware-vm-requirements"></a>檢查 VMware VM 需求

確定 VM 符合下表摘要說明的 Azure 需求。

**VM 需求** | **詳細資料**
--- | ---
**作業系統磁碟大小** | 最多 2048 GB。
**作業系統磁碟計數** | 1
**資料磁碟計數** | 64 或以下
**資料磁碟 VHD 大小** | 最多 4095 GB
**網路介面卡** | 支援多個介面卡
**共用 VHD** | 不支援
**FC 磁碟** | 不支援
**硬碟格式** | VHD 或 VHDX。<br/><br/> 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，VM 仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援。 先停用再啟用 VM 的複寫功能。
**VM 名稱** | 介於 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。
**VM type** | 第 1 代 - Linux 或 Windows<br/><br/>第 2 代 - 僅限 Windows

VM 必須執行支援的作業系統。 如需支援版本的完整清單，請參閱 [Site Recovery 支援矩陣](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

在容錯移轉案例期間，您可能想要從內部部署網路連線到 Azure 中已複寫的 VM。

若要在容錯移轉後使用 RDP 連線到 Windows VM，請執行下列作業：

1. 若要透過網際網路存取，請在內部部署 VM 上啟用 RDP，再進行容錯移轉。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。
2. 若要透過站對站 VPN 存取，請在內部部署機器上啟用 RDP。 您應該在 [Windows 防火牆] -> [允許的應用程式與功能] 中，針對 [網域] 和 [私人] 網路允許 RDP。
   確認作業系統的 SAN 原則已設為 [OnlineAll]。 [深入了解](https://support.microsoft.com/kb/3031135)。 觸發容錯移轉時，VM 上不應該有任何暫止的 Windows 更新。 如果有，在更新完成之前，您將無法登入虛擬機器。
3. 在容錯移轉之後，於 Windows Azure VM 上，核取 [開機診斷] 以檢視 VM 的螢幕擷取畫面。 如果您無法連線，請檢查 VM 是否正在執行，並檢閱這些[疑難排解祕訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

若要在容錯移轉後使用 SSH 連線到 Linux VM，請執行下列作業：

1. 在容錯移轉之前，於內部部署機器上，確認安全殼層服務已設定為在系統開機時自動啟動。 確認防火牆規則允許 SSH 連線。

2. 在容錯移轉之後，於 Azure VM 上，針對已容錯移轉之 VM 上的網路安全性群組規則及其所連線的 Azure 子網路，允許 SSH 連接埠的連入連線。
   [新增 VM 的公用 IP 位址](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。 您可以核取 [開機診斷] 以檢視 VM 的螢幕擷取畫面。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為 VMware VM 設定 Azure 的災害復原](tutorial-vmware-to-azure.md)

