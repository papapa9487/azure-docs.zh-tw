---
title: "在 Microsoft Azure 中探索、識別並分類個人資料 | Microsoft Docs"
description: "深入了解搜尋、分類、探索及識別資料"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 953df2f0dbbccc153b0a4206c9c0b5f5ea9a51a7
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中探索、識別並分類個人資料

本文提供指導方針，說明如何在數個 Azure 工具和服務中探索、識別和分類個人資料，包括使用 Azure 資料目錄、Azure Active Directory、SQL Database、Azure HDInsight 中適用於 Hadoop 叢集的 Power Query、Azure 資訊保護、Azure 搜尋服務，以及適用於 Azure Cosmos DB 的 SQL 查詢。

## <a name="scenario-problem-statement-and-goal"></a>情節、問題陳述和目標

美國的運動公司會收集各種個人和其他資料。 這包括客戶和員工資料。 該公司會將資料保存在多個資料庫中，並將它儲存在其 Azure 環境中的數個不同位置。 除了銷售運動設備之外，它們還會主辦全世界精銳運動事件並管理註冊，包含在 EU 和在某些案例中，它們所收集的客戶資料包含醫療資訊。

由於公司每年都會主辦多場國際自行車巡迴賽，並在全球各地都擁有代表團人員，幾個資料集就相當大了。 該公司也有開發人員建置的應用程式，可供客戶與員工使用。

公司需要解決下列問題：

- 必須從公司收集的其他資料中分類/辨別客戶和員工的個人資料，從而確保適當的存取與安全性。
- 資料管理需要跨 Azure 環境的不同區域，輕鬆地探索客戶個人資料的位置。
- 出現在共用文件和電子郵件通訊中的客戶和員工個人資料必須加以標記，才能協助確保它保持安全。
- 公司的應用程式開發人員需要一個方法，能夠輕鬆地在其 web 和行動裝置應用程式中搜尋客戶和員工的個人資料。
- 開發人員也需要查詢其文件資料庫來取得個人資料。

### <a name="company-goals"></a>公司目標

