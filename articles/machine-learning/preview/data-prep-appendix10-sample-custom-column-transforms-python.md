---
title: "在 Azure Machine Learning 資料準備中衍生新資料行的範例 Python | Microsoft Docs"
description: "本文件提供在 Azure ML 資料準備中建立新資料行的 Python 程式碼範例"
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
ms.date: 09/12/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9a6e331e622b007232a62b34c2220d60d1d050ca
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="sample-of-custom-column-transforms-python"></a>自訂資料行轉換的範例 (Python) 
這個轉換在功能表中的名稱是「新增資料行 (指令碼)」

閱讀本附錄之前，請先參閱 [Python 擴充性概觀](data-prep-python-extensibility-overview.md)

## <a name="test-equivalence-and-replace-values"></a>等價測試並取代值 
如果 Col1 中的值是小於 4，則新資料行的值應該是 1，否則其值為 2 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>目前的日期和時間 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>類型轉型 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>評估 Null 值 
如果 Col1 包含 Null，則會將新的資料行標示為 Bad，否則會將它標示為 Good 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>新的計算資料行 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch 計算 
自從 Unix Epoch 以來的秒數 (假設 Col1 已經是日期 ) 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```






