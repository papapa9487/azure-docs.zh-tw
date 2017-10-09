---
title: "可供 Azure Machine Learning 資料準備使用的範例目的地/輸出 | Microsoft Docs"
description: "本文件提供可用於 Azure ML 資料準備之自訂資料目的地/輸出的一組範例"
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
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---


# <a name="sample-of-destination-connections-python"></a>目的地連線的範例 (Python) 
閱讀本附錄之前，請先參閱 [Python 擴充性概觀](data-prep-python-extensibility-overview.md)


### <a name="write-to-excel"></a>寫入至 Excel 


寫入至 Excel 需要額外的文件庫，新增新的文件庫記載於擴充性概觀中。 `openpyxl` 是需要新增的文件庫。

寫入之前，可能需要一些其他變更。 資料準備中使用的某些資料類型在部分目的地格式中不支援。 例如，如果 "Error" 物件存在，就無法正確序列化至 Excel。 因此在嘗試寫入至 Excel 之前，需要「取代錯誤值」轉換，它會從任何資料行移除錯誤。

假設上述所有工作皆已完成，後續行會將資料資料表寫入 Excel 文件中的單一工作表。 新增「寫入資料流程 (指令碼)」轉換，並且在運算式區段中輸入下列程式碼：


#### <a name="on-windows"></a>在 Windows 上 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>在 MacOS/OS X 上 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```

