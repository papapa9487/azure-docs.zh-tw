---
title: "開始使用 Azure SQL 資料同步 (預覽) | Microsoft Docs"
description: "本教學課程可協助您開始使用 Azure SQL 資料同步 (預覽)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: ddcf6868a0fca88a52774e20623d25de31c063bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="get-started-with-azure-sql-data-sync-preview"></a>開始使用 Azure SQL 資料同步 (預覽)
在本教學課程中，您將了解如何使用包含 Azure SQL Database 和 SQL Server 執行個體的混合式同步群組設定 Azure SQL 資料同步。 新的同步處理群組會依照您設定的排程完整設定和同步。

本教學課程假設您先前至少有一些使用 SQL Database 和 SQL Server 的經驗。 

如需 SQL 資料同步的概觀，請參閱[使用 Azure SQL 資料同步 (預覽)，跨多個雲端和內部部署資料庫同步處理資料](sql-database-sync-data.md)。

如需示範如何設定 SQL 資料同步的完整 PowerShell 範例，請參閱下列文章：
-   [使用 PowerShell 在多個 Azure SQL Database 之間進行同步處理](scripts/sql-database-sync-data-between-sql-databases.md)
-   [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>步驟 1 - 建立同步群組

### <a name="locate-the-data-sync-settings"></a>尋找資料同步設定

1.  在瀏覽器中導覽至 Azure 入口網站。

2.  在入口網站中，從您的儀表板或從工具列上的 SQL Database 圖示找出 SQL 資料庫。

    ![Azure SQL 資料庫清單](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  在 [SQL 資料庫] 頁面上，選取您想要作為資料同步的中樞資料庫使用的現有 SQL 資料庫。[SQL 資料庫] 頁面隨即開啟。

4.  在所選取資料庫的 [SQL 資料庫] 頁面上，選取 [同步至其他資料庫]。 [資料同步] 頁面隨即開啟。

    ![[同步至其他資料庫] 選項](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>建立新的同步群組

1.  在 [資料同步] 頁面上，選取 [新的同步群組]。 [新的同步群組] 頁面隨即開啟，且系統會反白顯示步驟 1：[建立同步群組]。 [建立資料同步群組] 頁面同時也會開啟。

2.  在 [建立資料同步群組] 頁面上，執行下列步驟：

    1.  在 [同步群組名稱] 欄位中，輸入新同步群組的名稱。

    2.  在 [同步中繼資料的資料庫] 區段中，選擇要建立新的資料庫 (建議) 或使用現有的資料庫。

        > [!NOTE]
        > Microsoft 建議您建立新的空白資料庫作為同步中繼資料的資料庫。 資料同步會在此資料庫中建立資料表，並頻繁執行工作負載。 這個資料庫會作為同步中繼資料的資料庫，自動和選取區域中所有同步群組共用。 您無法在不卸除同步中繼資料資料庫或其名稱的情況下變更它。

        如果您選擇 [新資料庫]，請選取 [建立新的資料庫]。 [SQL Database] 頁面隨即開啟。 在 [SQL Database] 頁面上，命名並設定新的資料庫。 然後選取 [確定]。

        如果您選擇 [現有的資料庫]，請從清單中選取資料庫。

    3.  在 [自動同步] 區段中，先選取 [開啟] 或 [關閉]。

        如果您選擇 [開啟]，請在 [同步處理頻率] 區段中輸入數字，然後選取 [秒]、[分鐘]、[小時] 或 [天]。

        ![指定同步處理頻率](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  在 [衝突解決] 區段中，選取 [中樞獲勝] 或 [成員獲勝]。

        ![指定解決衝突的方式](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  選取 [確定] 並等候新的同步群組建立和部署完成。

## <a name="step-2---add-sync-members"></a>步驟 2 - 新增同步成員

在建立和部署新的同步群組之後，系統會反白顯示 [新的同步群組] 頁面中的步驟 2：[新增同步成員]。

在 [中樞資料庫] 區段中，輸入中樞資料庫所在 SQL Database 伺服器的現有認證。 請不要在此區段輸入「新」的認證。

![中樞資料庫已新增至同步群組](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>新增 Azure SQL Database

在 [成員資料庫] 區段中，選取 [新增 Azure 資料庫]，可選擇性地將 Azure SQL Database 新增至同步群組。 [設定 Azure 資料庫] 頁面隨即開啟。

在 [設定 Azure 資料庫] 頁面上，執行下列步驟：

1.  在 [同步成員名稱] 欄位中，提供新同步成員的名稱。 這個名稱與資料庫本身的名稱不同。

2.  在 [訂用帳戶] 欄位中，選取相關聯的 Azure 訂用帳戶以便計費。

3.  在 [Azure SQL Server] 欄位中，選取現有的 SQL 資料庫伺服器。

4.  在 [Azure SQL Database] 欄位中，選取現有的 SQL 資料庫。

5.  在 [同步方向] 欄位中，選取 [雙向同步]、[至中樞] 或 [來自中樞]。

    ![新增 SQL Database 同步處理成員](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  在 [使用者名稱] 和 [密碼] 欄位中，輸入成員資料庫所在 SQL Database 伺服器的現有認證。 請不要在此區段輸入「新」的認證。

7.  選取 [確定] 並等候新的同步成員建立和部署完成。

    ![已新增 SQL Database 同步處理成員](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-an-on-premises-sql-server-database"></a>新增內部部署 SQL Server 資料庫

在 [成員資料庫] 區段中，選取 [新增內部部署資料庫]，可選擇性地將內部部署 SQL Server 新增至同步群組。 [設定內部部署] 頁面隨即開啟。

在 [設定內部部署] 頁面上，執行下列步驟：

1.  選取 [選擇同步代理程式閘道]。 [選取同步代理程式] 頁面隨即開啟。

    ![選擇同步代理程式閘道](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  在 [選擇同步代理程式閘道] 頁面上，選擇要使用現有的代理程式，或建立新的代理程式。

    如果您選擇 [現有代理程式]，請從清單中選取現有的代理程式。

    在 [建立新的代理程式] 刀鋒視窗中，請執行下列步驟：

    1.  從提供的連結下載用戶端同步代理程式軟體，並安裝在 SQL Server 所在的電腦上。
 
        > [!IMPORTANT]
        > 您必須在防火牆開啟輸出 TCP 連接埠 1433，以讓用戶端代理程式和伺服器通訊。


    2.  輸入代理程式的名稱。

    3.  選取 [建立並產生金鑰]。

    4.  將代理程式金鑰複製到剪貼簿。
        
        ![建立新同步代理程式](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  選取 [確定] 以關閉 [選取同步代理程式] 頁面。

    6.  在 SQL Server 電腦上，找出並執行用戶端同步代理程式應用程式。

        ![資料同步用戶端代理程式應用程式](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  在同步處理代理程式應用程式中，選取 [提交代理程式金鑰]。 [同步中繼資料的資料庫組態] 對話方塊隨即開啟。

    8.  在 [同步中繼資料的資料庫組態] 對話方塊中，貼上從 Azure 入口網站複製的代理程式金鑰。 還要提供輸入中繼資料資料庫所在 Azure SQL Database 伺服器的現有認證。 (如果您已建立新的中繼資料資料庫，此資料庫會位在和中樞資料庫相同的伺服器)。選取 [確定] 並等待完成組態。

        ![輸入代理程式金鑰和伺服器認證](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   如果在此時收到防火牆錯誤，您必須在 Azure 上建立防火牆規則，以允許來自 SQL Server 電腦的傳入流量。 您可以在入口網站手動建立規則，但在 SQL Server Management Studio (SSMS) 中建立可能會更容易。 在 SSMS 中，嘗試連線到 Azure 上的中樞資料庫。 請將其名稱輸入為 \<hub_database_name\>.database.windows.net。 若要設定 Azure 防火牆規則，請按照對話方塊中的步驟操作。 然後返回用戶端同步代理程式應用程式。

    9.  在用戶端同步代理程式應用程式中，按一下 [註冊]，以向代理程式註冊 SQL Server 資料庫。 [SQL Server 組態] 對話方塊隨即開啟。

        ![新增和設定 SQL Server 資料庫](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. 在 [SQL Server 組態] 對話方塊方塊中，選擇要使用 SQL Server 驗證或 Windows 驗證來連線。 如果您選擇 SQL Server 驗證，請輸入現有的認證。 提供 SQL Server 名稱和您要同步之資料庫的名稱。選取 [測試連接] 來測試您的設定。 然後選取 [儲存]。 已註冊的資料庫會出現在清單中。

        ![現在已註冊 SQL Server 資料庫](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. 您現在可以關閉用戶端同步代理程式應用程式。

    12. 在入口網站中的 [設定內部部署] 頁面上，選取 [選取資料庫]。 [選取資料庫] 頁面隨即開啟。

    13. 在 [選取資料庫] 頁面上的 [同步成員名稱] 欄位中，提供新同步成員的名稱。 這個名稱與資料庫本身的名稱不同。 從清單中選取資料庫。 在 [同步方向] 欄位中，選取 [雙向同步]、[至中樞] 或 [來自中樞]。

        ![選取內部部署資料庫](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. 選取 [確定] 以關閉 [選取資料庫] 頁面。 然後選取 [確定] 以關閉 [設定內部部署] 頁面，並等候系統建立並部署新的同步成員。 最後，按一下 [確定] 以關閉 [選取同步成員] 頁面。

        ![內部部署資料庫已新增至同步群組](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  若要連接到 [SQL 資料同步] 和本機代理程式，請將您的使用者名稱新增至 `DataSync_Executor` 角色。 資料同步會在 SQL Server 執行個體上建立此角色。

## <a name="step-3---configure-sync-group"></a>步驟 3 - 設定同步群組

在建立並部署新的同步群組成員之後，系統會反白顯示 [新的同步群組] 頁面中的步驟 3：[設定同步群組]。

1.  在 [資料表] 頁面上，從同步群組成員清單中選取資料庫，然後選取 [重新整理結構描述]。

2.  從可用資料表清單中，選取您想要同步的資料表。

    ![選取要同步的資料表](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  預設會選取資料表中的所有資料行。 如果您不想同步所有資料行，請取消選取您不想要同步的資料行核取方塊。請務必保留選取的主索引鍵資料行。

    ![選取要同步的欄位](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  最後，選取 [儲存]。

## <a name="next-steps"></a>後續步驟
恭喜！ 您已建立一個同時包含 SQL Database 執行個體與 SQL Server 資料庫的同步群組。

如需 SQL 資料同步的詳細資訊，請參閱：

-   [使用 Azure SQL 資料同步，跨多個雲端和內部部署資料庫同步處理資料](sql-database-sync-data.md)
-   [Azure SQL 資料同步最佳做法](sql-database-best-practices-data-sync.md)
-   [對 Azure SQL 資料同步的問題進行疑難排解](sql-database-troubleshoot-data-sync.md)

-   示範如何設定 SQL 資料同步的完整 PowerShell 範例：
    -   [使用 PowerShell 在多個 Azure SQL Database 之間進行同步處理](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 設定「資料同步」在內部部署的 Azure SQL Database 和 SQL Server 之間進行同步處理](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下載 SQL 資料同步 REST API 文件](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

如需 SQL Database 的詳細資訊，請參閱：

-   [SQL Database 概觀](sql-database-technical-overview.md)
-   [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
