---
title: "規劃 Azure 檔案同步 (預覽) | Microsoft Docs"
description: "了解規劃 Azure 檔案部署時的考量事項。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3c003d498600a2cfd12ef2adfb7c16f9dfaddb37
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>規劃 Azure 檔案同步 (預覽) 部署
透過 Azure 檔案同步 (預覽)，您可以將共用複寫到內部部署或 Azure 中的 Windows Server。 您及您的使用者接著可透過 Windows Server 存取檔案共用，例如透過 SMB 或 NFS 共用。 這特別適用於離 Azure 資料中心很遠的位置 (例如分公司) 存取和修改資料的情況。 資料可以在多個 Windows Server 端點之間複寫，例如多個分公司之間。 

本指南說明部署 Azure 檔案同步時的考量事項。建議您閱讀[規劃 Azure 檔案部署](storage-files-planning.md)測試指南。 

## <a name="understanding-azure-file-sync-terminology"></a>了解 Azure 檔案同步術語
開始詳細說明 Azure 檔案同步之前，請務必了解相關術語。

### <a name="storage-sync-service"></a>儲存體同步服務
儲存體同步服務是最上層的 Azure 資源，代表 Azure 檔案同步。儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 由於儲存體同步服務可以透過多個同步群組與多個儲存體帳戶建立同步關係，因此最上層資源必須與儲存體帳戶資源不同。 一個訂用帳戶可以部署多個儲存體同步服務資源。

### <a name="sync-group"></a>同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會彼此保持同步。 例如，如果您有兩組不同的檔案需要透過 AFS 管理，您會建立兩個同步群組並將不同的端點新增至每個群組。 儲存體同步服務可以視需要裝載許多同步群組。  

### <a name="registered-server"></a>已註冊的伺服器
已註冊的伺服器物件代表您的伺服器 (或叢集) 與儲存體同步服務之間的信任關係。 您可以視需要向儲存體同步服務執行個體註冊許多伺服器。 不過，在任何指定時間，只能向一個儲存體同步服務註冊一個伺服器 (或叢集)。

### <a name="azure-file-sync-agent"></a>Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可同步處理 Windows Server 與 Azure 檔案共用。 Azure 檔案同步代理程式是由三個主要元件所組成： 
- **FileSyncSvc.exe**：負責監視伺服器端點上之變更，並起始同步至 Azure 之工作階段的背景 Windows 服務。
- **StorageSync.sys**：負責將檔案分層到 Azure 檔案服務 (啟用雲端階層處理時) 的 Azure 檔案同步檔案系統篩選器。
- **PowerShell 管理 Cmdlet**：用來與 Microsoft.StorageSync Azure 資源提供者互動的 PowerShell Cmdlet。 根據預設，您可以在以下位置找到這些 Cmdlet：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="cloud-endpoint"></a>雲端端點
雲端端點是 Azure 檔案共用，屬於同步群組的一部分。 整個 Azure 檔案共用會同步，而且 Azure 檔案共用只能是一個雲端端點的成員，因此會是一個同步群組。 如果您將內含一組現有檔案的 Azure 檔案共用當作雲端端點新增至同步群組，這些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

### <a name="server-endpoint"></a>伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾或磁碟區的根目錄。 相同磁碟區中可以有多個伺服器端點，但前提是其命名空間未重疊 (例如 F:\sync1 和 F:\sync2)。 您可以為每個伺服器端點個別設定雲端階層處理原則。 如果您將內含一組現有檔案的伺服器位置當作伺服器端點新增至同步群組，這些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

### <a name="cloud-tiering"></a>雲端階層處理 
雲端階層處理是 Azure 檔案同步的選擇性功能，可讓不常使用或存取的檔案分層到 Azure 檔案服務。 將檔案分層時，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 會將本機檔案取代為資料指標，或代表 Azure 檔案服務中檔案 URL 的重新分析點。 階層式檔案在 NTFS 中具有「離線」屬性集，因此協力廠商應用程式可以識別階層式檔案。 當使用者開啟階層式檔案時，Azure 檔案同步會順暢地從 Azure 檔案服務重新叫用檔案資料，使用者不需要知道檔案未儲存在本機系統上。 這項功能也稱為階層式存放裝置管理 (HSM)。

## <a name="azure-file-sync-interoperability"></a>Azure 檔案同步互通性 
本節涵蓋 Azure 檔案同步與 Windows Server 功能和角色以及協力廠商解決方案的互通性。

### <a name="supported-versions-of-windows-server"></a>支援的 Windows Server 版本
Azure 檔案同步目前支援的 Windows Server 版本包括：

| 版本 | 支援的 SKU | 支援的部署選項 |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter 和 Standard | 完整 (具有 UI 的 Server) |
| Windows Server 2012 R2 | Datacenter 和 Standard | 完整 (具有 UI 的 Server) |

