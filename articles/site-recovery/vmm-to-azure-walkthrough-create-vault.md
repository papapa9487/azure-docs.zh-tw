---
title: "使用 Azure Site Recovery 設定將 Hyper-V (含 System Center VMM) 複寫至 Azure 的保存庫 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 設定將 Hyper-V (含 VMM) 複寫至 Azure 的保存庫所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>步驟 7：設定 Hyper-V 複寫的保存庫

本文說明如何使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務來設定保存庫，並指定要從內部部署位置複寫到 Azure 的項目。


請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源功能表] 中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取 [至 Azure] > [是，利用 Hyper-V]。 選取 [是] 以確認您未使用 VMM。 

     ![選擇目標](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>後續步驟

移至[步驟 8：設定來源和目標](vmm-to-azure-walkthrough-source-target.md)
