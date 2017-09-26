---
title: "將資料載入 Azure SQL 資料倉儲 – Data Factory | Microsoft Docs"
description: "本教學課程使用 Azure Data Factory 將資料載入 Azure SQL 資料倉儲，並使用 SQL Server 資料庫作為資料來源。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>使用 Data Factory 將資料載入 SQL 資料倉儲

您可以從任何[支援來源資料存放區](../data-factory/copy-activity-overview.md)，使用 Azure Data Factory 將資料載入至 Azure SQL 資料倉儲。 例如，您可以使用 Data Factory，將 Azure SQL Database 或 Oracle 資料庫中的資料載入 SQL 資料倉儲。 本文中的教學課程會示範如何從內部部署 SQL Server 資料庫將資料載入 SQL 資料倉儲。

**預估時間**︰一旦符合先決條件後，本教學課程需要大約 10-15 分鐘才能完成。

## <a name="prerequisites"></a>必要條件

- 您需要 **SQL Server 資料庫**，其中的資料表含有要複製到 SQL 資料倉儲的資料。  

- 您需要線上 **SQL 資料倉儲**。 如果您還沒有資料倉儲，請了解如何[建立 Azure SQL 資料倉儲](sql-data-warehouse-get-started-provision.md)。

- 您需要 **Azure 儲存體帳戶**。 如果您還沒有儲存體帳戶，請了解如何[建立儲存體帳戶](../storage/common/storage-create-storage-account.md)。 為了達到最佳效能，在相同的 Azure 區域中找出儲存體帳戶和資料倉儲。

