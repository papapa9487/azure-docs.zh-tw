---
title: "使用 Azure 資料庫移轉服務將 SQL Server 移轉到 Azure SQL Database | Microsoft Docs"
description: "了解如何使用 Azure 資料庫移轉服務，從內部部署 SQL Server 移轉到 Azure SQL。"
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/17/2017
ms.openlocfilehash: 3938af29caec99f076452529cbc5d93cf2c8802b
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>將 SQL Server 移轉到 Azure SQL Database
您可以使用 Azure 資料庫移轉服務，將內部部署 SQL Server 執行個體的資料庫移轉到 Azure SQL Database。 在本教學課程中，您要使用 Azure 資料庫移轉服務，將已還原至內部部署 SQL Server 2016 (或更新版本) 執行個體的 **Adventureworks2012** 資料庫移轉到 Azure SQL Database。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 使用 Data Migration Assistant 評估您的內部部署資料庫。
> * 使用 Data Migration Assistant 移轉範例結構描述。
> * 建立 Azure 資料庫移轉服務的執行個體。
> * 使用 Azure 資料庫移轉服務來建立移轉專案。
> * 執行移轉。
> * 監視移轉。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要：

- 下載及安裝 [SQL Server 2016 或更新版本](https://www.microsoft.com/sql-server/sql-server-downloads) (任何版本)。
- 啟用 TCP/IP 通訊協定，在 SQL Server Express 安裝期間預設會停用，方法是遵循[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的指示。
- 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 您可以依照[在 Azure 入口網站中建立 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) 一文中的詳細資料來建立 Azure SQL Database 執行個體。
- 下載及安裝[資料移轉小幫手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更新版本。
- 使用 Azure Resource Manager 部署模型來建立 Azure 資料庫移轉服務的 VNET，其使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 提供站對站連線能力給您的內部部署來源伺服器。
- 確定用來連線至來源 SQL Server 執行個體的認證具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 權限。
- 確定用來連線至目標 Azure SQL DB 執行個體的認證，在目標 Azure SQL 資料庫上具有 CONTROL DATABASE 權限。
- 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server。

## <a name="assess-your-on-premises-database"></a>評估您的內部部署資料庫
將資料從內部部署 SQL Server 執行個體移轉到 Azure SQL Database 之前，您必須評估 SQL Server 資料庫是否有任何可能會阻礙移轉的問題。 在資料移轉小幫手 v3.3 或更新版本中，遵循[執行 SQL Server 移轉評估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所描述的步驟，完成內部部署資料庫評估。 所需的步驟摘要如下：
1.  在 Data Migration Assistant 中，選取 [New] \(新增\) (+) 圖示，然後選取 [Assessment] \(評估\) 專案類型。
2.  在 [Source server type] \(來源伺服器類型\) 文字方塊中指定專案名稱，選取 [SQL Server]，然後在 [Target server type] \(目標伺服器類型\) 文字方塊中，選取 [Azure SQL Database]。
3.  選取 [Create] \(建立\) 以建立專案。

    當您要評估來源 SQL Server 資料庫移轉到 Azure SQL Database 時，可以選擇下列其中一種或兩種評估報告類型：
    - 檢查資料庫相容性
    - 檢查功能同位

    預設會選取這兩種報告類型。
4.  在 Data Migration Assistant 的 [Options] \(選項\) 畫面上，選取 [Next] \(下一步\)。
5.  在 [Select sources] \(選取來源\) 畫面的 [Connect to a server] \(連線到伺服器\) 對話方塊中，提供您 SQL Server 的連線詳細資料，然後選取 [Connect] \(連線\)。
6.  在 [新增資源] 對話方塊中，依序選取 [AdventureWorks2012]、[新增]，然後選取 [開始評估]。

    評估完成時，結果會如下圖所示：

    ![評估資料移轉](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    對於 Azure SQL Database，評估會識別阻礙移轉的問題，和功能同位問題。

7.  選取特定的選項，檢閱評估結果是否有阻礙移轉的問題和功能同位問題。
    - **SQL Server 功能同位**類別提供一組完整的建議、Azure 中可使用的替代方法以及補救步驟，協助您規劃移轉專案所需的時間和精力。
    - **相容性問題**類別則識別反映出相容性問題的部分支援或不支援功能，這些相容性問題可能會阻礙將內部部署 SQL Server 資料庫移轉到 Azure SQL Database。 同時也提供協助您解決這些問題的建議。


## <a name="migrate-the-sample-schema"></a>移轉範例結構描述
當您滿意評估結果，而且選取的資料庫也適合移轉到 Azure SQL Database 之後，請使用 Data Migration Assistant 將結構描述移轉到 Azure SQL Database。

> [!NOTE]
> 在 Data Migration Assistant 中建立移轉專案之前，請務必先確認您已經如必要條件中所述佈建 Azure SQL Database。 基於本教學課程的需要，Azure SQL Database 的名稱會假設為 **AdventureWorksAzure**，但您可以命名為不同的名稱。

若要將 **AdventureWorks2012** 結構描述移轉到 Azure SQL Database，請執行下列步驟：

1.  在資料移轉小幫手中，選取新增 (+) 圖示，然後在 [專案類型] 底下選取 [移轉]。
3.  在 [Source server type] \(來源伺服器類型\) 文字方塊中指定專案名稱，選取 [SQL Server]，然後在 [Target server type] \(目標伺服器類型\) 文字方塊中，選取 [Azure SQL Database]。
4.  在 [Migration Scope] \(移轉範圍\) 下，選取 [Schema only] \(僅結構描述\)。

    執行先前的步驟之後，Data Migration Assistant 介面應該如下圖所示：
    
    ![建立 Data Migration Assistant 專案](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  選取 [Create] \(建立\) 以建立專案。
6.  在 Data Migration Assistant 中，為您的 SQL Server 指定來源連線詳細資料，選取 [Connect] \(連線\)，然後選取 [AdventureWorks2012] 資料庫。

    ![Data Migration Assistant 來源連線詳細資料](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  在 [連線到目標伺服器] 下選取 [下一步]，指定 Azure SQL Database 的目標連線詳細資料、選取 [連線]，然後選取您已在 Azure SQL Database 中預先佈建的 [AdventureWorksAzure] 資料庫。

    ![Data Migration Assistant 目標連線詳細資料](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  選取 [Next] \(下一步\) 前進到 [Select objects] \(選取物件\) 畫面，您可以指定 **AdventureWorks2012** 資料庫中需要部署至 Azure SQL Database 的結構描述物件。

    預設會選取所有物件。

    ![產生 SQL 指令碼](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  選取 [Generate SQL script] \(產生 SQL 指令碼\) 來建立 SQL 指令碼，然後檢閱指令碼是否有任何錯誤。

    ![結構描述指令碼](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. 選取 [Deploy schema] \(部署結構描述\) 以將結構描述部署至 Azure SQL Database，並在結構描述部署好之後，檢查目標伺服器是否有任何異常狀況。

    ![部署結構描述](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者
1. 登入 Azure 入口網站，選取 [所有服務]，然後選取 [訂用帳戶]。
 
   ![顯示入口網站訂用帳戶](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]。
 
    ![顯示資源提供者](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  搜尋移轉，然後在 [Microsoft.DataMigration] 的右邊，選取 [註冊]。
 
    ![註冊資源提供者](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>建立執行個體
1.  在 Azure 入口網站中，選取 [+ 建立資源]，搜尋 Azure 資料庫移轉服務，然後從下拉式清單選取 [Azure 資料庫移轉服務]。

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  在 [Azure 資料庫移轉服務 (預覽)] 畫面上，選取 [建立]。
 
    ![建立 Azure 資料庫移轉服務執行個體](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  在 [資料庫移轉服務] 畫面上，指定服務的名稱、訂用帳戶、虛擬網路和定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

     ![設定 Azure 資料庫移轉服務執行個體設定](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  選取 [建立] 以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案
服務建立好之後，請在 Azure 入口網站中找到它，然後建立移轉專案。
1. 在 Azure 入口網站中，選取 [所有服務]，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]。
 
      ![找出 Azure 資料庫移轉服務的所有執行個體](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. 在 [Azure 資料庫移轉服務] 畫面上，搜尋您建立的 Azure DMS 執行個體名稱，然後選取該執行個體。
 
     ![找出您的 Azure 資料庫移轉服務執行個體](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. 選取 [+ New Migration Project] \(+ 新增移轉專案\)。
4. 在 [New migration project] \(新增移轉專案\) 畫面上，在 [Source server type] \(來源伺服器類型\) 中指定專案名稱、選取 [SQL Server]，然後在 [Target server type] \(目標伺服器類型\) 文字方塊中，選取 [Azure SQL Database]。

    ![建立資料庫移轉服務專案](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  選取 [Create] \(建立\) 以建立專案。


## <a name="specify-source-details"></a>指定來源詳細資料
1. 在 [Source details] \(來源詳細資料\) 畫面上，指定來源 SQL Server 的連線詳細資料。

    ![選取來源](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. 選取 [儲存]，然後選取 [AdventureWorks2012] 資料庫進行移轉。

    ![選取來源 DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>指定目標詳細資料
1. 選取 [儲存]，然後在 [Target details] \(目標詳細資料\) 畫面上指定目標的連線詳細資料，這個目標是使用 Data Migration Assistant 將 **AdventureWorks2012** 結構描述部署到的預先佈建 Azure SQL Database。

    ![選取目標](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. 選取 [儲存] 以儲存專案。
3. 在 [Migration summary] \(移轉摘要\) 畫面上，檢閱並確認與移轉專案相關聯的詳細資料。

    ![DMS 摘要](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. 選取 [ **儲存**]。

## <a name="run-the-migration"></a>執行移轉
1.  選取最近儲存的專案，選取 [+ New Activity] \(+ 新增活動\)，然後選取 [Run Data Migration] \(執行資料移轉\)。

    ![新增活動](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  出現提示時，請輸入來源伺服器與目標伺服器的認證，然後選取 [儲存]。
3.  在 [Map to target databases] \(對應到目標資料庫\) 畫面上，對應要進行移轉的來源資料庫和目標資料庫。

    如果目標資料庫包含與來源資料庫相同的資料庫名稱，Azure DMS 預設會選取目標資料庫。

    ![對應到目標資料庫](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. 在 [Select tables] \(選取資料表\) 畫面上選取 [儲存]，展開資料表清單，然後檢閱受影響欄位的清單。

    ![選取資料表](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  在 [Migration summary] \(移轉摘要\) 畫面上選取 [儲存]，在 [Activity name] \(活動名稱\) 文字方塊中，指定移轉活動的名稱。

    在這個畫面上，您也可以展開 [Choose validation option] \(選擇驗證選項\) 畫面，以便在當中指定要驗證已移轉資料庫的下列項目：
    - 結構描述
    - 資料一致性
    - 查詢正確性和效能

    ![選擇驗證選項](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  選取 [儲存]，檢閱摘要以確定來源和目標詳細資料符合您先前指定的內容。

    ![移轉摘要](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  選取 [Run migration] \(執行移轉\) 以啟動移轉活動，然後選取 [Refresh] \(重新整理\) 以檢閱目前的狀態。

    ![活動狀態](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>監視移轉
1. 選取移轉活動以檢閱活動的狀態。
2. 移轉完成後，請確認目標 Azure SQL Database。

    ![Completed](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
