---
title: "使用 Azure Site Recovery 將 Azure VM 複寫至次要地區 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 服務，將一個 Azure 區域中執行的 Azure VM 複寫至另一個區域。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>將 Azure 虛擬機器複寫到另一個 Azure 區域


本文說明如何使用 Azure Site Recovery 服務，將一個 Azure 區域中的 Azure 虛擬機器 (VM) 複寫至次要 Azure 區域。

>[!NOTE]
>
> Site Recovery 的 Azure VM 複寫目前為預覽狀態。

## <a name="prerequisites"></a>必要條件

* 您應該已備妥復原服務保存庫。 建議您在需要 VM 複寫的目標區域中建立保存庫。
* 如果您使用網路安全性群組 (NSG) 規則或防火牆 Proxy 對於 Azure VM 上的連出網際網路連線能力控制其存取權，請確定您允許所需的 URL 或 IP。 [深入了解](./site-recovery-azure-to-azure-networking-guidance.md)。
* 如果您有 Azure 中內部部署與來源位置之間的 ExpressRoute 或 VPN 連線，[請了解如何設定它們](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)。
* 您的 Azure 使用者帳戶必須具有[特定權限](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能啟用 Azure VM 的複寫。
必須啟用您的 Azure 訂用帳戶，才能在要作為災害復原區域的目標位置中建立 VM。 請連絡支援人員啟用所需的配額。

## <a name="enable-replication"></a>啟用複寫

在此程序，東亞用來作為來源位置，東南亞則作為目標。

1. 按一下保存庫中的 **+ 複寫**，啟用虛擬機器的複寫功能。
2. 確認 [來源：] 設為 **Azure**。
3. 將 [來源位置] 設定為東亞。
4. 在**部署模型**中，**傳統**或 **Resource Manager**。
5. 在**資源群組**中，選取來源 VM 所屬的群組。 會列出所選取資源群組下的所有 VM。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部 VM。 您只能選取可以啟用複寫的機器。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. 在 [設定] > [目標位置] 下，指定複寫來源 VM 資料的位置。 Site Recovery 提供適合的目標區域中，根據選取的 Vm 的區域清單。
8. Site Recovery 會設定目標設定的預設值。 可以視需要修改這些。

    - **目標資源群組**。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新資源群組。 如果建立的資源群組已存在，就會重複使用。
    - **目標虛擬網路**。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新虛擬網路。 此網路會對應至您的來源網路。 [深入了解](site-recovery-network-mapping-azure-to-azure.md)網路對應。
    - **目標儲存體帳戶**。 根據預設，Site Recovery 會建立符合來源 VM 儲存體設定的新目標儲存體帳戶。 如果建立的帳戶已經存在，就會重複使用。
    - **快取儲存體帳戶**。 Azure Site Recovery 會建立額外的快取儲存體帳戶，來源地區。 來源 VM 上的所有變更都會受到追蹤，並傳送到快取儲存體帳戶，然後複寫到目標位置。
    - **可用性設定組**。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新可用性設定組。 如果建立的集合已經存在，就會重複使用。
    - **複寫原則**。 Site Recovery 會定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Site Recovery 會對於復原點保留使用「24 小時」預設設定建立新的複寫原則，並對於應用程式一致快照集頻率使用「60 分鐘」。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. 按一下 [啟用複寫] 以開始保護 VM。

## <a name="customize-target-resources"></a>自訂目標資源

1. 修改這些目標預設值：

    - **目標資源群組**。 從訂用帳戶內的目標位置中所有資源群組的清單選取任何資源群組。
    - **目標虛擬網路**。 從目標位置中的所有虛擬網路清單中選取。
    - **可用性設定組** 您只能將可用性設定組設定新增至位於來源區域之集合中的 VM。
    - **目標儲存體帳戶**：新增任何可用的帳戶。

        ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. 按一下 [建立目標資源] > [啟用複寫]。 在初始複寫期間，VM 狀態可能需要一些時間才會重新整理。 按一下 [重新整理] 以取得最新狀態。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. 在 VM 受保護之後，請在 [複寫項目] 中檢查 VM 健康情況。



## <a name="next-steps"></a>後續步驟
[了解](../azure-to-azure-tutorial-dr-drill.md)如何執行測試容錯移轉。

