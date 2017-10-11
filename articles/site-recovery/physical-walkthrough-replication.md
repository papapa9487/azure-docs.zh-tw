---
title: "使用 Azure Site Recovery 針對實體伺服器到 Azure 的複寫設定複寫原則 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 服務將內部部署實體伺服器複寫至 Azure 儲存體時設定複寫原則所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 9ce23382001b54e7b9b7a58b8dd9aa61b400826d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-a-replication-policy-for-physical-server-replication-to-azure"></a>步驟 8：針對實體伺服器到 Azure 的複寫設定複寫原則


本文說明在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務將 Windows/Linux 實體伺服器複寫至 Azure 時，如何設定複寫原則。


請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="configure-a-policy"></a>設定原則

1. 按一下 [Site Recovery 基礎結構] > [複寫原則] > [+複寫原則]。
2. 在 [建立複寫原則]中，指定原則名稱。
3. 在 [RPO 臨界值] 中，指定 RPO 限制。 這個值表示資料復原點的建立頻率。 連續複寫超過此限制時會產生警示。
4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 複寫的 VM 可以還原至一個週期內的任何時間點。 複寫至進階儲存體的電腦支援最長保留 24 小時，標準儲存體則是 72 小時。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (以分鐘為單位)。 按一下 [確定]  以建立原則。

    ![複寫原則](./media/physical-walkthrough-replication/gs-replication2.png)
8. 當您建立新的原則時，該原則會自動與組態伺服器產生關聯。 依預設會自動建立容錯回復的比對原則。 例如，如果複寫原則是 **rep-policy**，容錯回復原則便會是 **rep-policy-failback**。 從 Azure 起始容錯回復時才會使用此原則。

## <a name="next-steps"></a>後續步驟

移至[步驟 9：安裝行動服務](physical-walkthrough-install-mobility.md)
