---
title: "如何對於容錯移轉的 Azure 虛擬機器在回復到主要 Azure 區域時重新保護 |Microsoft Docs"
description: "VM 從一個 Azure 區域容錯移轉到另一個 Azure 區域後，您可以使用 Azure Site Recovery 反方向保護機器。 了解在容錯移轉之前再次進行重新保護的步驟。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 32f5d2d142940bc515849dcd0edb1bb1f152aa6d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>對於容錯移轉的 Azure 區域在回復到主要區域時重新保護



>[!NOTE]
>
> Azure 虛擬機器的 Site Recovery 複寫目前為預覽狀態。


## <a name="overview"></a>概觀
您將虛擬機器從一個 Azure 區域[容錯移轉](site-recovery-failover.md)到另一個區域時，虛擬機器處於未受保護的狀態。 如果您想要使它們回復到主要區域，需要先保護虛擬機器，然後再次進行容錯移轉。 朝任一個方向進行容錯移轉沒有任何差異。 同樣地，啟用虛擬機器的保護後，容錯移轉後或容錯回復後的重新保護沒有任何差異。
為了說明重新保護的工作流程，並避免混淆，我們將使用東亞地區做為受保護電腦的主要網站，並使用東南亞地區做為機器的復原網站。 在容錯移轉期間，您會將虛擬機器容錯移轉至東南亞地區。 在容錯回復前，您需要重新保護從東南亞回復到東亞的虛擬機器。 本文說明使用重新保護的步驟。

> [!WARNING]
> 如果您已[完成移轉](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration)、已將虛擬機器移至另一個資源群組，或已刪除 Azure 虛擬機器，則您無法在之後執行容錯回復。

完成重新保護且受保護的虛擬機器開始複寫之後，您可以在虛擬機器上起始容錯移轉，以將它們回復到東亞地區。

在本文末尾或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中張貼意見或問題。

## <a name="prerequisites"></a>必要條件
1. 虛擬機器應該已獲得認可。
2. 目標網站 (在此情況下為東亞 Azure 區域) 應該可供使用，而且應該能夠存取/建立該區域的新資源。

## <a name="steps-to-reprotect"></a>重新保護的步驟

以下是使用預設設定重新保護虛擬機器的步驟。

1. 在 [保存庫] > [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的虛擬機器，然後選取 [重新保護]。 您也可以按一下機器，並從命令按鈕中選取**重新保護**。

![按一下滑鼠右鍵進行重新保護](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. 在刀鋒視窗中，注意保護的方向，[東南亞至東亞] 已選取。

![重新保護刀鋒視窗](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. 檢閱**資源群組、網路、儲存體和可用性設定組**資訊，並按一下 [確定]。 如果沒有任何標記為 (新) 的資源，將在重新保護時建立資訊。

這將觸發工作重新保護工作，首先會在目標網站 (在此情況下為 SEA) 植入最新的資料，完成後將複寫差異部分，然後容錯移轉回東南亞。

### <a name="reprotect-customization"></a>重新保護自訂
如果您要在重新保護時選擇擷取儲存體帳戶或網路，可以使用重新保護刀鋒視窗上提供的自訂選項。

![自訂選項](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

您可以在重新保護期間自訂目標虛擬機器的下列屬性。

![自訂刀鋒視窗](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|屬性 |注意事項  |
|---------|---------|
|目標資源群組     | 您可以選擇變更將建立虛擬機器的目標資源群組。 進行重新保護時，會刪除目標虛擬機器，因此您可以選擇新的資源群組，以便在容錯移轉後建立 VM         |
|目標虛擬網路     | 重新保護時無法變更網路。 若要變更網路，請重新進行網路對應。         |
|目標儲存體     | 您可以變更將在容錯移轉後建立虛擬機器的儲存體帳戶。         |
|快取儲存體     | 您可以指定將在複寫期間使用的快取儲存體帳戶。 如果您使用預設值，將建立新的快取儲存體帳戶 (如果不存在)。         |
|可用性設定組     |如果東亞中的虛擬機器屬於可用性設定組，您可以在東南亞選擇目標虛擬機器的可用性設定組。 預設值會尋找現有 SEA 可用性設定組，並試著使用它。 進行自訂時，您可以指定全新的 AV 設定組。         |


### <a name="what-happens-during-reprotect"></a>重新保護期間會發生什麼情況？

就和第一個啟用保護之後的情況一樣，以下是您使用預設設定建立的項目。
1. 在東亞地區中會建立快取儲存體帳戶。
2. 如果目標儲存體帳戶 (東南亞 VM 的原始儲存體帳戶) 不存在，會建立新的帳戶。 名稱為東亞虛擬機器的儲存體帳戶加上後置字元「asr」。
3. 如果目標 AV 設定組不存在，而且預設值偵測到需要建立新的 AV 設定組，則會在重新保護工作中建立。 如果您已自訂重新保護，將使用選取的 AV 設定組。
4.

以下是將觸發重新保護工作時發生的步驟列出的清單。 這是以目標端虛擬機器存在的情況為準。

1. 重新保護時會建立需要的項目。 如果這些項目已存在，則會重複使用。
2. 目標端 (東南亞) 虛擬機器會先關閉 (如果正在執行)。
3. 目標端虛擬機器的磁碟會由 Azure Site Recovery 複製到容器做為種子 blob。
4. 然後將刪除目標端虛擬機器。
5. 種子 blob 將由目前來源端 (東亞) 虛擬機器用來複寫。 這可確保僅複寫差異部份。
6. 來源磁碟與種子 blob 之間的重大變更會同步處理。 這需要一些時間才會完成。
7. 重新保護工作完成之後，差異複寫會開始依據原則建立復原點。

> [!NOTE]
> 您無法在復原計劃層級進行保護。 您只能在每個 VM 層級進行重新保護。

重新保護成功之後，虛擬機器將進入受保護狀態。

## <a name="next-steps"></a>後續步驟

在虛擬機器進入受保護狀態後，您就可以起始容錯移轉。 容錯移轉將關閉東亞 Azure 區域中的虛擬機器，然後建立並啟動東南亞區域虛擬機器。 因此，應用程式有一小段停機時間。 因此，請選擇應用程式允許停機時的容錯移轉時間。 建議您先測試虛擬機器的容錯移轉，確定運作正常，然後再起始容錯移轉。

-   [起始虛擬機器測試容錯移轉的步驟](site-recovery-test-failover-to-azure.md)

-   [起始虛擬機器容錯移轉的步驟](site-recovery-failover.md)
