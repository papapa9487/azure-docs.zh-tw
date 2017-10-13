---
title: "Azure SQL Database 客戶實作 - 技術 | Microsoft Docs"
description: "了解客戶實作 Azure SQL Database 來解決商務問題的相關技術詳細資料"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: 74b04cb7737011423556cbd956ecef770ac82ad4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Azure SQL Database 客戶實作技術研究

- [Daxko](sql-database-implementation-daxko.md)：Daxko/CSI Software 面臨挑戰︰歸功於其全方位企業軟體解決方案的成功，其體適能和娛樂中心的客戶群快速成長，但是要跟上該不斷成長的客戶群的 IT 基礎結構需求則考驗著該公司的 IT 人員。 該公司逐漸受到增加的作業額外負荷限制，特別是在管理日益成長的資料庫方面。 更糟的是，這些作業額外負荷已佔用新計畫 (例如該公司軟體的新行動功能) 的部分開發資源。

- [GEP](sql-database-implementation-gep.md)：GEP 提供軟體和服務，可讓世界各地的採購領導者充分影響其業務營運、策略及財務表現。 除了諮詢和受管理的服務，該公司還提供 SMART by GEP® 這個雲端型的全方位採購軟體平台。 不過，GEP 在嘗試使用自己的內部部署資料中心來支援 SMART by GEP 時遇到一些限制︰所需的投資過高，而其他國家/地區的法規需求則令人在必要的投資上更為卻步。 透過移至雲端，GEP 釋出了 IT 資源，讓它可以節省 IT 營運上的心力，而更專注於為其全球客戶開發新的價值來源。

- [SnelStart](sql-database-implementation-snelstart.md)：SnelStart 為荷蘭的中小企業 (SMB) 撰寫常用的財務和商務管理軟體。 它的 55,000 個客戶是由其 110 個員工提供服務，其中包括 35 個 IT 人員。 藉由從桌面軟體移至 Azure 上的軟體即服務 (SaaS) 方案組建，SnelStart 不僅透過使用 C# 中的熟悉環境將管理自動化，還透過使用彈性集區避免過度佈建或佈建不足以將效能和延展性最佳化，來充分利用了內建服務。 使用 Azure 讓 SnelStart 能夠在內部部署環境與雲端之間靈活移動客戶。

- [Umbraco](sql-database-implementation-umbraco.md)：為了簡化客戶部署，Umbraco 新增了 Umbraco 即服務 (UaaS)：這是一個軟體即服務 (SaaS) 方案，此方案既不需要內部部署又提供內建的調整功能，而且還透過讓開發人員能夠專注於產品創新而非解決方案管理，免除了管理上的額外負荷。 Umbraco 藉由倚賴 Microsoft Azure 所提供的彈性平台即服務 (PaaS) 模型而得以提供所有這些優點。

- [仲裁](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)：仲裁會透過 SQL Database 讓重要資料庫的工作負載加倍，同時降低 70% 的 DTU。

- [任務](https://customers.microsoft.com/en-US/story/quest)：任務專注於一個目標，在 SQL Server Enterprise 服務上提供其焦點：為資料庫專業人員提供最佳工具，可維護資料安全、移動該資料並監視資料庫作業。 透過焦點，SQL Server 資料庫系統管理員可以使用 Microsoft Azure 和 Azure SQL Database 來進行監視、偵測、診斷，並提供解析 SQL Server 效能問題的方法，不論坐在辦公室或在家工作都能辦到。

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam)：為提供客戶完整的受管理軟體即服務 (SaaS) 供應項目，本簡短案例研究重點說明了最佳做法與課程，是取自 Dynamics 365 for Operations 產品小組移轉至 Azure SQL Database 的經驗。 透過 Azure SQL Database，Dynamics 365 for Operations 小組能夠明顯地以較少人員來管理及操作服務，並能以立即可用的管理功能 (例如，資料庫自動備份、資料庫備份保留、高可用性和災害復原等功能) 輕鬆擴充。 這搭配上透過簡單式自動化佈建資料庫的能力，促使 Azure SQL Database 成為能經得起大規模服務的絕佳平台。

- [Microsoft OneDrive 和 SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states)：這個簡短的案例研究訴說 Microsoft OneDrive 和 SharePoint Online 移動到 Azure SQL Database 背後的故事，並說明這項移轉如何做到幾乎無限制的彈性容量管理，同時也大幅降低作業成本和基礎結構的負荷。