## <a name="configure-a-data-factory"></a>設定 Data Factory
1. 登入 [Azure 入口網站][]。
2. 找出您的資料倉儲，並按一下以開啟它。
3. 在主要刀鋒視窗中，按一下 [載入資料] > [Azure Data Factory]。

    ![啟動載入資料精靈](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. 如果您在 Azure 訂用帳戶中沒有 Data Factory，您會在瀏覽器的個別索引標籤中看到 [新增 Data Factory]對話方塊。 填入必要的資訊，然後按一下 [建立]。 建立 Data Factory 之後，[新增 Data Factory] 對話方塊隨即關閉，而且您會看到 [選取 Data Factory] 對話方塊。

    如果您在 Azure 訂用帳戶中已有一或多個 Data Factory，您會看到[選取 Data Factory] 對話方塊。 在這個對話方塊中，您可以選取現有的 Data Factory，或按一下[建立新的 Data Factory] 來建立新的。

    ![設定 Data Factory](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. 在 [選取 Data Factory] 對話方塊中，預設會選取 [載入資料] 選項。 按 [下一步] 以開始建立資料載入工作。

## <a name="configure-the-data-factory-properties"></a>設定 Data Factory 屬性
現在您已建立 Data Factory，下一個步驟是設定資料載入排程。

1. 針對 [工作名稱]，輸入 [DWLoadData fromSQLServer]。
2. 使用預設的 [立即執行一次] 選項，然後按 [下一步]。

    ![設定負載排程](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>設定來源資料存放區和閘道器
現在您要通知 Data Factory 關於您要載入資料的內部部署 SQL Server 資料庫。

1. 從支援的來源資料儲存類別目錄選擇 **SQL Server**，並按 [下一步]。

    ![選擇 SQL Server 來源](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. 隨即出現 [指定內部部署 SQL Server 資料庫] 對話方塊。 第一個 [連接名稱] 欄位會自動填入。 第二個欄位會詢問 [閘道器] 的名稱。 如果您使用的現有 Data Factory 已有一個閘道，您可以藉由從下拉式清單中選取它來重複使用閘道。 按一下 [建立閘道] 連結以建立資料管理閘道。  

    > [!NOTE]
    > 如果來源資料存放區是在內部部署或在 Azure IaaS 虛擬機器中，資料管理閘道是必要的。 閘道與 Data Factory 有 1 對 1 關聯性。 它不能從另一個 Data Factory 使用，但可供相同 Data Factory 中的多個資料載入工作使用。 閘道可以用來在執行資料載入工作時連接至多個資料存放區。
    >
    > 如需閘道的詳細資訊，請參閱[資料管理閘道](../data-factory/v1/data-factory-data-management-gateway.md)文章。

3. 隨即出現 [建立閘道] 對話方塊。 針對名稱，輸入 **GatewayForDWLoading**，然後按一下 [建立]。

4. 隨即出現 [設定閘道] 對話方塊。 按一下 [在這台電腦上啟動快速安裝]，在目前電腦上自動下載、安裝及註冊資料管理閘道。 進度會顯示在快顯視窗中。 如果電腦無法連線到資料存放區，您可以手動在電腦上[下載並安裝閘道](https://www.microsoft.com/download/details.aspx?id=39717)，可以連接到資料存放區，然後使用金鑰進行註冊。
    > [!NOTE]
    > 快速安裝適用於原生 Microsoft Edge 及 Internet Explorer。 如果您是使用 Google Chrome，先從 Chrome 線上應用程式商店安裝 ClickOnce 擴充功能。

    ![啟動快速安裝](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. 等候閘道安裝程式完成。 一旦閘道成功註冊且上線後，快顯視窗會關閉，且新的閘道會出現在 [閘道] 欄位中。 接著，填寫其餘必要欄位，如下所示，然後按 [下一步]。
    - **伺服器名稱**︰內部部署 SQL Server 的名稱。
    - **資料庫名稱**：SQL Server 資料庫。
    - **認證加密**：使用預設的「透過網頁瀏覽器」。
    - **驗證類型**︰選擇您所使用的驗證類型。
    - **使用者名稱**和**密碼**︰輸入擁有複製資料權限之使用者的使用者名稱和密碼。

    ![啟動快速安裝](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. 下一步是選擇要從中複製資料的資料表。 您可以使用關鍵字篩選資料表。 然後您可以預覽底部面板中的資料和資料表結構描述。 在完成您的選擇之後，按 [下一步]。

    ![選取資料表](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>設定目的地，您的 SQL 資料倉儲

現在，您需要提供目的地資訊給 Data Factory。

1. 您的 SQL 資料倉儲連接資訊會自動填入。 輸入使用者名稱的密碼。 然後按 [下一步]。

    ![設定目的地](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. 出現智慧型資料表對應，它會根據資料表名稱將來源對應至目的地資料表。 如果資料表不存在於目的地，依預設，ADF 會建立一個同名的資料表 (這適用於以 SQL Server 或 Azure SQL 資料庫作為來源的情形)。 您也可以選擇對應至現有的資料表。 檢閱並按 [下一步]。

    ![對應資料表](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. 檢閱結構描述對應，並尋找錯誤或警告訊息。 智慧型對應是根據資料行名稱。 如果在來源和目的地資料行之間有不支援的資料類型轉換，您會看到一則錯誤訊息與對應的資料表。 如果您選擇讓 Data Factory 自動建立資料表，則必要時會進行適當的資料類型轉換，以修正來源和目的地存放區之間的不相容性。

    ![對應結構描述](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. 按一下 [下一步] 。

## <a name="configure-the-performance-settings"></a>設定效能設定
在 [效能] 設定中，您可以設定 Azure 儲存體帳戶，在資料載入至 SQL 資料倉儲之前用來暫存資料 (使用 [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly) 會有較高的效能)。 複製完成之後，將會自動清除儲存體中的暫時資料。

選取現有的 Azure 儲存體帳戶，然後按一下 [下一步]。

![設定預備 blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>檢閱摘要資訊和部署管線

檢閱設定，然後按一下 [完成] 按鈕部署管線。

![部署 Data Factory](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>監視資料載入進度

您可以在 [部署] 頁面查看部署進度和結果。

1. 部署完成之後，按一下 [按一下這裡以監視複製管線] 連結，以監視資料載入進度。

    ![監視管線](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. 自動會從左邊的 [資源總管] 選取新建立的 **DWLoadData fromSQLServer** 資料載入管線。

    ![檢視管線](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. 按一下中間窗格中的管線，以查看對應至活動之每個資料表的詳細狀態。

    ![檢視資料表活動](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. 進一步按一下進入活動，您會在右窗格中看到資料載入詳細資料，包括資料大小、資料列、輸送量等。

    ![檢視資料表活動詳細資料](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. 若要啟動此更新，請移至您的 SQL 資料倉儲，按一下 [資料載入] > [Azure Data Factory]，選取您的 Factory，然後選擇 [監視現有的載入工作]。

## <a name="next-steps"></a>後續步驟

若要將資料庫移轉至 SQL 資料倉儲，請參閱[移轉概觀](sql-data-warehouse-overview-migrate.md)。

若要深入了解 Azure Data Factory 和其資料移動功能，請參閱下列文章︰

- [Azure Data Factory 簡介](../data-factory/introduction.md)
- [使用複製活動來移動資料](../data-factory/copy-activity-overview.md)
- [使用 Azure Data Factory 從 Azure SQL 資料倉儲來回移動資料](../data-factory/connector-azure-sql-data-warehouse.md)

若要瀏覽 SQL 資料倉儲中的資料，請參閱下列文章︰

- [使用 Visual Studio 和 SSDT 連接到 SQL 資料倉儲](sql-data-warehouse-query-visual-studio.md)
- [採用 Power BI 的視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

<!-- Azure references -->
[Azure 入口網站]: https://portal.azure.com

