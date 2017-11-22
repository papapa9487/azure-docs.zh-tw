---
title: "Azure SQL Database 多租用戶應用程式簡介 - Wingtip SaaS | Microsoft Docs"
description: "藉由使用採用 Azure SQL Database - Wingtip SaaS 的範例多租用戶應用程式來學習"
keywords: SQL Database Azure
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: ddd51c23c7e7d01e38b02c79c27d1951eea61e70
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL Database 多租用戶 SaaS 應用程式範例的簡介

Wingtip SaaS 應用程式是一個多租用戶應用程式範例，可示範 SQL Database 的獨特優點。 此應用程式使用租用戶各有資料庫的 SaaS 應用程式模式來維護多租用戶。 此應用程式是設計成用來展示支援 SaaS 案例 (包括數個 SaaS 設計和管理模式) 的 Azure SQL Database 功能。 為了快速啟動並執行，五分鐘內就會部署 Wingtip SaaS 應用程式！

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存放庫可取得應用程式原始程式碼和管理指令碼。 若要執行指令碼，請[將 Learning Modules 資料夾](#download-and-unblock-the-wingtip-saas-scripts)下載到您的本機電腦。

## <a name="application-architecture"></a>應用程式架構

Wingtip SaaS 應用程式會使用「各租用戶資料庫」模型，並使用 SQL 彈性集區將效率最大化。 若要將租用戶佈建和對應至其資料，則會使用目錄資料庫。 核心 Wingtip SaaS 應用程式會使用具有三個範例租用戶的集區，再加上目錄資料庫。 完成許多 Wingtip SaaS 教學課程，可藉由引進分析資料庫、跨資料庫結構描述管理等等，造成初始部署的附加元件。


![Wingtip SaaS 架構](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


在進行教學課程並使用應用程式時，務必將焦點放在與資料層相關的 SaaS 模式。 換句話說，著重於資料層，且不要過度分析應用程式本身。 了解這些 SaaS 模式的實作方式是在應用程式中實作這些模式的關鍵，同時考慮針對您特定的業務需求進行任何必要的修改。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS 教學課程

部署應用程式之後，請瀏覽在初始部署時建立的下列教學課程。 這些教學課程會探索常見 SaaS 模式，其利用 SQL Database、SQL 資料倉儲和其他 Azure 服務的內建功能。 教學課程包含 PowerShell 指令碼，以及可大幅簡化理解的詳細說明，並且在您的應用程式中實作相同的 SaaS 管理模式。


| 教學課程 | 說明 |
|:--|:--|
| [Azure SQL Database 多租用戶 SaaS 應用程式範例的指引和祕訣](saas-dbpertenant-wingtip-app-guidance-tips.md) | **從這裡開始！** 下載並執行 PowerShell 指令碼，以準備應用程式的組件。 |
|[部署及探索 Wingtip SaaS 應用程式](saas-dbpertenant-get-started-deploy.md)|  將 Wingtip SaaS 應用程式部署至 Azure 訂用帳戶並加以探索。 |
|[佈建租用戶並在目錄中註冊](saas-dbpertenant-provision-and-catalog.md)| 了解應用程式如何使用目錄資料庫連線至租用戶，以及目錄如何將租用戶對應至其資料。 |
|[監視及管理效能](saas-dbpertenant-performance-monitoring.md)| 了解如何使用 SQL Database 的監視功能，以及如何設定超過效能閾值時的警示。 |
|[透過 Log Analytics (OMS) 監視](saas-dbpertenant-log-analytics.md) | 了解如何使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 來監視跨多個集區的大量資源。 |
|[還原單一租用戶](saas-dbpertenant-restore-single-tenant.md)| 了解如何將租用戶資料庫還原到先前的時間點。 同時包含還原到平行資料庫 (讓現有的租用戶資料庫留在線上) 的步驟。 |
|[管理租用結構描述](saas-tenancy-schema-management.md)| 了解如何更新結構描述，以及更新跨所有 Wingtip SaaS 租用戶的參考資料。 |
|[執行臨機操作分析](saas-tenancy-adhoc-analytics.md) | 建立臨機操作分析資料庫並對所有租用戶執行即時分散式查詢。  |
|[執行租用戶分析](saas-tenancy-tenant-analytics.md) | 將租用戶資料擷取到分析資料庫或資料倉儲中，以便執行離線分析查詢。 |


## <a name="next-steps"></a>後續步驟

- [Azure SQL Database 多租用戶 SaaS 應用程式範例的指引和祕訣](saas-dbpertenant-wingtip-app-guidance-tips.md)

- [部署 Wingtip SaaS 應用程式](saas-dbpertenant-get-started-deploy.md)
