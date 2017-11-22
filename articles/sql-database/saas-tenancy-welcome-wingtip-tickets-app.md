---
title: "歡迎使用 Wingtips 應用程式 - Azure SQL Database | Microsoft Docs"
description: "了解雲端環境中 Azure SQL Database 的資料庫租用模型和相關範例 Wingtips SaaS 應用程式。"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>歡迎使用 Wingtip Tickets 範例 SaaS Azure SQL Database 租用應用程式

歡迎使用 Wingtip Tickets 範例 SaaS Azure SQL Database 租用應用程式與其教學課程。 資料庫租用是指您的應用程式會提供給付費客戶以裝載於該應用程式中的資料隔離模式。 暫時以最簡化的方式來說，就是每個用戶端本身都有一整個資料庫，或是和其他用戶端共用資料庫。

## <a name="wingtip-tickets-app"></a>Wingtip Tickets 應用程式

Wingtip Tickets 範例應用程式會示範不同的資料庫租用模型在設計和管理多租用戶 SaaS 應用程式的效果。 隨附的教學課程直接描述這些相同效果。 Wingtip Tickets 建置在 Azure SQL Database 上。

Wingtip Tickets 的設計旨在處理實際 SaaS 用戶端使用的各種不同設計和管理案例。 Wingtip Tickets 中考量到所會出現的使用模式。

您可以在五分鐘內於自己的 Azure 訂用帳戶中安裝 Wingtip Tickets 應用程式。 安裝包括插入數個租用戶的範例資料。 因為安裝之間不會彼此互動或干擾，所以您可以安全地安裝應用程式和所有模式的管理指令碼。

#### <a name="code-in-github"></a>GitHub 中程式碼

應用程式程式碼和管理指令碼全都可在 GitHub 上取得：

- **獨立應用程式**模式：[WingtipTicketsSaaS-StandaloneApp 儲存機制](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- **每一租用戶一個資料庫**模型：[WingtipTicketsSaaS-DbPerTenant 儲存機制](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)。
- **標準多租用戶**模型：[WingtipTicketsSaaS-MultiTenantDB 儲存機制](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB)。

Wingtip Tickets 應用程式的同一個程式碼基底可以重複用於上述列出的所有模型。 您可以使用 Github 中的程式碼來開始自己的 SaaS 專案。



## <a name="major-database-tenancy-models"></a>主要資料庫租用模型

Wingtip Tickets 是一個場地活動列表與售票 SaaS 應用程式。 Wingtip 可提供場地所需的服務。 每個場地都適用下列所有項目：

- 付費以裝載於您的應用程式。
- 是 Wingtip 的租用戶。
- 裝載事件。 包括下列事件：
    - 票證價格。
    - 票證銷售額。
    - 購買票證的客戶。

該應用程式搭配使用管理指令碼和教學課程示範一個完整的 SaaS 案例。 該案例包含下列活動：

- 佈建租用戶。
- 監視和管理效能。
- 結構描述管理。
- 跨租用戶報告和分析。

不論所需的規格為何，都能提供所有活動。



## <a name="code-samples-for-each-tenancy-model"></a>每個租用模型的程式碼範例

強調說明一組應用程式模型。 然而，其他實作可混合兩個或更多模型的元素。

#### <a name="standalone-app-model"></a>獨立應用程式模型

![獨立應用程式模型][standalone-app-model-62s]

此模型使用單一租用戶應用程式。 因此，此模型只需要一個資料庫，而且只儲存單一租用戶的資料。 租用戶會和資料庫中的其他租用戶完全隔離。

當您針對許多不同用戶端銷售自己的應用程式執行個體時，可以使用此模型，讓每個用戶端自行執行。 該用戶端就是唯一的租用戶。 資料庫不只會儲存唯一用戶端的資料，該資料庫還會儲存該用戶端的許多客戶資料。

#### <a name="database-per-tenant"></a>每一租用戶一個資料庫

![每一租用戶一個資料庫模型][database-per-tenant-model-35d]

此模型在應用程式的執行個體中會有多個租用戶。 但對於每個新租用戶，都會配置另一個資料庫專供新的租用戶使用。

此模型可完全隔離每個租用戶的資料庫。 Azure SQL Database 服務精密複雜，讓此模型變得可行。

- [SQL Database 多租用戶 SaaS 應用程式範例的簡介][saas-dbpertenant-wingtip-app-overview-15d]：包含此模型的詳細資訊。

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>標準的多租用戶資料庫 (混合式)

![標準的多租用戶資料庫模型 (混合式)][sharded-multitenantdb-model-hybrid-79m]

此模型在應用程式的執行個體中會有多個租用戶。 這個模型在部分或所有其資料庫中也有多個租用戶。 此模型很適合用來提供不同服務層級，讓重視完全隔離資料庫的用戶端支付更多費用。

每個資料庫的結構描述都包含一個租用戶識別碼。 即使只儲存唯一租用戶的那些資料庫都有租用戶識別碼。

- [SQL Database 多租用戶 SaaS 應用程式範例的簡介][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>每個租用模型的教學課程

每個租用模型均記載下列各項：

- 一套教學課程文章。
- 儲存在該模式專用 Github 存放庫中的原始程式碼：
    - Wingtip Tickets 應用程式的程式碼。
    - 管理案例的指令碼。

#### <a name="tutorials-for-management-scenarios"></a>管理案例的教學課程

每個模型的教學課程文章都涵蓋下列管理案例：

- 佈建租用戶。
- 監視和管理效能。
- 結構描述管理。
- 跨租用戶報告和分析。
- 將一個租用戶還原到較早的時間點。
- 災害復原。



## <a name="next-steps"></a>後續步驟

- [SQL Database 多租用戶 SaaS 應用程式範例的簡介][saas-dbpertenant-wingtip-app-overview-15d]：包含此模型的詳細資訊。

- [多租用戶 SaaS 資料庫租用模式][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "獨立應用程式模型"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "每一租用戶一個資料庫模型"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "標準的多租用戶資料庫模型 (混合式)"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


