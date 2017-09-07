---
title: "關於 Azure 檔案儲存體的常見問題集 | Microsoft Docs"
description: "尋找關於 Azure 檔案儲存體之常見問題集的解答。"
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
ms.date: 07/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 17868591e1056ff2bddde0e082695af529f58f02
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="frequently-asked-questions-about-azure-file-storage"></a>關於 Azure 檔案儲存體的常見問題集

## <a name="general"></a>一般
* **問：什麼是 Azure 檔案儲存體？**  
   
    Azure 檔案儲存體是 Azure 中的分散式檔案系統。 其所提供的 SMB 通訊協定介面可讓使用者將儲存體當做原生共用，掛接到支援的 Azure 虛擬機器或內部部署機器上。

* **問：Azure 檔案儲存體為何很實用？**  
   
    Azure 檔案儲存體可跨多部 VM 和平台存取共用資料。 請參閱 [Azure 檔案儲存體為何很實用](storage-files-introduction.md#why-azure-file-storage-is-useful)。

* **問：何時應該使用 Azure 檔案、Azure Blob 或 Azure 磁碟？**  
   
    Microsoft Azure 提供在雲端中儲存及存取資料的幾種方式。  
   
    Azure 檔案儲存體 - 提供 SMB 介面、用戶端程式庫和 REST 介面，允許從任何位置輕鬆存取儲存的檔案。  
   
    Azure Blob - 提供用戶端程式庫和 REST 介面，允許在區塊 Blob 中大規模地儲存及存取非結構化資料。  
   
    Azure 資料磁碟 - 提供用戶端程式庫和 REST 介面，允許持續從連結的虛擬硬碟儲存和存取資料。  
   
    如需詳細資訊，請參閱[決定何時使用 Azure Blob、Azure 檔案或 Azure 資料磁碟](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* **問：如何開始使用 Azure 檔案儲存體？**  
   
    您可以從建立 Azure 檔案共用開始。 您可以使用 Azure 入口網站、Azure 儲存體 PowerShell Cmdlet、Azure 儲存體用戶端程式庫或 Azure 儲存體 REST API 來建立 Azure 檔案共用。在本教學課程中，您將了解：

    * [了解如何使用入口網站建立 Azure 檔案共用](storage-how-to-create-file-share.md#create-file-share-through-the-portal)
    * [了解如何使用 PowerShell 建立 Azure 檔案共用](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [了解如何使用 CLI 建立 Azure 檔案共用](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **問：Azure 檔案儲存體支援何種複寫？**  
   
    Azure 檔案儲存體目前只支援 LRS 或 GRS。 我們打算支援 RA-GRS，但時程尚未決定。

## <a name="security-authentication-and-access-control"></a>安全性、驗證和存取控制

* **問：有哪些不同方式可在 Azure 檔案儲存體中存取檔案？**
    
    您可以使用 SMB 3.0 通訊協定或使用[儲存體總管](http://storageexplorer.com/)之類的工具，在本機電腦上掛接檔案共用以存取檔案共用中的檔案。 從您的應用程式中，您可以使用儲存體用戶端程式庫、REST API 或 PowerShell 來存取 Azure 檔案共用中的檔案。

* **問：要如何提供透過網頁瀏覽器對特定檔案的存取權？**
    
    使用 SAS 時，您可以產生在一段時間內都是有效的具有特定權限的權杖。 例如，您可以產生可在一段特定時間內對特定檔案進行唯讀存取的權杖。 任何人只要擁有此 URL，就可以在其有效時間內，直接從任何網頁瀏覽器存取檔案。 從儲存體總管之類的 UI 就能輕鬆產生 SAS 金鑰。

* **問：可以對共用內的資料夾指定唯讀或唯寫權限嗎？**
    
    如果是透過 SMB 掛接檔案共用，則您對權限沒有此層級的控制。 不過，您可以透過 REST API 或用戶端程式庫來建立共用存取簽章 (SAS)，以達到此目的。  

* **問：如何啟用 Azure 檔案儲存體的伺服器端加密？**

    Azure 檔案儲存體的[伺服器端加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)在所有區域和公用與國內雲端公開上市。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Microsoft Azure 儲存體資源提供者 API](/rest/api/storagerp/storageaccounts)、[Azure PowerShell](https://msdn.microsoft.com/library/azure/mt607151.aspx) 或 [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)，為 Azure 檔案儲存體啟用 SSE。
    
    啟用 Azure 檔案儲存體上的 SSE 之後，該儲存體帳戶中寫入檔案儲存體的所有新資料將會自動加密。 這項功能適用於現有或新的儲存體帳戶中，寫入現有或新共用的所有新資料。 此用此功能不會額外收費。 深入了解[如何在 Azure 檔案儲存體上啟用 SSE](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* **問：Azure 檔案儲存體是否支援 Active Directory 型驗證？**
   
    我們目前不支援 AD 式驗證或 ACL，但在我們的功能要求清單中卻有它。 目前，Azure 儲存體帳戶金鑰可用來提供檔案共用的驗證。 我們的確提供透過 REST API 或用戶端程式庫使用共用存取簽章 (SAS) 的因應措施。 使用 SAS 時，您可以產生在一段時間內都是有效的具有特定權限的權杖。 例如，您可以產生具有指定檔案唯讀存取權並在 10 分鐘到期的權杖。 擁有此權杖的任何人，在 10 分鐘的有效時間內具有該檔案的唯讀存取權。
   
    僅透過 REST API 或用戶端程式庫才支援 SAS。 透過 SMB 通訊協定掛接檔案共用時，您無法使用 SAS 來委派其內容的存取。 
    
* **問：Azure 檔案儲存體支援哪些資料合規性原則？**

   Azure 檔案儲存體會在與 Azure 儲存體中其他儲存體服務相同的儲存體架構之上執行，並套用相同的資料合規性原則。 如需 Azure 儲存體資料合規性的詳細資訊，您可以下載並參考 [Microsoft Azure 資料保護文件](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)。

## <a name="on-premises-access"></a>內部部署存取

* **問：必須使用 Azure ExpressRoute 從內部部署 VM 連線到 Azure 檔案儲存體嗎？**
   
    編號 如果沒有 ExpressRoute，您仍然可以從內部部署存取檔案共用，只要將連接埠 445 (TCP 輸出) 開啟供網際網路存取即可。 不過，您可以搭配使用 ExpressRoute 與 Azure 檔案儲存體 (如果需要)。

* **問：如何在本機電腦上掛接 Azure 檔案共用？** 
    
    只要已開啟連接埠 445 (TCP 輸出) 且您的用戶端支援 SMB 3.0 通訊協定 (例如使用 Windows 10 或 Windows Server 2012)，即可透過 SMB 通訊協定掛接檔案共用。 請與您的當地 ISP 提供者合作，以將連接埠解除封鎖。 同時，您可以使用[儲存體總管](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents)檢視檔案。


## <a name="billing-and-pricing"></a>計費和定價
* **問：Azure VM 和檔案共用之間的網路流量，會計算為向訂用帳戶收費的外部頻寬嗎？**
   
    如果檔案共用和 VM 位於相同的 Azure 區域，兩者之間的流量是免費的。 如果位於不同的區域，兩者之間的流量會以外部頻寬收費。

## <a name="backup"></a>備份

* **問：如何備份我的 Azure 檔案共用？**
    
    您可以使用 AzCopy、RoboCopy，或是可備份已掛接檔案共用的協力廠商備份工具。 我們預期在不久的將來能夠擷取檔案共用的快照，您將能夠使用這項功能來備份 Azure 檔案共用。

## <a name="performance"></a>效能

* **問：Azure 檔案儲存體的調整限制有哪些？**
    如需 Azure 檔案儲存體延展性和效能目標的資訊，請參閱 [Azure 儲存體延展性和效能目標](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files)。

* **問：嘗試將檔案解壓縮到 Azure 檔案儲存體時，執行速度很緩慢。我該怎麼辦？**
    
    若要將大量檔案傳輸到 Azure 檔案儲存體，建議您使用 AzCopy (Windows、Linux/Unix 預覽版) 或 Azure PowerShell，因為這些工具已針對網路傳輸最佳化。

* **問：已發行哪些修補程式來修正 Azure 檔案儲存體的效能變慢問題？**
    
    Windows 小組最近發行了修補程式，以修正當客戶從 Windows 8.1 或 Windows Server 2012 R2 存取 Azure 檔案儲存體時效能變慢的問題。 如需詳細資訊，請查看相關聯的知識庫文章：[當您從 Windows 8.1 或 Server 2012 R2 存取 Azure 檔案儲存體時效能變慢](https://support.microsoft.com/kb/3114025)。

## <a name="features-and-interoperability-with-other-services"></a>與其他服務的功能互通性
* **問：容錯移轉叢集的「檔案共用見證」是否為 Azure 檔案儲存體的其中一個使用案例？**
   
    目前不受支援。

* **問：REST API 中是否有重新命名作業？**
   
    目前沒有。

* **問：可以有巢狀共用，換句話說，共用下的共用嗎？**
    
    編號 檔案共用是您可以掛接的虛擬驅動程式，因此不支援巢狀共用。

* **問：搭配 IBM MQ 使用 Azure 檔案儲存體**
    
    IBM 已發行文件來指引 IBM MQ 客戶設定 Azure 檔案儲存體與其服務。 如需詳細資訊，請查看 [如何使用 Microsoft Azure 檔案服務來設定 IBM MQ 多重執行個體佇列管理員](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)。


## <a name="troubleshooting"></a>疑難排解
* **問：如何針對 Azure 檔案儲存體錯誤進行疑難排解？**
    
    您可以參考 [Azure 檔案儲存體疑難排解文章](storage-troubleshoot-windows-file-connection-problems.md)以取得端對端疑難排解指引。 


## <a name="see-also"></a>另請參閱
請參閱這些連結以取得 Azure 檔案儲存體的相關詳細資訊。

### <a name="conceptual-articles-and-videos"></a>概念性文章和影片
* [Azure 檔案儲存體：適用於 Windows 和 Linux 的無摩擦雲端 SMB 檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [如何搭配使用 Azure 檔案儲存體與 Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>檔案儲存體的工具支援
* [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [使用 Azure CLI 搭配 Azure 儲存體](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [針對 Azure 檔案儲存體的問題進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>部落格文章
* [Azure 檔案儲存體現已公開推出](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 檔案儲存體內部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File storage](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/) (將資料移轉至 Azure 檔案儲存體)

### <a name="reference"></a>參考
* [Storage Client Library for .NET 參考資料](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

