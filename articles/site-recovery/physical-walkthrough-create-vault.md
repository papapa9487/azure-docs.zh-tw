---
title: "使用 Azure Site Recovery 針對實體伺服器到 Azure 的複寫設定保存庫 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 來設定保存庫以將實體伺服器複寫至 Azure 時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>步驟 6：針對實體伺服器到 Azure 的複寫設定保存庫


本文說明如何設定保存庫。 您將在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務來建立保存庫，並指定要將哪些項目從內部部署位置複寫到 Azure。


請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。




## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>選取保護目標

選取您要複寫的項目以及您要複寫到的位置。

1. 按一下 [復原服務保存庫] > 保存庫。
2. 在 [資源] 功能表中，按一下 [Site Recovery] > [準備基礎結構] > [保護目標]。
3. 在 [保護目標] 中，選取 [至 Azure] > [未虛擬化/其他]。


## <a name="next-steps"></a>後續步驟

移至[步驟 7：設定來源和目標](physical-walkthrough-source-target.md)
