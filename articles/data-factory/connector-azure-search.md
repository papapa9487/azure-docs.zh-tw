---
title: "使用 Azure Data Factory 將資料複製到搜尋服務索引 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 管線中的「複製活動」，將資料推送或複製到 Azure 搜尋服務索引。"
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: ebf63cd6d0f9e62d1001d74ce06b4849e08c0de0
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 搜尋服務索引

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-azure-search-connector.md)
> * [第 2 版 - 預覽](connector-azure-search.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料複製到「Azure 搜尋服務」索引。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Azure 搜尋服務連接器](v1/data-factory-azure-search-connector.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從任何支援的來源資料存放區複製到「Azure 搜尋服務」索引。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

## <a name="getting-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義「Azure 搜尋服務」連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對「Azure 搜尋服務」已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**AzureSearch** | 是 |
| URL | Azure 搜尋服務的 URL。 | 是 |
| key | Azure 搜尋服務的系統管理金鑰。 請將此欄位標示為 SecureString。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!IMPORTANT]
> 將資料從雲端資料存放區複製到「Azure 搜尋服務」索引時，在「Azure 搜尋服務」已連結的服務中，您必須藉由在 connactVia 中指定明確的區域來參考 Azure Integration Runtime。 請將區域設定為您「Azure 搜尋服務」所在的區域。 若要深入了解，請參閱 [Azure Integration Runtime] (concepts-integration-runtime.md#azure-integration-runtime)。

**範例：**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供「Azure 搜尋服務」資料集所支援的屬性清單。

若要從「Azure 搜尋服務」複製資料，請將資料集的類型屬性設定為 **RelationalTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**AzureSearchIndex** | 是 |
| IndexName | Azure 搜尋服務索引的名稱。 Data Factory 不會建立索引。 索引必須存在於 Azure 搜尋服務中。 | 是 |

**範例：**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供「Azure 搜尋服務」來源所支援的屬性清單。

### <a name="azure-search-as-sink"></a>Azure 搜尋服務作為接收器

若要將資料複製到「Azure 搜尋服務」，請將複製活動中的來源類型設定為 **AzureSearchIndexSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**AzureSearchIndexSink** | 是 |
| writeBehavior | 指定若文件已經存在於索引中，是否要合併或取代。 請參閱 [WriteBehavior 屬性](#writebehavior-property)。<br/><br/>允許的值為：**Merge** (預設值) 和 **Upload**。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料上傳至 Azure 搜尋服務中。 如需詳細資訊，請參閱 [WriteBatchSize 屬性](#writebatchsize-property)。<br/><br/>允許的值為：整數 1 到 1,000；預設值為 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 屬性

AzureSearchSink 會在寫入資料時更新插入。 換句話說，在撰寫文件時，如果文件索引鍵已經存在於 Azure 搜尋服務索引中，Azure 搜尋服務就會更新現有的文件，而不是擲回衝突例外狀況。

AzureSearchSink (藉由使用 AzureSearch SDK) 提供下列兩種更新插入行為：

- **合併**︰將新文件中的所有資料行與現有的文件相結合。 對於新文件中含有 null 值的資料行，則會保留現有文件中的值。
- **上傳**：新的文件會取代現有的文件。 針對新文件中未指定的資料行，不論現有的文件中是否具有非 null 的值，都會將值設為 null。

預設行為是**合併**。

### <a name="writebatchsize-property"></a>WriteBatchSize 屬性

Azure 搜尋服務支援批次寫入文件。 一個批次可包含 1 到 1,000 個動作。 一個動作可指示一份文件來執行上傳/合併作業。

**範例：**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>資料類型支援

下表指出是否支援 Azure 搜尋服務資料類型。

| Azure 搜尋服務資料類型 | 在 Azure 搜尋服務接收器中受到支援 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| 兩倍 | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| 字串陣列 | N |
| GeographyPoint | N |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。