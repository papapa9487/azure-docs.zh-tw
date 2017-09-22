---
title: "適用於 Azure Cosmos DB 的 Azure CLI 範例 | Microsoft Docs"
description: "Azure CLI 範例：建立及管理 Azure Cosmos DB 帳戶、資料庫、容器、區域和防火牆。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 06/07/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 189034b049e776a3b929930be937ec60bc1db241
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="azure-cli-samples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure CLI 範例

下表包含適用於 Azure Cosmos DB 之範例 Azure CLI 指令碼的連結。 您可以在 [Azure CLI 2.0 參考](https://docs.microsoft.com/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。

| |  |
|---|---|
|**建立 Azure Cosmos DB 帳戶、資料庫和容器**||
|[建立 DocumentDB API、圖形或資料表 API 帳戶](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 建立單一 Azure Cosmos DB API 帳戶、資料庫和容器，以便與 DocumentDB、圖形或資料表 API 搭配使用。 |
| [建立 MongoDB API 帳戶](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 建立單一 Azure Cosmos DB MongoDB API 帳戶、資料庫和集合。 |
|**調整 Azure Cosmos DB**||
| [調整容器輸送量](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 變更容器的已佈建輸送量。|
|[複寫多個區域中的 Azure Cosmos DB 資料庫帳戶和設定容錯移轉優先順序](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|使用指定的容錯移轉優先順序，將帳戶資料複寫到全球多個區域中。|
|**保護 Azure Cosmos DB**||
| [取得帳戶金鑰](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 取得帳戶主要和次要的主要 (master) 寫入金鑰，以及主要和次要唯讀金鑰。|
| [取得 MongoDB 連接字串](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 取得連接字串以將您的 MongoDB 應用程式連線到 Azure Cosmos DB 帳戶。|
|[重新產生帳戶金鑰](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|重新產生帳戶的主要或唯讀金鑰。|
|[建立防火牆](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 建立輸入 IP 存取控制原則，以限制從核准的電腦集合和/或雲端服務存取帳戶。|
|**高可用性、災害復原、備份和還原**||
|[設定容錯移轉原則](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|針對要在其中複寫帳戶的每個區域，設定容錯移轉優先順序。|
|**將 Azure Cosmos DB 連線到資源**||
|[將 Web 應用程式連線到 Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|建立 Azure Cosmos DB 資料庫和 Azure Web 應用程式並將兩者連線。|
|||

