---
title: "Azure Machine Learning 資料準備之執行與資料環境的支援組合 | Microsoft Docs"
description: "本文件提供 Azure ML 資料準備的不同執行階段與資料來源之支援組合的完整清單"
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
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="supported-matrix-for-this-release"></a>此版本支援的矩陣 
當您使用資料來源載入程式碼，或使用資料準備取得 Pandas 或 Spark 資料框架時，支援以下實驗計算環境和資料位置的組合：

|     |本機檔案  |Azure BLOB  |SQL Server Database***  |
|---------|---------|---------|---------|---------|
|本機 Python    |     支援    |不支援         | 不支援        |         |
|Docker (Linux VM) Python     |僅在專案檔中支援*         | 不支援        |        不支援 |         |
|Docker (Linux VM) PySpark     |僅在專案檔中支援*     |支援         | 支援**        |         |
|Azure 資料科學虛擬機器 Python     |僅在專案檔中支援*         |不支援         |不支援         |         |
|Azure 資料科學虛擬機器 PySPark     | 僅在專案檔中支援*        |不支援         |不支援         |         |
|Azure HDInsight PySpark     | 不支援        |支援         |支援**         |         |
|Azure HDInsight Python     | 不支援        | 不支援        | 不支援        |         |

Azure Data Lake Store 目前不支援任何計算目標。

*使用本機檔案路徑時，會將專案中的檔案複製到計算環境，然後在該環境中讀取。 不會複製專案外部的檔案，也不會在計算環境中解析路徑。 請考慮使用「資料來源取代」，使您的程式碼可以在本機執行時使用本機文件，然後切換至 Azure 儲存體 Blob 以進行不同的執行設定。 您也可以使用資料來源支援的取樣，以管理僅在某些執行設定中執行的大型資料。

**使用 Maven JDBC SQL Server 驅動程式 6.2.1。 您必須確定該計算環境的 spark_dependencies.yml 檔案包含此封裝 (或相容的封裝)。

***支援 Azure SQL Database、Azure SQL 資料倉儲或 Microsoft SQL Server，讓資料庫可從計算環境中連線。 

