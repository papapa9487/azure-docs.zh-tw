---
title: "在 Azure SQL 資料倉儲中暫停、繼續、使用 REST 調整 | Microsoft Docs"
description: "透過 REST、T-SQL 和 PowerShell 管理 SQL 資料倉儲中的運算能力。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 21de7337-9356-49bb-a6eb-06c1beeba2c4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 07/25/2017
ms.author: elbutter
ms.openlocfilehash: 24e43205c0c562fca9b1c2c0e5eed4da54e17ed7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>管理 Azure SQL 資料倉儲中的計算能力 (REST)
> [!div class="op_single_selector"]
> * [概觀](sql-data-warehouse-manage-compute-overview.md)
> * [入口網站](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>調整計算能力
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWU，請使用[建立或更新資料庫][Create or Update Database] REST API。 下例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停計算
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用[暫停資料庫][Pause Database] REST API。 下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>繼續計算
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要啟動資料庫，請使用[繼續資料庫][Resume Database] REST API。 下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>檢查資料庫狀態

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟
如需其他管理工作的詳細資訊，請參閱[管理概觀][Management overview]。

<!--Image references-->

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pause Database]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Resume Database]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Create or Update Database]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
