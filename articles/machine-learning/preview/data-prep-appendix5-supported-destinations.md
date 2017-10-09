---
title: "可供 Azure Machine Learning 資料準備使用的支援資料目的地/輸出 | Microsoft Docs"
description: "本文件提供 Azure ML 資料準備可使用的支援目的地/輸出完整清單"
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
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-exports-for-this-preview"></a>適用於此預覽的支援資料匯出 
您可匯出成幾個不同的格式。 可以先使用這些格式來保留資料準備的中繼結果，然後再整合至其餘 Machine Learning 工作流程。

## <a name="types"></a>類型 
### <a name="csv-file"></a>CSV 檔案 
將逗號分隔值檔案寫入儲存體

#### <a name="options"></a>選項
- 行尾結束符號
- 將 Null 取代為
- 將錯誤取代為 
- 分隔符號


### <a name="parquet"></a>Parquet ###
將資料集寫入儲存體作為 Parquet。

Parquet 格式在儲存體中可以有各種形式。 對於較小的資料集，有時會使用單一 '.parquet' 檔案，各種 Python 程式庫支援讀取/寫入至單一 '.parquet' 檔案。 目前，AMLWB 依賴 PyArrow Python 程式庫在本機「互動」使用期間寫出 Parquet。 這表示單一檔案 Parquet 是本機互動使用期間，目前唯一支援的 Parquet 輸出格式。

(在 Spark 上) 向外延展執行期間，AMLWB 依賴 Spark 的 Parquet 讀取/寫入功能。 適用於 Parquet 的 Spark 預設輸出格式 (目前唯一支援) 與 HIVE 資料集的結構相似。 這表示資料夾包含許多 '.parquet' 檔案，每個檔案都是較大資料集的較小分割。 

#### <a name="caveats"></a>需要注意的事項 ####
Parquet 格式相對比較年輕，並且在不同程式庫之間存在一些不一致的實作方法。 例如，Spark 在寫出 PyArrow 未寫出的 Parquet 時，對於「資料行名稱」中具備的有效字元設定限制。 「資料行名稱」中不可有集合中的任何字元 " ,;{}()\\n\\t="。

>[!NOTE]
>為了確保與 Spark 相容，每當資料寫入 Parquet 時，「資料行名稱」中出現的這些字元會以 '_' (底線) 取代。**

>[!NOTE]
>為了確保本機和向外延展寫入 Parquet 之任何資料之間的一致性 (透過應用程式、Python 或 Spark)，會將其「資料行名稱」妥善處理，以確保 Spark 相容性。 為了確保在 Sparks 中寫入 Parquet 字元時產生預期的「資料行名稱」，在寫出之前應該先移除無效集合。



## <a name="locations"></a>位置 
### <a name="local"></a>本機 
本機硬碟或對應的網路儲存位置

### <a name="azure-blob"></a>Azure BLOB 
Azure 儲存體 (BLOB) 需要 Azure 訂用帳戶


