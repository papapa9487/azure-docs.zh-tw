---
title: "啟用 Azure SQL Database 的自動調整 | Microsoft Docs"
description: "您可以輕鬆在 Azure SQL Database 上啟用自動調整。"
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: bf8e0203112a42132a80e234964747c550fea284
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="enable-automatic-tuning"></a>啟用自動微調

Azure SQL Database 是自動管理的資料服務，會不斷地監視您的查詢，並識別您為改善工作負載效能可以執行的動作。 您可以檢閱建議並加以手動套用，或讓 Azure SQL Database 自動套用矯正措施 - 這稱為**模式**。 可在伺服器或資料庫層級啟用自動調整。

## <a name="enable-automatic-tuning-on-server"></a>在伺服器上啟用自動調整
在伺服器層級上，您可以選擇繼承「Azure 預設值」的自動調整設定，或不繼承設定。 Azure 預設值已啟用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，且已停用 DROP_INDEX。

### <a name="portal"></a>入口網站
若要在 Azure SQL Database 伺服器上啟用自動調整，請導覽至 Azure 入口網站中的伺服器，然後選取功能表中的**自動調整**。 依序選取您想要啟用的自動調整以及 [套用]：

![伺服器](./media/sql-database-automatic-tuning-enable/server.png)

伺服器上的自動調整選項會套用到伺服器上的所有資料庫。 根據預設，所有資料庫會都繼承其父伺服器中的設定，但這可加以覆寫並針對每個資料庫個別加以指定。

### <a name="rest-api"></a>REST API
[按一下這裡以深入了解如何透過 REST API 在伺服器層級上啟用自動調整](https://docs.microsoft.com/rest/api/sql/serverautomatictuning) \(英文\)

## <a name="enable-automatic-tuning-on-database"></a>在資料庫上啟用自動調整

Azure SQL Database 可讓您個別指定每個資料庫上的自動調整設定。 在資料庫層級上，您可以選擇繼承父伺服器「Azure 預設值」的自動調整設定，或不繼承設定。 Azure 預設值已啟用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，且已停用 DROP_INDEX。

> [!NOTE]
> 一般建議是在伺服器層級管理自動調整設定，以便在每一個資料庫上自動套用相同的組態設定。 如果資料庫與相同伺服器上的其他資料庫不同，請在個別資料庫上設定自動調整。
>

### <a name="portal"></a>入口網站

若要在單一資料庫上啟用自動調整，請導覽至 Azure 入口網站中的資料庫，然後選取 [自動調整]。 您可以選取選項來將單一資料庫設定成從伺服器繼承設定，或者您也可以為資料庫個別指定組態。

![資料庫](./media/sql-database-automatic-tuning-enable/database.png)

一旦您選取適當的設定後，按一下 [套用]。

### <a name="rest-api"></a>Rest API
[按一下這裡以深入了解如何透過 REST API 在單一資料庫上啟用自動調整](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning) \(英文\)

### <a name="t-sql"></a>T-SQL

若要透過 T-SQL 在單一資料庫上啟用自動調整，請連線到該資料庫並執行下列查詢：

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
將自動調整設定成 [AUTO]，將會套用 Azure 預設值。 設定成 [INHERIT]，將會從父伺服器繼承自動調整設定。 若選擇 [CUSTOM]，您將需要手動設定自動調整。

若要透過 T-SQL 設定個別的自動調整選項，請連線到資料庫並執行如下的查詢：

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
將個別的調整選項設定成 [ON]，將會覆寫該資料庫所繼承的任何設定，並啟用該調整選項。 將它設定成 [OFF]，也會覆寫資料庫所繼承的任何設定，並停用該調整選項。 指定為 [DEFAULT] 的自動調整選項，將會繼承資料庫層級自動調整設定的組態。  

## <a name="disabled-by-the-system"></a>被系統停用
自動調整會監視它在資料庫上採取的所有動作，而且在某些情況下，它可以判斷自動調整無法適當地在資料庫上運作。 在此情況下，調整選項將會被系統停用。 在大部分情況下，發生此問題的原因是因為特定資料庫上未啟用查詢資料存放區，或是查詢存放區處於唯讀的狀態。

## <a name="next-steps"></a>後續步驟
* 閱讀[自動調整文章](sql-database-automatic-tuning.md)，進一步了解自動調整，以及它如何協助您改善效能。
* 如需 Azure SQL Database 效能建議的概觀，請參閱[效能建議](sql-database-advisor.md)。
* 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解如何檢視排名最前面查詢的效能影響。
