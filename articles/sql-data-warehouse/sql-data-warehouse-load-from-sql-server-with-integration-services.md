---
title: "將資料從 SQL Server 載入 Azure SQL 資料倉儲 (SSIS) | Microsoft Docs"
description: "示範如何建立 SQL Server Integration Services (SSIS) 封裝，以將資料從各種資料來源移至 SQL 資料倉儲。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 03/30/2017
ms.author: cakarst;douglasl;barbkess
ms.openlocfilehash: 6c9cebdd715b6997d0633bc725a3945ba9e0c357
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>將資料從 SQL Server 載入 Azure SQL 資料倉儲 (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

建立 SQL Server Integration Services (SSIS) 來將資料從 SQL Server 載入 Azure SQL 資料倉儲。 您可以選擇性地重建、轉換和清理通過 SSIS 資料流程傳遞的資料。

在本教學課程中，您將：

* 在 Visual Studio 中建立新的整合服務專案
* 連接到資料來源，包括 SQL Server (做為來源) 和 SQL 資料倉儲 (做為目的地)。
* 設計 SSIS 封裝，用來將資料從來源載入目的地。
* 執行 SSIS 封裝載入資料。

本教學課程中使用 SQL Server 做為資料來源。 SQL Server 可在內部部署或在 Azure 虛擬機器上執行。

## <a name="basic-concepts"></a>基本概念
封裝是 SSIS 的工作單位。 相關的封裝集成群組則為專案。 在 Visual Studio 中使用 SQL Server Data Tools 建立專案和設計封裝。 設計程序是視覺化的程序，您可從工具箱拖曳元件至設計介面、連接這些元件、並設定其屬性。 完成您的封裝之後，您可以選擇性地將它部署到 SQL Server 以獲得完整管理、監視和安全性。

## <a name="options-for-loading-data-with-ssis"></a>以 SSIS 載入資料的選項
SQL Server Integration Services (SSIS) 是彈性的工具組合，提供連接至 SQL 資料倉儲、並將資料載入 SQL 資料倉儲的各種選項。

1. 使用「ADO NET 目的地」連接到 SQL 資料倉儲。 本教學課程使用 ADO NET 目的地，因為它的設定選項最少。
2. 使用「OLE DB 目的地」連接到 SQL 資料倉儲。 此選項可能會提供比 ADO NET 目的地稍好的效能。
3. 使用「Azure Blob 上傳」工作在 Azure Blob 儲存體中預備資料。 然後使用「SSIS 執行 SQL」工作來啟動可將資料載入 SQL 資料倉儲的 Polybase 指令碼。 此選項提供此處所列三個選項中最佳的效能。 若要取得 Azure Blob 上傳工作，請下載[適用於 Azure 的 Microsoft SQL Server 2016 Integration Services Feature Pack][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]。 若要深入了解 Polybase，請參閱 [PolyBase 指南][PolyBase Guide]。

## <a name="before-you-start"></a>開始之前
若要逐步執行本教學課程，您需要：

1. **SQL Server Integration Services (SSIS)**。 SSIS 是 SQL Server 的元件，需有試用版或授權版的 SQL Server。 若要取得 SQL Server 2016 Preview 的試用版，請參閱 [SQL Server 試用版][SQL Server Evaluations]。
2. **Visual Studio**。 若要取得免費的 Visual Studio Community Edition，請參閱 [Visual Studio Community][Visual Studio Community]。
3. **適用於 Visual Studio 的 SQL Server Data Tools (SSDT)**。 若要取得適用於 Visual Studio 的 SQL Server Data Tools，請參閱[下載 SQL Server Data Tools (SSDT)][Download SQL Server Data Tools (SSDT)]。
4. **範例資料**。 本教學課程會使用 AdventureWorks 範例資料庫中儲存在 SQL Server 中的範例資料，做為要載入 SQL 資料倉儲的來源資料。 若要取得 AdventureWorks 範例資料庫，請參閱 [AdventureWorks 2014 範例資料庫][AdventureWorks 2014 Sample Databases]。
5. **SQL 資料倉儲資料庫和權限**。 本教學課程會連接到 SQL 資料倉儲執行個體，並載入資料至執行個體。 您必須具有建立資料表以及載入資料的權限。
6. **防火牆規則**。 您必須先在使用您本機電腦 IP 位址的 SQL 資料倉儲上建立防火牆規則，才您可以將資料上傳到此 SQL 資料倉儲。

## <a name="step-1-create-a-new-integration-services-project"></a>步驟 1：建立新的 Integration Services 專案
1. 啟動 Visual Studio。
2. 從 [檔案] 功能表中，選取 [新增 | 專案]。
3. 瀏覽至 [安裝 |範本 |商務智慧 |整合服務]  專案類型。
4. 選取 [整合服務專案] 。 提供 [名稱] 和 [位置] 的值，然後選取 [確定]。

Visual Studio 隨即開啟，並建立新的整合服務 (SSIS) 專案。 然後 Visual Studio 會在專案中開啟一個新的 SSIS 封裝 (Package.dtsx) 的設計工具。 您會看到下列畫面區域：

* 左側是 SSIS 元件的 **工具箱** 。
* 中間是設計介面，有多個索引標籤。 您通常至少會使用 [控制流程] 和 [資料流程]**資料流程** 索引標籤。
* 右側是 [方案總管] 和 [屬性] 窗格。
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>步驟 2：建立基本資料流程
1. 將 [資料流程工作] 從 [工具箱] 拖曳至設計介面的中心 (設計介面在 [控制流程]  索引標籤上)。
   
    ![][02]
2. 按兩下 [資料流程工作] 以切換到 [資料流程] 索引標籤。
3. 從 [工具箱] 中的 [其他來源] 清單中，將 ADO.NET 來源拖曳至設計介面。 在來源配接器仍選取的狀態下，在 [屬性] 窗格中將其名稱變更為 [SQL Server 來源]。
4. 從 [工具箱] 中的 [其他目的地] 清單中將 ADO.NET 目的地拖曳至 ADO.NET 來源下方的設計介面。 在目的地配接器仍選取的狀態下，在 [屬性] 窗格中將其名稱變更為 [SQL DW 目的地]。
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>步驟 3︰ 設定來源配接器
1. 按兩下來源配接器以開啟 [ADO.NET 來源編輯器] 。
   
    ![][03]
2. 在 [ADO.NET 來源編輯器] 的 [連接管理員] 索引標籤上，按一下 [ADO.NET 連接管理員] 清單旁的 [新增] 按鈕開啟 [設定 ADO.NET 連接管理員] 對話方塊，並針對本教學課程會從之載入資料的 SQL Server 資料庫建立連接設定。
   
    ![][04]
3. 在 [設定 ADO.NET 連接管理員] 對話方塊中，按一下 [新增] 按鈕以開啟 [連接管理員] 對話方塊並建立新的資料連接。
   
    ![][05]
4. 在 [連接管理員]  對話方塊中，執行下列動作。
   
   1. 在 [提供者] 選取 SqlClient 資料提供者。
   2. 在 [伺服器名稱] 輸入 SAP 伺服器名稱。
   3. 在 [登入伺服器]  區段中，選取或輸入驗證資訊。
   4. 在 [連接到資料庫]  區段，選取 AdventureWorksDW 範例資料庫。
   5. 按一下 [測試連接] 。
      
       ![][06]
   6. 在報告連接測試結果的對話方塊中，按一下 [確定] 以回到 [連接管理員] 對話方塊。
   7. 在 [連接管理員] 對話方塊中，按一下 [確定] 以回到 [設定 ADO.NET 連接管理員] 對話方塊。
5. 在 [設定 ADO.NET 連接管理員] 對話方塊中，按一下 [確定] 以回到 [ADO.NET 來源編輯器]。
6. 在 [ADO.NET 來源編輯器] 的 [資料表或檢視的名稱] 清單中，選取 [Sales.SalesOrderDetail] 資料表。
   
    ![][07]
7. 按一下 [預覽] 以在 [預覽查詢結果] 對話方塊中查看來源資料表前 200 個資料列的資料。
   
    ![][08]
8. 在 [預覽查詢結果] 對話方塊中，按一下 [關閉] 以回到 [ADO.NET 來源編輯器]。
9. 在 [ADO.NET 來源編輯器] 中，按一下 [確定] 以完成資料來源設定。

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>步驟 4︰將目的地配接器連接到來源配接器
1. 在設計介面上選取來源配接器。
2. 選取從來源配接器延伸出來的藍色箭號，將它拖曳到目的地編輯器直到它扣住定位。
   
    ![][10]
   
    在典型 SSIS 封裝中，您可以在來源和目的地之間使用 SSIS 工具箱中的多個其他元件，在資料通過 SSIS 資料流程時重新建構、轉換、清理資料。 為了讓這個範例盡可能簡單，我們直接將來源連接到目的地。

## <a name="step-5-configure-the-destination-adapter"></a>步驟 5︰設定目的地配接器
1. 按兩下目的地配接器開啟 [ADO.NET 目的地編輯器] 。
   
    ![][11]
2. 在 [ADO.NET 目的地編輯器] 的 [連接管理員] 索引標籤上，按一下 [連接管理員] 清單旁的 [新增] 按鈕，以開啟 [設定 ADO.NET 連接管理員] 對話方塊，並針對本教學課程會將資料載入的 Azure SQL 資料倉儲資料庫建立連接設定。
3. 在 [設定 ADO.NET 連接管理員] 對話方塊中，按一下 [新增] 按鈕以開啟 [連接管理員] 對話方塊並建立新的資料連接。
4. 在 [連接管理員]  對話方塊中，執行下列動作。
   1. 在 [提供者] 選取 SqlClient 資料提供者。
   2. 在 [伺服器名稱] 輸入 SQL 資料倉儲名稱。
   3. 在 [登入伺服器] 區段，選取 [使用 SQL Server 驗證] 並輸入驗證資訊。
   4. 在 [連接到資料庫]  區段，選取現有的 SQL 資料倉儲資料庫。
   5. 按一下 [測試連接] 。
   6. 在報告連接測試結果的對話方塊中，按一下 [確定] 以回到 [連接管理員] 對話方塊。
   7. 在 [連接管理員] 對話方塊中，按一下 [確定] 以回到 [設定 ADO.NET 連接管理員] 對話方塊。
5. 在 [設定 ADO.NET 連接管理員] 對話方塊中，按一下 [確定] 以回到 [ADO.NET 目的地編輯器]。
6. 在 [ADO.NET 目的地編輯器] 中，按一下 [使用資料表或檢視] 清單旁的 [新增] 按鈕以開啟 [建立資料表] 對話方塊，建立具有符合來源資料表之資料行清單的新目的地資料表。
   
    ![][12a]
7. 在 [建立資料表]  對話方塊中，執行下列動作。
   
   1. 將目的地資料表的名稱變更為 **SalesOrderDetail**。
   2. 移除 **rowguid** 資料行。 SQL 資料倉儲不支援 **uniqueidentifier** 資料類型。
   3. 將 **LineTotal** 資料行的資料類型變更為 [money]。 SQL 資料倉儲不支援 **decimal** 資料類型。 如需支援的資料類型資訊，請參閱[建立資料表 (Azure SQL 資料倉儲，平行資料倉儲)][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]。
      
       ![][12b]
   4. 按一下 [確定] 以建立資料表，並回到 [ADO.NET 目的地編輯器]。
8. 在 [ADO.NET 目的地編輯器] 中選取 [對應] 索引標籤，以查看來源資料行對應到目的地資料行的方式。
   
    ![][13]
9. 按一下 [確定]  完成資料來源設定。

## <a name="step-6-run-the-package-to-load-the-data"></a>步驟 6︰執行封裝以載入資料
按一下工具列上的 [開始] 按鈕，或選取 [偵錯] 功能表上其中一個 [執行] 選項以執行封裝。

當封裝開始執行時，您會看到用來表示活動的黃色旋轉滾輪以及目前已處理的資料列數。

![][14]

封裝執行完成時，您會看到用來表示成功的綠色打勾記號，以及從來源載入到目的地的資料列總數。

![][15]

恭喜！ 您已使用 SQL Server Integration Services 成功將資料載入 Azure SQL 資料倉儲。

## <a name="next-steps"></a>後續步驟
* 深入了解 SSIS 資料流程。 從這裡開始：[資料流程][Data Flow]。
* 了解如何在設計環境中進行封裝的偵錯和疑難排解。 從這裡開始：[套件開發的疑難排解工具][Troubleshooting Tools for Package Development]。
* 了解如何將 SSIS 專案和封裝部署到 Integration Services 伺服器或其他儲存位置。 從這裡開始：[部署專案和套件][Deployment of Projects and Packages]。

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550
