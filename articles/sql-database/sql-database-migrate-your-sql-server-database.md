---
title: "將 SQL Server DB 移轉至 Azure SQL Database | Microsoft Docs"
description: "學習如何將 SQL Server Database 移轉至 Azure SQL Database。"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,load & move data
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/27/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 375d3ea0230e7d3fd0fc02ca7e0b8a7a76c24a27
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---

# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>將 SQL Server Database 移轉至 Azure SQL Database

將 SQL Server 資料庫移動到 Azure SQL Database 的程序需要三個步驟：先準備資料庫，接著匯出資料庫，然後匯入資料庫。 您會在本教學課程中學到：

> [!div class="checklist"]
> * 使用 [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 來準備 SQL Server 中的資料庫，以便將資料庫移轉至 Azure SQL Database
> * 將資料庫匯出至 BACPAC 檔案
> * 將 BACPAC 檔案匯入 Azure SQL Database

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定已完成下列必要條件：

- 已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 安裝 SSMS 時亦會安裝最新版的 SQLPackage，此命令列公用程式可用於自動化處理資料庫開發工作的範圍。 
- 已安裝 [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA)。
- 您已識別並可存取要移轉的資料庫。 本教學課程使用 SQL Server 2008R2 或更新版本執行個體上的 [SQL Server 2008R2 AdventureWorks OLTP 資料庫](https://msftdbprodsamples.codeplex.com/releases/view/59211)，不過您亦可使用任何選擇的資料庫。 若要修正相容性問題，請使用 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。

## <a name="prepare-for-migration"></a>為移轉做準備

您已準備好執行移轉。 遵循以下步驟，使用 **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** 評估資料庫對於移轉至 Azure SQL Database 的整備程度。

1. 開啟 **Data Migration Assistant**。 您可在連線至內含規劃移轉資料庫之 SQL Server 執行個體的任何電腦上執行 DMA，而無須在裝載 SQL Server 執行個體的電腦上將其安裝。

     ![開啟 data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. 在左側功能表中，按一下 [新增]以建立 [評估] 專案。 填寫表單的 [專案名稱] \(其他所有值應保留預設值)，然後按一下 [建立]。 此時會開啟 [選項]  頁面。

     ![新 data migration assistant 專案](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3. 在 [選項] 頁面上，按 [下一步]。 此時會開啟 [選取來源] 頁面。

     ![新資料移轉選項](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-options.png) 

4. 在 [選取來源] 頁面上，輸入內含規劃移轉伺服器的 SQL Server 執行個體名稱。 視需要變更此頁面上的其他值。 按一下 [ **連接**]。

     ![新資料移轉選取來源](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources.png)

5. 在 [選取來源] 頁面的 [新增來源] 部分，針對要測試相容性的資料庫選取核取方塊。 按一下 [新增] 。

     ![新資料移轉選取來源](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-sources-add.png)

6. 按一下 [開始評估]。

     ![新資料移轉開始評估](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-start-assessment.png)

7. 完成評估後，先查看資料庫是否充分相容可供執行移轉。 尋找以綠色圓圈標示的核取記號。

     ![新資料移轉評估結果相容](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-compatible.png)

8. 檢閱結果。 顯示的 **SQL Server 功能同位檢查**結果是要檢閱的預設結果。 特別是檢閱不支援和部分支援功能的相關資訊，以及關於建議動作的提供資訊。 

     ![新資料移轉評估同位檢查](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results-parity.png)

9. 按一下左上角的選項，以檢閱**相容性問題**。 特別是針對每個相容性層級，檢閱關於移轉封鎖程式、行為變更和已取代的功能等資訊。 針對 AdventureWorks2008R2 資料庫，檢閱自 SQL Server 2008 以來執行的全文檢索搜尋變更，以及自 SQL Server 2000 以來執行的 SERVERPROPERTY('LCID') 變更。 如需這些變更的詳細資訊，請參閱提供的詳細資訊連結。 全文檢索搜尋的許多選項和設定皆已變更 

   > [!IMPORTANT] 
   > 您將資料庫移轉至 Azure SQL Database 後，可選擇於目前的相容性層級 (針對 AdventureWorks2008R2 資料庫為等級 100) 或更高等級運作資料庫。 如需於特定相容性層級操作資料庫的含意與選項詳細資訊，請參閱 [ALTER DATABASE 相容性層級 (ALTER DATABASE Compatibility Level)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。 如需相容性層級其他相關資料庫等級設定的資訊，另請參閱 [ALTER DATABASE 範圍組態 (ALTER DATABASE SCOPED CONFIGURATION)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。
   >

10. 或是按一下 [Export report (匯出報告)]，以將報告另存為 JSON 檔案。
11. 關閉 Data Migration Assistant。

## <a name="export-to-bacpac-file"></a>匯出至 BACPAC 檔案 

BACPAC 檔案是一種副檔名為 BACPAC 的 ZIP 檔案，其包含來自 SQL Server Database 的中繼資料和資料。 BACPAC 檔案可以儲存在 Azure Blob 儲存體或是本機儲存體，以供封存或移轉用途 - 例如從 SQL Server 到 Azure SQL Database。 為維持匯出作業的交易一致性，您必須確保在執行匯出時未執行任何寫入活動。

遵循以下步驟，使用 SQLPackage 命令列公用程式將 AdventureWorks2008R2 資料庫匯出至本機儲存體。

1. 開啟 Windows 命令提示字元，並將目錄變更為具有 **130** 版本 SQLPackage 的資料夾 - 例如 **C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin**。

2. 在命令提示字元中執行下列 SQLPackage 命令，以將 **AdventureWorks2008R2** 資料庫從 **localhost** 匯出至 **AdventureWorks2008R2.bacpac**。 將這些值變更為適合您環境的值。

    ```SQLPackage
    sqlpackage.exe /Action:Export /ssn:localhost /sdn:AdventureWorks2008R2 /tf:AdventureWorks2008R2.bacpac
    ```

    ![sqlpackage 匯出](./media/sql-database-migrate-your-sql-server-database/sqlpackage-export.png)

執行完成後，產生的 BACPAC 檔案即會儲存於 sqlpackage 可執行檔的所在目錄。 在本範例中為 C:\Program Files (x86)\Microsoft SQL Server\130\DAC\bin。 

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。 從執行 SQLPackage 命令列公用程式的所在電腦登入，可使步驟 5 中的防火牆規則建立作業更加輕鬆。

## <a name="create-a-sql-server-logical-server"></a>建立 SQL Server 邏輯伺服器

[SQL Server 邏輯伺服器](sql-database-features.md)會作為多個資料庫的中央管理點。 遵循以下步驟建立 SQL Server 邏輯伺服器，以包含完成移轉的 Adventure Works OLTP SQL Server Database。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 在 [新增] 頁面的搜尋視窗中鍵入 **sql server**，然後從篩選清單中選取 [SQL Database (邏輯伺服器)]。

    ![選取邏輯伺服器](./media/sql-database-migrate-your-sql-server-database/logical-server.png)

3. 在 [所有項目] 頁面上按一下 [SQL Server (邏輯伺服器)]，然後在 [SQL Server (邏輯伺服器)] 頁面上按一下 [建立]。

    ![建立邏輯伺服器](./media/sql-database-migrate-your-sql-server-database/logical-server-create.png)

4. 使用下列資訊填寫 SQL Server (邏輯伺服器) 表單，如上圖所示︰     

   | 設定       | 建議的值 | 說明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **伺服器名稱** | 輸入任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 | 
   | **伺服器管理員登入** | 輸入任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)。 |
   | **密碼** | 輸入任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須包含下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
   | **訂用帳戶** | 選取一個訂用帳戶 | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | 選擇現有資源群組，或建立新群組，例如 **myResourceGroup**。 |  如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   | **位置** | 輸入新伺服器的任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

   ![建立邏輯伺服器完成表單](./media/sql-database-migrate-your-sql-server-database/logical-server-create-completed.png)

5. 按一下 [建立] 以佈建邏輯伺服器。 佈建需要幾分鐘的時間。 

> [!IMPORTANT]
> 請記住您的伺服器名稱、伺服器系統管理員登入名稱和密碼。 在本教學課程後續的內容中，您會需要這些值。
>

## <a name="create-a-server-level-firewall-rule"></a>建立伺服器層級防火牆規則

SQL Database 服務會[在伺服器層級建立防火牆](sql-database-firewall-configure.md)，防止外部應用程式和工具連線到伺服器或伺服器上的任何資料庫，除非建立防火牆規則以針對特定的 IP 位址開啟防火牆。 遵循以下步驟，針對您執行 SQLPackage 命令列公用程式所在電腦的 IP 位址，建立 SQL Database 伺服器層級防火牆規則。 這可讓 SQLPackage 透過 Azure SQL Database 防火牆連線至 SQL Server Database 邏輯伺服器。 

1. 在左側功能表中按一下 [所有資源]，然後在 [所有資源] 頁面按一下新伺服器。 伺服器的概觀頁面隨即開啟，並提供可進行進一步設定的選項。

     ![邏輯伺服器概觀](./media/sql-database-migrate-your-sql-server-database/logical-server-overview.png)

2. 在概覽頁面 [設定] 下方的左側功能表中，按一下 [防火牆]。 SQL Database 伺服器的 [防火牆設定] 頁面隨即開啟。 

3. 按一下工具列上的 [新增用戶端 IP]，以新增您目前所用電腦的 IP 位址，然後再按一下 [儲存]。 系統會為此 IP 位址建立伺服器層級防火牆規則。

     ![設定伺服器防火牆規則](./media/sql-database-migrate-your-sql-server-database/server-firewall-rule-set.png)

4. 按一下 [確定] 。

您現在可以運用 SQL Server Management Studio 或您選擇的其他工具，使用先前建立的伺服器管理帳戶從此 IP 位址連線至此伺服器上的所有資料庫。

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路進行連線，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非 IT 部門開啟連接埠 1433，否則您無法連線至 Azure SQL Database 伺服器。
>

## <a name="import-a-bacpac-file-to-azure-sql-database"></a>將 BACPAC 檔案匯入到 Azure SQL Database 

最新版本的 SQLPackage 命令列公用程式，支援於指定[服務層和效能等級](sql-database-service-tiers.md)建立 Azure SQL Databose。 為了在匯入時取得最佳效能，若服務層與效能等級高於您立即所需，請選取高服務層和效能等級，然後在完成匯入後相應減少。

遵循以下步驟，使用 SQLPackage 命令列公用程式將 AdventureWorks2008R2 資料庫匯入至 Azure SQL Database。 雖然您可以針對這項工作使用 SQL Server Management Studio，但是 SQLPackage 是大部分實際執行環境的慣用方法，可獲得最大彈性和最佳效能。 請參閱[使用 BACPAC 檔案從 SQL Server 移轉至 Azure SQL Database](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)。

- 在命令提示字元中執行下列 SQLPackage 命令，以將 **AdventureWorks2008R2** 資料庫從本機儲存體匯入至您先前建立到新資料庫、[進階] 服務層以及 P6 服務目標的 SQL Server 邏輯伺服器。 將角括號中的值取代為 SQL Server 邏輯伺服器的適當值，並指定新資料庫的名稱 (也會取代角括號)。 您也可以選擇將資料庫版本和服務目標的值變更成適合您的環境。 基於本教學課程的用途，已移轉的資料庫稱為 **myMigratedDatabase**。

    ```
    SqlPackage.exe /a:import /tcs:"Data Source=<your_server_name>.database.windows.net;Initial Catalog=<your_new_database_name>;User Id=<change_to_your_admin_user_account>;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
    ```

   ![sqlpackage 匯入](./media/sql-database-migrate-your-sql-server-database/sqlpackage-import.png)

> [!IMPORTANT]
> SQL Server 邏輯伺服器會接聽連接埠 1433。 如果您嘗試從公司防火牆連線至 SQL Server 邏輯伺服器，則必須在公司防火牆中開啟此連接埠，才能成功連線。
>

## <a name="connect-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 連線

使用 SQL Server Management Studio，建立對 Azure SQL Database 伺服器與新近已移轉資料庫 (在此教學課程中稱為 **myMigratedDatabase**) 的連線。 若您在執行 SQLPackage 的另一部所處電腦上執行 SQL Server Management Studio，請使用先前程序中說明的步驟，建立此電腦的防火牆規則。

1. 開啟 SQL Server Management Studio。

2. 在 [連接到伺服器] 對話方塊中，輸入下列資訊：
   - **伺服器類型**：指定資料庫引擎
   - **伺服器名稱**︰輸入您的完整伺服器名稱，例如 **mynewserver20170403.database.windows.net**
   - **驗證**：指定 SQL Server 驗證
   - **登入**︰輸入您的伺服器管理帳戶
   - **密碼**：輸入伺服器管理帳戶的密碼
 
    ![以 ssms 連線](./media/sql-database-migrate-your-sql-server-database/connect-ssms.png)

3. 按一下 [ **連接**]。 此時會開啟 [物件總管] 視窗。 

4. 在 [物件總管] 中，展開 [資料庫]，然後展開 [myMigratedDatabase] 以檢視範例資料庫中的物件。

## <a name="change-database-properties"></a>變更資料庫屬性

您可使用 SQL Server Management Studio 變更服務層、效能等級和相容性層級。 在匯入階段，建議您匯入至更高的效能層級資料庫以獲得最佳效能，但您可以在匯入完成之後相應減少該資料庫以節省成本，直到您準備好主動使用匯入的資料庫為止。 變更相容性層級可能會產生較佳的效能，並存取 Azure SQL Database 服務的最新功能。 當您移轉較舊的資料庫時，會在與所匯入資料庫相容的最低支援層級維護其資料庫相容性層級。 如需詳細資訊，請參閱[改善 Azure SQL Database 中相容性層級 130 的查詢效能](sql-database-compatibility-level-query-performance-130.md).

1. 在 [物件總管] 中，於 [myMigratedDatabase] 上按一下滑鼠右鍵，然後按一下 [新增查詢]。 此時會開啟已連線到您資料庫的查詢視窗。

2. 執行下列命令，將服務層設定為 [標準]，並將效能等級設定為 [S1]。

    ```
    ALTER DATABASE myMigratedDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

    ![變更服務層](./media/sql-database-migrate-your-sql-server-database/service-tier.png)

3. 執行下列命令，將資料庫相容性層級變更為 **130**。

    ```
    ALTER DATABASE myMigratedDatabase  
    SET COMPATIBILITY_LEVEL = 130;
    ```

   ![變更相容性層級](./media/sql-database-migrate-your-sql-server-database/compat-level.png)

## <a name="next-steps"></a>後續步驟 
在本教學課程中，您已準備、匯出和匯入資料庫。 您已了解如何︰

> [!div class="checklist"]
> * 準備 SQL Server 中的資料庫，以便將資料庫移轉至 Azure SQL Database
> * 將資料庫匯出至 BACPAC 檔案
> * 將 BACPAC 檔案匯入 Azure SQL Database

前進至下一個教學課程，以了解如何保護資料庫。

> [!div class="nextstepaction"]
> [保護 Azure SQL Database](sql-database-security-tutorial.md)。