Windows Server 的未來版本將會在發行時新增，舊版 Windows 可能會根據使用者意見反應而新增。

> [!Important]  
> 建議您透過 Windows Update 的最新更新，確保搭配 Azure 檔案同步使用的所有 Windows Server 都是最新的。 

### <a name="file-system-features"></a>檔案系統功能
| 功能 | 支援狀態 | 注意事項 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Azure 檔案同步會保留 Windows ACL，並由 Windows Server 在伺服器端點上強制執行。 如果在雲端中直接存取 Azure 檔案服務，該檔案服務可能尚未支援 Windows ACL。 |
| 永久連結 | Skipped | |
| 符號連結 | Skipped | |
| 掛接點 | 部分支援 | 掛接點可以是伺服器端點的根目錄，但如果包含在伺服器端點的命名空間，則會略過。 |
| 接合 | Skipped | |
| 重新分析點 | Skipped | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案將會同步 (不會封鎖)，但會以完整檔案同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，下載變更之後，就無法再將檔案設成疏鬆 |
| 替代資料流 (ADS) | 已保留，但未同步 | |

> [!Note]  
> 僅支援 NTFS 磁碟區。

### <a name="failover-clustering"></a>容錯移轉叢集
Azure 檔案同步的 [一般用途的檔案伺服器] 部署選項支援 Windows Server 容錯移轉叢集。 [用於應用程式資料的向外延展檔案伺服器] \(SOFS) 或叢集共用磁碟區 (CSV) 不支援容錯移轉叢集。

> [!Note]  
> 您必須在容錯移轉叢集中的每個節點上安裝 Azure 檔案同步代理程式，同步才能正常運作。

### <a name="windows-server-data-deduplication"></a>Windows Server 重複資料刪除
針對未啟用雲端階層處理的磁碟區，Azure 檔案同步支援在磁碟區上啟用重複資料刪除。 已啟用雲端階層處理的 Azure 檔案同步與重複資料刪除之間目前不支援互通。

### <a name="anti-virus-solutions"></a>防毒解決方案
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒程式可能會導致重新叫用階層式檔案。 由於階層式檔案具有「離線」屬性集，因此建議洽詢您的軟體廠商，了解如何設定其解決方案以略過讀取離線檔案。 

下列解決方案已知支援略過離線檔案：

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (請參閱第 90 頁的＜Scan only what you need to＞(只掃描必要部分) 一節)
- [Kaspersky 防毒程式](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>備份解決方案
備份解決方案類似防毒解決方案，可能會導致重新叫用階層式檔案。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不是使用內部部署備份產品。

### <a name="encryption-solutions"></a>加密解決方案
加密解決方案的支援取決於其實作方式。 Azure 檔案共用已知可用於：

- BitLocker 加密
- Azure RMS (及舊版 Active Directory RMS)

Azure 檔案共用已知無法用於：

- NTFS 加密檔案系統 (EFS)

一般而言，Azure 檔案同步應該可以與檔案系統之下的加密解決方案 (例如 BitLocker) 和以檔案格式實作的解決方案 (例如 BitLocker) 互通，但沒有特殊互通性可與檔案系統之上的解決方案 (例如 NTFS 加密檔案系統) 互通。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
沒有其他階層式存放裝置管理解決方案可與 Azure 檔案同步搭配使用。

## <a name="region-availability"></a>區域可用性
Azure 檔案同步處於預覽狀態，僅適用於下列區域：

| 區域 | 資料中心位置 |
|--------|---------------------|
| 美國西部 | 美國加利福尼亞州 |
| 西歐 | 荷蘭 |
| 東南亞 | 新加坡 |
| 澳洲東部 | 澳洲新南威爾斯 |

在預覽版中，僅支援與儲存體同步服務相同之區域中的 Azure 檔案共用同步。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
Azure 檔案同步代理程式的更新會定期發行，以新增功能並解決任何找到的問題。 建議啟用 Microsoft Update，以取得我們所發行的所有 Azure 檔案同步代理程式更新。 話雖如此，我們了解某些組織會想要嚴格控制更新。 若要使用舊版 Azure 檔案同步代理程式進行部署：

- 在最初發行新的主要版本之後三個月，儲存體同步服務會允許使用舊的主要版本。 例如，在發行版本 2.\* 之後三個月，儲存體同步服務會支援版本 1.\*。
- 過了三個月後，儲存體同步服務會透過與其同步群組同步，開始封鎖使用過期版本的已註冊伺服器。
- 在使用舊的主要版本的三個月內，所有 Bug 修正只會套用至目前的主要版本。

## <a name="next-steps"></a>後續步驟
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)

