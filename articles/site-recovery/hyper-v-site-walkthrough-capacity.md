---
title: "使用 Azure Site Recovery 規劃產能並調整 Hyper-V VM 複寫 (不含 VMM) 至 Azure | Microsoft Docs"
description: "使用 Azure Site Recovery 將 Hyper-V VM 複寫至 Azure 時，請使用本文規劃產能和調整"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8687af60-5b50-481c-98ee-0750cbbc2c57
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: c7891c188c2cecbbf056fa79672a13bb16fa7fcf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-to-azure-replication"></a>步驟 3：規劃產能並調整 Hyper-V 對 Azure 的複寫

使用這份文件找出在將內部部署 Hyper-V VM (不含 System Center VMM) 複寫至 Azure 時，使用 [Azure Site Recovery](site-recovery-overview.md) 規劃產能和調整的方法。

閱讀本文之後，在本文下方張貼意見，或在 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 提出技術問題。


## <a name="how-do-i-start-capacity-planning"></a>如何開始容量規劃？


您需收集複寫環境的相關資訊，然後使用此資訊搭配本文中強調的考量事項來規劃容量。


## <a name="gather-information"></a>收集資訊

1. 收集有關複寫環境的資訊，包括 VM、每個 VM 的磁碟和每個磁碟的儲存體。
2. 識別複寫資料的每日變更 (流失) 率。 請下載 [Hyper-V 容量規劃工具](https://www.microsoft.com/download/details.aspx?id=39057)來取得變更率。 我們建議您執行此工具一週以上的時間來擷取平均值。
 

## <a name="figure-out-capacity"></a>找出產能

以您所收集的資訊作為基礎，執行 [Site Recovery Capacity Planner 工具](http://aka.ms/asr-capacity-planner-excel)來分析您的來源環境和工作負載，並且評估來源位置的頻寬需求和伺服器資源，以及在目標位置所需的資源 (虛擬機器和儲存體等等)。 有幾種模式可讓您執行工具：

- 快速規劃：在此模式中執行工具，以 VM、磁碟、儲存體及變更率的平均數作為基礎，取得網路與伺服器的預測。
- 詳細規劃：在此模式中執行工具，提供每個工作負載的 VM 層級詳細資料。 分析 VM 相容性，並取得網路和伺服器預測。

現在執行工具：

1. 下載[工具](http://aka.ms/asr-capacity-planner-excel)
2. 若要執行快速規劃，請遵循[這些指示](site-recovery-capacity-planner.md#run-the-quick-planner)，並選取 **Hyper-V 至 Azure** 情節。
3. 若要執行詳細規劃，請遵循[這些指示](site-recovery-capacity-planner.md#run-the-detailed-planner)，並選取 **Hyper-V 至 Azure** 情節。

## <a name="control-network-bandwidth"></a>控制網路頻寬

在計算您所需要的頻寬之後，您會有幾個選項可控制用於複寫的頻寬數量：

* **節流頻寬**︰複寫至 Azure 的 Hyper-V 流量會經過特定的 Hyper-V 主機。 您可以在主機伺服器上進行頻寬節流。
* **影響頻寬**︰您可以使用幾個登錄機碼來影響用於複寫的頻寬。

### <a name="throttle-bandwidth"></a>節流頻寬
1. 在 Hyper-V 主機伺服器上開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，Microsoft Azure 備份的捷徑位於桌面上或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在嵌入式管理單元中，按一下 [變更屬性] 。
3. 在 [節流] 索引標籤上，選取 [啟用備份操作的網際網路頻寬使用節流設定]，然後設定工作和非工作時數的限制。 有效範圍是每秒 512 Kbps 到 102 Mbps。

    ![節流頻寬](./media/hyper-v-site-walkthrough-capacity/throttle2.png)

您也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) Cmdlet 來設定節流。 以下是一個範例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要節流。

### <a name="influence-network-bandwidth"></a>影響網路頻寬
1. 在登錄中瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要影響複製磁碟上的頻寬流量，請修改 **UploadThreadsPerVM**的值，如果不存在則請建立機碼。
   * 若要影響從 Azure 容錯回復流量的頻寬，請修改 **DownloadThreadsPerVM**的值。
2. 預設值為 4。 在 “overprovisioned” 網路中，這些登錄機碼必須變更自其預設值。 最大值為 32。 監視流量，將此值最佳化。

## <a name="next-steps"></a>後續步驟

移至[步驟 4：規劃網路](hyper-v-site-walkthrough-network.md)。
