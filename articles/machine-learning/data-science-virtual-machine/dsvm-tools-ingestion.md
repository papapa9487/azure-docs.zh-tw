---
title: "資料科學虛擬機器資料擷取工具 - Azure | Microsoft Docs"
description: "資料科學虛擬機器資料擷取工具"
keywords: "資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 564c06c5017a77431b7d6fed7b43c47141b12252
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>資料科學虛擬機器資料擷取工具

資料科學或 AI 專案中的第一個技術步驟是識別將使用的資料集，並將該資料集匯入分析環境。 資料科學虛擬機器 (DSVM) 提供工具和程式庫，可將不同來源的資料匯入 DSVM 本機的分析資料儲存體，或匯入雲端或內部部署的資料平台。 

以下是 DSVM 提供的一些資料移動工具。 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將資料從 Azure 儲存體 Blob 複製到 Azure Data Lake Store 的工具。 它也可以在兩個 Azure Data Lake Store 帳戶之間複製資料。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 Azure 儲存體將多個 Blob 複製到 Azure Data Lake Store。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `adlcopy` 取得說明。    |
| 範例的連結      | [使用 AdlCopy]https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 上的相關工具      | AzCopy、Azure 命令列工具     |

## <a name="azure-command-line"></a>Azure 命令列

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Azure 的管理工具。 它也包含從 Azure 資料平台 (例如 Azure 儲存體 Blob、Azure Data Lake 儲存體) 移動資料的動詞命令     |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 將 Azure 儲存體、Azure Data Lake Store 的資料匯入及匯出      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `az` 取得說明。    |
| 範例的連結      | [使用 Azure CLI](https://docs.microsoft.com/cli/azure/?viee-cli-latest)     |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將資料複製到本機檔案、Azure 儲存體 Blob、檔案和資料表或從本機檔案、Azure 儲存體 Blob、檔案和資料表複製資料的工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將檔案複製到 Blob 儲存體、在帳戶之間複製 Blob。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `azcopy` 取得說明。    |
| 範例的連結      | [AzCopy on Windows](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)      |
| DSVM 上的相關工具      | AdlCopy     |


## <a name="azure-cosmos-db-documentdb-api-data-migration-tool"></a>Azure Cosmos DB：DocumentDB API 資料移轉工具

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 從各種來源 (包括 JSON 檔案、CSV 檔案、SQL、MongoDB、Azure 資料表儲存體、Amazon DynamoDB 及 Azure Cosmos DB DocumentDB API 集合) 將資料匯入至 Azure Cosmos DB 或 Azure DocumentDB 的工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 VM 將檔案匯入至 CosmosDB、從 Azure 資料表儲存體將資料匯入至 CosmosDB，或從 SQL Server 資料庫將資料匯入至 CosmosDB。     |
|  如何使用/執行它？    |   若要使用命令列版本，請開啟命令提示字元，然後輸入 `dt`。 若要使用 GUI 工具，請開啟命令提示字元，然後輸入 `dtui`。    |
| 範例的連結      | [CosmosDB 匯入資料](https://docs.microsoft.com/en-us/azure/cosmos-db/import-data)      |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 在 SQL Server 和資料檔案之間複製資料的 SQL Server 工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將 CSV 檔案匯入至 SQL Server 資料表、將 SQL Server 資料表匯出至檔案。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `bcp` 取得說明。    |
| 範例的連結      | [大量複製公用程式](https://docs.microsoft.com/en-us/sql/tools/bcp-utility)      |
| DSVM 上的相關工具      | SQL Server、sqlcmd      |


## <a name="microsoft-data-management-gateway"></a>Microsoft 資料管理閘道器

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將內部部署資料來源連線至雲端服務進行取用的工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將 VM 連線至內部部署資料來源。      |
|  如何使用/執行它？    |   從 [開始] 功能表中啟動「Microsoft 資料管理閘道器」。    |
| 範例的連結      | [資料管理閘道](https://msdn.microsoft.com/library/dn879362.aspx)      |
| DSVM 上的相關工具      | AzCopy、AdlCopy、bcp    |
