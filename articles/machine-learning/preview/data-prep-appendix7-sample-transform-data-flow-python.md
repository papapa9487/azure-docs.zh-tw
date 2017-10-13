---
title: "可用於 Azure Machine Learning 資料準備之轉換資料流程轉換的範例 | Microsoft Docs"
description: "本文件提供可用於 Azure ML 資料準備之轉換資料流程轉換的一組範例"
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
ms.openlocfilehash: f43f65ca89349fc790684e9bd7acd2f19e15abe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>自訂資料流程轉換的範例 (Python) 
這個轉換在功能表中的名稱是 [轉換資料流程 (指令碼)]。閱讀本附錄之前，請先參閱 [Python 擴充性概觀](data-prep-python-extensibility-overview.md)

## <a name="transform-frame"></a>轉換框架
### <a name="create-a-new-column-dynamically"></a>以動態方式建立新的資料行 
以動態方式建立資料行 (city2)，並且將現有城市資料行的多個不同版本 San Francisco 調解成一個。
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>新增新的彙總
建立新的框架，具有針對分數資料行 (依據 risk_category 資料行分組) 計算的第一個和最後一個彙總
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>對資料行進行極端值調整 
重新編寫資料以符合在資料行中減少極端值的公式
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>轉換資料流程
### <a name="fill-down"></a>向下填滿 
向下填滿需要兩個轉換。
假設資料看起來如下所示：


|State         |City       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|California    |洛杉磯|
|              |San Diego  |
|              |San Jose   |
|Texas         |達拉斯     |
|              |San Antonio|
|              |Houston    |

首先建立包含下列程式碼的 '新增資料行 (指令碼)' 轉換
```python
    row['State'] if len(row['State']) > 0 else None
```
現在建立包含下列程式碼的轉換資料流程 (指令碼) 轉換
```python
    df = df.fillna( method='pad')
```

資料現在如下所示：

|State         |newState         |City       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|California    |California    |洛杉磯|
|              |California    |San Diego  |
|              |California    |San Jose   |
|Texas         |Texas         |達拉斯     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |

