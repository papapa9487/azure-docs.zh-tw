---
title: "Azure CLI Redis Cache 範例 | Microsoft Docs"
description: "Azure Redis Cache 的 Azure CLI 範例。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: a0f3b294f2a655a5ff891d4fd1be9137080349a6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure Redis Cache 的 Azure CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

| | |
|---|---|
|**建立快取**||
| [建立快取](./scripts/create-cache.md) | 建立資源群組和基本層 Azure Redis Cache。 |
| [使用叢集建立高級快取](./scripts/create-premium-cache-cluster.md) | 透過啟用的叢集建立資源群組與高級層快取。|
| [取得快取詳細資料](./scripts/show-cache.md) | 取得 Azure Redis Cache 執行個體的詳細資料，包括佈建狀態。 |
| [取得主機名稱、連接埠及金鑰](./scripts/cache-keys-ports.md) | 取得 Azure Redis Cache 執行個體的主機名稱、連接埠和金鑰。 |
|**Web 應用程式加上快取**||
| [將 Web 應用程式連線至 redis 快取](./../app-service/scripts/app-service-cli-app-service-redis.md) | 建立 Azure Web 應用程式和 Redis Cache,，然後將 Redis 連線詳細資料新增至應用程式設定。 |
|**刪除快取**||
| [刪除快取](./scripts/delete-cache.md) | 刪除 Azure Redis Cache 執行個體  |
| | |

如需有關 Azure CLI 2.0 的詳細資訊，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (英文) 和[開始使用 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (英文)。
