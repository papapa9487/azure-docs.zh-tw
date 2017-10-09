---
title: "Azure Data Factory 中的取得中繼資料活動 | Microsoft Docs"
description: "深入了解如何使用 SQL Server 預存程序活動，以從 Data Factory 管線叫用 Azure SQL Database 或 Azure SQL 資料倉儲中的預存程序。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory 中的取得中繼資料活動
GetMetadata 活動可用來擷取 Azure Data Factory 中任何資料的中繼資料。 僅第 2 版的資料處理站支援此活動。 使用案例如下：

- 驗證任何資料的中繼資料資訊
- 當資料準備好/可用時觸發管線

下列功能可用於控制流程：
- GetMetadata 活動的輸出可用於條件運算式，以執行驗證。
- 透過 Do-Until 迴圈滿足條件時，可以觸發管線

GetMetadata 活動採用資料集做為必要的輸入，並輸出可用的中繼資料資訊做為輸出。 目前僅支援 Azure Blob 資料集。 支援的中繼資料欄位是大小、結構和上次修改時間。  

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版本 (GA))，請參閱 [Data Factory V1 文件](v1/data-factory-introduction.md)。


## <a name="syntax"></a>語法

### <a name="get-metadata-activity-definition"></a>取得中繼資料活動定義：
在下列範例中，GetMetadata 活動會傳回 MyDataset 所代表資料的相關中繼資料。 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>資料集定義：

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>輸出
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>類型屬性
目前 GetMetadata 活動可以從 Azure 儲存體資料集擷取下列類型的中繼資料資訊。

屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
欄位清單 | 列出所需的中繼資料資訊類型。  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    否<br/>如果空白，活動會傳回全部 3 個支援的中繼資料資訊。 
資料集 | GetMetadata 活動要擷取其中繼資料活動的參考資料集。 <br/><br/>目前支援的資料集類型是 Azure Blob。 有兩個子屬性： <ul><li><b>referenceName</b>：現有 Azure Blob 資料集的參考</li><li><b>類型</b>：由於參考的是資料集，類型為 DatasetReference</li></ul> |    <ul><li>String</li><li>DatasetReference</li></ul> | 是

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
