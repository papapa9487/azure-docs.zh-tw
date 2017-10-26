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
ms.workload: NA
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: 95690e10730e64aa7181bc2e2e3b53ba9184f89e
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="enable-automatic-tuning"></a>啟用自動微調

Azure SQL Database 是自動管理的資料服務，會不斷地監視您的查詢，並識別您為改善工作負載效能可以執行的動作。 您可以檢閱建議並加以手動套用，或讓 Azure SQL Database 自動套用矯正措施 - 這稱為**模式**。 可在伺服器或資料庫層級啟用自動調整。

## <a name="enable-automatic-tuning-on-server"></a>在伺服器上啟用自動調整

若要在 Azure SQL Database 伺服器上啟用自動調整，請導覽至 Azure 入口網站中的伺服器，然後選取功能表中的**自動調整**。 依序選取您想要啟用的自動調整以及 [套用]：

![伺服器](./media/sql-database-automatic-tuning-enable/server.png)

伺服器上的自動調整選項會套用到伺服器上的所有資料庫。 根據預設，所有資料庫會都繼承其父伺服器中的設定，但這可加以覆寫並針對每個資料庫個別加以指定。

## <a name="configure-automatic-tuning-on-database"></a>在資料庫上設定自動調整

Azure 入口網站可讓您個別指定每個資料庫上的自動調整設定。

> [!NOTE]
> 一般建議是在伺服器層級管理自動調整設定，以便在每一個資料庫上自動套用相同的組態設定。 如果資料庫與相同伺服器上的其他資料庫不同，請在個別資料庫上設定自動調整。
>

若要在單一資料庫上啟用自動調整，請導覽至 Azure 入口網站中的資料庫，然後選取 [自動調整]。 您可以選取選項來將單一資料庫設定成從伺服器繼承設定，或者您也可以為資料庫個別指定組態。

![資料庫](./media/sql-database-automatic-tuning-enable/database.png)

一旦您選取適當的設定後，按一下 [套用]。

## <a name="next-steps"></a>後續步驟
* 閱讀[自動調整文章](sql-database-automatic-tuning.md)，進一步了解自動調整，以及它如何協助您改善效能。
* 如需 Azure SQL Database 效能建議的概觀，請參閱[效能建議](sql-database-advisor.md)。
* 請參閱[查詢效能深入解析](sql-database-query-performance.md)，以了解如何檢視排名最前面查詢的效能影響。