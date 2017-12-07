---
title: "還原 Azure 資料倉儲 - 本機與異地備援 | Microsoft Docs"
description: "復原 Azure SQL 資料倉儲中資料庫之資料庫還原選項的概觀。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="sql-data-warehouse-restore"></a>SQL 資料倉儲還原
> [!div class="op_single_selector"]
> * [概觀][Overview]
> * [入口網站][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

「SQL 資料倉儲」提供本機和異地還原作為其資料倉儲災害復原功能的一部分。 使用資料倉儲備份將您的資料倉儲還原至主要區域中的某個還原點，或使用異地備援備份來還原至不同的地理區域。 本文說明還原資料倉儲的詳細資訊。

## <a name="what-is-a-data-warehouse-restore"></a>什麼是資料倉儲還原？
資料倉儲還原係指從現有或已刪除之資料倉儲的備份建立的新資料倉儲。 還原的資料倉儲會重新建立特定時間的已備份資料倉儲。 由於「SQL 資料倉儲」是一個分散式系統，因此會從儲存在 Azure Blob 中的許多備份檔案建立一個資料倉儲還原。 

資料庫還原是所有商務持續性和災害復原策略中不可或缺的一部分，因為它可在您的資料發生意外損毀或刪除之後重新建立該資料。

如需詳細資訊，請參閱：

* [SQL 資料倉儲備份](sql-data-warehouse-backups.md)
* [商務持續性概觀](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>資料倉儲還原點
使用「Azure 進階儲存體」的一項優點是，「SQL 資料倉儲」會使用「Azure 儲存體」Blob 快照集來備份主要資料倉儲。 每個快照都有一個代表快照開始時間的還原點。 若要還原資料倉儲，您需選擇一個還原點並發出還原命令。  

「SQL 資料倉儲」一律是將備份還原到新的資料倉儲。 您可以保留已還原的資料倉儲和目前的資料倉儲，或是刪除它們其中之一。 如果您想要以已還原的資料倉儲取代目前的資料倉儲，您可以將它重新命名。

如果您需要還原已刪除或暫停的資料倉儲，您可以[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>異地備援還原
您可以將資料倉儲還原到在您選擇的效能等級支援您 Azure SQL 資料倉儲的任何區域。 請注意，在預覽期間，並非所有區域均支援 9000 和 18000 DWU。

> [!NOTE]
> 若要執行異地備援還原，您不可選擇退出這項功能。
> 
> 

## <a name="restore-timeline"></a>還原時間軸
您可以將資料庫還原到過去七天內的任何可用還原點。 快照集每四到八個小時會啟動，並且可供使用七天。 當快照集存在時間超過七天，它就會過期，而且無法再使用其還原點。

## <a name="restore-costs"></a>還原成本
所還原資料倉儲的儲存體收費方式是以「Azure 進階儲存體」費率計費。 

如果您將已還原的資料倉儲暫停，會依據 Azure 進階儲存體費率向您收取儲存體費用。 暫停的好處是不會向您收取 DWU 計算資源的費用。

如需 SQL 資料倉儲價格的相關詳細資訊，請參閱 [SQL 資料倉儲價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="uses-for-restore"></a>還原的用途
資料倉儲還原的主要用途是在發生意外資料遺失或損毀之後復原資料。

您也可以使用資料倉儲還原來保留超過七天的備份。 還原備份之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 

## <a name="related-topics"></a>相關主題
### <a name="scenarios"></a>案例
* 如需商務持續性概觀，請參閱[商務持續性概觀](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

若要執行資料倉儲還原，請使用下列方式來進行還原：

* Azure 入口網站 - 請參閱[使用 Azure 入口網站來還原資料倉儲](sql-data-warehouse-restore-database-portal.md)
* PowerShell Cmdlet - 請參閱[使用 PowerShell Cmdlet 來還原資料倉儲](sql-data-warehouse-restore-database-powershell.md)
* REST API - 請參閱[使用 REST API 來還原資料倉儲](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
