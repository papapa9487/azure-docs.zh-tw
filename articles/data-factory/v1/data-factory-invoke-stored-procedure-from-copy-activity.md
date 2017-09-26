---
title: "從 Azure Data Factory 複製活動叫用預存程序 | Microsoft Docs"
description: "了解如何從 Azure Data Factory 複製活動叫用 Azure SQL Database 或 SQL Server 中的預存程序。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2ef4385b3307c58d97012c3d0a732da5fbf82ea8
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>從 Azure Data Factory 中的複製活動叫用預存程序
將資料複製到 [SQL Server](data-factory-sqlserver-connector.md) 或 [Azure SQL Database](data-factory-azure-sql-connector.md) 時，您可以在複製活動中設定 **SqlSink** 以叫用預存程序。 您可以使用預存程序來執行將資料插入到目的地資料表之前所需的任何額外處理 (合併資料行、查詢值、插入到多個資料表等)。 此功能會利用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。 

下列範例示範如何從 Data Factory 管線 (複製活動) 叫用 SQL Server 資料庫中的預存程序：  

## <a name="output-dataset-json"></a>輸出資料集 JSON
在輸出資料集 JSON 中，將 **type** 設定為：**SqlServerTable**。 請將它設定為 **AzureSqlTable**，以與 Azure SQL Database 搭配使用。 **tableName** 屬性的值必須與預存程序第一個參數的名稱相符。  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>複製活動 JSON 中的 SqlSink 區段
依下列方式，在複製活動 JSON 中定義 **SqlSink** 區段。 若要在將資料插入到接收/目的地資料庫時叫用預存程序，請同時指定 **SqlWriterStoredProcedureName** 和 **SqlWriterTableType** 屬性的值。 如需這些屬性的描述，請參閱 [SQL Server 連接器一文中的 SqlSink 一節](data-factory-sqlserver-connector.md#sqlsink)。

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>預存程序定義 
在資料庫中，使用與 **SqlWriterStoredProcedureName** 相同的名稱來定義預存程序。 預存程序會處理來自來源資料存放區的輸入資料，然後將資料插入到目的地資料庫的資料表中。 預存程序第一個參數的名稱必須與資料集 JSON 中定義的 tableName (Marketing) 相符。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>資料表類型定義
在資料庫中，使用與 **SqlWriterTableType** 相同的名稱來定義資料表類型。 資料表類型的結構描述必須與輸入資料集的結構描述相符。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>後續步驟
如需完整的 JSON 範例，請檢閱下列連接器文章： 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)

