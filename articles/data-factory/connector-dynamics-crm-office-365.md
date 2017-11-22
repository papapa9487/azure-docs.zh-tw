---
title: "使用 Azure Data Factory 從/至 Dynamics CRM 和 365 複製資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 Dynamics CRM 和 365 複製到支援的接收資料存放區，或是從支援的接收資料存放區複製到 Dynamics CRM 和 365。"
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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: c2de89ba3adaaa7d745731cff74269deecef03e2
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>使用 Azure Data Factory 從/至 Dynamics 365/Dynamics CRM 複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Dynamics 365/Dynamics CRM 複製資料以及複製資料至 Dynamics 365/Dynamics CRM。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Dynamics 365/Dynamics CRM 複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 Dynamics 365/Dynamics CRM。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Dynamics 連接器支援下列 Dynamics 版本和驗證類型：

| Dynamics 版本 | 驗證類型 | 已連結的服務範例 |
|:--- |:--- |:--- |
| Dynamics 365 線上版 <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 驗證](#dynamics-365-and-dynamics-crm-online) |
| 搭配 IFD 的 Dynamics 365 內部部署版 <br> 搭配 IFD 的 Dynamics CRM 2016 內部部署版 <br> 搭配 IFD 的 Dynamics CRM 2015 內部部署版 | IFD | [搭配 IFD 的 Dynamics 內部部署版 + IFD 驗證](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

IFD 是「網際網路對向部署」的縮寫。

> [!NOTE]
> 若要使用 Dynamics 連接器，請將您的密碼儲存在 Azure Key Vault 中，然後讓 ADF 在執行資料複製時從該處提取密碼。 若要了解如何設定，請參閱[已連結的服務屬性](#linked-service-properties)一節。

## <a name="getting-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Dynamics 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Dynamics 已連結服務支援的屬性：

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 和 Dynamics CRM Online

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**Dynamics**。 | 是 |
| deploymentType | Dynamics 執行個體的部署類型。 如果是 Dynamics Online，就必須是 **"Online"**。 | 是 |
| organizationName | Dynamics 執行個體的組織名稱。 | 否，當與使用者關聯的 Dynamics 執行個體超過一個時，應該指定。 |
| authenticationType | 連線到 Dynamics 伺服器時所要使用的驗證類型。 如果是 Dynamics Online，請指定 **"Office365"**。 | 是 |
| username | 指定連線到 Dynamics 時所要使用的使用者名稱。 | 是 |
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 您必須將密碼放在 Azure Key Vault 中，然後將密碼設定為 "AzureKeyVaultSecret"。 請參閱[在金鑰保存庫中儲存認證](store-credentials-in-key-vault.md)深入了解。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 (用於來源)；是 (用於接收) |

>[!IMPORTANT]
>若要將資料複製到 Dynamics，使用 Dynamics 附近的位置明確[建立 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，並在下列範例所示的連結服務中產生關聯。

**範例：使用 Office365 驗證的 Dynamics 線上版**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>搭配 IFD 的 Dynamics 365 和 Dynamics CRM 內部部署版

相較於 Dyanmics Online，額外的屬性為 "hostName" 和 "port"。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**Dynamics**。 | 是 |
| deploymentType | Dynamics 執行個體的部署類型。 如果是搭配 IFD 的 Dynamics 內部部署版，就必須是 **"OnPremisesWithIfd"**。| 是 |
| **hostName** | 內部部署 Dynamics 伺服器的主機名稱。 | 是 |
| **port** | 內部部署 Dynamics 伺服器的連接埠。 | 否，預設值為 443 |
| organizationName | Dynamics 執行個體的組織名稱。 | 是 |
| authenticationType | 連線到 Dynamics 伺服器時所要使用的驗證類型。 如果是搭配 IFD 的 Dynamics 內部部署版，請指定 **"Ifd"**。 | 是 |
| username | 指定連線到 Dynamics 時所要使用的使用者名稱。 | 是 |
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 請注意，您必須將密碼放在 Azure Key Vault 中，然後將密碼設定為 "AzureKeyVaultSecret"。 請參閱[在金鑰保存庫中儲存認證](store-credentials-in-key-vault.md)深入了解。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 (來源)；是 (接收) |

>[!IMPORTANT]
>若要將資料複製到 Dynamics，以 Dynamics 附近的位置明確[建立 Azure IR](create-azure-integration-runtime.md#create-azure-ir)，並在下列範例所示的連結服務中產生關聯。

**範例：使用 IFD 驗證之搭配 IFD 的 Dynamics 內部部署版**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Dynamics 資料集所支援的屬性清單。

若要從/至 Dynamics 複製資料，請將資料集的類型屬性設定為 **DynamicsEntity**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**DynamicsEntity** |是 |
| entityName | 要擷取之實體的邏輯名稱。 | 否 (來源，如果已指定活動來源中的「查詢」)；是 (接收) |

> [!IMPORTANT]
>- **從 Dynamics 複製資料時，Dynamics 資料集內必須要有 "structure" 區段**，此區段定義您想要複製之 Dynamics 資料的資料行名稱和資料類型。 若要深入了解，請參閱[資料集結構](concepts-datasets-linked-services.md#dataset-structure)和 [Dynamics 的資料類型對應](#data-type-mapping-for-dynamics)。
>- **將資料複製到 Dynamics 時，Dynamics 資料集內不一定要有 "structure" 區段**。 要複製到哪些資料行則由來源資料的結構描述決定。 如果來源是沒有標頭的 CSV 檔案，在輸入資料集中指定 "structure"，包含對應至 CSV 檔案中欄位的資料行名稱和資料類型，依照順序一個接一個指定。

**範例：**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Dynamics 來源和接收所支援的屬性清單。

### <a name="dynamics-as-source"></a>Dynamics 作為來源

若要從 Dynamics 複製資料，請將複製活動中的來源類型設定為 **DynamicsSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**DynamicsSource**  | 是 |
| query  | FetchXML 是一個在 Microsoft Dynamics (線上版和內部部署版) 中使用的專屬查詢語言。 請參閱以下範例，若要深入了解，請參閱[使用 FetchXML 建立查詢](https://msdn.microsoft.com/en-us/library/gg328332.aspx)。 | 否 (如果已指定資料集中的 "entityName")  |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>範例 FetchXML 查詢

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-sink"></a>Dynamics 作為接收

若要複製資料至 Dynamics ，將複製活動中的接收類型設定為 **DynamicsSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動接收器的類型屬性必須設定為：**DynamicsSink**  | 是 |
| writeBehavior | 作業的寫入行為。<br/>允許的值為：**"Upsert"**。 | 是 |
| writeBatchSize | 每個批次中寫入 Dynamics 的資料列計數。 | 否 (預設值為 10) |
| ignoreNullValues | 指出在寫入作業期間是否要忽略輸入資料中的 null 值 (索引鍵欄位除外)。<br/>允許的值為：**true** 和 **false**。<br>- true：進行 upsert/更新作業時，目的地物件中的資料保持不變，進行插入作業時，插入定義的預設值。<br/>- false：進行 upsert/更新作業時，將目的地物件中的資料更加為 NULL，進行插入作業時，插入 NULL 值。  | 否 (預設值為 false) |

>[!NOTE]
>Dynamics 的接收 writeBatchSize 和複製活動 [parallelCopies](copy-activity-performance.md#parallel-copy) 的預設值都是 10 中，這表示同一時間會提交 100 筆記錄給 Dynamics。

**範例：**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics 的資料類型對應

從 Dynamics 複製資料時，會使用下列從 Dynamics 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

請參考以下的對應表，根據您的來源 Dynamics 資料類型，在資料集結構中設定對應的 ADF 資料類型：

| Dynamics 資料類型 | Data Factory 過渡期資料類型 | 支援作為來源 | 支援作為接收 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | 十進位 | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | Boolean | ✓ |  |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | 十進位 | ✓ |  |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ |  |
| AttributeType.Status | Int32 | ✓ |  |


> [!NOTE]
> 不支援 Dynamics 資料類型 AttributeType.CalendarRules 和 AttributeType.PartyList。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。