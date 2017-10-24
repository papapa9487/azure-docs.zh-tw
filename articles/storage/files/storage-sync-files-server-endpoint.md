---
title: "新增/移除 Azure 檔案同步 (預覽) 伺服器端點 | Microsoft Docs"
description: "了解規劃 Azure 檔案服務部署時的考量事項。"
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 92ac80953623a5a94d3104f30787c9636308c707
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>新增/移除 Azure 檔案同步 (預覽) 伺服器端點
Azure 檔案同步 (預覽) 可讓您將貴組織的檔案共用集中在「Azure 檔案」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 它會將您的 Windows Server 轉換成 Azure 檔案共用的快速快取來達到這個目的。 您可以使用 Windows Server 上可用的任何通訊協定來存取本機資料 (包括 SMB、NFS 和 FTPS)，並且可以在世界各地擁有任何所需數量的快取。

「伺服器端點」代表「已註冊的伺服器」上的特定位置，例如伺服器磁碟區上的資料夾或磁碟區的根目錄。 相同磁碟區中可以有多個伺服器端點，但前提是其命名空間未重疊 (例如 F:\sync1 和 F:\sync2)。 您可以為每個伺服器端點個別設定雲端階層原則。 如果您將內含一組現有檔案的伺服器位置當作伺服器端點新增至同步群組，那些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

如需如何從頭到尾部署 Azure 檔案同步的資訊，請參閱[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>必要條件
若要建立伺服器端點，您必須先確定已符合下列準則： 
- 伺服器已安裝 Azure 檔案同步代理程式且已註冊。 如需安裝 Azure 檔案同步代理程式的指示，請參閱[向 Azure 檔案同步 (預覽) 註冊/取消註冊伺服器](storage-sync-files-server-registration.md)文章。 
- 確定已部署儲存體同步服務。 如需如何部署儲存體同步服務的詳細資訊，請參閱[如何部署 Azure 檔案同步 (預覽)](storage-sync-files-deployment-guide.md)。 
- 確定已部署同步群組。 了解如何[建立同步群組](storage-sync-files-deployment-guide.md#create-a-sync-group)。
- 確定伺服器已連線到網際網路，而且 Azure 可供存取。

## <a name="add-a-server-endpoint"></a>新增伺服器端點
若要新增伺服器端點，請瀏覽至所需的同步群組，並選取 [新增伺服器端點]。

![在 [同步群組] 窗格中新增新的伺服器端點](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

[新增伺服器端點] 底下需要下列資訊：

- **已註冊的伺服器**：要在其上建立伺服器端點的伺服器或叢集名稱。
- **路徑**：Windows Server 上要隨著同步群組同步的路徑。
- **雲端階層**：切換啟用或停用雲端階層的開關，可將不常使用或存取的檔案分層至 Azure 檔案服務。
- **磁碟區可用空間**：伺服器端點所在磁碟區要保留的可用空間量。 例如，如果具有單一伺服器端點之磁碟區上的磁碟區可用空間設定為 50%，則大約有一半的資料量會分層至 Azure 檔案服務。 無論雲端階層是否啟用，您的 Azure 檔案共用在同步群組中一律會有完整的資料複本。

選取 [建立] 以新增伺服器端點。 同步群組命名空間中的檔案現在會保持同步。 

## <a name="remove-a-server-endpoint"></a>移除伺服器端點
針對伺服器端點啟用時，雲端階層會將檔案「分層」到 Azure 檔案共用。 這樣可讓內部部署檔案共用作為快取，而不是資料集的完整複本，以便有效率地使用檔案伺服器上的空間。 不過，如果移除伺服器端點時，本機伺服器上仍有已分層的檔案，那些檔案會變成無法存取。 因此，如果您想要繼續存取檔案，您必須從 Azure 檔案服務重新叫用已分層的檔案，然後再繼續取消註冊。 

這可以透過 PowerShell Cmdlet 來完成，如下所示：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 如果裝載伺服器的本機磁碟區沒有足以重新叫用所有已分層資料的可用空間，`Invoke-StorageSyncFileRecall` Cmdlet 將會失敗。  

移除伺服器端點：

1. 瀏覽至您的伺服器註冊所在的儲存體同步服務。
2. 瀏覽至需要的同步群組。
3. 在儲存體同步服務中，移除同步群組中所要移除的伺服器端點。 這可以透過以滑鼠右鍵按一下 [同步群組] 窗格中的相關伺服器端點來完成。

    ![從同步群組移除伺服器端點](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>後續步驟
- [向 Azure 檔案同步 (預覽) 註冊/取消註冊伺服器](storage-sync-files-server-registration.md)
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)