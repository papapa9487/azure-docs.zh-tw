---
title: "使用 Azure Site Recovery 準備 Azure 資源將 Hyper-V VM (不含 System Center VMM) 複寫至 Azure | Microsoft Docs"
description: "說明您在使用 Azure Site Recovery 開始將 Hyper-V VM (不含 VMM) 複寫至 Azure 之前，在 Azure 中需要的項目"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 28fa722c-675e-4637-98eb-7ccbf3806d69
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: 1a30cadaab7e053184f0be133f1da5bfddc1fd91
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-to-azure"></a>步驟 5：準備要將 Hyper-V 複寫至 Azure 的 Azure 資源

使用本文章中的指示來準備 Azure 資源，讓您可以使用 [Azure Site Recovery](site-recovery-overview.md) 服務，將內部部署 Hyper-V VM (不含 System Center VMM) 複寫至 Azure。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。

## <a name="before-you-start"></a>開始之前

確認您已閱讀[必要條件](hyper-v-site-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>設定 Azure 帳戶

- 取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。
- 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
- 在 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)的＜各地區上市情況＞底下，查看 Site Recovery 的支援地區。
- 了解 [Site Recovery 定價](site-recovery-faq.md#pricing)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。


## <a name="set-up-an-azure-network"></a>設定 Azure 網路

- 設定 Azure 網路。 在容錯移轉之後建立的 Azure VM 會置於這個網路。
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

移至[步驟 6：準備 Hyper-V 資源](hyper-v-site-walkthrough-prepare-hyper-v.md)
