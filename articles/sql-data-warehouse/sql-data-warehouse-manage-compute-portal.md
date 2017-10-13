---
title: "管理 Azure SQL 資料倉儲中的計算能力 (Azure 入口網站) | Microsoft Docs"
description: "管理計算能力的 Azure 入口網站工作。 透過調整 DWU 以調整計算資源。 或者，暫停和繼續計算資源以節省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>管理 Azure SQL 資料倉儲中的計算能力 (Azure 入口網站)
> [!div class="op_single_selector"]
> * [概觀](sql-data-warehouse-manage-compute-overview.md)
> * [入口網站](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>調整計算能力
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更計算資源︰

1. 開啟 [Azure 入口網站][Azure portal]，開啟您的資料庫，然後按一下 [調整]。

    ![按一下 [調整]][1]
2. 在 [調整] 刀鋒視窗中，將滑桿向左或右移動來變更 DWU 設定。

    ![移動滑桿][2]
3. 按一下 [儲存] 。 確認訊息隨即出現。 按一下 [是] 以確認或 [否] 以取消。

    ![按一下 [Save] (儲存)。][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停計算
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫︰

1. 開啟 [Azure 入口網站][Azure portal]，並開啟您的資料庫。 請注意，狀態為 **線上**。

    ![線上狀態][6]
2. 若要暫停計算和記憶體資源，請按一下 [暫停] ，接著就會出現確認訊息。 按一下 [是] 以確認或 [否] 以取消。

    ![確認暫停][7]
3. 當「SQL 資料倉儲」啟動資料庫時，狀態會是 [暫停中] 。
4. 當狀態為 [已暫停] 時，即表示暫停作業完成，不會再對您收取 DWU 費用。

    ![暫停狀態][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>繼續計算
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要繼續資料庫︰

1. 開啟 [Azure 入口網站][Azure portal]，並開啟您的資料庫。 請注意，狀態為 **已暫停**。

    ![暫停資料庫][4]
2. 若要繼續資料庫，請按一下 [啟動] ，接著就會出現確認訊息。 按一下 [是] 以確認或 [否] 以取消。

    ![確認繼續][5]
3. 當「SQL 資料倉儲」啟動資料庫時，狀態會是 [恢復中]。
4. 當狀態為 [線上] 時，即表示資料庫已就緒。

    ![線上狀態][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱[管理概觀][Management overview]。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
