---
title: "Cortana Intelligence 解決方案評估工具 | Microsoft Docs"
description: "身為 Microsoft 合作夥伴，以下是將您的 Cortana Intelligence 解決方案發佈至 AppSource 所需遵循的所有步驟。"
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8340ae1231b7f40f739d7bbb63cee1bf0f095ab5
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

--- 
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Cortana Intelligence 解決方案評估工具
## <a name="overview"></a>概觀
您可以使用 Cortana Intelligence 解決方案評估工具，來評估您的進階分析解決方案是否符合 Microsoft 所建議的最佳做法。 Microsoft 很榮幸能與合作夥伴 (ISV / SI) 合作，為客戶、經銷商和實作程序提供高品質的解決方案。 本指南會逐步引導您完成使用解決方案評估工具搭配您解決方案的程序，並說明特定的最佳做法。

## <a name="getting-started"></a>開始使用
請[下載](https://aka.ms/aa-evaluation-tool-download)並安裝 Cortana Intelligence 解決方案評估工具。

必要條件：
- Windows 10：[Windows 10 的官方網站](https://www.microsoft.com/en-us/windows)
- Azure PowerShell：[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0) \(英文\)。

## <a name="identifying-your-app"></a>識別您的應用程式
安裝完成之後，請開啟工具並開始進行首次評估。

![開啟評估工具](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

提供關於您解決方案的識別資訊。

![連接 Azure 訂用帳戶](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

連接到您的 Azure 訂用帳戶並提供包含您應用程式的資源群組。

![選取資源](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

載入資源群組之後，請選取包含在您解決方案中的資源，並將所有資料資源的可存取性識別為下列其中之一：
- 擷取
- 使用
- 內部

我們會使用這項資訊來深入了解您的解決方案如何利用各種元件，並確保與使用者互動的元件符合最佳做法。

### <a name="ingestion"></a>擷取
「擷取」在此案例中係指從解決方案外部提取資料時所使用的任何資料來源，或是解決方案的任何外部服務將資料推送到此解決方案時所使用的任何資料來源。

### <a name="consumption"></a>耗用量
「使用」在此案例中係指用來將資料直接或間接推送給使用者的任何資料集。 例如：
- 從 PowerBI 中用於直接查詢的資料集。
- 在 WebApp 中查詢的資料集。

>[!NOTE]
如果特定的資源同時用於擷取和使用，請選擇 [使用]。

### <a name="internal"></a>內部
針對僅用於內部應用程式處理的任何資料資源，請使用「內部」。

接下來，系統會提示您針對先前步驟中所指定的任何資料庫提供有效認證：

![設定測試必要條件](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>解決方案測試案例
解決方案工具會針對您的解決方案執行一組自動化測試。

![設定測試執行](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

測試完成之後，系統會要求您提供解決方案不符合需求的原因說明或理由。

![提供業務理由](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

例如，如果您的解決方案會發佈到 Azure SQL DW，評估測試就會要求您也發佈到 Azure Analysis Services。 

您的解決方案可能會使用執行 SQL Server Analysis Services (而非 Azure Analysis Services) 的 IaaS 虛擬機器。 這是一個可接受的測試失敗原因。
## <a name="packaging-your-evaluation-results"></a>封裝您的評估結果
完成測試案例後，您的評估套件將匯出為 ZIP 檔案，且系統會要求您針對評估工具提供意見反應。 

您必須將這個測試結果 ZIP 檔案分享給 Microsoft，以便讓您的解決方案先經過評估，再獲得核准來新增到 AppSource

![為評估工具評分](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

本文的上面小節涵蓋此工具的各種功能，現在讓我們檢閱此工具所評估的最佳做法類型。

## <a name="security-evaluation-considerations"></a>安全性評估考量
### <a name="databases-should-use-azure-active-directory-authentication"></a>資料庫應使用 Azure Active Directory 驗證
解決方案中的任何 Azure SQL 或 Azure SQL DW 資源都應啟用 Azure Active Directory (AAD) 驗證。 AAD 可讓您於單一位置管理所有身分識別和角色。

| 如需下列項目的詳細資訊 | 請參閱此文章 |
| --- | --- |
| AAD 搭配 SQL Database 和 SQL 資料倉儲 | [利用 SQL Database 或 SQL 資料倉儲使用 Azure Active Directory 驗證來驗證](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| 設定和管理 AAD | [使用 SQL Database 或 SQL 資料倉儲設定和管理 Azure Active Directory 驗證](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Azure WebApps 驗證 | [Azure App Service 中的驗證與授權](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| 設定 WebApps 以搭配 AAD | [如何設定 App Service 應用程式使用 Azure Active Directory 登入](https://docs.microsoft.com/en-us/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>使用者可存取的資料集應支援角色型存取控制
執行評估工具時，系統會要求您指定任何報告或發佈資源。 系統會假設這些資源是提供給使用者存取，而非開發人員。 您應該為這些資源提供角色型存取控制 (RBAC)，以確保使用者只能存取經過授權的資料。

具體而言，下列所有 Azure 資源都可設定為使用 RBAC 並被視為可接受：
- 安全的 HDInsight，請參閱[已加入網域之 HDInsight 叢集的 Hadoop 安全性簡介](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL，請參閱 [AAD 驗證搭配 Azure SQL]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)
- Azure Analysis Services，請參閱[針對 Azure Analysis Services 管理資料庫角色和使用者](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) \(英文\)
- Azure SQL 資料倉儲 (請注意，因為 SQL DW 可支援 RBAC，因此不建議用於直接使用者存取)。

如果您使用其他支援 RBAC 的資源類型，請在測試案例理由中指出。

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store 應使用靜態加密
Azure Data Lake Store (ADLS) 支援預設使用 ADLS 受管理加密金鑰的靜態加密。 您也可以使用 Azure Key Vault 設定加密。

如需指定 ADLS 加密設定的相關資訊，請參閱[建立 Azure Data Lake Store 帳戶](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account)。

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL 和 Azure SQL 資料倉儲應使用加密
Azure SQL 和 Azure SQL DW 都支援透明資料加密 (TDE)，這項功能可提供資料與記錄檔的即時加密和解密。

| 如需下列項目的詳細資訊 | 請參閱此文章 |
| --- | --- |
| 透明資料加密 (TDE) | [透明資料加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL 資料倉儲搭配 TDE | [SQL 資料倉儲加密 TDE TSQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| 設定 Azure SQL 以搭配 TDE | [Azure SQL Database 的透明資料加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| 設定 Azure SQL 以搭配 Always Encrypted | [SQL Database Always Encrypted Azure Key Vault](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

除了 TDE 之外，Azure SQL 也支援新的資料加密技術 Always Encrypted，這項技術可確保資料不僅會在靜態及在用戶端和伺服器間移動時受到加密，也能加密於伺服器上執行命令時所使用的資料。

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>所有虛擬機器都必須從 Azure Marketplace 部署
為了在整個 AppSource 都提供相同層級的安全性，我們要求所有與 Cortana Intelligence 解決方案一起部署的虛擬機器都必須在 Azure Marketplace 上經過認證和發佈。

若要搜尋目前的 Azure Marketplace 映像清單，請參閱 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute)。

如需如何針對 Azure Marketplace 發佈虛擬機器映像的相關資訊，請參閱[建立 Azure Marketplace 的虛擬機器映像的指南](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)。

## <a name="scalability-evaluation-considerations"></a>延展性評估考量
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Cortana Intelligence 解決方案應包含一個可調整的巨量資料平台
Cortana Intelligence 解決方案應可因應非常大的資料大小進行調整。 在 Azure 中，這表示它們應包含兩個千兆位元組 (PB) 規模資料平台的其中一個：
- Azure Data Lake Store
- Azure SQL 資料倉儲

如果您的解決方案不要求支援這些資料大小，或您使用替代的資料平台，請在測試案例理由中提供說明。
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Cortana Intelligence 解決方案應包含專用的擷取資料環境
Cortana Intelligence 解決方案通常應避免將資料直接插入到關聯式資料來源。 未經處理資料應改為儲存在非結構化環境中，並使用 Azure Data Factory 針對任何關聯式存放區進行等冪插入/更新。

如需使用 Azure Data Factory 複製資料的詳細資訊，請參閱[教學課程：使用 Visual Studio 建立具有複製活動的管線](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio)。

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL 資料倉儲應使用 PolyBase 進行資料擷取
Azure SQL DW 支援 PolyBase 以進行可高度擴充、平行的資料擷取。 PolyBase 可讓您使用 Azure SQL DW，針對儲存在 Azure Blob 儲存體或 Azure Data Lake Store 的外部資料集發出查詢。 這可提供比其他大量更新方法更為優異的效能。

如需開始使用 PolyBase 和 Azure SQL DW 的指示，請參閱[在 SQL 資料倉儲中使用 PolyBase 載入資料](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase)。

如需使用 PolyBase 和 Azure SQL DW 的最佳做法詳細資訊，請參閱[在 SQL 資料倉儲中使用 PolyBase 的指南](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide)。

## <a name="availability-evaluation-considerations"></a>可用性評估考量

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>使用者可存取的資料集應支援大量的並行使用者
執行評估工具時，系統會要求您指定任何報告或發佈資源。 系統會假設這些資源是提供給使用者存取，而非開發人員。 這些資源應支援中等/大型數量的並行使用者。

具體而言，Azure SQL 資料倉儲「不應」是提供給使用者的唯一資料來源。 如果 Azure SQL DW 是作為資源提供給進階使用者，則應向一般使用者提供 Azure Analysis Services。

如需關於 Azure SQL DW 並行限制的詳細資訊，請參閱 [SQL 資料倉儲中的並行存取和工作負載管理](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency)。

如需 Azure Analysis Services 的詳細資訊，請參閱 [Analysis Services 概觀](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)。

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL 資源應該有唯讀複本以供容錯移轉
Azure SQL 資料庫支援針對次要執行個體進行異地複寫。 此執行個體可接著用來當作容錯移轉執行個體，以提供高可用性應用程式。

如需 Azure SQL 資料庫異地複寫的詳細資訊，請參閱 [SQL Database 異地複寫概觀](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview)。

如需如何設定 Azure SQL 異地複寫的指示，請參閱[使用 Transact-SQL 為 Azure SQL Database 設定作用中異地複寫](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql)。

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL 資料倉儲應已啟用異地備援備份
Azure SQL DW 支援每日備份至異地備援儲存體。 即使您無法存取儲存在主要區域中的快照集，此異地複寫也可確保您能夠還原資料倉儲。 此功能針對 Cortana Intelligence 解決方案預設為開啟，且不應該停用。

如需 Azure SQL DW 備份和還原的詳細資訊，請參閱 [SQL 資料倉儲備份](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups)。

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>虛擬機器應使用可用性設定組進行設定
Azure 虛擬機器應該在可用性設定組中設定，以便將規劃與未規劃維護事件的影響降到最低。

如需 Azure 虛擬機器可用性的詳細資訊，請參閱[管理 Azure 中 Windows 虛擬機器的可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability)。

## <a name="other-evaluation-considerations"></a>其他評估考量
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Cortana Intelligence 應用程式針對資料協調流程應使用集中式工具
使用單一工具來對資料移動及轉換進行管理和排程，能確保關鍵任務資料的一致性。 它可提供關於重試邏輯、相依性管理，警示/記錄等項目的清楚邏輯。我們建議針對 Azure 中的資料協調流程使用 [Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-introduction)。

如果您針對資料協調流程使用 Azure Data Factory 以外的工具，請說明您使用的是哪些工具。
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Azure Machine Learning 模型應使用 Azure Data Factory 重新訓練
Azure Machine Learning (AzureML) 提供簡單易用的工具，以建立和部署預測模型和機器學習管線。 不過，要注意的是，這些 AzureML 模型的生產部署並非以單一固定的資料集為基礎，而是會適應真實世界現象的變動動態。

如需在 AzureML 中建立重新訓練 Web 服務的詳細資訊，請參閱[以程式設計方式重新訓練機器學習服務模型](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically)。

如需使用 Azure Data Factory 自動化模型訓練程序的詳細資訊，請參閱[使用更新資源活動更新 Azure Machine Learning 模型](https://docs.microsoft.com/en-us/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity)。

## <a name="existing-documentation"></a>現有文件
[Microsoft Azure 認證協助拓展您的雲端業務](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Cortana Intellignece 認證](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)


