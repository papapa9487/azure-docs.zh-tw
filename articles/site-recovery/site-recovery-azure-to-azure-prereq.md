---
title: "使用 Azure Site Recovery 複寫至 Azure 的必要條件 | Microsoft Docs"
description: "本文摘要說明使用 Azure Site Recovery 服務將 VM 和實體機器複寫至 Azure 的必要條件。"
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
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>使用 Azure Site Recovery 將 Azure 虛擬機器複寫至另一個區域的必要條件

> [!div class="op_single_selector"]
> * [從 Azure 複寫至 Azure](site-recovery-azure-to-azure-prereq.md)
> * [從內部部署複寫至 Azure](site-recovery-prereq.md)

Azure Site Recovery 是一項有助於建立商務持續性和災害復原 (BCDR) 策略的服務，可協調：
* 將 Azure 虛擬機器複寫到另一個 Azure 區域。
* 將內部部署實體伺服器與虛擬機器複寫至 Azure 或次要資料中心。 

當主要位置運作中斷時，您可以容錯移轉至次要位置，讓應用程式和工作負載保持可用。 當主要位置恢復正常作業時，您即可容錯回復至該位置。 如需有關 Site Recovery 的詳細資訊，請參閱[什麼是 Site Recovery？](site-recovery-overview.md)。

本文摘要說明開始進行從內部部署到 Azure 的 Site Recovery 複寫的必要條件。

在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="azure-requirements"></a>Azure 需求

**需求** | **詳細資料**
--- | ---
**Azure 帳戶** | [Microsoft Azure](http://azure.microsoft.com/) 帳戶。<br/><br/> 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
**Site Recovery 服務** | 如需有關 Site Recovery 價格的詳細資訊，請參閱 [Site Recovery 價格](https://azure.microsoft.com/pricing/details/site-recovery/)。 建議您在想要做為災害復原位置的目標 Azure 區域中建立復原服務保存庫。 例如，如果來源 VM 是在美國東部運作，而且您想要複寫到美國中部，建議您在美國中部建立保存庫。|
**Azure 容量** | 對於您想要做為災害復原位置的目標 Azure 區域，您需要有足夠容量可容納虛擬機器、儲存體帳戶和網路元件的訂用帳戶。 您可以連絡支援人員增加容量。
**儲存體指引** | 請確定您遵循來源 Azure 虛擬機器的[儲存體指引](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，以避免任何效能問題。 如果您遵循預設設定，Site Recovery 會根據來源組態建立所需的儲存體帳戶。 如果您自訂並選取您自己的設定，請確定您按照[虛擬機器磁碟的延展性目標](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)進行。
**網路服務指南** | 您需要將 Azure VM 對於特定 URL 或 IP 範圍的輸出連線能力列入白名單。 如需詳細資料，請參閱[複寫 Azure 虛擬機器的網路指引](site-recovery-azure-to-azure-networking-guidance.md)文章。
**Azure VM** | 確定 Windows 或 Linux VM 上有全部最新的根憑證。 如果沒有最新的根憑證，VM 會因安全性條件限制而無法註冊至 Site Recovery。

>[!NOTE]
>如需特定設定支援的詳細資料，請參閱[支援矩陣](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="next-steps"></a>後續步驟
- 深入了解[複寫 Azure 虛擬機器的網路指引](site-recovery-azure-to-azure-networking-guidance.md)。
- [複寫 Azure 虛擬機器](site-recovery-azure-to-azure.md)來開始保護您的工作負載。
