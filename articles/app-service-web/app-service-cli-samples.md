---
title: "Azure CLI 範例 - App Service | Microsoft Docs"
description: "Azure CLI 範例 - App Service"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5c995ca118676935f4f9b0c72c266b9d52c181cb
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="azure-cli-samples"></a>Azure CLI 範例

下表包含使用 Azure CLI 所建置之 Bash 指令碼的連結。

| | |
|-|-|
|**建立應用程式**||
| [建立 Web 應用程式並從 從 GitHub 部署程式碼](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Azure Web 應用程式和從公用 GitHub 存放庫部署程式碼。 |
| [建立可從 GitHub 連續部署的 Web 應用程式](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 建立可從您擁有的 GitHub 存放庫連續發佈的 Azure Web 應用程式。 |
| [建立 Web 應用程式並從本機 Git 存放庫部署程式碼](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Web 應用程式並設定從本機 Git 存放庫推送程式碼的作業。 |
| [建立 Web 應用程式並將程式碼部署至預備環境](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 建立具有部署位置以供放置預備程式碼變更的 Azure Web 應用程式。 |
| [在 Docker 容器中建立 ASP.NET 核心 Web 應用程式](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| 在 Linux 上建立 Azure Web 應用程式，並從 Docker Hub 載入 Docker 映像。 |
|**設定應用程式**||
| [將自訂網域對應至 Web 應用程式](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Azure Web 應用程式，並向它對應自訂網域名稱。 |
| [將自訂 SSL 憑證繫結至 Web 應用程式](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Azure Web 應用程式，並將自訂網域名稱的 SSL 憑證加以繫結。 |
|**調整應用程式**||
| [手動調整 Web 應用程式](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Web 應用程式，並將它調整到 2 個執行個體中。 |
| [透過高可用性架構將 Web 應用程式調整為全球可用](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 在兩個不同的地理區域中建立兩個 Azure Web 應用程式，並使用 Azure 流量管理員讓它們可透過單一端點來使用。 |
|**將應用程式連線至資源**||
| [將 Web 應用程式連線至 SQL Database](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Azure Web 應用程式和 SQL Database，然後將資料庫連接字串新增至應用程式設定。 |
| [將 Web 應用程式連線至儲存體帳戶](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Azure Web 應用程式和儲存體帳戶，然後將儲存體連接字串新增至應用程式設定。 |
| [將 Web 應用程式連線至 redis 快取](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Web 應用程式和 redis 快取，然後將 redis 連線詳細資料新增至應用程式設定。) |
| [將 Web 應用程式連線至 Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Web 應用程式和 Cosmos DB，然後將 Cosmos DB 連線詳細資料新增至應用程式設定。 |
|**監視應用程式**||
| [使用 Web 伺服器記錄監視 Web 應用程式](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | 建立 Azure Web 應用程式、為其啟用記錄，並將記錄下載到本機電腦。 |
| | |
