---
title: "Azure 區域之間的 Azure 虛擬機器複寫如何在 Azure Site Recovery 中運作？  | Microsoft Docs"
description: "本文概要說明使用 Azure Site Recovery 服務複寫 Azure 區域之間的 Azure VM 時所用的元件和架構。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: ec397eaeda963f257d1bd996f1f57189bcde17ca
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---

# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Azure VM 複寫如何在 Site Recovery 中運作？


本文說明使用 [Azure Site Recovery](site-recovery-overview.md) 服務從一個區域複寫 Azure 虛擬機器 (VM) 並復原到另一個區域時的相關元件和程序。

>[!NOTE]
>Site Recovery 服務的 Azure VM 複寫目前為預覽狀態。

請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。

## <a name="architectural-components"></a>架構元件

下圖提供特定區域 (在此範例中為美國東部位置) 之中 Azure VM 環境的高階檢視。 在 Azure VM 的環境中：
- 應用程式可以在 VM 上執行，且磁碟分散於不同的儲存體帳戶。
- VM 可以包含在虛擬網路內的一個或多個子網路。

![客戶環境](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

了解[支援矩陣](site-recovery-support-matrix-azure-to-azure.md)中的部署必要條件和需求。

## <a name="replication-process"></a>複寫程序

### <a name="step-1"></a>步驟 1

您在 Azure 入口網站中啟用 Azure VM 複寫時，會在目標區域中自動建立下圖和下表所示的資源。 根據預設，會根據來源地區設定建立資源。 您可以視需要自訂目標設定。 [深入了解](site-recovery-replicate-azure-to-azure.md)。

![啟用複寫程序，步驟 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

**Resource** | **詳細資料**
--- | ---
**目標資源群組** | 在容錯移轉之後複寫的 VM 所屬的資源群組。
**目標虛擬網路** | 在容錯移轉之後複寫的 VM 所在的虛擬網路。 在來源和目標的虛擬網路之間會建立網路對應，反之亦然。
**快取儲存體帳戶** | 來源 VM 上的變更複寫到目標儲存體帳戶之前，會受到追蹤並傳送到目標位置中的快取儲存體帳戶。 這可確保對於 VM 上執行的生產應用程式所造成的影響降到最低。
**目標儲存體帳戶**  | 將資料複寫至其中的目標位置儲存體帳戶。
**目標可用性設定組**  | 在容錯移轉之後複寫的 VM 所在的可用性設定組。

### <a name="step-2"></a>步驟 2

啟用複寫時，會在 VM 上自動安裝 Site Recovery 擴充行動服務。 發生的情況如下：

1. 向 Site Recovery 註冊 VM。

2. 對於 VM 設定連續複寫。 VM 磁碟上的資料寫入持續傳送到來源位置的快取儲存體帳戶中。

   ![啟用複寫程序，步驟 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Site Recovery 永遠不會需要 VM 的輸入連線能力。 VM 僅需要 Site Recovery 服務 URL/IP 位址、Office 365 驗證 URL/IP 位址和快取儲存體帳戶 IP 位址的輸出連線能力。 如需詳細資訊，請參閱[複寫 Azure 虛擬機器的網路指引](site-recovery-azure-to-azure-networking-guidance.md)文章。

## <a name="continuous-replication-process"></a>連續複寫程序

連續複寫進行之後，磁碟寫入會立即傳送至快取儲存體帳戶。 Site Recovery 會處理資料，並將資料傳送到目標儲存體帳戶。 處理資料之後，目標儲存體帳戶會每隔幾分鐘產生復原點。

## <a name="failover-process"></a>容錯移轉程序

您起始容錯移轉時，會在目標資源群組、目標虛擬網路，目標子網路和目標可用性設定組中建立 VM。 在容錯移轉時，您可以使用任何復原點。

![容錯移轉程序](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>後續步驟

- 深入了解 Azure VM 複寫的[網路](site-recovery-azure-to-azure-networking-guidance.md)。
- 依照逐步解說[複寫 Azure VM。](site-recovery-azure-to-azure.md)

