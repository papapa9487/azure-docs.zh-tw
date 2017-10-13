---
title: "使用 Data Factory 將資料複製到 Azure 資料表儲存體或從該處複製資料 | Microsoft Docs"
description: "了解如何使用 Data Factory 將資料從支援的來源存放區複製到「Azure 資料表儲存體」，或從「資料表儲存體」複製到支援的接收存放區。"
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
ms.openlocfilehash: 2fa03b82750585454430da0c29392db57b20d3c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 資料表或從該處複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-azure-table-connector.md)
> * [第 2 版 - 預覽](connector-azure-table-storage.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料複製到「Azure 資料表」及從該處複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Azure 資料表儲存體連接器](v1/data-factory-azure-table-connector.md)。

## <a name="supported-scenarios"></a>支援的案例

您可以將資料從任何支援的來源資料存放區複製到「Azure 資料表」，或將資料從「Azure 資料表」複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個「Azure 資料表」連接器支援同時使用「帳戶金鑰」和「服務 SAS」 (共用存取簽章) 驗證來複製 Blob。

## <a name="get-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義「Azure 資料表儲存體」專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

### <a name="using-account-key"></a>使用帳戶金鑰

您可以使用帳戶金鑰來建立「Azure 儲存體」已連結的服務，此金鑰可將「Azure 儲存體」的全域存取權提供給資料處理站。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設為： **AzureStorage** |是 |
| connectionString | 針對 connectionString 屬性指定連接到 Azure 儲存體所需的資訊。 請將此欄位標示為 SecureString。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>使用服務 SAS 驗證

您也可以使用「共用存取簽章」(SAS) 來建立「Azure 儲存體」已連結的服務，此簽章可將受限制/有時間限制的存取權提供給資料處理站，來存取儲存體中的所有/特定資源。

