---
title: "Azure 儲存體簡介 | Microsoft Docs"
description: "Azure 儲存體 (Microsoft 的雲端資料儲存體) 簡介。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 163f35682a4fdaa971f715c7429153bfdcf6a584
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
<!-- this is the same version that is in the MVC branch -->
# <a name="introduction-to-microsoft-azure-storage"></a>Microsoft Azure 儲存體簡介

Microsoft Azure 儲存體是 Microsoft 管理的雲端服務，可提供高度可用、安全、持久、可擴充和備援的儲存體。 Microsoft 會為您進行維護和處理重大問題。 

Azure 儲存體包含三項資料服務：Blob 儲存體、檔案儲存體和佇列儲存體。 Blob 儲存體同時支援標準和進階儲存體，而進階儲存體僅使用 SSD 以取得可能最快的效能。 另一項功能是非經常性存取儲存體，可讓您以較低的成本儲存大量很少存取的資料。

您會在本文中了解下列各項：
* Azure 儲存體服務
* 儲存體帳戶類型
* 存取您的 blob、佇列和檔案
* 加密
* 複寫 
* 將資料傳入或傳出儲存體
* 許多可用的儲存體用戶端程式庫。 


<!-- RE-ENABLE THESE AFTER MVC GOES LIVE 
To get up and running with Azure Storage quickly, check out one of the following Quickstarts:
* [Create a storage account using PowerShell](storage-quick-create-storage-account-powershell.md)
* [Create a storage account using CLI](storage-quick-create-storage-account-cli.md)
-->


## <a name="introducing-the-azure-storage-services"></a>Azure 儲存體服務簡介

若要使用 Azure 儲存體所提供的任何服務 (Blob 儲存體、檔案儲存體和佇列儲存體)，請先建立儲存體帳戶，再從該儲存體帳戶中的特定服務傳入/出資料。 

## <a name="blob-storage"></a>Blob 儲存體

Blob 基本上類似您在電腦 (或平板電腦、行動裝置等) 上儲存的檔案。 它們可以是圖片、Microsoft Excel 檔案、HTML 檔案、虛擬硬碟 (VHD)、巨量資料 (例如記錄、資料庫備份) - 幾乎涵蓋任何項目。 Blob 會儲存在容器 (類似於資料夾) 中。 

在 Blob 儲存體中儲存檔案之後，您可以從世界各地使用 URL、REST 介面，或其中一個 Azure SDK 儲存體用戶端程式庫存取這些檔案。 儲存體用戶端程式庫提供多種語言，包括 Node.js、Java、PHP、Ruby、Python 和 .NET。 

Blob 有三種類型：區塊 Blob、附加 Blob 和分頁 Blob (用於 VHD 檔案)。

* 區塊 Blob 用來保存高達 4.7 TB 的一般檔案。 
* 分頁 Blob 用來保存隨機存取檔案 (大小上限為 8 TB)。 這些用來備份 VM 的 VHD 檔案。
* 附加 Blob 和區塊 Blob 相似，由區塊所組成，但已針對附加作業最佳化。 這些 Blob 用於將資訊記錄至多個 VM 中的相同 Blob 之類的事情。

若是超大型資料集，網路限制會使得透過線路上傳或下載資料至 Blob 儲存體變得不切實際，您可以將一組硬碟送至 Microsoft，以便直接從資料中心匯入或匯出資料。 請參閱 [使用 Microsoft Azure 匯入/匯出服務將資料移轉至 Blob 儲存體](../storage-import-export-service.md)。

## <a name="file-storage"></a>檔案儲存體

Azure 檔案服務可讓您設定高可用性網路檔案共用，其可使用標準伺服器訊息區塊 (SMB) 通訊協定來存取。 這表示多個 VM 可以透過讀取和寫入權限共用相同的檔案。 您也可以使用 REST 介面或儲存體用戶端程式庫來讀取檔案。 

區分 Azure 檔案儲存體與公司檔案共用上的檔案的方法之一，就是您可以使用指向檔案並包含共用存取簽章 (SAS) 權杖的 URL，從世界各地存取檔案。 您可以產生 SAS 權杖；SAS 權杖可允許特定一段時間內私人資產的特定存取。 

