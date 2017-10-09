---
title: "可用於 Azure Machine Learning 資料準備之其他來源資料連線的範例 | Microsoft Docs"
description: "本文件提供可用於 Azure ML 資料準備之來源資料連線的一組範例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="sample-of-custom-source-connections-python"></a>自訂來源連線的範例 (Python) 
閱讀本附錄之前，請先參閱 [Python 擴充性概觀](data-prep-python-extensibility-overview.md)

## <a name="loading-data-from-dataworld"></a>從 data.world 載入資料

### <a name="prerequisites"></a>必要條件

#### <a name="register-yourself-at-dataworld"></a>在 data.world 自行註冊
需要您從 data.world 網站取得的 API 權杖。

#### <a name="install-dataworld-library"></a>安裝 data.world 程式庫

從 [檔案] -> [開啟命令列介面]，開啟 Azure Machine Learning Workbench 命令列介面

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

然後在提示您提供權杖的命令列上執行 `dw configure`。 當您輸入權杖時，會在您的主目錄中建立 .dw/ 目錄，而且您的權杖將儲存在該目錄中。

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
現在您應該能夠匯入 data.world 程式庫。

#### <a name="load-data-into-data-preparation"></a>將資料載入資料準備

建立新的指令碼型資料流程，並使用下列指令碼從 data.world 載入資料

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>將 CosmosDB 資料載入資料準備

建立新的指令碼型資料流程，並使用下列指令碼從 CosmosDB 載入資料 (必須先安裝程式庫，請參閱上述的參考文件連結)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

