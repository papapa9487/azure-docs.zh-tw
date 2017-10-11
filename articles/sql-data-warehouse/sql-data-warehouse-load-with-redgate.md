---
title: "使用 Redgate 將資料載入至 Azure 資料倉儲 | Microsoft Docs"
description: "了解如何針對資料倉儲案例使用 Redgate 的資料平台 Studio。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="load-data-with-redgate-data-platform-studio"></a>使用 Redgate 資料平台 Studio 載入資料
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

本教學課程會示範如何使用 [Redgate 的資料平台 Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) 將資料從內部部署 SQL Server 移至 Azure SQL 資料倉儲。 資料平台 Studio 會套用最適當的相容性修正檔與最佳化，因此它是開始使用 SQL 資料倉儲最快的方式。

> [!NOTE]
> [Redgate](http://www.red-gate.com) 是長期的 Microsoft 合作夥伴，提供各種 SQL Server 工具。 在資料平台 Studio 中的這項功能已免費提供商業和非商業使用。
> 
> 

## <a name="before-you-begin"></a>開始之前
### <a name="create-or-identify-resources"></a>建立或識別資源
開始進行本教學課程之前，您需要有：

* **內部部署 SQL Server 資料庫**︰您要匯入至 SQL 資料倉儲的資料必須來自內部部署 SQL Server (2008R2 版或更新版本)。 資料平台 Studio 無法直接從 Azure SQL Database 或文字檔匯入資料。
* **Azure 儲存體帳戶**︰資料平台 Studio 在將資料載入 SQL 資料倉儲之前會設置 Azure Blob 儲存體中的資料。 儲存體帳戶必須使用「Resource Manager」部署模型 (預設值) 而非「傳統」部署模型。 如果您沒有儲存體帳戶，請參閱如何建立儲存體帳戶。 
* **SQL 資料倉儲**︰本教學課程中會將資料從內部部署 SQL Server 移動到 SQL 資料倉儲，因此您在線上需要有資料倉儲。 如果您還沒有資料倉儲，請了解如何建立 Azure SQL 資料倉儲。

> [!NOTE]
> 如果在相同區域中建立儲存體帳戶和資料倉儲，則會改進效能。
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>步驟 1︰使用您的 Azure 帳戶登入資料平台 Studio
開啟網頁瀏覽器並瀏覽至[資料平台 Studio](https://www.dataplatformstudio.com/) 網站。 使用您用來建立儲存體帳戶和資料倉儲的相同 Azure 帳戶登入。 如果您的電子郵件地址與工作或學校帳戶和 Microsoft 帳戶相關聯，請務必選擇擁有資源存取權的帳戶。

> [!NOTE]
> 如果這是您第一次使用資料平台 Studio，系統會要求您授與應用程式權限以管理您的 Azure 資源。
> 
> 

## <a name="step-2-start-the-import-wizard"></a>步驟 2︰開始匯入精靈
從 DPS 主畫面中，選取 [匯入至 Azure SQL 資料倉儲連結] 以啟動匯入精靈。

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>步驟 3︰安裝資料平台 Studio 閘道器
若要連線到您的內部部署 SQL Server 資料庫，您需要安裝 DPS 閘道器。 閘道器是用戶端代理程式，可提供您內部部署環境的存取權、擷取資料，並將它上傳至儲存體帳戶。 您的資料永遠不會通過 Redgate 的伺服器。 若要安裝閘道器：

1. 按一下 [建立閘道器] 連結
2. 使用所提供的安裝程式下載並安裝閘道器

![][2]

> [!NOTE]
> 可以在具有來源 SQL Server 資料庫網路存取的任何電腦上安裝閘道。 它會使用 Windows 驗證與目前使用者的認證存取 SQL Server 資料庫。
> 
> 

安裝之後，閘道器狀態會變更為 [已連接]，而您可以選取 [下一步]。

## <a name="step-4-identify-the-source-database"></a>步驟 4︰識別來源資料庫
在 [輸入伺服器名稱]文字方塊中，輸入裝載資料庫的伺服器名稱，然後選取 [下一步]。 然後，從下拉式功能表中，選取您想要匯入資料的來源資料庫。

![][3]

DPS 會檢查選取的資料庫以找到要匯入的資料表。 依預設，DPS 會匯入資料庫中的所有資料表。 您可以展開 [所有資料表] 連結來選取或取消選取資料表。 選取 [下一步] 按鈕以向前移動。

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>步驟 5︰選擇要將資料暫存的儲存體帳戶
DPS 會提示您要將資料暫存的位置。 從您的訂用帳戶選擇現有的儲存體帳戶，並選取 [下一步]。

> [!NOTE]
> DPS 會在選擇的儲存體帳戶中建立新的 blob 容器，並針對每個匯入使用不同的資料夾。
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>步驟 6：建立資料倉儲
接下來，選取要匯入資料的線上 [Azure SQL 資料倉儲](http://aka.ms/sqldw)資料庫。 一旦您已選取您的資料庫後，需要輸入認證以連接到資料庫，然後選取 [下一步]。

![][5]

> [!NOTE]
> DPS 會將來源資料的資料表合併到資料倉儲。 如果資料表名稱需要 DPS 以在資料倉儲中覆寫現有的資料表，則 DPS 會警告您。 您可以點選 [在匯入之前刪除所有現有的物件]，選擇性地在資料倉儲中刪除任何現有的物件。
> 
> 

## <a name="step-7-import-the-data"></a>步驟 7︰將資料匯入
DPS 會確認您想要匯入資料。 只要按一下 [開始匯入] 按鈕即可開始匯入資料。

![][6]

DPS 會顯示視覺效果，顯示從內部部署 SQL Server 擷取和上傳資料的進度，以及匯入 SQL 資料倉儲的進度。

![][7]

一旦匯入完成後，DPS 會顯示資料匯入的摘要，以及已執行之相容性修正的變更報告。

![][8]

## <a name="next-steps"></a>後續步驟
若要瀏覽您在 SQL 資料倉儲內的資料，請先檢視︰

* [查詢 Azure SQL 資料倉儲 (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [使用 Power BI 視覺化資料][Visualize data with Power BI]

若要深入了解 Redgate 的資料平台 Studio：

* [瀏覽 DPS 首頁](http://www.dataplatformstudio.com/)
* [在 Channel9 上觀看 DPS 的示範](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

如需在 SQL 資料倉儲中移轉及載入資料之其他方式的概觀，請參閱︰

* [將您的解決方案移轉至 SQL 資料倉儲][Migrate your solution to SQL Data Warehouse]
* [將資料載入 Azure SQL 資料倉儲](sql-data-warehouse-overview-load.md)

如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀](sql-data-warehouse-overview-develop.md)。

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