檔案共用可以用於許多常見案例： 

* 許多內部部署應用程式會使用檔案共用。 這項功能可讓您更輕鬆地將共用資料的應用程式移轉至 Azure。 如果您將檔案共用掛接至內部部署應用程式使用的相同磁碟機代號，則存取檔案共用的應用程式部分應會在變動最小 (如果有的話) 的情況下運作。

* 組態檔可以儲存在檔案共用上並從多個 VM 進行存取。 由群組中多個開發人員所用的工具和公用程式可以儲存於檔案共用，確保所有人都可以找到它們並使用相同的版本。

* 可以寫入檔案共用且稍後進行處理或分析的資料範例有三個：診斷記錄、計量和損毀傾印。

這個階段不支援以 Active Directory 為基礎的驗證和存取控制清單 (ACL)，但未來將會提供支援。 儲存體帳戶認證用來提供檔案共用存取權的驗證。 這表示任何掛接共用的人員都具有共用的完整讀取/寫入權限。

## <a name="queue-storage"></a>佇列儲存體

Azure 佇列服務用來儲存及擷取訊息。 佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬則訊息。 佇列通常用來儲存要以非同步方式處理的訊息清單。 

例如，假設您希望客戶能夠上傳圖片，而且要建立每張圖片的縮圖。 您可以讓客戶在上傳圖片時等候您建立縮圖。 另外，也可以使用佇列。 當客戶完成上傳時，將訊息寫入佇列。 然後讓 Azure Function 從佇列擷取訊息並建立縮圖。 這項處理的每個部分都可以個別調整，讓您在針對您的使用量進行微調時有更多控制權。

