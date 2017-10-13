---
title: "複寫應用程式 (Azure 至 Azure) | Microsoft Docs"
description: "本文說明如何設定將一個 Azure 區域中執行的虛擬機器複寫至 Azure 的另一個區域。"
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
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>將 Azure 虛擬機器複寫到另一個 Azure 區域



>[!NOTE]
>
> Azure 虛擬機器的 Site Recovery 複寫目前為預覽狀態。

本文說明如何設定將一個 Azure 區域中執行的虛擬機器複寫至另一個 Azure 區域。

## <a name="prerequisites"></a>必要條件

* 本文假設您已經知道 Site Recovery 和復原服務保存庫。 您必須先建立「復原服務保存庫」。

    >[!NOTE]
    >
    > 建議您在要 VM 複寫的位置中建立復原服務保存庫。 例如，如果您的目標位置是「美國中部」，請在「美國中部」建立保存庫。

* 如果您使用網路安全性群組 (NSG) 規則或防火牆 Proxy 對於 Azure VM 上的連出網際網路連線能力控制其存取權，請確定您已將所需的 URL 或 IP 列入白名單中。 如需詳細資料，請參閱[網路指引文件](./site-recovery-azure-to-azure-networking-guidance.md)。

* 如果 Azure 中的內部部署與來源位置之間有 ExpressRoute 或 VPN 連線，請遵循 [Azure 至內部部署 ExpressRoute / VPN 組態的 Site Recovery 考量](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)文件。

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能啟用 Azure 虛擬機器的複寫功能。

* 必須啟用您的 Azure 訂用帳戶，才能在要做為 DR 區域的目標位置中建立 VM。 您可以連絡支援人員啟用所需的配額。

## <a name="enable-replication-from-azure-site-recovery-vault"></a>啟用從 Azure Site Recovery 保存庫進行複寫
為了說明這一點，我們將「東亞」Azure 位置中執行的 VM 複寫到「東南亞」位置。 步驟如下：

 按一下保存庫中的 **+ 複寫**，啟用虛擬機器的複寫功能。

1. **來源：**這是指機器的源點，在此案例中為 **Azure**。

2. **來源位置：**這是您想要保護虛擬機器的 Azure 區域。 為了說明這一點，來源位置將是「東亞」

3. **部署模型：**這是指來源機器的 Azure 部署模型。 您可選擇使用傳統管理員或資源管理員，且屬於特定模型的機器將在下一個步驟中列出，以提供保護。

      >[!NOTE]
      >
      > 您只能複寫傳統的虛擬機器，並將它復原為傳統的虛擬機器。 您無法將它復原為資源管理員虛擬機器。

4. **資源群組：**這是來源虛擬機器所屬的資源群組。 選取的資源群組下的所有 VM 會都在下一個步驟中列出，以供保護。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部機器。 您只能選取可以啟用複寫的機器。 然後按一下 [確定]。
    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


在 [設定] 區段下，您可以設定目標網站內容

1. **目標位置**：這是將複寫來源虛擬機器資料的位置。 端視您選取的機器位置而定，Site Recovery 將提供適當目標區域的清單。

    > [!TIP]
    > 建議您保留與復原服務保存庫相同的目標位置。

2. **目標資源群組**：這是所有複寫的虛擬機器所屬的資源群組。根據預設，ASR 將在目標區域中建立新的資源群組，其名稱會有「asr」尾碼。 如果 ASR 建立的資源群組已經存在，就會重複使用。您也可以選擇自訂該群組，如下一節所示。    
3. **目標虛擬網路：**根據預設，ASR 將在目標區域中建立新的虛擬網路，其名稱會有「asr」尾碼。 這將對應於來源網路，並且將用於任何未來的保護。

    > [!NOTE]
    > [檢查網路服務詳細資料](site-recovery-network-mapping-azure-to-azure.md)，深入了解網路對應。

4. **目標儲存體帳戶：**根據預設，ASR 將建立新目標儲存體帳戶，模擬來源 VM 儲存體組態。 如果 ASR 建立的儲存體帳戶已經存在，就會重複使用。

5. **快取儲存體帳戶：**ASR 需要在來源地區中有額外的儲存體帳戶 (稱為快取儲存體)。 在來源 VM 上發生的所有變更都會受到追蹤，並傳送到快取儲存體帳戶，然後複寫到目標位置。

6. **可用性設定組：**根據預設，ASR 將在目標區域中建立新的可用性設定組，其名稱會有「asr」尾碼。 如果 ASR 建立的可用性設定組已經存在，就會重複使用。

7.  **複寫原則：**這會定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，ASR 將對於復原點保留使用「24 小時」預設設定建立新的複寫原則，並對於應用程式一致快照集頻率使用「60 分鐘」。

    ![啟用複寫](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>自訂目標資源

如果您想要變更 ASR 使用的預設值，可以按照您的需求變更設定。

1. **自訂：**按一下將變更 ASR 使用的預設值。

2. **目標資源群組：**您可以從訂用帳戶內的目標位置中現有的所有資源群組清單選取資源群組。

3. **目標虛擬網路：**您可以找到目標位置中所有虛擬網路的清單。

4. **可用性設定組：**您只能將可用性集合設定新增到來源範圍中的可用性所屬的虛擬機器。

5. **目標儲存體帳戶：**

![啟用複寫](./media/site-recovery-replicate-azure-to-azure/customize.PNG)按一下**建立目標資源**及 [啟用複寫]


虛擬機器受到保護之後，您就可以在**複寫項目**下檢查 VM 健全狀況的狀態

>[!NOTE]
>在初始複寫期間，狀態可能需要一些時間才能重新整理，因此您有一段時間不會看見進度。 您可以按一下刀鋒視窗頂端的 [重新整理] 按鈕，以取得最新狀態。
>

![啟用複寫](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>後續步驟
- [深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
- [深入了解](site-recovery-failover.md)不同類型的容錯移轉及如何執行。
- 深入了解[使用復原計劃](site-recovery-create-recovery-plans.md)降低 RTO。
- 深入了解容錯移轉之後[重新保護 Azure VM](site-recovery-how-to-reprotect.md)。
