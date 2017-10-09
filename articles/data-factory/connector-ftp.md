---
title: "使用 Azure Data Factory 從 FTP 伺服器複製資料 | Microsoft Docs"
description: "了解如何使用 Azure Data Factory 管線中的複製活動，從 FTP 伺服器將資料複製到支援的接收資料存放區。"
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
ms.openlocfilehash: 52c90de32e3545cdb1f0210dfa695c7bcb67bedc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-ftp-server-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 FTP 伺服器複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-ftp-connector.md)
> * [第 2 版 - 預覽](connector-ftp.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 FTP 伺服器複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱 [第 1 版中的 FTP 連接器](v1/data-factory-ftp-connector.md)。

## <a name="supported-scenarios"></a>支援的案例

您可以將資料從 FTP 伺服器複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 FTP 連接器支援：

- 使用**基本**或**匿名**驗證複製檔案。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析檔案。

## <a name="get-started"></a>開始使用
您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](create-self-hosted-integration-runtime.md)。

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 FTP 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 FTP 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設定為：**FtpServer** | 是 |
| 主機 | 指定 FTP 伺服器的名稱或 IP 位址。 | 是 |
| 連接埠 | 指定 FTP 伺服器所接聽的連接埠<br/>允許的值為：整數，預設值為 **21**。 | 否 |
| enableSsl | 指定是否使用透過 SSL/TLS 的 FTP 通道。<br/>允許的值為：**true** (預設值)、**false**。 | 否 |
| enableServerCertificateValidation | 指定是否在使用透過 SSL/TLS 的 FTP 通道時啟用伺服器 SSL 憑證驗證。<br/>允許的值為：**true** (預設值)、**false**。 | 否 |
| authenticationType | 指定驗證類型。<br/>允許的值為：**基本**、**匿名** | 是 |
| userName | 指定擁有 FTP 伺服器存取權限的使用者。 | 否 |
| password | 指定使用者 (使用者名稱) 的密碼。 請將此欄位標示為 SecureString。 | 否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用「Azure 整合執行階段」或「自我裝載整合執行階段」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的「Azure 整合執行階段」。 |否 |

**範例 1：使用匿名驗證**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2：使用基本驗證**

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "<ftp server>",
            "port": 21,
            "enableSsl": true,
            "enableServerCertificateValidation": true,
            "authenticationType": "Basic",
            "userName": "<username>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 FTP 資料集所支援的屬性清單。

若要從 FTP 複製資料，請將資料集的類型屬性設定為 **FileShare**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為：**FileShare** |是 |
| folderPath | 資料夾的路徑。 例如：資料夾/子資料夾/ |是 |
| fileName | 如果您想要從特定的檔案複製，請在 **folderPath** 中指定該檔案的名稱。 如果沒有為此屬性指定任何值，資料集就會指向資料夾中的所有檔案作為來源。 |否 |
| fileFilter | 指定要用來在 folderPath (而不是所有檔案) 中選取檔案子集的篩選器。 只有在未指定檔案名稱時套用。 <br/><br/>允許的萬用字元為︰`*` (多個字元) 和 `?` (單一字元)。<br/>- 範例 1：`"fileFilter": "*.log"`<br/>- 範例 2：`"fileFilter": 2017-09-??.txt"` |否 |
| format | 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要以特定格式來剖析檔案，以下是支援的檔案格式類型：**TextFormat****JsonFormat****AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援的層級為：**Optimal** 和 **Fastest**。 |否 |
| useBinaryTransfer | 指定是否使用二進位傳輸模式。 值對二進位模式為真 (預設值)，對 ASCII 則為假。 |否 |

**範例：**

```json
{
    "name": "FTPDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<FTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 FTP 來源所支援的屬性清單。

### <a name="ftp-as-source"></a>FTP 作為來源

若要從 FTP 複製資料，請將複製活動中的來源類型設定為 **FileSystemSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設定為：**FileSystemSource** |是 |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。<br/>允許的值為：**true** (預設值)、**false** | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<FTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