<!-- this bookmark is used by other articles; you'll need to update them before this goes into production ROBIN-->
## <a name="table-storage"></a>表格儲存體
<!-- add a link to the old table storage to this paragraph once it's moved -->
標準 Azure 表格儲存體現在屬於 Cosmos DB。 Azure 表格儲存體也有進階資料表，可提供輸送量最佳化的資料表、全域發佈，以及自動次要索引。 若要深入了解並試用新的進階體驗，請查看 [Azure Cosmos DB：資料表 API](https://aka.ms/premiumtables)。

## <a name="disk-storage"></a>磁碟儲存體

Azure 儲存體小組也擁有磁碟，其中包含虛擬機器所使用的所有受控和非受控磁碟功能。 如需有關這些功能的詳細資訊，請參閱[計算服務文件](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。

## <a name="types-of-storage-accounts"></a>儲存體帳戶類型 

下表顯示各種儲存體帳戶，以及各自可以使用的物件。

|**儲存體帳戶的類型**|**一般用途：標準**|**一般用途：進階**|**Blob 儲存體、經常性存取和非經常性存取層**|
|-----|-----|-----|-----|
|**支援的服務**| Blob、檔案、佇列服務 | Blob 服務 | Blob 服務|
|**支援的 Blob 類型**|區塊 Blob、分頁 Blob 及附加 Blob | 分頁 Blob | 區塊 Blob 和附加 Blob|

### <a name="general-purpose-storage-accounts"></a>一般用途的儲存體帳戶

一般用途的儲存體帳戶有兩種。 

#### <a name="standard-storage"></a>標準儲存體 

標準儲存體帳戶是最廣泛使用的儲存體帳戶，可用於所有類型的資料。 標準儲存體帳戶會使用磁性媒體來儲存資料。

#### <a name="premium-storage"></a>進階儲存體

進階儲存體可為分頁 blob 提供高效能儲存體，主要用於 VHD 檔案。 進階儲存體帳戶使用 SSD 來儲存資料。 Microsoft 建議對所有 VM 使用進階儲存體。

### <a name="blob-storage-accounts"></a>Blob 儲存體帳戶

Blob 儲存體帳戶是專門的儲存體帳戶，用來儲存區塊 blob 和附加 blob。 您無法將分頁 blob 儲存在這些帳戶中，因此無法儲存 VHD 檔案。 這些帳戶允許您將存取層設定為經常性存取或非經常性存取；存取層可以隨時變更。 

經常性存取層用於經常存取的檔案 - 您需支付較高的儲存體成本，但存取 blob 的成本低很多。 對於非經常性存取層中儲存的 blob，您需支付較高的 blob 存取成本，但儲存體的成本低很多。

## <a name="accessing-your-blobs-files-and-queues"></a>存取您的 blob、檔案和佇列

每個儲存體帳戶都有兩種驗證金鑰，任一種金鑰均可用於任何作業。 金鑰有兩個，所以您可以偶爾變換金鑰以增強安全性。 務必將金鑰放在安全的地方，因為其擁有權 (連同帳戶名稱) 允許無限制存取儲存體帳戶中的所有資料。 

本節說明保護儲存體帳戶及其資料的兩種方式。 如需保護儲存體帳戶和資料的詳細資訊，請參閱 [Azure 儲存體安全性指南](storage-security-guide.md)。

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>使用 Azure AD 保護儲存體帳戶的存取權

控制儲存體帳戶金鑰的存取權，是保護儲存體資料存取的其中一種方式。 使用 Resource Manager 角色型存取控制 (RBAC)，您可以將角色指派給使用者、群組或應用程式。 這些角色會繫結至特定一組允許或不允許的動作。 使用 RBAC 授與儲存體帳戶存取權時，只會處理該儲存體帳戶的管理作業，例如變更存取層。 您無法使用 RBAC 來授與資料物件 (如特定容器或檔案共用) 的存取權。 不過，您可以使用 RBAC 授與儲存體帳戶金鑰的存取權，而這些金鑰可用來讀取資料物件。 

### <a name="securing-access-using-shared-access-signatures"></a>使用共用存取簽章保護存取權 

您可以使用共用存取簽章和預存存取原則來保護您的資料物件。 共用存取簽章 (SAS) 是一個字串，包含可附加至資產 URI 的安全性權杖，可讓您委派特定儲存體物件的存取權，以及指定存取的權限和日期/時間範圍之類的限制。 這項功能有廣泛的功能。 如需詳細資訊，請參閱[使用共用存取簽章 (SAS)](storage-dotnet-shared-access-signature-part-1.md)。

### <a name="public-access-to-blobs"></a>公開存取 Blob

Blob 服務可讓您提供容器與其 Blob 或特定 Blob 的公開存取權。 當您將容器或 Blob 指定為公用時，任何人都可以進行匿名讀取；不需要驗證。 例如，當您有網站使用 Blob 儲存體中的影像、影片或文件時，您會想要執行此動作。 如需詳細資訊，請參閱[管理對容器與 Blob 的匿名讀取權限](../blobs/storage-manage-access-to-resources.md) 

## <a name="encryption"></a>加密

有一些基本加密類型可用於儲存體服務。 

### <a name="encryption-at-rest"></a>待用加密 

您可以在 Azure 儲存體帳戶的檔案服務 (預覽) 或 Blob 服務上啟用儲存體服務加密 (SSE)。 若已啟用，寫入特定服務的所有資料會在寫入之前加密。 當您讀取資料時，資料會在傳回之前解密。 

### <a name="client-side-encryption"></a>用戶端加密

儲存體用戶端程式庫具有您可以呼叫的方法，在透過網路將資料從用戶端傳送到 Azure 之前，可以程式設計方式將資料加密。 它會以加密方式儲存，這表示待用時也會加密。 讀回資料時，您會在接收資訊之後加以解密。 

### <a name="encryption-in-transit-with-azure-file-shares"></a>透過 Azure 檔案共用進行傳輸時加密

如需共用存取簽章的詳細資訊，請參閱 [使用共用存取簽章 (SAS)](../storage-dotnet-shared-access-signature-part-1.md) 。 如需安全存取儲存體帳戶的詳細資訊，請參閱[管理對容器與 Blob 的匿名讀取權限](../blobs/storage-manage-access-to-resources.md)和 [Azure 儲存體服務的驗證](https://msdn.microsoft.com/library/azure/dd179428.aspx)。

如需保護儲存體帳戶和加密的詳細資訊，請參閱 [Azure 儲存體安全性指南](storage-security-guide.md)。

## <a name="replication"></a>複寫

為了確保您資料的持久性，Azure 儲存體能夠保持 (及管理) 多個資料複本。 這稱為複寫，有時候稱為備援。 當您設定儲存體帳戶時，您可選取複寫類型。 在大部分情況下，在設定儲存體帳戶之後可以修改此設定。 

所有儲存體帳戶都有**本機備援儲存體 (LRS)**。 這表示 Azure 儲存體會在設定儲存體帳戶時指定的資料中心管理三個資料複本。 將變更認可至一個複本時，系統會在傳回成功前更新其他兩個複本。 這表示三個複本一直都保持同步。此外，這三個複本位於不同的容錯網域和升級網域中，這表示即使保留資料的儲存體節點失敗或已離線進行更新，仍可使用資料。 

**本地備援儲存體 (LRS)**

如上所述，使用 LRS，您在單一資料中心內有三個資料複本。 如果儲存體節點失敗或已離線進行更新，這會處理資料變得無法使用的問題，但不是整個資料中心變得無法使用的情況。

**區域備援儲存體 (ZRS)**

區域備援儲存體 (ZRS) 會維護三個本機資料複本，以及另一組的三個資料複本。 第二組的三個複本會以非同步方式、跨一或兩個區域內的資料中心複寫。 請注意，ZRS 僅適用於一般用途的儲存體帳戶中的區塊 Blob。 此外，建立儲存體帳戶並選取 ZRS 後，就無法轉換為採用任何其他類型的複寫，反之亦然。

ZRS 帳戶可提供比 LRS 更高的持久性，但 ZRS 帳戶並沒有計量或記錄功能。 

**異地備援儲存體 (GRS)**

異地備援儲存體 (GRS) 會在主要區域中維護三個本機資料複本，加上在距離主要區域數百哩的次要區域中維護另一組的三個資料複本。 在主要區域發生問題時，Azure 儲存體將會容錯移轉至次要區域。 

**讀取權限異地備援儲存體 (RA-GRS)** 

除了您取得次要位置中資料的讀取權限以外，讀取權限的異地備援儲存體與 GRS 完全相同。 如果主要資料中心暫時變得無法使用，您可以繼續從次要位置讀取資料。 這很有幫助。 例如，您可能有變更為唯讀模式並指向次要複本的 Web 應用程式，允許即使無法取得更新也可進行某些存取。 

> [!IMPORTANT]
> 除非您建立帳戶時指定了 ZRS，否則就可以在建立儲存體帳戶之後，變更資料的複寫方式。 不過請注意，從 LRS 切換到 GRS 或 RA-GRS 時，可能必須支付額外的單次資料傳輸成本。
>

如需有關複寫的詳細資訊，請參閱 [Azure 儲存體複寫](storage-redundancy.md)。

如需災害復原資訊，請參閱[如果 Azure 儲存體發生中斷怎麼辦](storage-disaster-recovery-guidance.md)。

如需有關如何利用 RA-GRS 儲存體來確保高可用性的範例，請參閱[使用 RA-GRS 設計高可用性應用程式](storage-designing-ha-apps-with-ragrs.md)。

## <a name="transferring-data-to-and-from-azure-storage"></a>從 Azure 儲存體來回傳輸資料

您可以使用 AzCopy 命令列公用程式，在儲存體帳戶內或跨儲存體帳戶複製 Blob 和檔案資料。 請參閱下列其中一篇文章中的說明：

* [使用適用於 Windows 的 AzCopy 傳輸資料](storage-use-azcopy.md)
* [使用適用於 Linux 的 AzCopy 傳輸資料](storage-use-azcopy-linux.md)

AzCopy 建置於 [Azure 資料移動程式庫](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)之上，其目前可供預覽。

Azure 匯入/匯出服務可用於從儲存體帳戶匯入或匯出大量 blob 資料。 您可準備多個硬碟並將其郵寄至 Azure 資料中心，這些硬碟會在其中從硬碟機傳入/傳出資料並將硬碟機傳送給您。 如需匯入/匯出服務的詳細資訊，請參閱 [使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../storage-import-export-service.md)。

## <a name="pricing"></a>價格

如需 Azure 儲存體價格的詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="storage-apis-libraries-and-tools"></a>儲存體 API、程式庫和工具
任何可提出 HTTP/HTTPS 要求的語言皆可存取 Azure 儲存體資源。 另外，Azure 儲存體還提供了幾種熱門語言的程式設計程式庫。 這些程式庫可透過處理詳細資料 (例如同步和非同步叫用、進行批次作業、例外狀況管理、自動重試、運作方式等等) 來簡化使用 Azure 儲存體的許多項目。 程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

### <a name="azure-storage-data-services"></a>Azure 儲存體資料服務
* [儲存體服務 REST API](/rest/api/storageservices/)
* [適用於 .NET 的儲存體用戶端程式庫](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [適用於 Java/Android 的儲存體用戶端程式庫](https://azure.microsoft.com/develop/java/)
* [適用於 Node.js 的儲存體用戶端程式庫](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [適用於 PHP 的儲存體用戶端程式庫](https://azure.microsoft.com/develop/php/)
* [適用於 Python 的儲存體用戶端程式庫](https://azure.microsoft.com/develop/python/)
* [適用於 Ruby 的儲存體用戶端程式庫](https://azure.microsoft.com/develop/ruby/)
* [適用於 PowerShell 的儲存體 Cmdlet](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [適用於 CLI 2.0 的儲存體命令](/cli/azure/storage)

## <a name="next-steps"></a>後續步驟

* [深入了解 Blob 儲存體](../blobs/storage-blobs-introduction.md)
* [深入了解檔案儲存體](../storage-files-introduction.md)
* [深入了解佇列儲存體](../queues/storage-queues-introduction.md)

<!-- RE-ENABLE THESE AFTER MVC GOES LIVE 
To get up and running with Azure Storage quickly, check out one of the following Quickstarts:
* [Create a storage account using PowerShell](storage-quick-create-storage-account-powershell.md)
* [Create a storage account using CLI](storage-quick-create-storage-account-cli.md)
-->

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!--* [Get started with Azure Storage in five minutes](storage-getting-started-guide.md)
-->

### <a name="for-administrators"></a>針對系統管理員
* [搭配使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)
* [使用 Azure CLI 搭配 Azure 儲存體](../storage-azure-cli.md)

### <a name="for-net-developers"></a>針對 .NET 開發人員
* [以 .NET 開始使用 Azure Blob 儲存體](../blobs/storage-dotnet-how-to-use-blobs.md)
* [以 .NET 開始使用 Azure 表格儲存體](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [以 .NET 開始使用 Azure 佇列儲存體](../storage-dotnet-how-to-use-queues.md)
* [在 Windows 上開始使用 Azure 檔案儲存體](../storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>針對 Java/Android 開發人員
* [如何使用 Java 的 Blob 儲存體](../blobs/storage-java-how-to-use-blob-storage.md)
* [如何使用 Java 的表格儲存體](../../cosmos-db/table-storage-how-to-use-java.md)
* [如何使用 Java 的佇列儲存體](../storage-java-how-to-use-queue-storage.md)
* [如何使用 Java 的檔案儲存體](../storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>針對 Node.js 開發人員
* [如何使用 Node.js 的 Blob 儲存體](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [如何使用 Node.js 的表格儲存體](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [如何使用 Node.js 的佇列儲存體](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>針對 PHP 開發人員
* [如何使用 PHP 的 Blob 儲存體](../blobs/storage-php-how-to-use-blobs.md)
* [如何使用 PHP 的表格儲存體](../../cosmos-db/table-storage-how-to-use-php.md)
* [如何使用 PHP 的佇列儲存體](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>針對 Ruby 開發人員
* [如何使用 Ruby 的 Blob 儲存體](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [如何使用 Ruby 的表格儲存體](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [如何使用 Ruby 的佇列儲存體](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>針對 Python 開發人員
* [如何使用 Python 的 Blob 儲存體](../blobs/storage-python-how-to-use-blob-storage.md)
* [如何使用 Python 的表格儲存體](../../cosmos-db/table-storage-how-to-use-python.md)
* [如何使用 Python 的佇列儲存體](../storage-python-how-to-use-queue-storage.md)   
* [如何使用 Python 的檔案儲存體](../storage-python-how-to-use-file-storage.md) 
-->
