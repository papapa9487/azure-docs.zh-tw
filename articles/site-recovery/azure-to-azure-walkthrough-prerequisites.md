---
title: "在您開始將 Azure VM 複寫到另一個區域之前 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務，在 Azure 區域之間複寫 Azure VM 之前，所需採取的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-before-you-start"></a>步驟 2：開始之前

在您已檢閱使用 [Azure Site Recovery](site-recovery-overview.md)在 Azure 區域之間複寫 Azure 虛擬機器 (VM) 的[架構](azure-to-azure-walkthrough-architecture.md)之後，請使用這份文件來確認先決條件。

- 當您完成文件時，應該清楚了解部署工作所需條件，並已完成必要條件步驟。
- 請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。

>[!NOTE]
>
> Azure VM 複寫目前為預覽狀態。



## <a name="support-recommendations"></a>支援建議

請檢閱下表。 在[支援矩陣](site-recovery-support-matrix-azure-to-azure.md)中取得支援需求的完整清單。

**元件** | **需求**
--- | ---
**復原服務保存庫** | 建議您在要用來作為災害復原的目標 Azure 區域中，建立復原服務保存庫。 例如，如果您需要將美國東部的來源 VM 複寫到美國中部，請在美國中部建立保存庫。
**Azure 訂用帳戶** | 必須啟用您的 Azure 訂用帳戶，才能在要作為災害復原區域的目標位置中建立 VM。 請連絡支援人員啟用所需的配額。
**目標區域產能** | 在目標 Azure 區域中，訂用帳戶應該有足夠的容量可供 VM、儲存體帳戶和網路元件使用。
**儲存體** | 使用您來源 Azure VM 的 [儲存體指引](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，以避免效能問題。<br/><br/> 儲存體帳戶必須位於與保存庫相同的區域。<br/><br/> 您無法複寫到印度中部和南部的進階帳戶。<br/><br/> 如果您使用預設設定來部署複寫，Site Recovery 會以來源設定作為基礎建立所需的儲存體帳戶。 如果您要自訂設定，請遵循 [VM 磁碟的延展性目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)。
**網路功能** | 您必須允許來自 Azure VM 適用於特定 URL/IP 範圍的輸出連線能力。<br/><br/> 網路帳戶必須位於與保存庫相同的區域。
**Azure VM** | 請確定所有最新的根憑證位於 Windows/Linux Azure VM 上。 如果他們不這樣做，由於安全性限制，您將無法在 Site Recovery 中註冊 VM。
**Azure 使用者帳戶** | 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能啟用 Azure 虛擬機器的複寫功能。


## <a name="set-permissions-on-the-account"></a>設定帳戶的權限

1. 閱讀有關您進行複寫所需的[權限](site-recovery-role-based-linked-access-control.md)。
2. 遵循這些[指引](../active-directory/role-based-access-control-configure.md#add-access)來新增權限。


## <a name="verify-target-resources"></a>驗證目標資源

1. 啟用 Azure 訂用帳戶可讓您在要用來作為災害復原區域從而用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。
2. 請確定您的訂用帳戶具有足夠的資源，可讓 VM 的大小符合您的來源 VM。 根據預設，當設定複寫時，Site Recovery 會挑選相同大小的目標 VM，或最接近的大小。 深入了解針對目標資源進行[疑難排解](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097)。

## <a name="verify-azure-vm-certificates"></a>驗證 Azure VM 憑證

1. 檢查您要複寫的 Windows 或 Linux VM 上有全部最新的根憑證。 如果沒有最新的根憑證，VM 會因安全性條件限制而無法註冊至 Site Recovery。
2. 若為 Windows VM，請執行下列作業：

    - 在 VM 上安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。
    - 在中斷連線的環境中，請遵循貴組織的標準 Windows Update 流程/憑證更新流程，在 VM 上取得最新的根憑證和更新的 CRL。
3. 若為 Linux VM，請遵循 Linux 散發者提供的指引，取得最新的受信任根憑證以及 VM 上最新的憑證撤銷清單。 深入了解針對受信任根發行進行[疑難排解](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066)。


## <a name="next-steps"></a>後續步驟

移至[步驟 3：規劃網路服務](azure-to-azure-walkthrough-network.md)可設定輸出連線能力。