「共用存取簽章」(SAS) 可提供您儲存體帳戶中資源的委派存取權。 它可讓您在無需分享您帳戶存取金鑰的情況下，將您儲存體帳戶中的物件有限權限授與用戶端，該用戶端便可在指定的時間期間內及使用指定的權限集來進行存取。 SAS 是一種 URI，此 URI 會在其查詢參數中包含對儲存體資源進行驗證式存取所需的一切資訊。 若要使用 SAS 存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入 SAS 即可。 如需 SAS 的詳細資訊，請參閱 [共用存取簽章：了解 SAS 模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory 現在僅支援 **服務 SAS**，但不支援帳戶 SAS。 如需這兩種類型的詳細資料及其建構方式，請參閱[共用存取簽章的類型](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)。 可從 Azure 入口網站或「儲存體總管」產生的 SAS URL 是「帳戶 SAS」，並不受到支援。
>

若要使用「服務 SAS」驗證，以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設為： **AzureStorage** |是 |
| sasUri | 指定 Azure 儲存體資源 (例如 Blob、容器或資料表) 的共用存取簽章 URI。 請將此欄位標示為 SecureString。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

建立 **SAS URI** 時，請考量下列幾點：

- 根據連結的服務 (讀取、寫入、讀取/寫入) 在資料處理站中的使用方式，在物件上設定適當的讀取/寫入**權限**。
- 設定適當的 [過期時間]。 請確定 Azure 儲存體物件的存取權不會在管線的作用中期間內過期。
- 應視需要在正確的資料表層級建立 URI。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供「Azure 資料表」資料集所支援的屬性清單。

若要將資料複製到「Azure 資料表」或從該處複製資料，請將資料集的類型屬性設定為 **AzureTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**AzureTable** |是 |
| tableName |Azure 資料表資料庫執行個體中連結服務所參照的資料表名稱。 |是 |

**範例：**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data factory 的結構描述

針對無結構描述的資料存放區 (如 Azure 資料表)，Data Factory 服務會以下列一種方式推斷結構描述：

1. 如果您是使用資料集定義中的 **structure** 屬性來定義結構，Data Factory 服務會將此結構接受為結構描述。 在此情況下，如果資料列的資料行沒有值，系統就會為它提供 null 值。
2. 如果您沒有使用資料集定義中的 **structure** 屬性來指定結構，Data Factory 服務將會使用資料的第一列來推斷結構描述。 在此情況下，如果第一個資料列未包含完整的結構描述，複製作業的結果中就會遺失一些資料行。

因此，對於無結構描述的資料來源來說，最佳作法是使用 **structure** 屬性來指定資料結構。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供「Azure 資料表」來源和接收器所支援的屬性清單。

### <a name="azure-table-as-source"></a>Azure 資料表作為來源

若要從「Azure 資料表」複製資料，請將複製活動中的來源類型設定為 **AzureTableSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**AzureTableSource** |是 |
| AzureTableSourceQuery |使用自訂的 Azure 資料表查詢來讀取資料。 請參閱下一節中的範例。 |否 |
| azureTableSourceIgnoreTableNotFound |指出是否忍受資料表不存在的例外狀況。<br/>允許的值為：**True** 和 **False** (預設值)。 |否 |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery 範例

如果 Azure 資料表資料行是字串類型：

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

如果 Azure 資料表資料行是日期時間類型：

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Azure 資料表作為接收器

若要從「Azure 資料表」複製資料，請將複製活動中的來源類型設定為 **AzureTableSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**AzureTableSink** |是 |
| azureTableDefaultPartitionKeyValue |可供接收器使用的預設資料分割索引鍵值。 |否 |
| azureTablePartitionKeyName |指定其值用來作為分割區索引鍵的資料行名稱。 如果未指定，則會使用 "AzureTableDefaultPartitionKeyValue" 作為分割區索引鍵。 |否 |
| azureTableRowKeyName |指定其值用來作為資料列索引鍵的資料行名稱。 如果未指定，則會針對每個資料列使用 GUID。 |否 |
| azureTableInsertType |將資料插入 Azure 資料表的模式。 此屬性可控制針對輸出資料表中具有相符分割區和資料列索引鍵的現有資料列，是要取代還是合併其值。 <br/><br/>允許的值為：**merge** (預設值) 和 **replace**。 <br/><br> 此設定是在資料列層級套用，而不是在資料表層級套用，而且兩個選項都不會刪除存在於輸出資料表中但不存在於輸入中的資料列。 若要了解這些設定 (合併和取代) 的運作方式，請參閱[插入或合併實體](https://msdn.microsoft.com/library/azure/hh452241.aspx)和[插入或取代實體](https://msdn.microsoft.com/library/azure/hh452242.aspx)主題。 |否 |
| writeBatchSize |在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中。<br/>允許的值為：整數 (資料列數目) |否 (預設值為 10000) |
| writeBatchTimeout |在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中。<br/>允許的值為：時間範圍。 範例："00:20:00" (20 分鐘) |否 (預設為 90 秒 - 儲存體用戶端的預設逾時) |

**範例：**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

您必須先使用 "translator" 屬性將來源資料行對應至目的地資料行，才能使用目的地資料行作為 azureTablePartitionKeyName。

在下列範例中，來源資料行 DivisionID 會對應至目的地資料行 DivisionID。

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" 被指定為分割區索引鍵。

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure 資料表的資料類型對應

從「Azure 資料表」複製資料或將資料複製到該處時，會使用下列從「Azure 資料表」資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

將資料移入及移出 Azure 資料表時，從 Azure 資料表 OData 類型到 .NET 類型的對應會使用下列 [Azure 資料表服務定義的對應](https://msdn.microsoft.com/library/azure/dd179338.aspx)，反向對應亦適用。

| Azure 資料表資料類型 | Data Factory 過渡期資料類型 | 詳細資料 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |上限為 64 KB 的位元組陣列。 |
| Edm.Boolean |布林 |布林值。 |
| Edm.DateTime |DateTime |以國際標準時間 (UTC) 表示的 64 位元值。 支援的 DateTime 範圍從西元 1601 年 1 月 1 日午夜 12:00 開始 (C.E.), UTC. 此範圍結束於 9999 年 12 月 31 日。 |
| Edm.Double |double |64 位元的浮點值。 |
| Edm.Guid |Guid |128 位元的全域唯一識別碼。 |
| Edm.Int32 |Int32 |32 位元的整數。 |
| Edm.Int64 |Int64 |64 位元的整數。 |
| Edm.String |String |UTF 16 編碼值。 字串值最大可達 64 KB。 |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。