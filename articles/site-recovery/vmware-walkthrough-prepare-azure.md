---
title: "準備 Azure 資源以使用 Azure Site Recovery 將內部部署 VMware VM 複寫至 Azure | Microsoft Docs"
description: "說明在使用 Azure Site Recovery 開始將內部部署 VMware VM 複寫至 Azure 之前，需要在 Azure 中備妥的項目"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>步驟 5：準備將 VMWare 複寫至 Azure 的 Azure 資源


使用本文中的指示來準備 Azure 資源，以便使用 [Azure Site Recovery](site-recovery-overview.md) 服務將內部部署機器複寫至 Azure。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="before-you-start"></a>開始之前

確認您已閱讀[必要條件](vmware-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>設定 Azure 帳戶

- 取得 [Microsoft Azure 帳戶](http://azure.microsoft.com/)。
- 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。
- 在 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)的＜各地區上市情況＞底下，查看 Site Recovery 的支援地區。
- 了解 [Site Recovery 定價](site-recovery-faq.md#pricing)，並取得[定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。



## <a name="set-up-an-azure-network"></a>設定 Azure 網路

- 設定 Azure 網路。 在容錯移轉之後建立的 Azure VM 會置於這個網路。
- Azure 入口網站中的 Site Recovery 可以使用在 [Resource Manager](../resource-manager-deployment-model.md) 中或傳統模式中設定的網路。
- 此網路應該位於與「復原服務」保存庫相同的區域
- 了解[虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network/)。
- 深入了解容錯移轉後的 [Azure VM 連線](site-recovery-network-design.md)。


## <a name="set-up-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

- Site Recovery 會將內部部署機器複寫至 Azure 儲存體。 在容錯移轉發生後，會從該儲存體建立 Azure VM。
- 為複寫的資料設定 [Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)。
- Azure 入口網站中的 Site Recovery 可以使用在 Resource Manager 中或傳統模式中設定的儲存體帳戶。
- 此儲存體帳戶可以是標準或[進階](../storage/common/storage-premium-storage.md)帳戶。
- 如果您設定的是進階帳戶，則也需要一個額外的標準帳戶來記錄資料。


## <a name="next-steps"></a>後續步驟

移至[步驟 6：準備 VMware 資源](vmware-walkthrough-prepare-vmware.md)
