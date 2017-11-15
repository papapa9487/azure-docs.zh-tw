---
title: "可供 Azure Machine Learning 資料準備使用的支援資料來源 | Microsoft Docs"
description: "本文件提供 Azure Machine Learning 資料準備可使用的支援資料來源完整清單。"
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
ms.openlocfilehash: db4774de28a17e022de111986f72a1f15ec32beb
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Azure Machine Learning 資料準備支援的資料來源 
本文概述 Azure Machine Learning 資料準備目前支援的資料來源。

以下列出此版本支援的資料來源。

## <a name="types"></a>類型 
### <a name="directory-vs-file"></a>目錄和檔案
選擇單一檔案並將它讀入資料準備。 剖析檔案類型以判斷下一個畫面顯示之檔案連接的預設參數。

選擇目錄或目錄中的一組檔案 (檔案選擇器是多重選取)。 使用任一種方法，檔案都會讀入作為單一資料流程，並且讓檔案彼此附加，可視需要移除標題。

支援的檔案類型包括：
- 分隔符號 (csv、tsv、txt 等)
- 固定寬度
- 純文字
- JSON 檔案

### <a name="csv-file"></a>CSV 檔案
從儲存體讀取逗號分隔值檔案。

#### <a name="options"></a>選項
- 分隔符號
- 註解
- headers
- 小數符號
- 檔案編碼
- 要略過的行

### <a name="tsv-file"></a>TSV 檔案
從儲存體讀取定位字元分隔值檔案。

#### <a name="options"></a>選項
- 註解
- headers
- 檔案編碼
- 要略過的行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
讀取 Excel 檔案，藉由指定工作表名稱或號碼一次讀取一個工作表。

#### <a name="options"></a>選項
- 工作表名稱或號碼
- headers
- 要略過的行

### <a name="json-file"></a>JSON 檔案
從儲存體讀取 JSON 檔案。 檔案在讀取時會「壓平合併」。

#### <a name="options"></a>選項
- None

### <a name="parquet"></a>Parquet
讀取 Parquet 資料集，單一檔案或資料夾。

Parquet 格式在儲存體中可以有各種形式。 對於較小的資料集，有時會使用單一 .parquet 檔案。 各種 Python 程式庫均支援讀取或寫入至單一 .parquet 檔案。 目前，Azure Machine Learning 資料準備會依賴 PyArrow Python 程式庫，以便在本機互動使用期間讀取 Parquet。 它支援單一 .parquet 檔案 (只要它們以這類方式寫入，而不是較大型資料集的一部分) 以及 Parquet 資料集。

Parquet 資料集是一個以上 .parquet 檔案的集合，其中每一個都代表較大型資料集的較小分割。 資料集通常包含於資料夾中，而且是 Spark 和 Hive 這類平台的預設 parquet 輸出格式。

>[!NOTE]
>如果讀取的 Parquet 資料位於含有多個 .parquet 檔案的資料夾中，則最安全的方式是先選取要讀取的目錄，然後選取 [Parquet 資料集] 選項。 這可讓 PyArrow 讀取整個資料夾而非個別檔案。 確保支援讀取在磁碟上儲存 Parquet (例如資料夾分割) 的更複雜方法。

向外延展執行依賴 Spark 的 Parquet 讀取功能，並支援單一檔案以及資料夾，類似於本機互動使用。

#### <a name="options"></a>選項
- Parquet 資料集。 此選項決定 Azure Machine Learning 資料準備是否展開指定的目錄，並嘗試個別讀取每個檔案 (未選取的模式)，或它是否將目錄視為整個資料集 (選取的模式)。 使用選取的模式，PyArrow 會選擇最佳的方式來解譯檔案。


## <a name="locations"></a>位置
### <a name="local"></a>本機
本機硬碟或對應的網路儲存位置。

### <a name="azure-blob-storage"></a>Azure Blob 儲存體
Azure Blob 儲存體，需要 Azure 訂用帳戶。

