---
title: "可供 Azure Machine Learning 資料準備使用的支援資料來源 | Microsoft Docs"
description: "本文件提供 Azure Machine Learning 資料準備可使用的支援資料來源完整清單"
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
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>此版本支援的資料來源 
下列文件概述 Azure Machine Learning 資料準備目前支援的資料來源清單。

以下列出此版本支援的資料來源。

## <a name="types"></a>類型 
### <a name="directory-versus-file"></a>檔案和目錄
*檔案/目錄*：選擇單一檔案並將它讀入資料準備。 剖析檔案類型以判斷下一個畫面上之檔案連接的預設參數。 選擇目錄或目錄中的一組檔案 (檔案選擇器是多重選取)。 任一種方法都會導致檔案讀入以作為單一資料流程，並且讓檔案彼此附加 (可視需要移除標頭)。

檔案類型如下所示：
- 分隔符號 (.csv、.tsv、.txt 等等) 
- 固定寬度
- 純文字
- JSON 檔案

### <a name="csv-file"></a>CSV 檔案
從儲存體讀取 CSV 檔案。

#### <a name="options"></a>選項
- 分隔符號
- 註解
- headers
- 小數符號
- 檔案編碼
- 要略過的行

### <a name="tsv-file"></a>TSV 檔案
從儲存體讀取 TSV 值。

#### <a name="options"></a>選項
- 註解
- headers
- 檔案編碼
- 要略過的行

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
讀取 Excel 檔案，藉由指定工作表名稱或號碼一次讀取一個工作表。

#### <a name="options"></a>選項
- 工作表名稱/號碼
- headers
- 要略過的行

### <a name="json-file"></a>JSON 檔案
從儲存體讀取 JSON 檔案。 請注意，檔案在讀取時會「壓平合併」。

#### <a name="options"></a>選項
None

### <a name="parquet"></a>Parquet
讀取 Parquet 資料集，單一檔案或資料夾。

Parquet 格式在儲存體中可以有各種形式。 對於較小的資料集，有時會用單一 .parquet 檔案。 各種 Python 程式庫均支援讀取或寫入至單一 .parquet 檔案。 目前，Azure Machine Learning Workbench 會依賴 PyArrow Python 程式庫，以便在本機互動使用期間讀取 Parquet。 它支援單一 .parquet 檔案 (只要它們是以這類方式寫入的，而不是較大型資料集的一部分)。 它也支援 Parquet 資料集。 

Parquet 資料集是一個以上 .parquet 檔案的集合，其中每一個都代表較大型資料集的較小分割區。 資料集通常會包含在資料夾中。 它們是 Spark 和 Hive 等通用平台的預設 Parquet 輸出格式。

>[!NOTE]
>如果讀取的 Parquet 資料位於含有多個 .parquet 檔案的資料夾中，則最安全的方式是先選取要讀取的目錄，然後核取 [Parquet 資料集] 選項。 這可讓 PyArrow 讀取整個資料夾而非個別檔案。 確保支援讀取在磁碟上儲存 Parquet (例如資料夾分割) 的更複雜方法。

向外延展執行依賴 Spark 的 Parquet 讀取功能，並支援單一檔案以及資料夾。

#### <a name="options"></a>選項
*Parquet 資料集*：這個選項會決定 Azure Machine Learning Workbench 是否使用未核取模式或核取模式。 未核取模式會展開指定的目錄，並嘗試個別讀取其中的每個檔案。 核取模式會將目錄視為整個資料集，並讓 PyArrow 找出最佳的方式來解譯檔案。


## <a name="locations"></a>位置
### <a name="local"></a>本機
本機硬碟或對應的網路儲存位置。

### <a name="azure-blob-storage"></a>Azure Blob 儲存體
需要 Azure 訂用帳戶。

