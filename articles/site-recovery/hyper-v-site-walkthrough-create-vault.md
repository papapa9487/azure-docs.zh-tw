---
title: "使用 Azure Site Recovery 設定將 Hyper-V (不含 System Center VMM) 複寫至 Azure 的保存庫 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 設定將 Hyper-V 複寫至 Azure 的保存庫所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
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
3. 在 [保護目標] 中，選取 [至 Azure] > [是，利用 Hyper-V]。 選取 [否]  以確認您未使用 VMM。 

    ![選擇目標](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>後續步驟

移至[步驟 8：設定來源和目標](hyper-v-site-walkthrough-source-target.md)
