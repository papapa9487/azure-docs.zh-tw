---
title: "準備內部部署 VMware 資源以使用 Azure Site Recovery 來複寫至 Azure | Microsoft Docs"
description: "摘要說明將 VMware VM 上執行的工作負載複寫至 Azure 儲存體所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>步驟 6：準備將內部部署 VMWare 複寫至 Azure

使用本文中的指示來準備內部部署 VMware 伺服器以與 Azure Site Recovery 進行互動，以及準備 VMWare VM 來安裝行動服務。 行動服務必須安裝在您要複寫至 Azure 的所有內部部署 VM 上。

## <a name="prepare-for-automatic-discovery"></a>為自動探索做準備

Site Recovery 會自動探索在 vSphere ESXi 主機 (具有或不具有 vCenter 伺服器) 上執行的虛擬機器。 如需自動探索，Site Recovery 需要可存取主機與伺服器的帳戶：

1. 若要使用專用帳戶，請在 vCenter 層級建立具有下表中所述權限的角色。 指定名稱，例如 **Azure_Site_Recovery**。
2. 然後，在 vSphere 主機/vCenter 伺服器上建立使用者，並將角色指派給該使用者。 您在 Site Recovery 部署期間指定此使用者帳戶。


### <a name="vmware-account-permissions"></a>VMware 帳戶權限

Site Recovery 需要存取 VMware，才能讓處理序伺服器自動探索 VM，以及容錯移轉和容錯回復 VM。

- **移轉**︰如果您只想將 VMware VM 移轉至 Azure，而且絕不會容錯回復，您可以使用具有唯讀角色的 VMware 帳戶。 這種角色可以執行容錯移轉，但不能關閉受保護的來源電腦。 移轉時不需要這樣。
- **複寫/復原**︰如果您想要部署完整複寫 (複寫、容錯移轉、容錯回復)，則帳戶必須能夠執行建立和移除磁碟、啟動 VM 等作業。
- **自動探索**︰需要至少一個唯讀帳戶。


**Task** | **必要的帳戶/角色** | **權限** | **詳細資料**
--- | --- | --- | ---
**處理序伺服器自動探索 VMware VM** | 您需要至少一個唯讀使用者 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。
**容錯移轉** | 您需要至少一個唯讀使用者 | 資料中心物件 –> 傳播至子物件、role=Read-only | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。<br/><br/> 適用於移轉用途，而不是完整複寫、容錯移轉、容錯回復。
**容錯移轉和容錯回復** | 建議您建立具有必要權限的角色 (Azure_Site_Recovery)，然後將角色指派給 VMware 使用者或群組 | 資料中心物件 –> 傳播至子物件、role=Azure_Site_Recovery<br/><br/> 資料存放區 -> 配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案<br/><br/> 網路 -> 網路指派<br/><br/> 資源 -> 指派 VM 至資源集區、移轉已關閉電源的 VM、移轉已開啟電源的 VM<br/><br/> 工作 -> 建立工作、更新工作<br/><br/> 虛擬機器 -> 組態<br/><br/> 虛擬機器 -> 互動 -> 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝<br/><br/> 虛擬機器 -> 清查 -> 建立、註冊、取消註冊<br/><br/> 虛擬機器 -> 佈建 -> 允許虛擬機器下載、允許虛擬機器檔案上傳<br/><br/> 虛擬機器 -> 快照 -> 移除快照 | 在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。<br/><br/> 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 及網路)。


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>為行動服務的推入安裝做準備

行動服務必須安裝在您要複寫的所有 VM 上。 有幾個方式可以安裝此服務，包括手動安裝、從 Site Recovery 處理伺服器推入安裝，以及使用 System Center Configuration Manager 之類的方法來進行安裝。

如果您想要使用推入安裝，就必須準備一個可供 Site Recovery 用來存取 VM 的帳戶。

- 您可以使用網域或本機帳戶
- 在 Windows 上，如果您不使用網域帳戶，則必須停用本機電腦上的遠端使用者存取控制。 若要這樣做，請在登錄的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，新增 DWORD 項目 **LocalAccountTokenFilterPolicy**，值為 1。
- 如果您想要從 CLI 新增適用於 Windows 的登錄項目，請輸入︰``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- 在 Linux 上，帳戶應該是來源 Linux 伺服器上的根使用者。



## <a name="next-steps"></a>後續步驟

移至[步驟 7：建立保存庫](vmware-walkthrough-create-vault.md)
