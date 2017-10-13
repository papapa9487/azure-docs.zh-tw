---
title: "備份和還原 SQL Server | Microsoft Docs"
description: "描述 Azure 虛擬機器上執行的 SQL Server 資料庫之備份和還原考量。"
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 65557938673c5442758396a47873be1016e0f71b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure 虛擬機器中的 SQL Server 備份和還原
## <a name="overview"></a>概觀
Azure 儲存體會針對每個 Azure VM 磁碟維護 3 個複本，以確保針對資料遺失或實體資料損毀的保護。 因此，您並不需要和內部部署一樣地擔心上述問題。 不過，您仍然應該備份 SQL Server 資料庫，以避免應用程式或使用者錯誤 (例如插入錯誤資料或刪除資料表)，並能夠還原至某個時間點。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

針對在 Azure VM 中執行的 SQL Server，您可以藉助備份檔案目的地的連接磁碟，使用原生備份和還原技術。 不過，根據該 [虛擬機器的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)而定，您可以連接到 Azure 虛擬機器的磁碟數有所限制。 磁碟管理的負擔也需要加以考量。

從 SQL Server 2014 開始，您可以備份和還原至 Microsoft Azure Blob 儲存體。 SQL Server 2016 也提供這個選項的增強功能。 此外，針對儲存在 Microsoft Azure Blob 儲存體的資料庫檔案，SQL Server 2016 提供的選項，可使用 Azure 的快照集，用於幾乎即時的備份和快速的還原作業。 本文章提供這些選項的概觀，而您可以在 [SQL Server 備份及還原與 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148.aspx)中找到其他資訊。

> [!NOTE]
> 如需備份超大型資料庫選項的討論，請參閱 [適用於 Azure 虛擬機器的多 TB 級 SQL Server 資料庫備份策略](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)。
> 
> 

下列章節包含 Azure 虛擬機器所支援之不同版本 SQL Server 的特定資訊。

## <a name="sql-server-virtual-machines"></a>SQL Server 虛擬機器
當您的 SQL Server 執行個體在 Azure 虛擬機器上執行時，您的資料庫檔案已經位於 Azure 中的資料磁碟。 這些磁碟存在於 Azure Blob 儲存體中。 因此，備份資料庫的原因和方法就會稍有不同。 請考慮下列。 

* 您不再需要執行資料庫備份來提供硬體或媒體故障防護，因為 Microsoft Azure 提供這項防護，做為 Microsoft Azure 服務的一部分。
* 您仍然需要執行資料庫備份來提供防護，以免發生使用者錯誤，或基於保存之目的、稽核的原因或系統管理目的而備份。
* 您可以直接在 Azure 中儲存備份的檔案。 如需詳細資訊，請參閱下列各節提供的不同版本 SQL Server 的指引。

