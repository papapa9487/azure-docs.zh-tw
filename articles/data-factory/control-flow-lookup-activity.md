---
title: "Azure Data Factory 中的查閱活動 | Microsoft Docs"
description: "了解如何使用查閱活動從外部來源查閱值。 此輸出可進一步供後續的活動參考。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: d498705ef7f714b4f15b8d2722053bf3081b5045
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory 中的查閱活動
查閱活動可用來讀取或查閱任何外部來源的記錄/表格名稱/值。 此輸出可進一步供後續的活動參考。 

針對查閱，目前支援下列資料來源：
- Azure Blob 中的 JSON 檔案
- 內部部署的 JSON 檔案
- Azure SQL Database (從查詢轉換的 JSON 資料)
- Azure 表格儲存體 (從查詢轉換的 JSON 資料)

當您想從組態檔或資料來源動態擷取檔案/記錄/表格的清單時，查閱活動就很有用。 活動的輸出可進一步供其他活動使用，以對那些項目執行特定處理。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版本 (GA))，請參閱 [Data Factory V1 文件](v1/data-factory-introduction.md)。


## <a name="example"></a>範例
在此範例中，複製活動會將資料從 Azure SQL Database 中的 SQL 資料表複製到 Azure Blob 儲存體。 SQL 資料表的名稱會儲存在 Blob 儲存體的 JSON 檔案中。 查閱活動在執行階段會查閱表格名稱。 這個方法可動態修改 JSON 來，而不必重新部署管線/資料集。 

### <a name="pipeline"></a>管線
這個管線包含兩個活動：**查閱**和**複製**。 

- 查閱活動會設定為使用 LookupDataset，它會參考 Azure Blob 儲存體中的位置。 查閱活動會從這個位置的 JSON 檔案讀取 SQL 資料表的名稱。 
- 複製活動會使用查閱活動 (SQL 資料表名稱) 的輸出。 來源資料集 (SourceDataset) 中的 tableName 會設定為使用查閱活動的輸出。 複製活動會將資料從 SQL 資料表複製到 Azure Blob 儲存體 SinkDataset 所指定的位置。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>查閱資料集
查詢資料集是 AzureStorageLinkedService 所指定 Azure 儲存體中查閱資料夾內的 sourcetable.json 檔案。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>複製活動的來源資料集
來源資料集會使用查閱活動的輸出，亦即 SQL 資料表的名稱。 複製活動會將資料從這個 SQL 資料表複製到接收資料集指定之 Azure Blob 儲存體中的位置。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>複製活動的接收資料集
複製活動會將資料從 SQL 資料表複製到 AzureStorageLinkedService 所指定 Azure 儲存體中的 CSV 資料夾內的 filebylookup.csv 檔案。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
這個儲存體帳戶包含具有 SQL 資料表名稱的 JSON 檔案。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 的連結服務
此 Azure SQL 資料庫包含要複製到 Blob 儲存體的資料。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>物件集合

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>物件的陣列

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>類型屬性
名稱 | 說明 | 類型 | 必要
---- | ----------- | ---- | --------
資料集 | 資料集屬性提供查閱的資料集參考。 目前支援的資料集類型為：<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | 索引鍵/值組 | 是
來源 | 資料集特定的來源屬性，與複製活動來源相同 | 索引鍵/值組 | 否
firstRowOnly | 傳回第一個資料列或所有資料列。 | 布林值 | 否

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [Web 活動](control-flow-web-activity.md)