- 所有客戶和員工的個人資料必須都已在 Azure 資料目錄中加以標記/標註，以便能夠輕鬆地找到。 在理想情況下，客戶和員工的個人資料會分開標記/標註。
- 必須能夠輕鬆地從位於 Azure Active Directory 中的客戶和員工使用者設定檔和工作資訊找到個人資料。
- 必須能夠輕鬆地查詢位在多個 SQL Database 中的個人資料。 
- 某些公司的大型資料集是透過 Azure HDInsight 進行管理，並儲存在 Hadoop 中。 必須將這些資料集匯入 Excel 中，以便可以查詢個人資料。
- 在文件及電子郵件通訊中共用的個人資料必須加以分類、標記，並使用 Azure 資訊保護來保持安全。
- 公司的應用程式開發人員必須能夠在他們所建立的應用程式中探索客戶和員工的個人資料，而他們可以使用 Azure 搜尋服務來進行。
- 開發人員必須能夠在其文件資料庫中尋找個人資料。

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory：資料探索

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的雲端式、多租用戶目錄和身分識別管理服務。 您可以使用 [Azure 入口網站](https://portal.azure.com/)來找出客戶和員工的使用者設定檔，以及包含 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 環境中個人資料的使用者工作資訊。

如果您需要尋找或變更特定使用者的個人資料，這對您會特別有用。 您也可以新增或變更使用者設定檔及工作資訊。 您必須使用具備目錄全域管理員身分的帳戶來登入。

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>如何找出或檢視使用者設定檔和工作資訊？

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。

2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

   ![如何找出使用者設定檔和工作資訊](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. 在 [使用者和群組] 刀鋒視窗上，選取 [使用者]。

  ![開啟使用者和群組](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. 在 [使用者和群組 - 使用者] 刀鋒視窗中，從清單選取使用者，然後在選取的使用者之刀鋒視窗上，選取 [設定檔] 以檢視可能包含個人資料的使用者設定檔資訊。

  ![選取使用者](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. 如果您要新增或變更使用者設定檔資訊，可以這麼做，然後在命令列中選取 [儲存]。
6. 在選取的使用者之刀鋒視窗上，選取 [工作資訊] 來檢視可能包含個人資料的使用者工作資訊。

 ![檢視工作資訊](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. 如果您要新增或變更使用者工作資訊，可以這麼做，然後在命令列中選取 [儲存]。

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database：資料探索

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 是雲端資料庫，可協助開發人員建置及維護應用程式。 您可以使用標準 SQL 查詢在 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 中找到個人資料。 Azure SQL 彈性查詢 (預覽) 可讓使用者執行跨資料庫查詢。

[SQL Database](../sql-database/sql-database-technical-overview.md) 的詳細教學課程會說明多個使用 SQL 資料庫的層面，包括如何建置 SQL Database 以及如何執行資料查詢。 以下摘要是教學課程中的可用資訊，以及特定章節的連結。

### <a name="how-do-i-build-a-sql-database"></a>如何建置 SQL Database？

有三種方式可以完成：

- 您可以在 [Azure 入口網站](https://portal.azure.com/)中建立 Azure SQL Database。 在本教學課程中，您將使用資源群組和邏輯伺服器內的一組特定運算及儲存體資源。 您會使用名為 AdventureWorks 的虛構公司之範例資料。 您還會建立伺服器層級防火牆規則。 若要了解如何執行這項操作，請造訪[在 Azure 入口網站中建立 Azure SQL Database](../sql-database/sql-database-get-started-portal.md) 教學課程。

  ![建立 Azure SQL Database](media/how-to-discover-classify-personal-data-azure/create-database.png)
- 您也可以在 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) CLI 中建立 SQL Database，這是以瀏覽器為基礎的命令列工具。 此工具可在 Azure 入口網站中使用，且可以直接從該處執行。 在本教學課程中，您要啟動工具、定義指令碼變數、建立資源群組和邏輯伺服器，並設定伺服器防火牆規則。 然後使用範例資料建立資料庫。 若要了解如何以這種方式建立您的資料庫，請造訪[使用 Azure CLI 建立單一 Azure SQL Database](../sql-database/sql-database-get-started-cli.md) 教學課程。

  ![CLI 教學課程](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Linux 系統管理員和開發人員通常都是使用 Azure CLI。 某些使用者認為它比 PowerShell (這是您的第三個選項) 更簡單且更直覺。

- 最後，您可以使用 PowerShell 來建立 SQL Database，它是用來建立及管理 Azure 和其他資源的命令列/指令碼工具。 在本教學課程中，您要啟動工具、定義指令碼變數、建立資源群組和邏輯伺服器，並設定伺服器防火牆規則。 然後使用範例資料建立資料庫。

本教學課程需要 Azure PowerShell 模組 4.0 版或更新版本。 執行 Get-Module -ListAvailable AzureRM 來尋找您的版本。 如果您需要安裝或升級，請參閱安裝 Azure PowerShell 模組。

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

若要了解如何以這種方式建立您的資料庫，請造訪[使用 PowerShell 建立單一 Azure SQL Database](../sql-database/sql-database-get-started-powershell.md) 教學課程。

>[!Note]
Windows 系統管理員通常會使用 PowerShell，但有些則偏好使用 Azure CLI。

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>如何在 Azure 入口網站中搜尋 SQL Database 中的個人資料？**

您可以使用 Azure 入口網站內部的內建查詢編輯器工具來搜尋個人資料。 您要使用 SQL Server 系統管理員登入和密碼來登入工具，然後輸入查詢。

  ![使用入口網站搜尋 SQL](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

本教學課程的步驟 5 會在查詢編輯器窗格中顯示一個範例查詢，但它不會著重於個人或機密資訊 (它還會結合來自兩個資料表的資料，並在傳回的資料集中建立來源資料行的別名)。 下列螢幕擷取畫面會顯示步驟 5 的查詢，以及傳回的結果窗格：

  ![查詢編輯器](media/how-to-discover-classify-personal-data-azure/query-editor.png)

如果您的資料庫稱為 MyTable，個人資訊的範例查詢可能就會包括名稱、社會安全號碼和識別碼，且會看起來像這樣：

「選取名稱、社會安全號碼、MyTable 的識別碼」

您要執行查詢，然後在 [結果] 窗格中查看查詢結果。

如需有關在 Azure 入口網站中如何查詢 SQL Database 的詳細資訊，請瀏覽教學課程的[查詢 SQL Database](../sql-database/sql-database-get-started-portal.md) 一節。

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>如何跨多個資料庫搜尋資料？

SQL 彈性查詢 (預覽) 可讓您執行跨資料庫和多個資料庫的查詢，並傳回單一結果。 [教學課程概觀](../sql-database/sql-database-elastic-query-overview.md)包含情節的詳細描述，並說明垂直和水平資料庫資料分割之間的差異。 水平資料分割稱為「分區化」。

  ![垂直資料分割](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![水平資料分割](media/how-to-discover-classify-personal-data-azure/horizontal.png)

若要開始使用，請造訪 [Azure SQL Database 彈性查詢概觀 (預覽)](../sql-database/sql-database-elastic-query-overview.md) 頁面。

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (適用於匯入 Azure HDInsight Hadoop 叢集)：大型資料集的資料探索

Hadoop 是開放原始碼 Apache 儲存體和大型資料集的處理服務，會在 Hadoop 叢集中進行分析並加以儲存。 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) 可讓使用者在 Azure 中使用 Hadoop 叢集。 Power Query 是 Excel 增益集，除此之外，還可協助使用者從不同的來源探索資料。

與 Azure HDInsight 中的 Hadoop 叢集相關聯之個人資料，都可以使用 Power Query 匯入 Excel 中。 一旦資料在 Excel 中，您就可以使用查詢來識別它。

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>如何使用 Excel Power Query 將 Azure HDInsight 中的 Hadoop 叢集匯入 Excel 中？

HDInsight 教學課程將引導您完成這整個程序。 它會說明必要條件，並包含[開始使用 Azure HDInsight](../hdinsight/hdinsight-hadoop-linux-tutorial-get-started.md) 教學課程的連結。 指示涵蓋 Excel 2016 以及 2013 和 2010 (舊版本 Excel 的步驟會稍有不同)。 如果您沒有 Excel Power Query 增益集，本教學課程會告訴您如何完成設定。 您會在 Excel 中開始教學課程，且必須具有與您叢集相關聯的 Azure Blob 儲存體帳戶。

  ![在 Excel 中的查詢](media/how-to-discover-classify-personal-data-azure/excel.png)

若要了解如何執行這項操作，請造訪[使用 Power Query 將 Excel 連線到 Hadoop](../hdinsight/hdinsight-connect-excel-power-query.md) 教學課程。

來源：[使用 Power Query 將 Excel 連線到 Hadoop](../hdinsight/hdinsight-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure 資訊保護：文件和電子郵件的個人資料分類

[Azure 資訊保護](https://www.microsoft.com/cloud-platform/azure-information-protection)可以協助 Azure 客戶套用標籤，將內部或外部共用的文件與電子郵件通訊加以分類及保護。 這些項目有些可能會包含客戶或員工的個人資訊。 系統管理員或使用者可以自動或手動方式定義規則和條件。 例如，如果使用者所要儲存的文件包含信用卡資訊，就會看到系統管理員已設定的標籤建議。

### <a name="how-do-i-try-it"></a>如何嘗試？

如果您需要讓 Azure 資訊保護嘗試查看它是否適合貴組織，請瀏覽[快速入門教學課程](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial)。 會引導您執行五個基本步驟 — 從安裝到設定原則、查看動作中的分類、標籤和共用 — 且花費時間不超過一小時。

### <a name="how-do-i-deploy-it"></a>如何進行部署？

如果您需要為貴組織部署 Azure 資訊保護，請瀏覽[分類、標籤和保護的部署藍圖](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap)。

### <a name="is-there-anything-else-i-should-know"></a>還有其他須知事項嗎？

如需能協助您思考設定方式的補充資訊，請造訪[就緒、設定、保護！](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
部落格文章。 如需有關 Azure 資訊保護的詳細資訊，請檢查下列的深入了解連結。

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure 搜尋服務：開發人員應用程式的資料探索

[Azure 搜尋服務](https://azure.microsoft.com/services/search/)是開發人員的雲端搜尋解決方案，可為您的應用程式提供豐富的資料搜尋體驗。 Azure 搜尋服務可讓您在使用者定義的索引中找出資料、源自 Azure Cosmo DB、Azure SQL Database、Azure Blob 儲存體、Azure 資料表儲存體或自訂客戶 JSON 資料。 您也可以使用 Azure 搜尋服務 REST API 來建構 Lucene 查詢，從而搜尋個人資料類型或特定人員的個人資料。 功能包括全文檢索搜尋、簡單查詢語法及 Lucene 查詢語法。 

## <a name="how-do-i-use-sql-to-query-data"></a>如何使用 SQL 來查詢資料？

若要開始進行基本概念，請瀏覽 [Azure CosmosD DB：如何使用 SQL 查詢](../cosmos-db/tutorial-query-documentdb.md)教學課程。 本教學課程提供一個範例文件及兩個範例 SQL 查詢和結果。

如需有關建置 SQL 查詢的詳細指引，請瀏覽 [Azure Cosmos DB 文件 DB API 的 SQL 查詢。](../cosmos-db/documentdb-sql-query.md)

如果您還不熟悉 Azure Cosmos DB 且需要了解如何建立資料庫、新增集合及新增資料，請瀏覽 [Azure Cosmos DB：建置 DocumentDB API 的 web 應用程式](../cosmos-db/create-documentdb-dotnet.md)快速入門教學課程。 如果您需要使用 .NET 以外的語言 (例如 JAVA 或 Python) 來執行這項操作，在您進入站台後，只要選擇您慣用的語言即可。

## <a name="next-steps"></a>後續步驟

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[什麼是 SQL Database？](../sql-database/sql-database-technical-overview.md)

[Azure 入口網站中可用的 SQL Database 查詢編輯器] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[什麼是 Azure 資訊保護？](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[什麼是 Azure Rights Management？](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure 資訊保護：就緒、設定、保護！](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)

