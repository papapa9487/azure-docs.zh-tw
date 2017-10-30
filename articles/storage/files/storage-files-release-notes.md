---
title: "Azure 檔案同步代理程式版本資訊 | Microsoft Docs"
description: "Azure 檔案同步處理版本資訊"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 1db3fef17e24022bff59665558f4a354f8c37d1d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="azure-file-sync-agent-release-notes"></a>Azure 檔案同步代理程式版本資訊
Azure 檔案同步 (預覽) 可讓您將貴組織的檔案共用集中在「Azure 檔案」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 它會將您的 Windows Server 轉換成 Azure 檔案共用的快速快取來達到這個目的。 您可以使用 Windows Server 上可用的任何通訊協定來存取本機資料 (包括 SMB、NFS 和 FTPS)，並且可以在世界各地擁有任何所需數量的快取。

本文件涵蓋 Azure 檔案同步代理程式支援版本的版本資訊。

## <a name="supported-versions"></a>支援的版本
Azure 檔案同步支援下列版本：

| 代理程式版本號碼 | 發行日期 | 支援期限 |
|----------------------|--------------|------------------|
| 1.1.0.0 | 2017-09-26 | 目前版本 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-1100"></a>代理程式版本 1.1.0.0
下列版本資訊適用於在 2017 年 9 月 9 日發行的代理程式版本 1.1.0.0。 這個版本標示為 Azure 檔案同步的初始預覽版本！

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步 (預覽) 部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件 (MSI)。
- 在 Windows Server Core 或 Nano Server 部署選項上不受到支援。
- 只在 Windows Server 2016 和 2012 R2 上受到支援。
- 代理程式需要至少 2 GB 的實體記憶體。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步 (預覽) 進行疑難排解](storage-sync-files-troubleshoot.md)
- 請勿使用檔案伺服器資源管理員 (或其他) 檔案檢測：如果檔案因為檔案檢測而遭到封鎖，檔案檢測會造成無止盡的同步失敗。
- 重複的已註冊伺服器 (包括 VM 複製) 會導致非預期的結果 (特別是同步可能永遠沒有交集)。
- 在相同的磁碟區上不支援重複資料刪除和雲端階層。
 
### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 路徑長度超過 2048 個字元
- 如果大於 2K，這是安全性描述元的 DACL 部分 (只有在當您於單一項目上具有超過 40 個存取控制項目時，才會發生這個問題)
- 安全性描述元的 SACL 部分 (用來稽核)
- 擴充屬性
- 替代資料流
- 重新分析點
- 永久連結
- 如果變更從其他端點同步至該檔案時，將不會保留壓縮 (如果在伺服器檔案上設定)
- 使用 EFS (或其他使用者模式加密) 加密的任何檔案，會阻止我們的服務讀取資料 
    
    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料，其餘的資料則可以在 Azure 中加密。
 
### <a name="server-endpoints"></a>伺服器端點
- 伺服器端點只能在 NTFS 磁碟區上建立。 目前 Azure 檔案同步不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 伺服器端點不在系統磁碟區上 (例如，C:\MyFolder 不是可接受的路徑，除非 C:\MyFolder 是掛接點)。
- 只有叢集磁碟才支援容錯移轉叢集，叢集共用磁碟區 (CSV) 不支援。
- 伺服器端點可能無法為巢狀，但是可以彼此平行並存於相同的磁碟區。
- 從伺服器一次刪除大量目錄 (超過 10000 個) 可能會造成同步失敗 - 批次刪除 10000 個以下的目錄，並且確定刪除作業同步成功，然後再刪除下一個批次。
- 在磁碟區根目錄不支援。
- 請勿在伺服器端點內儲存 OS 或應用程式分頁檔。
 
### <a name="cloud-tiering"></a>雲端階層處理
- 為了確保可以正確地重新叫用檔案，系統在最多 32 小時內不會自動階層處理新的或變更的檔案，包括設定新的伺服器端點之後的第一次階層處理，我們提供 PowerShell Cmdlet 以隨選階層處理，這樣您就可以更有效率地評估階層處理，而不需等待背景處理序。
- 如果階層式檔案是使用 Robocopy 來複製到其他位置，則產生的檔案不會進行階層處理，，但是可能會設定離線屬性，因為 Robocopy 不正確地在複製作業中包含該屬性。
- 從 SMB 用戶端檢視檔案屬性時，因為 SMB 快取檔案中繼資料，所以離線屬性可能會顯示為不正確設定。