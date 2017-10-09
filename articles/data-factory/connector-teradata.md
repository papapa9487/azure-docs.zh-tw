---
title: "使用 Azure Data Factory 從 Teradata 複製資料 | Microsoft Docs"
description: "了解 Data Factory 服務的「Teradata 連接器」，此連接器可讓您將資料從 Teradata 資料庫複製到 Data Factory 所支援作為接收器的資料存放區。"
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
ms.openlocfilehash: 77cf65930a6d622e54395dd563901977c9d76170
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>使用 Azure Data Factory 從 Teradata 複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-onprem-teradata-connector.md)
> * [第 2 版 - 預覽](connector-teradata.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Teradata 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Teradata 連接器](v1/data-factory-onprem-teradata-connector.md)。

## <a name="supported-scenarios"></a>支援的案例

您可以將資料從 Teradata 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Teradata 連接器支援：

- Teradata **12 版和更新版本**。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。

## <a name="prerequisites"></a>必要條件

若要使用這個 Teradata 接收器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。
- 在 Integration Runtime 電腦上安裝 [Teradata 的 .Net 資料提供者](http://go.microsoft.com/fwlink/?LinkId=278886) 14 版或更新版本。

## <a name="getting-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Teradata 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Teradata 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**Teradata** | 是 |
| 伺服器 | Teradata 伺服器的名稱。 | 是 |
| authenticationType | 用來連線到 Teradata 資料庫的驗證類型。<br/>允許的值為：**Basic** (基本) 和 **Windows**。 | 是 |
| username | 指定連線到 Teradata 資料庫時所要使用的使用者名稱。 | 是 |
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 請將此欄位標示為 SecureString。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Teradata 資料集所支援的屬性清單。

若要從 Teradata 複製資料，請將資料集的類型屬性設定為 **RelationalTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**RelationalTable** | 是 |
| tableName | Teradata 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例：**

```json
{
    "name": "TeradataDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Teradata 來源所支援的屬性清單。

### <a name="teradata-as-source"></a>Teradata 作為來源

若要從 Teradata 複製資料，請將複製活動中的來源類型設定為 **RelationalSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**RelationalSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"`。 | 否 (如果已指定資料集中的 "tableName") |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-teradata"></a>Teradata 的資料類型對應

從 Teradata 複製資料時，會使用下列從 Teradata 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Teradata 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| 位元組 |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| 十進位 |十進位 |
| 兩倍 |Double |
| 圖形 |String |
| Integer |Int32 |
| 間隔日 |TimeSpan |
| 間隔日至小時 |TimeSpan |
| 間隔日至分鐘 |TimeSpan |
| 間隔日至秒鐘 |TimeSpan |
| 間隔小時 |TimeSpan |
| 間隔小時至分鐘 |TimeSpan |
| 間隔小時至秒鐘 |TimeSpan |
| 間隔分鐘 |TimeSpan |
| 間隔分鐘至秒鐘 |時間範圍 |
| 間隔月 |String |
| 間隔第二 |TimeSpan |
| 間隔年 |String |
| 間隔年至月 |String |
| Number |Double |
| Period(Date) |String |
| Period(Time) |String |
| Period(Time With Time Zone) |String |
| Period(Timestamp) |String |
| Period(Timestamp With Time Zone) |String |
| SmallInt |Int16 |
| 時間 |TimeSpan |
| 時區的時間 |String |
| Timestamp |DateTime |
| 時區的時間戳記 |Datetimeoffset |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |String |


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
