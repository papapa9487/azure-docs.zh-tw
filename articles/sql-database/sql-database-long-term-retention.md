---
title: "儲存多達 10 年的 Azure SQL Database 備份 | Microsoft Docs"
description: "了解 Azure SQL Database 如何支援多達 10 年的儲存備份。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 25e651203f804fbf32d632b5f83145a3f3f72a7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>儲存多達 10 年的 Azure SQL Database 備份
許多應用程式具有法規、相容性或其他商務用途，需要您保留 Azure SQL Database [自動備份](sql-database-automated-backups.md)所提供超過 7-35 天的資料庫備份。 使用長期備份保留功能可讓您將 SQL Database 備份儲存在 Azure 復原服務保存庫中多達 10 年。 每個保存庫最多可以儲存 1000 個資料庫。 您接著可以選取資料庫中的任何備份以將其還原為新的保存庫。

> [!IMPORTANT]
> 長期備份保留期目前為預覽版本，可在下列區域使用︰澳大利亞東部、澳大利亞東南部、巴西南部、美國中部、東亞、美國東部、美國東部 2、印度中部、印度南部、日本東部、日本西部、美國中北部、北歐、美國中南部、東南亞、西歐和美國西部。
>

> [!NOTE]
> 您可以在 24 小時期間在每個保存庫啟用多達 200 個資料庫。 我們建議您對每一部伺服器使用個別的保存庫，以將這項限制的影響降到最低。 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>SQL Database 長期備份保留的運作方式

使用長期備份保留可以建立 SQL Database 伺服器與 Azure 復原服務保存庫的關聯。 

* 您必須在與建立 SQL 伺服器相同的 Azure 訂用帳戶中，以及相同的地理區域和資源群組中建立保存庫。 
* 接著，您可以針對任何資料庫設定保留原則。 原則會使每週的完整資料庫備份複製到復原服務保存庫，並保留指定的保留週期 (最多 10 年)。 
* 接著，您可以從任何備份將資料庫還原至訂用帳戶中任何伺服器的新資料庫。 Azure 儲存體會從現有的備份建立複本，且此複本不影響現有資料庫的效能。

> [!TIP]
> 如需作法指南，請參閱[設定 Azure SQL Database 長期備份保留並從中還原](sql-database-long-term-backup-retention-configure.md)。

## <a name="enable-long-term-backup-retention"></a>啟用長期備份保留

若要設定資料庫的長期備份保留︰

1. 在相同的區域、訂用帳戶與資源群組中建立 Azure 復原服務保存庫做為 SQL Database 伺服器。 
2. 向保存庫註冊伺服器。
3. 建立 Azure 復原服務保護原則。
4. 將保護原則套用到需要長期備份保留的資料庫。

請執行下列作業之一，在 Azure 復原服務保存庫中設定、管理自動備份的長期保留並從該保留還原：

* 使用 Azure 入口網站：按一下 長期備份保留 並選取資料庫，然後按一下設定。 

   ![選取進行長期備份保留的資料庫](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* 使用 PowerShell：請移至[設定 Azure SQL Database 長期備份保留並從中還原](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>還原以長期備份保留功能儲存的資料庫

從長期備份保留備份復原︰

1. 列出儲存備份的保存庫。
2. 列出對應至您邏輯伺服器的容器。
3. 列出對應至資料庫之保存庫內的資料來源。
4. 列出可用來還原的復原點。
5. 從復原點將資料庫還原到您訂用帳戶內的目標伺服器。

請執行下列作業之一，在 Azure 復原服務保存庫中設定、管理自動備份的長期保留並從該保留還原：

* 使用 Azure 入口網站：請移至[使用 Azure 入口網站管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。 

* 使用 PowerShell：請移至[使用 PowerShell 管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="get-pricing-for-long-term-backup-retention"></a>取得長期備份保留的價格

SQL Database 的長期備份保留是根據 [Azure 備份服務定價費率](https://azure.microsoft.com/pricing/details/backup/)收費。

SQL Database 伺服器註冊至保存庫之後，您需支付儲存在保存庫中之每週備份所使用的總儲存體。

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>檢視以長期備份保留儲存的可用備份

請執行下列作業之一，使用 Azure 入口網站，在 Azure 復原服務保存庫中設定、管理自動備份的長期保留，並從該保留還原：

* 使用 Azure 入口網站：請移至[使用 Azure 入口網站管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。 

* 使用 PowerShell：請移至[使用 PowerShell 管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="disable-long-term-retention"></a>停用長期保留

復原服務會根據提供的保留原則自動處理備份的清除。 

若要停止將特定資料庫的備份傳送到保存庫，請移除該資料庫的保留原則。
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> 保存庫中已有的備份不會受到影響。 當保留期限到期時，復原服務會自動刪除它們。

## <a name="long-term-backup-retention-faq"></a>長期備份保留常見問題集

**可以手動刪除保存庫中的特定備份嗎？**

目前不支援。 當保留期限已過期時，保存庫會自動清除備份。

**是否可以註冊我的伺服器來將備份儲存至多個保存庫？**

否，備份目前一次只能儲存到一個保存庫。

**我可以在不同的訂用帳戶中有保存庫與伺服器嗎？**

否，目前保存庫與伺服器必須位於相同的訂用帳戶和資源群組。

**我可以使用在不同於伺服器區域的區域中建立的保存庫嗎？**

否，保存庫與伺服器必須位於相同的區域，以將複製時間降至最低，並避免產生流量費用。

**我可以在一個保存庫中儲存幾個資料庫？**

目前每個保存庫最多可支援 1000 個資料庫。 

**每個訂用帳戶可以建立幾個保存庫？**

每個訂用帳戶最多可以建立 25 個保存庫。

**每個保存庫每天可以設定幾個資料庫？**

每個保存庫每天最多可以設定 200 個資料庫。

**長期備份保留是否可與彈性集區搭配運作？**

是。 集區中的任何資料庫可以使用保留原則設定。

**是否可以選擇建立備份的時間？**

否，SQL Database 會控制備份排程，以便將資料庫上的效能影響降到最低。

**我的資料庫啟用了透明資料加密。可以用在保存庫嗎？** 

是，支援透明資料加密。 即使原始資料庫不存在，您也可以從保存庫還原資料庫。

**如果我的訂用帳戶已暫止，則保存庫中的備份會發生什麼事？** 

如果您的訂用帳戶已暫停，我們會保留現有的資料庫和備份。 新的備份不會複製到保存庫。 在您重新啟動訂用帳戶之後，服務會繼續將備份複製到保存庫。 您的保存庫會在訂用帳戶暫止之前，使用已在該處複製的備份，供還原作業存取。 

**是否可存取 SQL Database 備份檔案，以便下載或還原至 SQL Server？**

否，目前無法。

**SQL 保留原則內是否可以有多個排程 (每日、每週、每月、每年)？**

否，目前僅虛擬機器備份提供多個排程。

**如果我們在使用中的異地複寫次要資料庫上設定長期備份保留，會發生什麼情況？**

因為複本不留備份，所以目前沒有在次要資料庫上進行長期備份保留的選項。 不過，對使用者來說，對使用中的異地複寫次要資料庫設定長期備份保留相當重要，原因如下：
* 當容錯移轉發生而資料庫變成主要資料庫時，我們會進行完整備份，並將此完整備份上傳到保存庫。
* 客戶不用為設定次要資料庫的長期備份保留另行付費。

## <a name="next-steps"></a>後續步驟
因為資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一環。 若要深入了解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性概觀](sql-database-business-continuity.md)。
