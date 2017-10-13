---
title: "查詢分區化 Azure SQL Database | Microsoft Docs"
description: "使用彈性資料庫用戶端程式跨分區執行查詢。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: a4379c15-f213-4026-ab6f-a450ee9d5758
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2016
ms.author: torsteng
ms.openlocfilehash: 67bcb3c7fe33341103f28bc70e8cc2acbb924cae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="multi-shard-querying"></a>多分區查詢
## <a name="overview"></a>概觀
使用[彈性資料庫工具](sql-database-elastic-scale-introduction.md)，您可以建立分區化資料庫解決方案。 **多分區查詢**用於資料收集/報告等工作，這些工作需要跨越數個分區執行查詢。 (這與在單一分區上執行所有工作的[資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)相反。) 

1. 使用 [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)[**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) 或 [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) 方法，取得 [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) 或 [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx)。 請參閱[**建構 ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) 和[**取得 RangeShardMap 或 ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)。
2. 建立 **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** 物件。
3. 建立 **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**。 
4. 將 **[CommandText 屬性](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)**設定為 T-SQL 命令。
5. 呼叫 **[ExecuteReader 方法](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**，以執行命令。
6. 使用 **[MultiShardDataReader 類別](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**，以檢視結果。 

## <a name="example"></a>範例
下列程式碼說明如何使用給定的 **ShardMap** (名為 *myShardMap*) 來使用多分區查詢。 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                 } 
           } 
    } 


主要差異是多分區連接的建構方式。 其中，**SqlConnection** 在單一資料庫上運作，**MultiShardConnection** 接受分區集合做為輸入。 從分區對應填入分區集合。 然後，使用 **UNION ALL** 語意在分區集合上執行查詢，以組成單一的整體結果。 (選擇性) 在命令上使用 **ExecutionOptions** 屬性，可將資料列的來源分區名稱加入至輸出。 

請注意 **myShardMap.GetShards()**的呼叫。 這個方法會從分區對應擷取所有分區，並提供簡單的方式跨所有相關的資料庫執行查詢。 在呼叫 **myShardMap.GetShards()**所傳回的集合分區上執行 LINQ 查詢，可以進一步調整多分區查詢的分區集合。 結合部分結果原則，多分區查詢目前的功能已設計成可適當處理數十個到數百個分區。

多分區查詢目前的限制在於無法驗證所查詢的分區和 Shardlet。 資料相依路由可在查詢時驗證給定的分區是屬於分區對應，但多分區查詢不會執行這項檢查。 這可能會導致在已從分區對應中移除的資料庫上執行多分區查詢。

## <a name="multi-shard-queries-and-split-merge-operations"></a>多分區查詢與分割合併作業
多分區查詢不會驗證所查詢資料庫上的 Shardlet 是否參與進行中的分割/合併作業。 (請參閱[使用彈性資料庫分割合併工具來縮放](sql-database-elastic-scale-overview-split-and-merge.md)。)這可能會導致不一致的情況，亦即在相同的多分區查詢中，多個資料庫顯示相同 Shardlet 的資料列。 請注意這些限制，在執行多分區查詢時，請考慮清空進行中的分割/合併作業和分區對應的變更。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>另請參閱
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** 類別和方法。

使用 [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)來管理分區。 包含稱為 [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) 的命名空間，提供使用單一查詢和結果來查詢多個分區的功能。 它提供一組分區的查詢抽象方法。 它也提供替代執行原則，特別是部分結果，以處理查詢許多分區時的失敗。  

