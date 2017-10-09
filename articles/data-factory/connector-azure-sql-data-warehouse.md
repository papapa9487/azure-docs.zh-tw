---
title: "使用 Data Factory 將資料複製到 Azure SQL 資料倉儲或從該處複製資料 | Microsoft Docs"
description: "了解如何使用 Data Factory 將資料從支援的來源存放區複製到「Azure SQL 資料倉儲」，或從「SQL 資料倉儲」複製到支援的接收存放區。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fb67c70d1e85307c38a185e2b47729880880d55b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL 資料倉儲或從該處複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [第 2 版 - 預覽](connector-azure-sql-data-warehouse.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料複製到「Azure SQL 資料倉儲」及從該處複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Azure SQL 資料倉儲連接器](v1/data-factory-azure-sql-data-warehouse-connector.md)。

## <a name="supported-scenarios"></a>支援的案例

您可以將資料從「Azure SQL 資料倉儲」複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到「Azure SQL 倉儲」。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個「Azure SQL 資料倉儲」連接器支援：

- 使用 SQL 驗證來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 作為接收器時，使用 **PolyBase** 或大量插入來載入資料。 **建議**使用前者，以獲得較佳的複製效能。

## <a name="getting-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義「Azure SQL 資料倉儲」連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對「Azure SQL 資料倉儲」已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**AzureSqlDW** | 是 |
| connectionString |針對 connectionString 屬性指定連線到 Azure SQL 資料倉儲執行個體所需的資訊。 僅支援基本驗證。 請將此欄位標示為 SecureString。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |


> [!IMPORTANT]
> 請設定 [Azure SQL 資料倉儲防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)和資料庫伺服器，以[允許 Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果您要將資料從 Azure 外部 (包括從具有「自我裝載 Integration Runtime」的內部部署資料來源) 複製到「Azure SQL 資料倉儲」，請為傳送資料給「Azure SQL 資料倉儲」的電腦設定適當的 IP 位址範圍。

**範例：**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供「Azure SQL 資料倉儲」資料集所支援的屬性清單。

若要從「Azure SQL 資料倉儲」複製資料或將資料複製到該處，請將資料集的類型屬性設定為 **AzureSqlDWTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**AzureSqlDWTable** | 是 |
| tableName |「Azure SQL 資料倉儲」執行個體中已連結的服務所參考的資料表或檢視名稱。 | 是 |

**範例：**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供「Azure SQL 資料倉儲」來源和接收器所支援的屬性清單。

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL 資料倉儲作為來源

若要從「Azure SQL 資料倉儲」複製資料，請將複製活動中的來源類型設定為 **SqlDWSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**SqlDWSource** | 是 |
| SqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 範例：`select * from MyTable`. |否 |
| sqlReaderStoredProcedureName |從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |

**注意事項：**

- 如果已為 SqlSource 指定 **sqlReaderQuery** ，「複製活動」就會針對「Azure SQL 資料倉儲」來源執行這項查詢以取得資料。 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
- 如果您未指定 "sqlReaderQuery" 或 "sqlReaderStoredProcedureName"，就會使用資料集 JSON 的 "structure" 區段中定義的資料行，來建構要針對「Azure SQL 資料倉儲」執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。
- 當您使用 **sqlReaderStoredProcedureName** 時，仍然必須在資料集 JSON 中指定一個虛設的 **tableName**。

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**範例：使用預存程序**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**預存程序定義：**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL 資料倉儲作為接收器

若要將資料複製到「Azure SQL 資料倉儲」，請將複製活動中的接收器類型設定為 **SqlDWSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**SqlDWSink** | 是 |
| allowPolyBase |指出是否使用 PolyBase (適用的話) 而不是使用 BULKINSERT 機制。 <br/><br/> 建議使用 PolyBase 將資料載入 SQL 資料倉儲。 請參閱 [使用 PolyBase 將資料載入 Azure SQL 資料倉儲](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 一節中的條件約束和詳細資料。<br/><br/>允許的值為：**True** (預設值) 和 **False**。  |否 |
| polyBaseSettings |可以在 **allowPolybase** 屬性設定為 **true** 時指定的一組屬性。 |否 |
| rejectValue |指定在查詢失敗前可以拒絕的資料列數目或百分比。<br/><br/>在 **CREATE EXTERNAL TABLE (Transact-SQL)** 主題的 [引數](https://msdn.microsoft.com/library/dn935021.aspx) 一節中，深入了解 PolyBase 的拒絕選項。 <br/><br/>允許的值為：0 (預設值)、1、2… |否 |
| rejectType |指定要將 rejectValue 選項指定為常值或百分比。<br/><br/>允許的值為：**值** (預設值) 和**百分比**。 |否 |
| rejectSampleValue |決定在 PolyBase 重新計算已拒絕的資料列百分比之前，所要擷取的資料列數目。<br/><br/>允許的值為：1、2… |是，如果 **rejectType** 是 **percentage** |
| useTypeDefault |指定當 PolyBase 從文字檔擷取資料時，如何處理分隔符號文字檔中的遺漏值。<br/><br/>從 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 的＜引數＞一節深入了解這個屬性。<br/><br/>允許的值為：**True**、**False** (預設值)。 |否 |
| writeBatchSize |當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中 只有在未使用 PolyBase 時才適用。<br/><br/>允許的值為：整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。只有在未使用 PolyBase 時才適用。<br/><br/>允許的值為：時間範圍。 範例：“00:30:00” (30 分鐘)。 |否 |
| preCopyScript |指定一個供「複製活動」在每次執行時將資料寫入到「Azure SQL 資料倉儲」前執行的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 |否 |(#repeatability-during-copy)。 |查詢陳述式。 |否 |

**範例：**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

若要深入了解如何使用 PolyBase 來有效率地載入到「SQL 資料倉儲」，請參閱下一節。

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 將資料載入 Azure SQL 資料倉儲

使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 是以高輸送量將大量資料載入 Azure SQL 資料倉儲的有效方法。 使用 PolyBase 而不是預設的 BULKINSERT 機制，即可看到輸送量大幅提升。 請參閱[複製效能參考編號](copy-activity-performance.md#performance-reference)了解詳細的比較。 如需使用案例的逐步解說，請參閱[使用 Azure Data Factory 在 15 分鐘內將 1 TB 載入至 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md)。

* 如果您的來源資料位在 Azure Blob 或 Azure Data Lake Store，且其格式與 PolyBase 相容，您就可以使用 PolyBase 直接複製到 Azure SQL 資料倉儲。 請參閱**[使用 PolyBase 直接複製](#direct-copy-using-polybase)**了解詳細資料。
* 如果您的來源資料存放區與格式不受 PolyBase 支援，您可以改為使用[使用 PolyBase 分段複製](#staged-copy-using-polybase)功能。 它也能透過將資料自動轉換為 PolyBase 相容的格式，並將資料儲存在 Azure Blob 儲存體中，來提供更佳的輸送量。 然後，它會將資料載入 SQL 資料倉儲。

### <a name="direct-copy-using-polybase"></a>使用 PolyBase 直接複製

SQL 資料倉儲 PolyBase 直接支援 Azure Blob 和 Azure Data Lake Store (使用服務主體) 做為來源與特定檔案格式需求。 如果您的來源資料符合本節所述準則，您就可以使用 PolyBase，從來源資料存放區直接複製到 Azure SQL 資料倉儲。 否則，您可以使用 [使用 PolyBase 分段複製](#staged-copy-using-polybase)。

> [!TIP]
> 若要有效率地將資料從 Data Lake Store 複製到 SQL 資料倉儲，深入了解 [使用 Data Lake Store 與 SQL 資料倉儲時，Azure Data Factory 能讓您更輕鬆容易發現資料中的重要資訊](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)。

如果不符合需求，Azure Data Factory 會檢查設定，並自動切換回適用於資料移動的 BULKINSERT 機制。

1. 「來源已連結服務」的類型為：**AzureStorage** 或 **AzureDataLakeStore**。
2. 「輸入資料集」**的類型為**：**AzureBlob** 或 **AzureDataLakeStoreFile**，而 `type` 屬性底下的格式類型為 **OrcFormat** 或具備下列組態的 **TextFormat**：

   1. `rowDelimiter` 必須是 **\n**。
   2. `nullValue` 設定為「空字串」 ("") 或將 `treatEmptyAsNull` 設定為 **true**。
   3. `encodingName` 設定為 **utf-8**，也就是「預設」值。
   4. 未指定 `escapeChar`、`quoteChar`、`firstRowAsHeader` 和 `skipLineCount`。
   5. `compression` 可以是「無壓縮」、**GZip** 或 **Deflate**。

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. 管線中複製活動的 **BlobSource** 或 **AzureDataLakeStore** 之下沒有 `skipHeaderLineCount` 設定。
4. 管線中複製活動的 **SqlDWSink** 之下沒有 `sliceIdentifierColumnName` 設定。 (PolyBase 保證所有資料都已更新，或在單一執行未更新任何項目。 若要達到「重複性」，您可以使用 `sqlWriterCleanupScript`)。
5. 目前沒有任何 `columnMapping` 使用於相關聯的複製活動。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>使用 PolyBase 分段複製

當來源資料不符合上一節介紹的準則時，您可以啟用透過過渡暫存 Azure Blob 儲存體 (不能是進階儲存體) 複製資料。 在此情況下，Azure Data Factory 會自動執行資料轉換，以符合 PolyBase 的資料格式需求，然後使用 PolyBase 將資料載入到「SQL 資料倉儲」，接著再從 Blob 儲存體清除您的暫存資料。 如需透過暫存 Azure Blob 複製資料通常如何運作的詳細資訊，請參閱 [分段複製](copy-activity-performance.md#staged-copy) 。

若要使用此功能，請建立 [Azure 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties)，這是指具有過渡 Blob 儲存體的 Azure 儲存體帳戶，然後針對複製活動指定 `enableStaging` 和 `stagingSettings` 屬性，如下列程式碼所示：

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>使用 PolyBase 時的最佳作法

除了 [Azure SQL 資料倉儲的最佳做法](../sql-data-warehouse/sql-data-warehouse-best-practices.md)之外，下列章節還提供其他最佳做法。

### <a name="required-database-permission"></a>必要的資料庫權限

若要使用 PolyBase，要用來將資料載入 SQL 資料倉儲的使用者必須具備目標資料庫的 ["CONTROL" 權限](https://msdn.microsoft.com/library/ms191291.aspx)。 達到此目標的其中一個方法是將該使用者新增為 "db_owner" 角色的成員。 依照[本節](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)了解如何進行這項動作。

### <a name="row-size-and-data-type-limitation"></a>資料列大小和資料類型限制

PolyBase 載入被限制為只能載入小於 **1 MB**，且不能載入至 VARCHR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX) 的資料列。 請參閱[這裡](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果您的來源資料包含大於 1 MB 的資料列，建議您將來源資料表垂直分割成數個小型資料表，而每個資料表的最大資料列大小均不超過限制。 然後可以使用 PolyBase 載入較小的資料表而在 Azure SQL 資料倉儲中合併在一起。

### <a name="sql-data-warehouse-resource-class"></a>SQL 資料倉儲資源類別

若要達到最佳的可能輸送量，請考慮透過 PolyBase 將更大的資源類別指派給要用來將資料載入 SQL 資料倉儲的使用者。 遵循[變更使用者資源類別範例](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example)，了解如何執行這項作業。

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的 tableName

下表提供的範例是關於如何針對各種結構描述和資料表名稱組合，在資料集 JSON 中指定 **tableName** 屬性。

| DB 結構描述 | 資料表名稱 | tableName JSON 屬性 |
| --- | --- | --- |
| dbo |MyTable |MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable 或 [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] 或 [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

如果您看到下列錯誤，可能是您為 tableName 屬性指定的值有問題。 請參閱資料表，以正確的方式指定 tableName JSON 屬性的值。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>包含預設值的資料行

Data Factory 中的 PolyBase 功能目前只接受與目標資料表中相同的資料行數目。 假設您有內含四個資料行的資料表，且其中一個資料行已使用預設值進行定義。 輸入資料應該仍會包含四個資料行。 提供 3 個資料行的輸入資料集會產生類似下列訊息的錯誤︰

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是一種特殊形式的預設值。 如果資料行可為 null，該資料行的輸入資料 (在 Blob 中) 可以是空的 (不能在輸入資料集中遺漏)。 PolyBase 會在 Azure SQL 資料倉儲中為其插入 NULL。

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲的資料類型對應

從「Azure SQL 資料倉儲」複製資料或將資料複製到該處時，會使用下列從「Azure SQL 資料倉儲」資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Azure SQL 資料倉儲資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String、Char[] |
| 日期 |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| 十進位 |十進位 |
| FILESTREAM 屬性 (varbinary(max)) |Byte[] |
| Float |兩倍 |
| image |Byte[] |
| int |Int32 |
| money |十進位 |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |十進位 |
| nvarchar |String、Char[] |
| real |單一 |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |十進位 |
| sql_variant |物件 * |
| 文字 |String、Char[] |
| 分析 |時間範圍 |
| timestamp |Byte[] |
| tinyint |位元組 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
