---
title: "Azure Migrate 中的評量計算 | Microsoft Docs"
description: "概括介紹 Azure Migrate 服務中的評量計算。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: f00825ff9a5018e67672ce452f01130f84919e52
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="assessment-calculations"></a>評量計算

[Azure Migrate](migrate-overview.md) 會評估要移轉至 Azure 的內部部署工作負載。 本文提供評量計算方式的相關資訊。



## <a name="overview"></a>概觀

Azure Migrate 評量有三個階段。 評量是會適用性分析開始，然後是以效能為基礎的大小估算，最後是每月成本預估。 機器必須先通過前一個評量，才可前往下一階段。 舉例而言，如果機器未通過 Azure 適用性檢查，就會標示為不適合 Azure，而不會再去計算大小和成本。 


## <a name="azure-suitability-analysis"></a>Azure 適用性分析

您想要移轉至 Azure 的機器必須符合 Azure 需求和限制。 例如，如果內部部署虛擬機器磁碟超過 4 TB，便無法託管在 Azure 上。 下表摘要說明 Azure 的相容性檢查。 

**檢查** | **詳細資料**
--- | ---
**開機類型** | 客體作業系統磁碟的開機類型必須是 BIOS，而不是 UEFI。
**核心** | 機器的核心數目必須等於 (或小於) Azure VM 支援的核心數目上限 (32)。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評量設定已指定緩和因數，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有效能記錄，Azure Migrate 會使用配置的核心，而不套用緩和因數。
**記憶體** | 機器的記憶體大小必須等於 (或小於) 的 Azure VM 允許的記憶體上限 (448 GB)。 <br/><br/> 如果有提供效能記錄，Azure Migrate 會將已使用的記憶體納入考量，進行比較。 如果已指定緩和因數，則會將使用的記憶體乘以緩和因數。<br/><br/> 如果沒有記錄，則會使用配置的記憶體，而不套用緩和因數。<br/><br/> 
**Windows Server 2003-2008 R2** | 32 位元和 64 位元支援。<br/><br/> Azure 僅能盡量提供支援。
**Windows Server 2008 R2，含所有 SP** | 64 位元支援。<br/><br/> Azure 提供完整支援。
**Windows Server 2012 與所有 SP** | 64 位元支援。<br/><br/> Azure 提供完整支援。
**Windows Server 2012 R2 與所有 SP** | 64 位元支援。<br/><br/> Azure 提供完整支援。
**Windows Server 2016 與所有 SP** | 64 位元支援。<br/><br/> Azure 提供完整支援。
**Windows Client 7 及更新版本** | 64 位元支援。<br/><br/> Azure 僅對 Visual Studio 訂用帳戶提供支援。
**Linux** | 64 位元支援。<br/><br/> Azure 對這些[作業系統](../virtual-machines/linux/endorsed-distros.md)提供完整支援。
**存放磁碟** | 已配置的磁碟大小必須小於或等於 4 TB (4096 GB)。<br/><br/> 附加至機器的磁碟數目必須小於或等於 65，作業系統磁碟亦包含在內。 
**網路功能** | 機器必須附加 32 個以內的網路介面卡。


## <a name="performance-based-sizing"></a>以效能為基礎調整大小

機器已標示為適用於 Azure 之後，Azure Migrate 會按照下列準則，將機器對應至 Azure 中的虛擬機器大小：

- **儲存體檢查**：Azure Migrate 會嘗試將附加至機器的每個磁碟對應至 Azure 中的磁碟：Azure Migrate 會將每秒 I/O (IOPS) 乘以緩和因數。 也可以將每個磁碟的輸送量 (以 MBps 為單位) 乘以緩和因數。 如此可提供有效的磁碟 IOPS 及輸送量。 Azure Migrate 會以此為基礎，將磁碟對應至 Azure 中的標準或高階磁碟。
    - 如果該服務找不到 IOPS 及輸送量符合需求的磁碟，便會將該機器標示為不適合 Azure。
    - 如果找到多個適合的磁碟，Azure Migrate 會挑選可支援儲存體備援方法的磁碟，以及可支援評量設定的指定位置的磁碟。
    - 如果有多個符合資格的磁碟，則會選取成本最低的磁碟。
- **存放磁碟輸送量**：[進一步了解](../azure-subscription-service-limits.md#storage-limits)關於每個磁碟和虛擬機器的 Azure 限制。
- **磁碟類型**：Azure Migrate 僅支援受控磁碟。
- **網路檢查**：Azure Migrate 會嘗試尋找可在內部部署機器上支援 NIC 數目的 Azure VM。
    - 為了達到目標，Azure Migrate 會機器在所有網路介面卡上傳出 (網路輸出) 的每秒傳輸資料量 (MBps) 彙總起來，將緩和因數套用到彙總的數字上。 會使用此數字來尋找可支援所要求的網路效能的 Azure VM。
    - Azure Migrate 採用虛擬機器的網路設定，並假設它是資料中心外部的網路。
    - 如果沒有可用的網路效能資料，調整虛擬機器大小時只會僅會考量網路介面卡的數量。
- **計算檢查**：儲存體和網路需求計算完畢後，Azure Migrate 會開始考量計算需求：
    - 如果有效能資料可供虛擬機器使用，則會查看使用的核心與記憶體，並套用緩和因數。 Azure Migrate 會根據該數字，嘗試在 Azure 中尋找適當的虛擬機器大小。
    - 如果找不到合適的大小，便會將機器標示為不適合 Azure。
    - 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後再套用位置及定價層設定，對虛擬機器大小提出最終建議。


## <a name="monthly-cost-estimation"></a>每月成本預估

調整大小的建議完成之後，Azure Migrate 會計算移轉後的計算和儲存成本。

- **計算成本**：使用建議的 Azure VM 大小時，Azure Migrate 會使用計費 API 來計算虛擬機器的每月成本。 計算時會將作業系統、軟體保證、位置和貨幣設定納入考量。 其會彙總所有機器的成本，以計算每月計算總成本。
- **儲存成本**：將機器附加的所有磁碟的每月成本彙總起來，計算出機器的每月儲存成本。 Azure Migrate 會將所有機器的儲存成本彙總起來，計算出每月的總儲存成本。 目前在計算時不會將評量設定中指定的優惠列入考量。

成本會以評量設定中指定的貨幣顯示。 


## <a name="next-steps"></a>後續步驟

[為內部部署 VMware VM 設定評量](tutorial-assessment-vmware.md)
