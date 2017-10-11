---
title: "使用 Azure Site Recovery 將實體伺服器複寫至 Azure 的必要條件 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務將在實體 Windows/Linux 伺服器上執行的工作負載複寫至 Azure 的必要條件。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 5f5cb4b9b6fcee6b56ccdcb6223afddd9de90fec
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-2-review-the-prerequisites-for-physical-server-to-azure-replication"></a>步驟 2：檢閱將實體伺服器複寫至 Azure 的必要條件

檢閱下表中摘要列出的必要條件。


**必要條件** | **詳細資料**
--- | ---
**Azure** | 了解 [Azure 需求](site-recovery-prereq.md#azure-requirements)
**內部部署組態伺服器** | 您需要一部執行 Windows Server 2012 R2 或更新版本的實體伺服器 (或 VMware VM)。 您在 Site Recovery 部署期間設定此伺服器。<br/><br/> 根據預設，處理序伺服器與主要目標伺服器也會安裝在此機器上。 相應增加時，您可能需要另一台處理序伺服器。 如果這樣做，則要符合與設定伺服器相同的需求。<br/><br/> [深入了解](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)。
**內部部署 VM** | 您想要複寫的電腦需執行[支援的作業系統](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)，並且要符合 [Azure 必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
**URL** | 設定伺服器需要存取這些 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> 如果您有以 IP 位址為基礎的防火牆規則，請確定這些規則允許對 Azure 的通訊。<br/></br> 允許 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 連接埠。<br/></br> 允許訂用帳戶的 Azure 區域和美國西部使用 IP 位址範圍 (用於存取控制和身分識別管理)。<br/><br/> 允許使用此 URL 下載 MySQL：http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
**行動服務** | 安裝在每部複寫的伺服器上。




## <a name="limitations"></a>限制

部署之前，請注意下表中摘要列出的限制。

**限制** | **詳細資料**
--- | ---
**Azure** | 儲存體和網路帳戶必須位於與保存庫相同的區域。<br/><br/> 如果您使用進階儲存體帳戶，則也需要有標準儲存體帳戶來儲存複寫記錄檔<br/><br/> 您無法複寫到印度中部和南部的進階帳戶。
**內部部署組態伺服器** | 如果您使用 VMware VM 作為組態伺服器機器，則 VMware VM 配接器類型應該是 VMXNET3。 如果不是，請[安裝此更新](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> 針對 VMware VM，應該安裝 vSphere PowerCLI 6.0。<br/><br> 電腦不應該是網域控制站。<br/><br/> 電腦應有靜態 IP 位址。<br/><br/> 主機名稱應該是 15 個字元或更少，而且作業系統應該是英文版。
**複寫機器** | 確認 [Azure VM 限制](site-recovery-prereq.md#azure-requirements)<br/><br/> 您無法複寫具有加密磁碟的 VM 或具有 UEFI/EFI 開機的 VM。<br/><br> 不支援共用磁碟叢集。 如果來源 VM 有 NIC Teaming，則在容錯移轉之後會轉換成單一 NIC。<br/><br/> 如果 VM 有 iSCSI 磁碟，Site Recovery 會在容錯移轉之後將它轉換成 VHD 檔案。 如果 Azure VM 可觸達 iSCSI 目標，則會連接它，然後同時看見它和 VHD。 如果發生這種情況，請中斷連接 iSCSI 目標。<br/><br/> 如果您想要啟用多 VM 一致性 (這可讓執行相同工作負載的 VM 一起復原到一致的資料點)，請開啟 VM 上的連接埠 20004。<br/><br/> Windows 必須安裝在 C 磁碟機。 OS 磁碟應該是基本磁碟，而非動態磁碟。 資料磁碟可以為動態。<br/><br/> VM 上的 Linux /etc/hosts 檔案中應該有項目，將本機主機名稱對應至所有網路介面卡相關聯的 IP 位址。 主機名稱、掛接點、裝置名稱、系統路徑和檔案名稱 (/etc; /usr) 只能是英文。<br/><br/> 支援特定類型的 [Linux 儲存體](site-recovery-support-matrix-to-azure.md#support-for-storage)。<br/><br/>在 VM 設定中建立或設定 **disk.enableUUID=true**。 這樣可提供一致的 UUID 給 VMDK，以便裝載正確，還可確保在容錯回復期間，只有差異變更會傳輸回到內部部署，而不需要完整複寫。


## <a name="next-steps"></a>後續步驟

- 如果您要進行完整部署，請移至[步驟 3：規劃容量](physical-walkthrough-capacity.md)
- 如果您要進行簡單的測試部署，請移至[步驟 4：規劃網路](physical-walkthrough-network.md)。