## <a name="sql-server-2016"></a>SQL Server 2016
Microsoft SQL Server 2016 支援在 SQL Server 2014 中也能找到的 [使用 Azure Blob 進行備份及還原](https://msdn.microsoft.com/library/jj919148.aspx) 功能。 不過它也包含下列增強功能：

| 2016 增強功能 | 詳細資料 |
| --- | --- |
| **串接** |當備份至 Microsoft Azure Blob 儲存體時，SQL Server 2016 支援備份至多個 Blob，以啟用可高達 12.8 TB 之大型資料庫的備份。 |
| **快照集備份** |藉由 Azure 快照集，SQL Server 檔案快照集備份對使用 Azure Blob 儲存體服務儲存的資料庫檔案，提供幾乎即時的備份及快速還原。 這項功能可簡化備份和還原原則。 檔案快照集備份也支援還原時間點。 如需詳細資訊，請參閱 [適用於在 Azure 中的資料庫檔案的快照集備份](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)。 |
| **管理備份排程** |SQL Server Managed Backup 至 Azure 現在支援自訂排程。 如需詳細資訊，請參閱 [SQL Server Managed Backup 至 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。 |

如需在使用 Azure Blob 儲存體時使用 SQL Server 2016 功能的教學課程，請參閱 [教學課程：使用 Microsoft Azure Blob 儲存體服務搭配 SQL Server 2016 資料庫](https://msdn.microsoft.com/library/dn466438.aspx)。

## <a name="sql-server-2014"></a>SQL Server 2014
SQL Server 2014 包含下列增強功能：

1. **備份和還原至 Azure**：
   
   * *SQL Server 備份至 URL* 現在也支援在 SQL Server Management Studio 中使用。 現在，在 SQL Server Management Studio 中使用「備份」或「還原」工作或維護計畫精靈時，已可使用備份至 Azure 的選項。 如需詳細資訊，請參閱 [SQL Server 備份至 URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)。
   * *SQL Server Managed Backup 至 Azure* 具有讓備份管理自動化的新功能。 這特別適用於在 Azure 機器上執行的 SQL Server 2014 執行個體之自動化備份管理。 如需詳細資訊，請參閱 [SQL Server Managed Backup 至 Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)。
   * *自動化備份*在 Azure 中的 SQL Server VM 所有現有和新的資料庫上，提供額外的自動化來自動啟用 *SQL Server Managed Backup 至 Azure*。 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份](virtual-machines-windows-sql-automated-backup.md)。
   * 如需 SQL Server 2014 備份至 Azure 的所有選項概觀，請參閱 [使用 Microsoft Azure Blob 儲存體服務備份及還原 SQL Server](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)。
2. **加密**：SQL Server 2014 支援在建立備份時加密資料。 它支援數種加密演算法以及使用憑證或非對稱金鑰。 如需詳細資訊，請參閱 [備份加密](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx)。

## <a name="sql-server-2012"></a>SQL Server 2012
如需 SQL Server 2012 中 SQL Server 備份及還原的詳細資訊，請參閱 [SQL Server 資料庫備份及還原 (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)。

從 SQL Server 2012 SP1 累計更新 2 開始，您可以備份到 Azure Blob 儲存體服務及從中還原。 這項增強功能可用來在於「Azure 虛擬機器」或在內部部署執行個體上執行的 SQL Server 上，備份 SQL Server 資料庫。 如需詳細資訊，請參閱 [SQL Server 備份及還原與 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)。

使用 Azure Blob 儲存體服務的一些優點包括：能夠略過針對連接的磁碟所設下的 16 個磁碟限制、容易管理，以及基於移轉或災害復原的目的，在 Azure 虛擬機器上執行之 SQL Server 執行個體的另一個執行個體或是內部部署執行個體可以直接使用備份檔案。 若要使用 Azure Blob 儲存體服務進行 SQL Server 備份的優點完整清單，請參閱 *SQL Server 備份及還原與 Azure Blob 儲存體服務* 中的 [優點](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)一節。

如需最佳做法建議和疑難排解資訊，請參閱 [備份與還原最佳做法 (Azure Blob 儲存體服務)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)。

## <a name="sql-server-2008"></a>SQL Server 2008
如需在 SQL Server 2008 R2 中進行 SQL Server 備份及還原，請參閱 [在 SQL Server 中備份和還原資料庫 (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)。

如需在 SQL Server 2008 中進行 SQL Server 備份及還原，請參閱 [在 SQL Server 中備份和還原資料庫 (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)。

## <a name="next-steps"></a>後續步驟
如果您計畫在 Azure VM 中部署 SQL Server，您可以在下列教學課程找到佈建的指示：[利用 Azure Resource Manager 在 Azure 中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

雖然備份和還原可用來將資料移轉，但對於移轉到 Azure VM 上的 SQL Server，可能仍有更容易的資料移轉路徑。 如需移轉選項和建議的完整討論，請參閱 [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

請檢閱其他 [在 Azure 虛擬機器中執行 SQL Server 的資源](virtual-machines-windows-sql-server-iaas-overview.md)。

