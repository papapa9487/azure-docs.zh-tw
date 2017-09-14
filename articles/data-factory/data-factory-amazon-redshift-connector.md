---
title: "使用 Data Factory 從 Amazon Redshift 移動資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 從 Amazon Redshift 移動資料。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 2e7b6a7c33e1f86133383f116df2fc2256133012
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>使用 Azure Data Factory 從 Amazon Redshift 移動資料
本文說明如何使用 Azure Data Factory 中的「複製活動」，從 Amazon Redshift 移動資料。 本文是根據[資料移動活動](data-factory-data-movement-activities.md)一文，該文提供使用複製活動來移動資料的一般概觀。 

您可以將資料從 Amazon Redshift 複製到任何支援的接收資料存放區。 如需複製活動所支援作為接收器的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 Data Factory 目前支援將資料從 Amazon Redshift 移到其他資料存放區，而不支援將資料從其他資料存放區移到 Amazon Redshift。

> [!TIP]
> 在 Redshift 中複製大量資料時，若想獲得最佳效能，請考慮透過 Amazon S3 使用內建的 Redshift UNLOAD。 如需詳細資料，請參閱「[使用 UNLOAD 複製 Amazon Redshift 中的資料](#use-unload-to-copy-data-from-amazon-redshift)」章節。

## <a name="prerequisites"></a>必要條件
* 如果您要將資料移到內部部署的資料存放區，請在內部部署的電腦上安裝[資料管理閘道](data-factory-data-management-gateway.md)。 接著，將 Amazon Redshift 叢集的存取權授與「資料管理閘道」(使用該電腦的 IP 位址)。 如需相關指示，請參閱 [授權存取叢集](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 。
* 如果您要移動資料到 Azure 資料存放區，請參閱 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) 以取得 Azure 資料中心所使用的計算 IP 位址和 SQL 範圍。

## <a name="getting-started"></a>開始使用
您可以藉由使用不同的工具/API，建立內含複製活動的管線，以從 Amazon Redshift 來源移動資料。

建立管線的最簡單方式就是使用「複製精靈」。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

您也可以使用下列工具來建立管線︰**Azure 入口網站**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 範本**、**.NET API** 及 **REST API**。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線： 

1. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立**資料集**，代表複製作業的輸入和輸出資料。 
3. 建立**管線**，其中含有以一個資料集作為輸入、一個資料集作為輸出的複製活動。 

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。  如需相關範例，其中含有用來從 Amazon Redshift 資料存放區複製資料之 Data Factory 實體的 JSON 定義，請參閱本文的 [JSON 範例：將資料從 Amazon Redshift 複製到 Azure Blob](#json-example-copy-data-from-amazon-redshift-to-azure-blob) 一節。 

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 Amazon Redshift 特定的 Data Factory 實體： 

## <a name="linked-service-properties"></a>連結服務屬性

下表提供 Amazon Redshift 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| 類型 |類型屬性必須設為： **AmazonRedshift**。 |是 |
| 伺服器 |Amazon Redshift 伺服器的 IP 位址或主機名稱。 |是 |
| 連接埠 |Amazon Redshift 伺服器用來接聽用戶端連線的 TCP 連接埠號碼。 |否，預設值︰5439 |
| 資料庫 |Amazon Redshift 資料庫的名稱。 |是 |
| username |可存取資料庫之使用者的名稱。 |是 |
| password |使用者帳戶的密碼。 |是 |

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 所有資料集類型 (Azure SQL、Azure Blob、Azure 資料表等) 的結構、可用性及原則等區段都相似。

不同類型資料集的 **typeProperties** 區段不同。 它提供資料存放區中資料的位置之相關資訊。 **RelationalTable** 資料集類型的 typeProperties 區段 (包含 Amazon Redshift 資料集) 具有下列屬性

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| tableName |Amazon Redshift 資料庫中連結服務所參照的資料表名稱。 |否 (如果已指定 **RelationalSource** 的 **query**) |

## <a name="copy-activity-properties"></a>複製活動屬性

如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 **typeProperties** 區段中，可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

複製活動的來源類型為 **AmazonRedshiftSource** 時，typeProperties 區段中會有下列屬性：

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| query | 使用自訂查詢來讀取資料。 |否 (如果已指定 **dataset** 的 **tableName**) |
| redshiftUnloadSettings | 使用 Amazon Redshift UNLOAD 時的屬性群組。 | 否 |
| s3LinkedServiceName | 指定 AwsAccessKey 類型的 ADF 連結服務名稱，指向 Amazon S3 作為暫時存放區。 | 「redshiftUnloadSettings」下的必要項目 |
| bucketName | 表示儲存暫時資料的 S3 貯體。 如果未提供，複製活動會自動產生貯體。 | 「redshiftUnloadSettings」下的必要項目 |

您也可以將 **RelationalSource** (包括 Amazon Redshift) 類型與下列 typeProperties 區段中的屬性搭配使用。 請注意，此來源類型不支援 Redshift UNLOAD。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| query |使用自訂查詢來讀取資料。 | 否 (如果已指定 **dataset** 的 **tableName**) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>使用 UNLOAD 複製 Amazon Redshift 中的資料

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) (英文) 是 Amazon Redshift 提供的機制，可以為 Amazon Simple Storage Service (Amazon S3) 中的一或多個檔案卸載查詢結果。 Amazon 建議使用此方式從 Redshift 複製大型資料集。

**範例：透過 UNLOAD、分段複製和 PolyBase 將 Amazon Redshift 的資料複製到 Azure SQL 資料倉儲**

此使用案例範例中，複製活動會先依照「redshiftUnloadSettings」的設定卸載 Amazon Redshift Amazon S3 的資料，然後依照「stagingSettings」指定的方式將該資料從 Amazon S3 複製到 Azure Blob，最後使用 PolyBase 將資料載入 SQL 資料倉儲。 複製活動會正確地處理所有暫時格式。

![從 Redshift 到 SQL DW 的複製工作流程](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>JSON 範例：將資料從 Amazon Redshift 複製到 Azure Blob
此範例示範如何將資料從 Amazon Redshift 資料庫複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。  

此範例具有下列 Data Factory 實體：

* [AmazonRedshift](#linked-service-properties)類型的連結服務。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
* [RelationalTable](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
* 具有使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 Amazon Redshift 中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Amazon Redshift 已連結的服務：**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure 儲存體連結服務：**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift 輸入資料集：**

設定 `"external": true` 可讓 Data Factory 服務知道資料集是在 Data Factory 外部，而不是由 Data Factory 中的活動所產生。 在不是由管線中的活動所產生的輸入資料集上，請將此屬性設定為 true。

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**具有 Azure Redshift 來源 (RelationalSource) 和 Blob 接收器的管線中複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource**，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift 的類型對應
如 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會藉由下列含有兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

將資料移到 Amazon Redshift 時，下列對應將用於從 Amazon Redshift 類型到 .NET 類型。

| Amazon Redshift 類型 | 以 .Net 為基礎的類型 |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |單一 |
| DOUBLE PRECISION |兩倍 |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| 日期 |DateTime |
| 時間戳記 |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>將來源對應到接收資料行
若要了解如何將來源資料集內的資料行與接收資料集內的資料行對應，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>從關聯式來源進行可重複的讀取
從關聯式資料存放區複製資料時，請將可重複性謹記在心，以避免產生非預期的結果。 在 Azure Data Factory 中，您可以手動重新執行配量。 您也可以為資料集設定重試原則，使得在發生失敗時，重新執行配量。 以上述任一方式重新執行配量時，您必須確保不論將配量執行多少次，都會讀取相同的資料。 請參閱[從關聯式來源進行可重複的讀取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

* [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得使用「複製活動」來建立管線的逐步指示。

