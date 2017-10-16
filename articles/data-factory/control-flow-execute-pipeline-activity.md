---
title: "在 Azure Data Factory 中執行管線活動 | Microsoft Docs"
description: "了解如何使用「執行管線活動」從一個資料處理站管線叫用另一個資料處理站管線。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: 39f687a4de9a79e88d11e246cd0097dd9346c0ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>在 Azure Data Factory 中執行管線活動
執行管線活動可允許資料處理站管線叫用另一個管線。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版本 (GA))，請參閱 [Data Factory V1 文件](v1/data-factory-introduction.md)。

## <a name="syntax"></a>語法

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>類型屬性
屬性 | 說明 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
名稱 | 執行管線活動的名稱。 | String | 是
類型 | 必須設為：**ExecutePipeline**。 | String | 是
管線 | 此管線叫用之相依管線的管線參考。 管線參考物件有兩個屬性：**referenceName** 和 **type**。 referenceName 屬性指定參考管線的名稱。 type 屬性必須設為 PipelineReference。 | PipelineReference | 是
參數 | 要傳遞至叫用之管線的參數 | 將參數名稱對應到引數值的 JSON 物件 | 否
waitOnCompletion | 定義活動執行是否等待相依的管線執行完成。 | 預設值為 false。 | Boolean | 否

## <a name="sample"></a>範例
此案例有兩個管線：

- **主要管線** - 此管線有一個執行管線活動，會呼叫叫用的管線。 主要管線採用兩個參數：`masterSourceBlobContainer`、`masterSinkBlobContainer`。
- **叫用的管線** - 此管線有一個複製活動，會將資料從 Azure Blob 來源複製到 Azure Blob 接收。 叫用的管線採用兩個參數：`sourceBlobContainer`、`sinkBlobContainer`。

### <a name="master-pipeline-definition"></a>主要管線定義

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "datasets": [],
    "linkedServices": [],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>叫用的管線定義

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "datasets": [
      {
        "name": "SourceBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sourceBlobContainer",
              "type": "Expression"
            },
            "fileName": "salesforce.txt"
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      },
      {
        "name": "sinkBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sinkBlobContainer",
              "type": "Expression"
            }
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ],
    "linkedServices": [
      {
        "name": "BlobStorageLinkedService",
        "properties": {
          "type": "AzureStorage",
          "typeProperties": {
            "connectionString": {
              "value": "DefaultEndpointsProtocol=https;AccountName=*****",
              "type": "SecureString"
            }
          }
        }
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="running-the-pipeline"></a>執行管線

為執行此範例中的主要管線，下列值會傳遞給 masterSourceBlobContainer 和 masterSinkBlobContainer 參數： 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

主要管線會將這些值轉送到叫用的管線，如下列範例所示： 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)