---
title: "使用 Azure Site Recovery 設定區域之間的 Azure VM 保存庫 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 針對 Azure 區域之間的 Azure 複寫設定保存庫時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>步驟 4：針對 Azure 到 Azure 的複寫設定保存庫

[規劃網路](azure-to-azure-walkthrough-network.md)之後，使用本文章來設定保存庫，讓 Azure 虛擬機器 (VM) 使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務複寫到另一個 Azure 區域。

- 當您完成本文時，應該已設定復原服務保存庫。
- 請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。



>[!NOTE]
>
> Azure VM 複寫目前為預覽狀態。




## <a name="create-a-vault"></a>建立保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> 建議您在要 VM 複寫的位置中，建立復原服務保存庫。 例如，如果您的目標位置是美國中部、請在**美國中部**建立保存庫。


## <a name="next-steps"></a>後續步驟

移至[步驟 5：啟用複寫](azure-to-azure-walkthrough-enable-replication.md)
