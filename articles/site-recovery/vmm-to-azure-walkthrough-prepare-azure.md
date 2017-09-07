---
title: "使用 Azure Site Recovery 準備 Azure 資源將 Hyper-V VM (含 System Center VMM) 複寫至 Azure | Microsoft Docs"
description: "說明您在使用 Azure Site Recovery 開始將 Hyper-V VM (含 VMM) 複寫至 Azure 之前，在 Azure 中需要的項目"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 63b005f37ab5e15e8a1b4645446d65f1529f1bbd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>步驟 5：準備將 Hyper-V (含 VMM) 複寫至 Azure 的 Azure 資源

[網路需求](vmm-to-azure-walkthrough-network.md)驗證過後，請依本文指示準備 Azure 資源，以使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中的內部部署 Hyper-V VM 複寫至 Azure。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="set-up-an-azure-account"></a>設定 Azure 帳戶

- 取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。
- 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
- 在 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)的＜各地區上市情況＞底下，查看 Site Recovery 的支援地區。
- 了解 [Site Recovery 定價](site-recovery-faq.md#pricing)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 請確定您的 Azure 帳戶有適當的[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)可建立 Azure VM。 [深入了解](../active-directory/role-based-access-built-in-roles.md) Azure 角色型存取控制。


## <a name="set-up-an-azure-network"></a>設定 Azure 網路

- 設定 [Azure 網路](../virtual-network/virtual-network-get-started-vnet-subnet.md)。 在容錯移轉之後建立的 Azure VM 會置於這個網路。
- 此網路應位於與復原服務保存庫相同的區域
- Azure 入口網站的 Site Recovery 可以在 [Resource Manager](../resource-manager-deployment-model.md) 中，或以傳統模式使用網路設定。
- 建議您在開始之前先設定網路。 若非如此，則必須在 Site Recovery 部署期間這麼做。
- 深入了解[虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network/)。


## <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- Site Recovery 會將內部部署機器複寫至 Azure 儲存體。 容錯移轉發生後，會從儲存體建立 Azure VM。
- 設定標準/進階 [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)來保存複寫到 Azure 的資料。
- [進階儲存體](../storage/common/storage-premium-storage.md)通常是用於需要持續高 IO 效能和低延遲性以裝載 IO 密集型工作負載的虛擬機器。
- 如果您想要使用進階帳戶來儲存複寫的資料，就也需要標準儲存體帳戶來儲存複寫記錄檔，這些記錄檔會擷取內部部署資料的進行中變更。
- 視您想要針對已容錯移轉的 Azure VM 使用的資源模型而定，您會以 [Resource Manager 模式](../storage/common/storage-create-storage-account.md)，或[傳統模式](../storage/common/storage-create-storage-account.md)設定帳戶。
- 建議您在開始之前先設定儲存體帳戶。 若非如此，則必須在 Site Recovery 部署期間這麼做。 帳戶必須位於與復原服務保存庫相同的區域中。
- 您無法跨相同訂用帳戶內的資源群組，或跨越不同訂用帳戶移動 Site Recovery 所使用的儲存體帳戶。


## <a name="next-steps"></a>後續步驟

移至[步驟 6：準備 VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)

