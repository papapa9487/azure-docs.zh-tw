---
title: "Azure SQL Database 自動、異地備援備份 | Microsoft Docs"
description: "SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: e779aab97a1b96d4a0e327865e957ecd0d97a278
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="learn-about-automatic-sql-database-backups"></a>了解自動 SQL Database 備份

SQL Database 會自動建立資料庫備份，並使用 Azure 讀取權限異地備援儲存體 (RA-GRS) 來提供異地備援。 這些備份是自動建立的，且不需額外付費。 您不需要執行任何動作來建立備份。 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您想要在自己的儲存體容器中保留備份，您可以設定長期的備份保留原則。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份？

SQL Database 使用 SQL Server 技術來建立[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差異](https://msdn.microsoft.com/library/ms175526.aspx)，及[交易記錄](https://msdn.microsoft.com/library/ms191429.aspx)備份。 交易記錄備份根據效能層級和資料庫活動量的頻率通常每隔 5-10 分鐘會進行一次。 具有完整和差異備份的交易記錄備份可讓您將資料庫還原到特定的時間點，至裝載資料庫相同的伺服器。 在您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。


您可以使用這些備份︰

* 將資料庫還原至保留期限內的某一個時間點。 這項作業將會在與原始資料庫相同的伺服器中建立新的資料庫。
* 將已刪除的資料庫還原至其被刪除的時間，或保留期限內的任何時間。 已刪除的資料庫只能在當中已建立原始資料庫的相同伺服器中還原。
* 將資料庫還原到另一個地理區域。 這可讓您在無法存取您的伺服器和資料庫時，從地理災害中復原。 在世界各地任何現有的伺服器中建立新的資料庫。 
* 從 Azure 復原服務保存庫中儲存的特定備份中還原資料庫。 這可讓您還原舊版的資料庫來符合規範要求，或執行舊版的應用程式。 請參閱[長期保留](sql-database-long-term-retention.md)。
* 若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從某個位置複製到另一個位置。 SQL 的「資料庫複寫」  指的是保持多個次要資料庫與主要資料庫同步。 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>在免費情況下包含多少備份儲存體？
SQL Database 提供高達 200% 的最大可佈建資料庫儲存體作為備份儲存體，且不須支付額外費用。 例如，如果您擁有可佈建 DB 大小為 250 GB 的標準 DB 執行個體，您就能免費獲得 500 GB 的備份儲存體。 如果您的資料庫超過所提供的備份儲存體，您可以連絡 Azure 支援人員來選擇縮短保留期限。 另一個選項是付費購買額外的備份儲存體，按讀取權限異地備援儲存體 (RA-GRS) 標準費率計費。 

## <a name="how-often-do-backups-happen"></a>備份頻率是？
每週進行一次完整備份，通常每幾個小時進行一次差異資料庫備份，以及通常每隔 5 - 10 分鐘進行一次交易記錄備份。 建立資料庫之後，會立即排程第一次完整備份。 通常會在 30 分鐘內完成，但如果資料庫很大，則時間可能更久。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 

備份儲存體異地複寫是根據 Azure 儲存體複寫排程進行。

## <a name="how-long-do-you-keep-my-backups"></a>您會保留我的備份多久？
每個 SQL Database 備份都有一個保留期限，該期限是以資料庫的[服務層](sql-database-service-tiers.md)為依據。 資料庫保留期限如下：


* 基本服務層為 7 天。
* 標準服務層為 35 天。
* 進階服務層為 35 天。

如果您將資料庫從「標準」或「進階」服務層降級到「基本」服務層，備份就會保存 7 天。 所有超過 7 天的現有備份都無法再使用。 

如果您將資料庫從「基本」服務層升級到「標準」或「進階」服務層，SQL Database 就會將現有備份自其建立日期算起保存 35 天。 它也會將新的備份自其建立日期算起保存 35 天。

如果您刪除資料庫，SQL Database 會以和保存線上資料庫備份的相同方式保存備份。 例如，假設您刪除保存期限為 7 天的「基本」資料庫。 已保存 4 天的備份會再保存 3 天。

> [!IMPORTANT]
> 如果您刪除裝載 SQL Database 的 Azure SQL Server，所有屬於該伺服器的資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器。
> 

## <a name="how-to-extend-the-backup-retention-period"></a>如何延長備份保留期限？
如果您的應用程式需要較長時間可進行備份，您可以延長內建的保留期限，方法為設定個別資料庫的長期備份保留原則 (LTR 原則)。 這可讓您將保留期限延長 35 天至多達 10 年。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

一旦您使用 Azure 入口網站或 API 將 LTR 原則新增至資料庫後，每週的完整資料庫備份將會自動複製到您自己的 Azure 備份服務保存庫。 如果您的資料庫是使用 TDE 加密，則備份會自動加密靜止。  服務保存庫將自動刪除您過期的備份，根據其時間戳記和 LTR 原則。  因此您不需要管理備份排程或擔心清理舊檔案的工作。 還原 API 支援保存庫中儲存的備份，只要保存庫在與您的 SQL Database 相同的訂用帳戶中。 您可使用 Azure 入口網站或 PowerShell 存取這些備份。

> [!TIP]
> 如需作法指南，請參閱[設定 Azure SQL Database 長期備份保留並從中還原](sql-database-long-term-backup-retention-configure.md)
>

## <a name="are-backups-encrypted"></a>備份經過加密？

Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL Database 預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 若要使用 Azure 入口網站還原至某個時間點，請參閱[使用 Azure 入口網站將資料庫還原至時間點](sql-database-recovery-using-backups.md)。
- 若要使用 PowerShell 還原至某個時間點，請參閱[使用 PowerShell 將資料庫還原至時間點](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 入口網站在「Azure 復原服務」保存庫中設定、管理自動備份的長期保留及從該保留還原，請參閱[使用 Azure 入口網站來管理長期備份保留 (英文)](sql-database-long-term-backup-retention-configure.md)。
- 若要使用 PowerShell 在「Azure 復原服務」保存庫中設定、管理自動備份的長期保留及從該保留還原，請參閱[使用 PowerShell 來管理長期備份保留 (英文)](sql-database-long-term-backup-retention-configure.md)。
