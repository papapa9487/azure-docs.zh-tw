---
title: "Azure SQL Database 單一資料庫 | Microsoft Docs"
description: "管理單一 Azure SQL Database 的服務層、效能層級及儲存體數量。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: f2dca5ac40dff077f9e5ce983b15fcb5b2624a14
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>管理 Azure SQL Database 中單一資料庫的資源

使用單一資料庫時，您可以決定資料庫在服務層處理其工作負載所需的資源量、效能等級，及其所需的儲存體數量。 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>使用 Azure 入口網站管理單一資料庫資源

若要使用 Azure 入口網站設定或變更新的或現有 Azure SQL 資料庫的服務層、效能等級或儲存體數量，請按一下 [定價層 (調整 DTU)] 為您的資料庫開啟 [設定效能] 視窗 - 如下列螢幕擷取畫面所示。 

- 選取您工作負載的服務層，設定或變更服務層。 
- 使用 [DTU] 滑桿來設定或變更服務層內的效能等級 (**DTU**)。
- 使用 [儲存體] 滑桿來設定或變更效能等級的儲存體數量。 

![設定服務層和效能等級](./media/sql-database-single-database-resources/change-service-tier.png)

> [!IMPORTANT]
> 選取 P11 或 P15 服務層時，檢閱 [P11 和 P15 資料庫目前的大小上限為 4 TB](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。
>

## <a name="manage-single-database-resources-using-powershell"></a>使用 PowerShell 管理單一資料庫資源

若要使用 PowerShell 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 PowerShell Cmdlet。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 

| Cmdlet | 說明 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|建立資料庫 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|取得一或多個資料庫|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|設定資料庫的屬性，或將現有資料庫移到彈性集區中。 例如，使用 **MaxSizeBytes** 屬性來設定資料庫的大小上限。|


> [!TIP]
> 如需可監視資料庫的效能計量、將其調整為較高的效能等級，並建立其中一個效能計量之警示規則的 PowerShell 範例指令碼，請參閱[使用 PowerShell 來監視和調整單一 SQL 資料庫](scripts/sql-database-monitor-and-scale-database-powershell.md)。

## <a name="manage-single-database-resources-using-the-azure-cli"></a>使用 Azure CLI 管理單一資料庫資源

若要使用 Azure CLI 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 [Azure CLI SQL Database](/cli/azure/sql/db) 命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。 如需建立和管理 SQL 彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。

| Cmdlet | 說明 |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|建立伺服器防火牆規則|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|列出伺服器上的防火牆規則|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|顯示防火牆規則的詳細資料|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|更新防火牆規則|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|刪除防火牆規則|


> [!TIP]
> 如需會在查詢資料庫的大小資訊後將單一 Azure SQL Database 調整為不同效能等級的 Azure CLI 範例指令碼，請參閱[使用 CLI 來監視和調整單一 SQL 資料庫](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

## <a name="manage-single-database-resources-using-transact-sql"></a>使用 Transact-SQL 管理單一資料庫資源

若要使用 Transact-SQL 來設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 Transact-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)，或任何可連線到 Azure SQL Database 伺服器並傳遞 Transact-SQL 命令的其他程式來發出這些命令。 

| 命令 | 說明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|建立新的資料庫。 您必須連線到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |修改 Azure SQL 資料庫。 |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|傳回 Azure SQL 資料庫或 Azure SQL 資料倉儲的版本 (服務層)、服務目標 (定價層) 和彈性集區名稱 (如果有的話)。 若已登入 Azure SQL Database 伺服器中的 master 資料庫，則傳回所有資料庫的相關資訊。 對於 Azure SQL 資料倉儲，您必須連線到 master 資料庫。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|列出 Azure SQL Database 伺服器上的資料庫數目、類型和持續時間。|

下列範例示範使用 ALTER DATABASE 命令變更資料庫大小上限的情形：

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>使用 REST API 管理單一資料庫資源

若要設定或變更 Azure SQL 資料庫服務層、效能等級和儲存體數量，請使用下列 REST API 要求。

| 命令 | 說明 |
| --- | --- |
|[資料庫 - 建立或更新](/rest/api/sql/databases/createorupdate)|建立新的資料庫或更新現有資料庫。|
|[資料庫 - 取得](/rest/api/sql/databases/get)|取得資料庫。|
|[資料庫 - 依彈性集區取得](/rest/api/sql/databases/getbyelasticpool)|取得彈性集區內的資料庫。|
|[資料庫 - 依建議的彈性集區取得](/rest/api/sql/databases/getbyrecommendedelasticpool)|取得建議之彈性集區內的資料庫。|
|[資料庫 - 依彈性集區列出](/rest/api/sql/databases/listbyelasticpool)|傳回將彈性集區中的資料庫列出的清單。|
|[資料庫 - 依建議的彈性集區列出](/rest/api/sql/databases/listbyrecommendedelasticpool)|傳回建議彈性集區內的資料庫清單。|
|[資料庫 - 依伺服器列出](/rest/api/sql/databases/listbyserver)|傳回伺服器中的資料庫清單。|
|[資料庫 - 更新](/rest/api/sql/databases/update)|更新現有的資料庫。|



## <a name="next-steps"></a>後續步驟

- 若要了解服務層、效能等級和儲存體數量，請參閱[服務層](sql-database-service-tiers.md)。
- 若要深入了解彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。
- 了解 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)
