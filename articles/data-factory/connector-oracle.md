---
title: "使用 Azure Data Factory 將資料複製到 Oracle 或從該處複製資料 | Microsoft Docs"
description: "了解如何使用 Data Factory 將資料從支援的來源存放區複製到 Oracle 資料庫，或從 Oracle 複製到支援的接收存放區。"
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
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: f5d6cf07c52920a795c42e7f3578b1666a86d3c5
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>使用 Azure Data Factory 從 Oracle 複製資料及將資料複製到該處
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-onprem-oracle-connector.md)
> * [第 2 版 - 預覽](connector-oracle.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Oracle 資料庫複製資料及將資料複製到該處。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Oracle 連接器](v1/data-factory-onprem-oracle-connector.md)。

## <a name="supported-scenarios"></a>支援的案例

您可以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 Oracle 資料庫。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Oracle 連接器支援下列版本的 Oracle 資料庫，並且支援「基本」或 OID 驗證。

    - Oracle 12c R1 (12.1)
    - Oracle 11g R1、R2 (11.1、11.2)
    - Oracle 10g R1、R2 (10.1、10.2)
    - Oracle 9i R1、R2 (9.0.1、9.2)
    - Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>必要條件

若要從不可公開存取的 Oracle 資料庫複製資料，或將資料複製到該處，您必須設定一個「自我裝載 Integration Runtime」。 如需有關 Integration Runtime 的詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)。 Integration Runtime 提供內建的 Oracle 驅動程式，因此從 Oracle 複製資料或將資料複製到該處時，您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Oracle 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Oracle 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**Oracle** | 是 |
| connectionString | 指定連線到 Oracle 資料庫執行個體所需的資訊。 請將此欄位標示為 SecureString。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Oracle 資料集所支援的屬性清單。

若要從 Oracle 複製資料或將資料複製到該處，請將資料集的類型屬性設定為 **OracleTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**OracleTable** | 是 |
| tableName |Oracle 資料庫中連結服務所參照的資料表名稱。 | 是 |

**範例：**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Oracle 來源和接收器所支援的屬性清單。

### <a name="oracle-as-source"></a>Oracle 作為來源

若要從 Oracle 複製資料，請將複製活動中的來源類型設定為 **OracleSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**OracleSource** | 是 |
| oracleReaderQuery | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"`。 | 否 |

如果您未指定 "oracleReaderQuery"，就會使用資料集的 "structure" 區段中定義的資料行，來建構要針對 Oracle 資料庫執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle 作為接收器

若要將資料複製到 Oracle，請將複製活動中的接收器類型設定為 **OracleSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**OracleSink** | 是 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中<br/>允許的值為：整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/>允許的值為：時間範圍。 範例：00:30:00 (30 分鐘)。 | 否 |
| preCopyScript | 指定一個供「複製活動」在每次執行時將資料寫入到 Oracle 前執行的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Oracle 的資料類型對應

從 Oracle 複製資料或將資料複製到該處時，會使用下列從 Oracle 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Oracle 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |String |
| CLOB |String |
| 日期 |DateTime |
| FLOAT |Decimal，字串 (如果精確度 > 28) |
| INTEGER |Decimal，字串 (如果精確度 > 28) |
| 長 |String |
| 長 RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| 數字 |Decimal，字串 (如果精確度 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| 時間戳記 |DateTime |
| 本地時區的時間戳記 |String |
| 時區的時間戳記 |String |
| 不帶正負號的整數 |數字 |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 不支援 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND 資料類型。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。