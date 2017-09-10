---
title: "使用 Azure Site Recovery 設定將 Hyper-V VM (不含 System Center VMM) 複寫至 Azure 的複寫原則 | Microsoft Docs"
description: "摘要說明您在將 Hyper-V VM 複寫至 Azure 儲存體時，設定複寫原則所需的步驟"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>步驟 9：設定 Hyper-V VM 複寫至 Azure 的複寫原則

本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務，在您要將 Hyper-V VM 複寫至 Azure (不含 System Center VMM) 時設定複寫原則。


請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。

## <a name="about-snapshots"></a>關於快照集

Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。
    - 應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。
    - 如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。 確認您設定的值低於您設定的其他復原點數目。

## <a name="set-up-a-replication-policy"></a>設定複寫原則

1. 若要建立新的複寫原則，請按一下 [準備基礎結構] > [複寫設定] > [+建立及關聯]。

    ![網路](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. 在 [建立及關聯原則] 中指定原則名稱。
3. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。

    > [!NOTE]
    > 複寫到進階儲存體時，不支援 30 秒的頻率。 限制取決於進階儲存體所支援之每 blob (100) 的快照集數目。 [深入了解](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)。

4. 在 [復原點保留] 中，指定每個復原點的保留週期長度 (以小時為單位)。 VM 可以復原到週期內的任意點。
5. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (1-12 小時)。
6. 在 [初始複寫開始時間]  中，指定開始初始複寫的時間。 複寫會透過您的網際網路頻寬發生，所以您可能想將它排程在忙碌時間之外。 然後按一下 [確定] 。

    ![複寫原則](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

當您建立新的原則時，該原則會自動與 Hyper-V 網站產生關聯。 您可以在 [複寫] > 原則名稱 > [關聯 Hyper-V 網站] 中，將 Hyper-V 網站 (及其中的 VM) 與多個複寫原則建立關聯。



## <a name="next-steps"></a>後續步驟

移至[步驟 10：啟用複寫](hyper-v-site-walkthrough-enable-replication.md)

