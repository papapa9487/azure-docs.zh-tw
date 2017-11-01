---
title: "關於 Azure 檔案服務的常見問題集 | Microsoft Docs"
description: "尋找關於 Azure 檔案服務之常見問題集的解答。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>關於 Azure 檔案服務的常見問題集
[Azure 檔案服務](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) \(英文\) 提供雲端中受到完整管理的檔案共用，可透過業界標準[伺服器訊息區塊 (SMB) 通訊協定](storage-files-introduction.md) (也稱為 Common Internet File System 或 CIFS) 存取。 Windows、Linux 和 macOS 的雲端部署或內部部署可同時掛接 Azure 檔案共用。 此外，透過 Azure 檔案同步 (預覽) 可以在 Windows Server 上快取 Azure 檔案共用，以便在資料的使用位置附近快速存取。

本文將討論一些有關 Azure 檔案服務特性與功能 (包括 Azure 檔案同步) 的常見問題。如果您在此處找不到問題的答案，歡迎透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure 儲存體論壇](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\) 
4. Microsoft 支援服務：若要建立新的支援案例，請巡覽至 Azure 入口網站上的 [說明 + 支援] 索引標籤，然後按一下 [新增支援要求]。

## <a name="general"></a>一般
* <a id="why-files-useful"></a>**Azure 檔案服務為何很實用？**  
   Azure 檔案服務可讓您在雲端中建立檔案共用，而不需要管理實體伺服器或裝置/應用裝置的額外負荷。 這表示您可以縮減花在套用作業系統更新和更換故障磁碟的時間，我們會幫您執行所有單調乏味的工作。 若要深入了解 Azure 檔案服務可協助處理的案例，請參閱[Azure 檔案服務為何很實用](storage-files-introduction.md#why-azure-files-is-useful)。

* <a id="file-access-options"></a>**有哪些不同方式可以存取 Azure 檔案服務中的檔案？**  
    您可以使用 SMB 3.0 通訊協定或使用[儲存體總管](http://storageexplorer.com/)之類的工具，在本機電腦上掛接檔案共用以存取檔案共用中的檔案。 從您的應用程式中，您可以使用儲存體用戶端程式庫、REST API、PowerShell 或 CLI 來存取 Azure 檔案共用中的檔案。

* <a id="what-is-afs"></a>**什麼是 Azure 檔案同步？**  
    Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 它會將您的 Windows Server 轉換成 Azure 檔案共用的快速快取來達到這個目的。 您可以使用 Windows Server 上可用的任何通訊協定來存取本機資料 (包括 SMB、NFS 和 FTPS)，並且可以在世界各地擁有任何所需數量的快取。

* <a id="files-versus-blobs"></a>**為什麼我要使用 Azure 檔案共用與 Azure Blob 儲存體來儲存我的資料？**  
    Azure 檔案和 Azure Blob 儲存體都可讓您在雲端中儲存大量資料，但其適用用途稍有不同。 Azure Blob 儲存體適用於需要儲存非結構化資料的大規模雲端原生應用程式。 為了充分發揮效能並進行擴充，Azure Blob 儲存體是比真實檔案系統更為簡單的儲存體抽象概念。 此外，Azure Blob 儲存體只能透過以 REST 為基礎的用戶端程式庫 (或直接透過以 REST 為基礎的通訊協定) 來加以存取。

    另一方面，Azure 檔案服務則會特地試圖成為檔案系統，具有您已熟知且喜愛多年之內部部署作業系統的所有檔案抽象概念。 和 Azure Blob 儲存體一樣，Azure 檔案服務也提供 REST 介面和以 REST 為基礎的用戶端程式庫，但與 Azure Blob 儲存體不同之處在於，Azure 檔案服務還會提供 Azure 檔案共用的 SMB 存取權。 這表示您可以直接將 Azure 檔案共用掛接在 Windows、Linux 或 macOS 上，以及掛接在內部部署環境或雲端 VM 中，而不需要撰寫任何程式碼，或對檔案系統附加任何特殊的驅動程式。 此外，您也可以使用 Azure 檔案同步在內部部署檔案系統上快取 Azure 檔案共用，以便能在資料的使用位置附近進行快速存取。 
   
    如需 Azure 檔案服務和 Azure Blob 儲存體之間差異的更深入討論，請參閱[決定何時使用 Azure Blob 儲存體、Azure 檔案服務或 Azure 磁碟](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要深入了解 Azure Blob 儲存體，請參閱 [Blob 儲存體簡介](../blobs/storage-blobs-introduction.md)。

* <a id="files-versus-disks"></a>**為什麼我要使用 Azure 檔案共用與 Azure 磁碟？**  
    Azure 磁碟僅僅是一個磁碟。 獨立磁碟本身並沒有多少用處，若要從 Azure 磁碟獲取價值，您必須將其連結至 Azure 中執行的虛擬機器。 您是如何在內部部署伺服器中使用磁碟的，就可以依同樣方式來使用 Azure 磁碟，不論是將其作為作業系統磁碟、作為作業系統的交換空間，或是作為應用程式的專用儲存體都行。 Azure 磁碟的一個有趣用途是在雲端建立檔案伺服器，以便在您可能會使用 Azure 檔案共用的同樣位置中使用。 當您需要的部署選項 (例如，NFS 通訊協定支援或進階儲存體) 目前不受 Azure 檔案服務支援時，在 Azure VM 中部署檔案伺服器會是在 Azure 中獲得檔案儲存體的絕佳方式，且這種方式具有極高效能。 

    另一方面，執行以 Azure 磁碟作為後端儲存體的檔案伺服器一般會比使用 Azure 檔案共用來得昂貴，原因有好幾個。 首先，除了要支付磁碟儲存體的費用外，您還必須支付執行一或多個 Azure VM 的費用。 其次，您還必須管理用來執行檔案伺服器的 VM，例如負責升級作業系統等工作。最後，如果您最終需要內部部署環境內所快取的資料，您就必須自行設定和管理複寫技術 (例如，分散式檔案系統複寫) 以實現此目的。

    若要從 Azure 檔案和以 Azure 磁碟作為後端儲存體之 Azure VM 所裝載的檔案伺服器獲取最大效益，其中一個有趣的方法是在雲端 VM 所裝載的檔案伺服器上安裝 Azure 檔案同步。 如果 Azure 檔案共用位於和檔案伺服器相同的區域，您可以啟用雲端分層，並將磁碟區可用空間百分比設定為最大值 (99%)。 這可確保只須複製最少資料，就可讓您對檔案伺服器使用任何應用程式，例如任何需要 NFS 通訊協定支援的項目。

    針對 Azure 中的高效能和高可用性檔案伺服器，如需設定方法的資訊，請參閱[在 Microsoft Azure 中部署 IaaS VM 客體叢集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) \(英文\)。 如需 Azure 檔案服務和 Azure 磁碟之間差異的更深入討論，請參閱[決定何時使用 Azure Blob 儲存體、Azure 檔案服務或 Azure 磁碟](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要深入了解 Azure 磁碟，請參閱 [Azure 受控磁碟概觀](../../virtual-machines/windows/managed-disks-overview.md)。

* <a id="get-started"></a>**如何開始使用 Azure 檔案服務？**  
    開始使用 Azure 檔案的程序很簡單：只要[建立檔案共用](storage-how-to-create-file-share.md)並掛接在您慣用的作業系統即可： 

    - [掛接在 Windows 上](storage-how-to-use-files-windows.md)
    - [掛接在 Linux 上](storage-how-to-use-files-linux.md)
    - [掛接在 macOS 上](storage-how-to-use-files-mac.md)

    如需深入了解如何部署 Azure 檔案共用來取代組織中的生產環境檔案共用，請參閱[規劃 Azure 檔案服務部署](storage-files-planning.md)。

* <a id="redundancy-options"></a>**Azure 檔案服務支援何種儲存體備援選項？**  
    Azure 檔案服務目前僅支援本地備援儲存體 (LRS) 和異地備援儲存體 (GRS)。 我們計劃在未來支援區域備援儲存體 (ZRS) 和讀取權限異地備援儲存體 (RA-GRS)，但目前尚無確切時間表。

* <a id="tier-options"></a>**Azure 檔案服務目前支援哪些儲存層？**  
    Azure 檔案服務目前只支援標準儲存層。 我們目前沒有支援進階與非經常性儲存層的時間表。 請注意，您無法從僅限 Blob 的儲存體帳戶或進階儲存體帳戶建立 Azure 檔案共用。

* <a id="give-us-feedback"></a>**我真的希望 Azure 檔案服務中能加入 *x* 功能，是否有此可能？**  
    當然，Azure 檔案服務小組很樂意聽到您對於我們所提供之服務的所有意見反應。 請在 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上投票支持您認同的功能要求！ 我們期待能透過諸多新的功能讓您感到滿意。

## <a name="azure-file-sync"></a>Azure 檔案同步
* <a id="afs-region-availability"></a>**目前有哪些區域支援 Azure 檔案同步 (預覽)？**  
    Azure 檔案同步目前已在美國西部、西歐、澳大利亞東部和東南亞提供使用。 我們會在努力達成正式上市的過程中陸續支援其他區域。 如需其他資訊，請參閱[區域可用性](storage-sync-files-planning.md#region-availability)。

* <a id="cross-domain-sync"></a>**相同的同步群組中是否可以同時有加入網域和未加入網域的伺服器？**  
    是，同步群組可以包含具有不同 Active Directory 成員資格的伺服器端點，未加入網域的端點也包括在內。 雖然就技術上來說，這樣的組態是可行的，但我們不建議您將此作為標準組態，因為針對某部伺服器上的檔案/資料夾所定義的 ACL，可能無法由同步群組中的其他伺服器強制執行。 為獲得最佳結果，建議您在相同 Active Directory 樹系的伺服器、雖位於不同 Active Directory 樹系但已建立信任關係的伺服器，或是不在網域中的伺服器之間進行同步，但不要同時在上述三者之間進行同步。

* <a id="afs-change-detection"></a>**我直接在 Azure 檔案共用中透過 SMB 建立了檔案，或是透過入口網站建立了檔案。檔案需要多久才會同步到同步群組中的伺服器？** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**當同一個檔案近乎同時地在兩部伺服器上進行變更時，會發生什麼事？**  
    Azure 檔案同步會使用簡單的衝突解決策略：兩項變更皆保留。 最新寫入者會保留原始檔案名稱。 較舊的檔案則會在名稱後面附加「來源」機器和衝突號碼，其分類法如下： 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    例如，如果較舊的寫入是發生在 `CentralServer`，則 `CompanyReport.docx` 的第一個衝突會變成 `CompanyReport-CentralServer.docx`。 第二個衝突則會以 `CompanyReport-CentralServer-1.docx` 來加以識別。

* <a id="afs-storage-redundancy"></a>**Azure 檔案同步是否支援異地備援儲存體 (GRS)？**  
    是，Azure 檔案同步同時支援本地備援儲存體 (LRS) 和異地備援儲存體 (GRS)。如果在配對區域之間進行 GRS 容錯移轉，則建議您將新的區域僅視為資料的備份。 Azure 檔案同步不會自動開始同步處理新的主要區域。 

* <a id="sizeondisk-versus-size"></a>**在使用 Azure 檔案同步後，為什麼檔案的「磁碟上的大小」屬性不符合「大小」屬性？**  
    Windows 檔案總管會顯示**大小**和**磁碟上的大小**兩個屬性來代表檔案的大小。 這兩個屬性的意義稍有不同；**大小**代表檔案的完整大小，**磁碟上的大小**則代表實際儲存在磁碟上的檔案資料流大小。 兩者會因為各種不同的原因而有所不同，例如壓縮、使用重複資料刪除，而在我們的案例中則是因為 Azure 檔案同步的雲端分層。如果檔案分層到 Azure 檔案共用，則磁碟上的大小會是零，因為檔案資料流是儲存在 Azure 檔案共用，而不是儲存在磁碟上。 檔案也可能部分分層 (或部分回收)，亦即有部分檔案是在磁碟上。 當應用程式 (例如，多媒體播放程式或壓縮公用程式) 讀取部分檔案時，就會發生這種情形。 

* <a id="is-my-file-tiered"></a>**如何判斷檔案是否已分層？**  
    有幾個方法可用來確認檔案是否已分層到 Azure 檔案共用：
    
    1. **檢查檔案的檔案屬性。** 若要這樣做，請在檔案上按一下滑鼠右鍵，瀏覽至 [詳細資料]，然後向下捲動至 [屬性] 屬性。 分層的檔案會具有下列屬性組：     
        
        | 屬性代號 | 屬性 | 定義 |
        |:----------------:|-----------|------------|
        | A | 封存 | 指出該檔案應該由備份軟體進行備份。 不論檔案是進行分層還是完全儲存在磁碟上，一律會設定這個屬性。 |
        | P | 疏鬆檔案 | 指出該檔案是疏鬆檔案，也就是 NTFS 所提供的特殊化檔案類型，可供在檔案的磁碟上資料流大多空白時進行有效率地使用。 Azure 檔案同步之所以使用疏鬆檔案，是因為檔案已完全分層 (亦即其檔案資料流僅儲存在雲端) 或部分回收 (亦即檔案有一部分已經在磁碟上)。 如果檔案已完全回收到磁碟上，Azure 檔案同步便會將它從疏鬆檔案轉換為一般檔案。 |
        | L | 重新分析點 | 指出該檔案有重新分析點，也就是可供檔案系統篩選器使用的特殊指標。 Azure 檔案同步會使用重新分析點來為 Azure 檔案同步的檔案系統篩選器 (StorageSync.sys) 定義檔案在雲端中的儲存位置。 這樣一來，使用者甚至不必知道正在使用 Azure 檔案同步或如何取得 Azure 檔案共用中所含檔案的存取權，就能順暢地進行存取。 當檔案完全回收時，Azure 檔案同步就會從檔案中移除重新分析點。 |
        | O | 離線 | 指出部分或所有檔案的內容並未儲存在磁碟上。 當檔案完全回收時，Azure 檔案同步就會移除此屬性。 |

        ![檔案的 [屬性] 對話方塊，已選取 [詳細資料] 索引標籤](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        藉由在檔案總管的資料表顯示中新增 [屬性] 欄位，也可以看到資料夾中所有檔案的屬性。 若要這樣做，請以滑鼠右鍵按一下現有資料行 (例如，[大小])，選取 [詳細]，然後從下拉式清單選取 [屬性]。
        
    2. **使用 `fsutil` 來檢查檔案的重新分析點。** 如上一個選項所述，分層的檔案一定會設定重新分析點 (亦即 Azure 檔案同步之檔案系統篩選器 (StorageSync.sys) 的特殊指標)。 您可以在提升權限的命令提示字元或 PowerShell 工作階段上，使用 `fsutil` 公用程式來檢查檔案是否有重新分析點：
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        如果檔案有重新分析點，您應該會看到**重新分析標記值：0x8000001e**。 這個十六進位值是 Azure 檔案同步所擁有的重新分析點數值。輸出中也會包含重新分析資料，此資料會顯現您的檔案在 Azure 檔案共用中的路徑。

        > [!Warning]  
        > `fsutil reparsepoint` 公用程式命令也可刪除重新分析點。 除非 Azure 檔案同步工程師團隊指示您執行此命令，否則請勿這麼做，因為這可能會造成資料遺失。 

* <a id="afs-recall-file"></a>**我想要使用的檔案已分層，該如何將其回收到磁碟以在本機使用？**  
    要將檔案回收到磁碟，最簡單的方式就是將它開啟。 Azure 檔案同步的檔案系統篩選器 (StorageSync.sys) 會順暢地從 Azure 檔案共用下載檔案，您不必執行任何工作。 對於可以部分讀取的檔案類型 (例如，多媒體或 zip 檔案)，開啟檔案並不會下載整個檔案。

    您也可以使用 PowerShell 來強制回收檔案。 例如，如果您想要一次回收許多檔案 (例如資料夾內的所有檔案)，便適合使用這種方法。 在 Azure 檔案同步安裝所在的伺服器節點開啟 PowerShell 工作階段，然後執行下列 PowerShell 命令：
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**如何強制讓檔案或目錄分層？**  
    雲端分層功能在啟用時，會自動根據上次的存取和修改時間來將檔案分層，以便達到雲端端點上所指定的磁碟區可用空間百分比，不過，有時候您可能會想要以手動方式強制將檔案分層。 例如，如果您要儲存短時間內不打算再次使用的大型檔案，並且想要讓磁碟區上的可用空間現在可供其他檔案/資料夾使用，便適合使用這種方法。 您可以使用下列 PowerShell 命令來強制分層：

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Azure 檔案同步處理會自動排除哪些檔案或資料夾？**
    根據預設，Azure 檔案同步會排除下列檔案：
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    預設也會排除下列資料夾：

    - \System Volume Information
    - \$RECYCLE.BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**我想要在 Windows Server 2008 R2、Linux 或 NAS 裝置使用 Azure 檔案同步，Azure 檔案同步是否有這方面的支援？**
    Azure 檔案同步目前只支援 Windows Server 2016 和 Windows Server 2012 R2。 現階段我們沒有其他計劃，但會保持開放態度，並樂於根據客戶需求來支援其他平台。 請前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)，讓我們知道您希望我們支援哪些平台。

## <a name="security-authentication-and-access-control"></a>安全性、驗證和存取控制
* <a id="ad-support"></a>**Azure 檔案服務是否支援以 Active Directory 為基礎的驗證和存取控制？**  
    Azure 檔案服務提供兩種管理存取控制的方式：

    - 使用 SAS 時，您可以產生在一段時間內都是有效的具有特定權限的權杖。 例如，您可以產生具有指定檔案唯讀存取權並在 10 分鐘到期的權杖。 擁有此權杖的任何人，在 10 分鐘的有效時間內具有該檔案的唯讀存取權。 SAS 金鑰目前只能透過 REST API 或用戶端程式庫來獲得支援，您必須使用儲存體帳戶金鑰，透過 SMB 來掛接 Azure 檔案共用。

    - Azure 檔案同步會保留所有 ACL (以 AD 為基礎或本機)，並將這些 ACL 複寫到其會同步處理的所有伺服器端點。 Windows Server 可能已向 Active Directory 驗證，因此 Azure 檔案同步可以提供絕佳的權宜措施，直到以 AD 為基礎之驗證的完整支援和 ACL 支援到來。

    Azure 檔案服務目前無法直接支援 Active Directory。

* <a id="encryption-at-rest"></a>**如何確保我的 Azure 檔案共用會進行待用加密？**  
    待用加密預計在所有區域均預設為啟用。 針對這些區域，您不必執行任何啟用加密的動作。 針對其他區域，請參閱[伺服器端加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="access-via-browser"></a>**要如何提供透過網頁瀏覽器對特定檔案的存取權？**  
    使用 SAS 時，您可以產生在一段時間內都是有效的具有特定權限的權杖。 例如，您可以產生可在一段特定時間內對特定檔案進行唯讀存取的權杖。 任何人只要擁有此 URL，就可以在其有效時間內，直接從任何網頁瀏覽器存取檔案。 從儲存體總管之類的 UI 就能輕鬆產生 SAS 金鑰。

* <a id="file-level-permissions"></a>**可以對共用內的資料夾指定唯讀或唯寫權限嗎？**  
    如果是透過 SMB 掛接檔案共用，則您對權限沒有此層級的控制。 不過，您可以透過 REST API 或用戶端程式庫來建立共用存取簽章 (SAS)，以達到此目的。

* <a id="ip-restrictions"></a>**是否可以對 Azure 檔案共用實作 IP 限制？**  
    是，可以在儲存體帳戶層級限制 Azure 檔案共用的存取。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="data-compliance-policies"></a>**Azure 檔案服務支援哪些資料合規性原則？**  
   Azure 檔案服務和 Azure 儲存體中的其他儲存體服務都是在相同的儲存體架構上運作，而且套用相同的資料合規性原則。 如需 Azure 儲存體資料合規性的詳細資訊，您可以下載並參考 [Microsoft Azure 資料保護文件](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) \(英文\) 和 [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx) \(英文\)。

## <a name="on-premises-access"></a>內部部署存取
* <a id="expressroute-not-required"></a>**必須使用 Azure ExpressRoute 來連線到 Azure 檔案服務嗎，還是必須在內部部署環境使用 Azure 檔案同步？**  
    否，不需要 ExpressRoute 就能存取 Azure 檔案共用。 如果您要直接在內部部署環境掛接 Azure 檔案共用，您只需要開啟連接埠 445 (TCP 輸出) 以供進行網際網路存取 (這是 SMB 用來進行通訊的連接埠)。 如果您要使用 Azure 檔案同步，您只需要開啟連接埠 443 (TCP 輸出) 以供進行 HTTPS 存取 (不需要 SMB)。 不過如有需要，ExpressRoute 也可與任一存取選項搭配使用。

* <a id="mount-locally"></a>**如何在本機電腦上掛接 Azure 檔案共用？**  
    只要已開啟連接埠 445 (TCP 輸出) 且您的用戶端支援 SMB 3.0 通訊協定 (例如使用 Windows 10 或 Windows Server 2016)，即可透過 SMB 通訊協定掛接檔案共用。 如果貴組織的原則或您的 ISP 封鎖連接埠 445，您可以使用 Azure 檔案同步來存取 Azure 檔案共用。

## <a name="backup"></a>備份
* <a id="backup-share"></a>**如何備份我的 Azure 檔案共用？**  
    您可以使用定期[共用快照集 (預覽)](storage-how-to-use-files-snapshots.md) 來防範意外刪除的情況。 您可以使用 AzCopy、RoboCopy，或是可備份已掛接檔案共用的協力廠商備份工具。 

## <a name="share-snapshots"></a>共用快照集
### <a name="share-snapshots---general"></a>共用快照集 - 一般
* <a id="what-are-snaphots"></a>**何謂檔案共用快照集？**  
    Azure 檔案共用快照集可讓您建立檔案共用的唯讀版本。 它也可讓您將較舊版本的內容複製回到相同的共用或是 Azure 或內部部署環境的替代位置，以供進行進一步的修改。 若要深入了解共用快照集，請參閱[共用快照集概觀](storage-snapshots-files.md)。

* <a id="where-are-snapshots-stored"></a>**共用快照集會儲存在哪裡？**  
    共用快照集會儲存在與檔案共用相同的儲存體帳戶中。

* <a id="snapshot-perf-impact"></a>**是否會影響效能？**  
    共用快照集並沒有任何效能負擔。

* <a id="snapshot-consistency"></a>**共用快照集應用程式是否會一致？**  
    否。 共用快照集不具有應用程式一致性。 使用者必須先將寫入從應用程式排清到共用，再擷取共用快照集。

* <a id="snapshot-limits"></a>**共用快照集數目是否有任何限制？**  
    Azure 檔案服務可保留的共用快照集只限 200 個。 共用快照集不會計入共用配額，所以所有共用快照集所使用的總空間沒有每個共用的限制。 但仍會適用儲存體帳戶限制。 保留 200 個共用快照集之後，就必須先刪除舊快照集才能建立新的共用快照集。

### <a name="create-share-snapshots"></a>建立共用快照集
* <a id="file-snaphsots"></a>**可以建立個別檔案的共用快照集嗎？**  
    共用快照集是在檔案共用層級建立的。 您可以從檔案共用快照集還原個別檔案，但您無法建立檔案層級的共用快照集。 不過，如果您已擷取共用層級的共用快照集，而且想要列出已變更特定檔案的共用快照集，您可以從 Windows 所掛接之共用上的「舊版」體驗來進行。 請前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)，讓我們知道您是否需要檔案快照集功能。

* <a id="encypted-snapshots"></a>**可以建立已加密檔案共用的共用快照集嗎？**  
    您可以對已啟用待用加密的 Azure 檔案共用擷取共用快照集。 您可以將共用快照集內的檔案還原至已加密的檔案共用。 如果您的共用已加密，則共用快照集也會加密。

* <a id="geo-redundant-snaphsots"></a>**我的共用快照集是否會異地備援？**
    共用快照集會擁有和目標 Azure 檔案共用相同的備援功能。 如果您為帳戶選取了異地備援儲存體 (GRS)，則共用快照集也會在配對區域中進行備援儲存。

### <a name="manage-share-snapshots"></a>管理共用快照集
* <a id="browse-snapshots-linux"></a>**可以從 Linux 瀏覽共用快照集嗎？**  
    您可以使用 Azure CLI 2.0 在 Linux 上進行建立、列出、瀏覽和還原。

* <a id="copy-snapshots-to-other-storage-account"></a>**可以將共用快照集複製到不同的儲存體帳戶嗎？**  
    您可以將共用快照集內的檔案複製到其他位置，但不能複製共用快照集本身。

### <a name="restore-data-from-share-snapshots"></a>從共用快照集還原資料
* <a id="promote-share-snapshot"></a>**可以將共用快照集升階到基底共用嗎？**  
    您只可以將共用快照集內的資料複製到任何您想要的目的地。 不過，您無法將共用快照集升階到基底共用。

* <a id="restore-snapshotted-file-to-other-share"></a>**可以將共用快照集內的資料還原到不同的儲存體帳戶嗎？**  
    是。 您可以將共用快照集內的檔案，複製到相同或不同區域中包含相同/不同儲存體帳戶的原始位置或替代位置。 您也可以將檔案複製到內部部署環境或任何其他雲端。    
  
### <a name="cleanup-share-snapshot"></a>清除共用快照集
* <a id="delete-share-keep-snapshots"></a>**是否可以刪除共用而不刪除共用快照集？**
    如果您的共用上有使用中的共用快照集，您將無法刪除共用。 有 API 可供用來刪除共用快照集與共用，而從 Azure 入口網站也可以達到相同目的。

* <a id="delete-share-with-snapshots"></a>**如果我刪除儲存體帳戶，共用快照集會發生什麼情況？**
    如果您刪除儲存體帳戶，則共用快照集會一併刪除。

## <a name="billing-and-pricing"></a>計費和定價
* <a id="vm-file-share-network-traffic"></a>**Azure VM 和 Azure 檔案共用之間的網路流量，會計算為向訂用帳戶收費的外部頻寬嗎？**  
    如果檔案共用和 VM 位於相同的 Azure 區域，兩者之間的流量是免費的。 如果位於不同的區域，兩者之間的流量會以外部頻寬收費。

* <a id="share-snapshot-price"></a>**共用快照集需要多少成本？**
     預覽期間不會對共用快照集容量收費。 但仍會適用標準儲存體的輸出和交易成本。 正式上市後，則會對共用快照集上的容量和交易收費。
     
     共用快照集具有累加性質。 基底共用快照集便是共用本身。 所有後續的共用快照集都是累加的，而且只會儲存與上一個共用快照集相異之處。 變更的內容才會計費。 如果您的共用有 100 GiB 資料，但在上一個共用快照集之後只變更了 5 GiB，則共用快照集只會再耗用 5 GiB，並只以 105 GiB 來計費。 如需交易和標準輸出費用的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

## <a name="scale-and-performance"></a>規模和效能
* <a id="files-scale-limits"></a>**Azure 檔案服務有何規模限制？**  
    如需 Azure 檔案服務延展性和效能目標的相關資訊，請參閱 [Azure 檔案服務延展性和效能目標](storage-files-scale-targets.md)。

* <a id="need-larger-share"></a>**我需要的檔案共用比 Azure 檔案服務目前提供的還大。是否可提高我的 Azure 檔案共用大小？**  
    否，Azure 檔案共用的大小上限是 5 TiB。 這是目前的固定限制，我們無法調整。 我們正在研究將共用大小提高到 100 TiBs 的解決方案，但目前沒有時間表。

* <a id="open-handles-quota"></a>**多少個用戶端可以同時存取相同的檔案？**  
    單一檔案的開啟控制代碼配額為 2000 個。 當您取得 2000 個開啟控制代碼時，就會出現錯誤指出已達到配額。

* <a id="zip-slow-performance"></a>**嘗試將檔案解壓縮到 Azure 檔案服務時，執行速度很緩慢。我該怎麼辦？**  
    若要將大量檔案傳輸到 Azure 檔案，建議您使用 AzCopy (Windows、Linux/Unix 預覽版) 或 Azure PowerShell，因為這些工具已針對網路傳輸最佳化。

* <a id="slow-perf-windows-81-2012r2"></a>**我將 Azure 檔案共用掛接在 Windows Server 2012 R2 或 Windows 8.1 上，然後我的效能變慢了，原因為何？**  
    已知將 Azure 檔案共用掛接在 Windows Server 2012 R2 和 Windows 8.1 時會發生問題，但此問題已於 Windows 8.1 和 Windows Server 2012 R2 的 2014 年 4 月累積更新中加以修補。 請確認您已對 Windows Server 2012 R2 和 Windows 8.1 的所有執行個體套用此修補程式，以獲得最佳效能 (當然，我們一律會建議您透過 Windows Update 來取得所有 Windows 修補程式)。 如需詳細資訊，請參閱相關的知識庫文章：[當您從 Windows 8.1 或 Server 2012 R2 存取 Azure 檔案服務時效能變慢](https://support.microsoft.com/kb/3114025)。

## <a name="features-and-interoperability-with-other-services"></a>與其他服務的功能互通性
* <a id="cluster-witness"></a>**我可以使用我的 Azure 檔案共用作為 Windows Server 容錯移轉叢集的「檔案共用見證」嗎？**  
    Azure 檔案共用目前不支援此使用案例。 如需如何為 Azure Blob 儲存體設定此功能的詳細資訊，請參閱[為容錯移轉叢集部署雲端見證](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。

* <a id="containers"></a>**我是否可以在 Azure 容器執行個體上掛接 Azure 檔案共用？**  
    是，Azure 檔案共用是保存超過容器執行個體存留期之資訊的絕佳選項。 如需詳細資訊，請參閱[使用 Azure 容器執行個體來掛接 Azure 檔案共用](../../container-instances/container-instances-mounting-azure-files-volume.md)。

* <a id="rest-rename"></a>**REST API 中是否有重新命名作業？**  
    目前沒有。

* <a id="nested-shares"></a>**可以有巢狀共用，換句話說，共用下的共用嗎？**  
    否。 檔案共用是您可以掛接的虛擬驅動程式，因此不支援巢狀共用。

* <a id="ibm-mq"></a>**對 IBM MQ 使用 Azure 檔案服務**  
    IBM 已發行文件來指引 IBM MQ 客戶對其服務設定 Azure 檔案服務。 如需詳細資訊，請查看 [如何使用 Microsoft Azure 檔案服務來設定 IBM MQ 多重執行個體佇列管理員](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)。

## <a name="see-also"></a>另請參閱
* [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
* [針對 Azure 檔案同步 (預覽) 進行移難排解](storage-sync-files-troubleshoot.md)