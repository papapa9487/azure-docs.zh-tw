---
title: "在移轉後進行管理 - Azure SQL Database | Microsoft Docs"
description: "了解如何在移轉至 Azure SQL Database 之後管理資料庫。"
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>如何在移轉之後管理 Azure SQL Database？

*有關管理 Azure SQL Database 投資的常見問題集*

您最近已將 SQL Server 資料庫移到 Azure SQL Database，或者您可能正在打算很快移動。 移動之後，後續步驟為何？ 假設 SQL Database 是一項「平台即服務」，Microsoft 會代表您處理數個領域。 但這對於貴公司處理主要領域 (例如安全性、業務持續性、資料庫維護、效能調整、監視等) 的作法到底有何改變？ 

本文的目的在於簡要地整理您要變換為管理 SQL Database 投資所需的資源和指引。 本文中的主要領域涵蓋業務持續性、安全性、資料庫維護及監視、效能和資料移動。 我們將涵蓋 SQL Server 與 SQL Database 之間不同的主要領域，並說明可協助您發揮最大效益並將風險降至最低的最佳做法。 

## <a name="manage-business-continuity-after-migration"></a>在移轉後管理業務持續性

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>如何建立及管理 SQL Database 上的備份？ 
SQL Database 會自動為您備份資料庫，並提供您還原到保留期間中任何時間點的功能。 標準和進階資料庫的保留期間為 35 天，而基本資料庫則為 7 天。 此外，長期保存功能可讓您將備份檔案保留更久 (最多 10 年)，並且隨時從這些備份進行還原。 此外，資料庫備份會複寫於異地，確保在發生災害或區域性災難時能夠於任何區域中進行異地還原。 請參閱[業務持續性概觀](sql-database-business-continuity.md)。

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>如何確保發生資料中心層級災害或區域性災難時的業務持續性？ 

資料庫備份會複寫於異地，確保在發生災害或區域性災難時能夠於任何區域中進行異地還原。 請參閱[業務持續性概觀](sql-database-business-continuity.md)。 此外，SQL Database 提供維護在另一個區域中主動異地複寫之次要資料庫的功能。 在自動容錯移轉群組中進行設定，可確保資料庫在發生災害時自動容錯移轉至次要資料庫。 如果未設定自動容錯移轉群組，您的應用程式則需要主動監視災害，並開始容錯移轉至次要資料庫。 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server 提供「可讀取的次要複本」給我，我可以存取 SQL Database 上的次要複本嗎？ 

可以，「作用中異地複寫」功能用來建立「可讀取的次要複本。 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>我的災害復原計劃如何從內部部署變更為 SQL Database？ 
SQL Server 實作要求您使用以下功能主動管理備份：例如容錯移轉叢集、資料庫鏡像、複寫、記錄傳送或只是普通的 BACPAC 備份。 不過，在 SQL Database 上，備份完全由 Microsoft 管理，而您只可以設定備份和災害復原計劃，且在 Azure 入口網站上按幾下滑鼠 (或在 Powershell 上使用幾個命令) 即可運作。 ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>發生災害時，如何復原資料庫？ 
SQL Database 會自動讓您將資料庫還原到過去 35 天內的任何時間點。 如果您遺失資料或面臨應用程式相關災害，這是一個選項。 

萬一您面臨區域性災害，如果已設定異地複寫的次要資料庫，您可以從另一個區域中異地次要資料庫進行復原。 如需即時存取您的應用程式，您可以手動容錯移轉至其他區域中的異地次要資料庫。 或者，如果您已設定自動容錯移轉群組，則在發生災害時，會自動進行此容錯移轉至異地次要資料庫。 如果您未設定異地複寫的次要資料庫，您仍可從自動複寫的備份檔案復原您的資料庫 (內建功能，不需要設定)，其復原時間相對較長 (12 小時 RTO) 且最多遺失一小時的資料。 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>容錯移轉到次要資料庫是透明的嗎？ 我的應用程式如何處理資料庫容錯移轉？ 
如果您已設定自動容錯移轉群組，則容錯移轉至次要資料庫是透明的。 不過，如果您沒有這樣做，則您的應用程式必須納入此邏輯，才能監視主要資料庫的可用性，然後手動容錯移轉至次要資料庫。 
 
## <a name="manage-security-after-migration"></a>在移轉後管理安全性

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>如何限制 SQL Database 的存取？ 
 
有幾種方法可鎖定對您的 SQL 資料庫的連線存取。 
1. 限制透過網際網路 Express Route 的流量，可讓您的 Azure 網路有專用光纖，您的資料就不會透過網際網路傳輸。 您也可以使用 Express Route 設定跨區域連線。 下列連結更詳細說明 Express Rout： 
 - [Express Route 簡介](../expressroute/expressroute-introduction.md)
 - [必要條件](../expressroute/expressroute-prerequisites.md) 
 - [工作流程](../expressroute/expressroute-workflows.md) 
 
2. 選取要連線到 SQL Database 的資源： 

   根據預設，SQL 資料庫會設定為「允許所有 Azure 服務」 – 這表示 Azure 中的所有 VM 可嘗試連線到資料庫。  仍然必須通過所有登入的驗證。 如果您不希望所有 Azure IP 存取您的資料庫，您可以停用「允許所有 Azure 服務」，並使用 [VNET 服務端點](sql-database-vnet-service-endpoint-rule-overview.md)來限制資料庫的輸入存取只來自指定之 Azure VNET 子網路內的 Azure 資源。 

   ![VNET 服務端點](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   替代選項就是為您的 VM 佈建[保留的 IP](../virtual-network/virtual-networks-reserved-public-ip.md)，並將伺服器防火牆設定中的特定 VM IP 位址列入白名單。 (將以下螢幕擷取畫面視為 Azure 入口網站中的範例。)藉由指派保留的 IP，即可免去必須以不斷變更的 IP 位址更新防火牆規則的麻煩。 

3. 避免在 Azure 外部公開連接埠 1433

   使用 [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps) 在 Azure 中執行 SSMS。 這麼一來，您不需要開啟通訊埠 1433 的連出連線，IP 是靜態的，所以只能對 RemoteApp 開啟資料庫，它可支援多重要素驗證 (MFA) 且適用於多位使用者。 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>SQL Database 提供哪些驗證方法？

SQL Database 和 SQL 資料倉儲中提供的主要驗證方法是 Azure Active Directory 驗證和 SQL 驗證。 Azure Active Directory (AD) 是集中式身分識別和存取管理服務，而 SQL 只是其中一項與 Azure AD 整合的 Azure 服務。 集中式管理服務的好處是您用於簡易驗證的所有 Azure 服務共用使用者的認證。 這也可讓 SQL Database 和 SQL 資料倉儲提供多重要素驗證和 Azure AD 網域內的來賓使用者帳戶。 

如果您已經有內部部署 Active Directory，您可以使目錄與 Azure Active Directory 結成同盟，將您的目錄延伸至 Azure。 


|||
|---|---|
| 如果您...|Azure SQL Database / Azure SQL 資料倉儲|
| 不想在 Azure 中使用 Azure Active Directory (AD)|使用 [SQL 驗證](sql-database-security-overview.md)|
| 已在內部部署 SQL Server 上使用 AD|[使 AD 與 Azure AD 結成同盟](../active-directory/connect/active-directory-aadconnect.md)，並使用 Azure AD 驗證。 如此一來，您即可使用單一登入。|
| 需要強制執行多重要素驗證 (MFA)|透過 [Microsoft 條件式存取](sql-database-conditional-access.md)要求 MFA 作為原則，並使用[具有 MFA 支援的 Azure AD 通用驗證](sql-database-ssms-mfa-authentication.md)。|
| 具有來自 Microsoft 帳戶 (live.com、outlook.com) 或其他網域 (gmail.com) 的來賓帳戶|在 SQL Database/資料倉儲中使用 [Azure AD 通用驗證](sql-database-ssms-mfa-authentication.md)，它會利用 [Azure AD B2B 共同作業](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)。|
| 已使用 Azure AD 認證從同盟網域登入 Windows|使用 [Azure AD 整合式驗證](sql-database-aad-authentication-configure.md)。|
| 已使用認證從不與 Azure 同盟的網域登入 Windows|使用 [Azure AD 密碼驗證](sql-database-aad-authentication-configure.md)。|
| 具有必須連線到 Azure SQL Database 或資料倉儲的中介層服務|使用 [Azure AD 權杖驗證](sql-database-aad-authentication-configure.md)。
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>如何才能限制應用程式端存取我資料庫中的敏感性資料？ 

若要防止未經授權的使用者檢視敏感性資料，SQL Database 中有幾個選項可用： 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 是一種用戶端加密形式，可將您資料庫中的敏感資料行加密 (所以位於資料庫管理員和未經授權使用者的加密文字中)。 Always Encrypted 金鑰會儲存在用戶端，因此只有獲得授權的用戶端可以將敏感資料行解密。 Always Encrypted 目前支援相等比較，所以 DBA 可以繼續在其 SQL 命令中查詢加密的資料行。 Always Encrypted 可以搭配各種金鑰存放區選項使用，例如 [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)、Windows 憑證存放區和本機硬體安全性模組。
- [動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)是一項資料遮罩功能，可藉由遮罩處理，讓應用程式層上不具權限的使用者無法看見敏感性資料。 您可以定義遮罩規則，以建立遮罩模式 (例如，只顯示國民身分證的最後 4 位數字並將其餘數字標示為 X)，並識別可以從遮罩規則中排除哪些使用者。
- [資料列層級安全性](/sql/relational-databases/security/row-level-security)可讓您根據執行查詢的使用者 (例如，群組成員資格或執行環境) 來控制資料庫資料表中的資料列存取。 在應用程式層 (而非資料庫層) 上進行存取限制，可簡化您的應用程式邏輯。 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>我擁有 SQL Database 的哪些加密選項，以及加密功能可防禦哪些動作項目？
SQL Database 提供三種主要加密技術： 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (這在上面的問題中提及)：端對端加密資料表中的敏感資料行，從未經授權的用戶端至實體磁碟。 伺服器和資料管理員無法查看敏感性資料，因為加密金鑰存放在用戶端上。 
- [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (TDE)：待用資料加密，可在資料庫層級進行加密，並保護資料檔案、記錄檔，以及實體媒體竊取中相關聯的備份。 預設會對所有新建立的資料庫啟用 TDE。
 
  下圖顯示加密技術選擇概觀。

   ![加密概觀](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>如何管理雲端的加密金鑰？ 
Always Encrypted (用戶端加密) 和透明資料加密 (待用資料加密) 有金鑰管理選項。 建議定期輪替加密金鑰，採用符合內部法規和合規性需求的頻率。

- **Always Encrypted**：Always Encrypted 有[雙重金鑰階層](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) - 敏感性資料的資料行是由 AES 256 資料行加密金鑰 (CEK) 加密，接著由資料行主要金鑰 (CMK) 加密。 針對 Always Encrypted 提供的用戶端驅動程式沒有 CMK 長度限制。

  CEK 的加密值會儲存在資料庫中，而 CMK 會儲存在受信任的金鑰存放區中，例如 Windows 憑證存放區、Azure Key Vault 或硬體安全性模組。 
  
  [CEK 和 CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell)可以輪替。 視含有加密資料行的資料表大小而定，CEK 輪替可能花費大量時間。 因此，請非常小心規劃 CEK 輪替。 另一方面，CMK 輪替不會干擾資料庫效能，可以透過區隔的角色完成。

  下圖顯示 Always Encrypted 中資料行主要金鑰的金鑰存放區選項 

   ![Always Encrypted CMK 存放區提供者](./media/sql-database-manage-after-migration/always-encrypted.png)

- **透明資料加密 (TDE)**：TDE 有雙重金鑰階層 – 每個使用者資料庫中的資料是由對稱 AES-256 資料庫唯一的資料庫加密金鑰 (DEK) 加密，接著由伺服器唯一的非對稱 RSA 2048 主要金鑰加密。 

  根據預設，為了方便起見，透明資料加密的主要金鑰是由 SQL Database 服務管理。 如果您的組織想要控管主要金鑰，有一個選項可將 [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) 當作金鑰存放區。 

  使用 Azure Key Vault，您的組織即可取得金鑰佈建、輪替和權限控制權。 [輪替或切換 TDE 主索引鍵的類型](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation)很快速，因為它只會將 DEK 重新加密。 

  若為區隔安全性與資料管理之間角色的組織，安全性管理員可以為 Azure Key Vault 中的 TDE 主要金鑰佈建金鑰內容，並將 Azure Key Vault 金鑰識別碼提供給資料庫管理員，以便用於在伺服器上進行待用資料加密。 

## <a name="monitoring-and-compliance-after-migration"></a>移轉後的監視與合規性

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>如何在 SQL Database 中監視資料庫活動？
SQL Database 內建一些監視功能，可追蹤資料庫的安全性和其他事件：
- [SQL 稽核](sql-database-auditing.md)可讓您在自有的 Azure 儲存體帳戶中收集資料庫事件的稽核記錄。
- [SQL 威脅偵測](sql-database-threat-detection.md)可讓您偵測可疑活動，並指出存取、破壞或利用資料庫中資料的可能惡意意圖。 SQL Database 威脅偵測會執行多組演算法，以偵測潛在弱點和 SQL 插入式攻擊，以及異常資料庫存取模式 (例如從不尋常的位置存取或由不熟悉的主體存取)。 如果在資料庫上偵測到威脅，安全性人員或其他指定的管理員會收到一封電子郵件通知。 每個通知都會提供可疑活動的詳細資料，以及建議如何進一步調查並減輕威脅。 
- [SQL 漏洞評估](sql-vulnerability-assessment.md)是一項資料庫掃描和報告服務，可讓您大規模監視資料庫的安全性狀態，以及找出安全性風險和您所定義的安全性基準偏離。 每次掃描後，系統會提供自訂的可行步驟和補救指令碼清單，以及可用於協助符合規範的評估報告。 
- [SQL-OMS 安全性同步處理應用程式](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)會利用 Operations Management Suite (OMS) 公用 API，將 SQL 稽核記錄推送到 OMS 中進行記錄分析，而且能夠定義自訂偵測警示，包括： 
 - SQL Database 的 [稽核] 圖格和儀表板，可提供清楚且一致的資料庫活動報告。 
 - SQL Log Analytics，用以分析資料庫活動，並調查可能表示疑似安全性違規的不一致及異常情況。
 - 所觀察事件上的進階警示特定規則，可觸發電子郵、Webhook 和 Azure 自動化 Runbook 警示 (也就是密碼變更、下班、特定 SQL 命令)。
- [Azure 資訊安全中心](../security-center/security-center-intro.md)提供跨越在 Azure、內部部署和其他雲端中執行之工作負載的集中式安全性管理。 您可以檢視是否已在所有資源上設定必要的 SQL Database 保護 (例如稽核和透明資料加密)，並根據自己的需求建立原則。 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>SQL Database 是否符合任何法規需求，以及這對於自己組織的合規性有何幫助？ 
Azure SQL Database 符合各種法規規範。 若要檢視已符合的最新一組規範，請瀏覽 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/compliance/complianceofferings)，並向下切入至對您的組織而言很重要的規範，以查看 Azure SQL Database 是否包含在合規的 Azure 服務之下。 請務必注意，雖然 Azure SQL Database 可能被公認為合規的服務，這有助於您組織的服務合規性，但不自動保證這點。 

## <a name="database-maintenance-and-monitoring-after-migration"></a>移轉後的資料庫維護與監視

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>如何監視資料大小和資源使用率的成長？

- 您可以在 Azure 入口網站中的 [監視] 圖表上，監視資料庫大小和資源使用率的相關計量。 

  ![監視圖表](./media/sql-database-manage-after-migration/monitoring-chart.png)

- 若要更深入了解並向下切入至查詢的詳細資料，您可以使用 Azure 入口網站上可用的 [查詢效能深入解析]。 這會要求您資料庫上的 [查詢存放區] 為作用中狀態。

  ![查詢效能深入解析](./media/sql-database-manage-after-migration/query-performance-insight.png)

- 或者，您也可以使用動態管理檢視 (DMV) 來檢視計量 - 使用 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)。 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>多久需要執行一次一致性檢查，例如 DBCC_CHECKDB？
DBCC_CHECKDB 會檢查資料庫中所有物件的邏輯與實體完整性。 您不需要執行這些檢查，因為這些檢查是由 Microsoft on Azure 管理。 如需詳細資訊，請參閱 [Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>在移轉後監視效能與資源使用率

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>如何在 Azure SQL Database 監視效能和資源使用率？
您可以在 Azure SQL Database 中使用下列方法監視效能與資源使用率：

- **Azure 入口網站**：選取資料庫，然後按一下 [概觀] 窗格中的圖表，Azure 入口網站即可顯示單一資料庫的使用率。 您可以修改此圖表來顯示多種計量，包括 CPU 百分比、DTU 百分比、資料 IO 百分比、工作階段百分比，以及資料庫大小百分比。 
  ![資源使用率](./media/sql-database-manage-after-migration/resource-utilization.png)

  您也可以從這個圖表，依照資源設定警示。 這些警示可讓您透過電子郵件回應資源條件、寫入至 HTTPS/HTTP 端點或執行動作。 如需詳細指示，請參閱[在 Azure SQL Database 中監視資料庫效能](sql-database-single-database-monitor.md)。

- **檢視**：您可以查詢 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 動態管理檢視，以傳回過去一小時的資源耗用量統計資料歷程記錄，而查詢 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 系統目錄檢視，可傳回過去 14 天的歷程記錄。 

- **查詢效能深入解析**：[查詢效能深入解析](sql-database-query-performance.md)可讓您針對特定的資料庫，查看前幾名資源耗用查詢和長時間執行查詢的歷程記錄。 這項功能需要啟用[查詢存放區](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，而且對資料庫有作用。

- **Log Analytics 中的 Azure SQL 分析 (預覽)**：[Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) 可讓您收集和視覺化主要的 Azure SQL Azure 效能計量，每個工作區最多支援 150,000 個 Azure SQL Database 和 5,000 個彈性集區。 您可以使用它來監視和收到通知。 您可以監視跨多個 Azure 訂用帳戶和彈性集區的 Azure SQL Database 和彈性集區計量，並可用來識別應用程式堆疊的各層問題。 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>如何確保我是使用適當的服務層和效能層級？
監視 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 動態管理檢視，以便了解 CPU、I/O 和記憶體耗用量。 您也可以使用 SQL Database 的[查詢效能深入解析](sql-database-query-performance.md)來查看資源耗用量。 如果您始終以高比例的可用資源執行作業，您應該考慮移到現有服務層內較高的效能層級或移到較高的服務層。 相反地，如果您始終使用低比例的可用資源，您可以考慮移到較低的效能層級或服務層。

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>我發現效能問題。 我的 Azure SQL Database 和 SQL Server 疑難排解方法有何不同？
在 Azure SQL Database 中，您的效能疑難排解方法的許多層面仍然相同，但會有一些差異。 例如，如果您發現整體效能降低，請監視 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)動態管理檢視，以便了解 CPU、I/O 和記憶體耗用量。 您可能需要根據工作負載需求來變更效能層級和/或服務層。
如需一組完整的調整效能問題相關建議，請參閱[在 Azure SQL Database 中調整效能](sql-database-performance-guidance.md)。 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>是否需要維護索引和統計資料？
Azure SQL Database 不會自動維護索引和統計資料。 您必須負責安排索引和統計資料的維護。 「Azure 自動化方法」一文會詳細說明幾個針對 Azure SQL Database 安排維護作業的選項。

## <a name="data-movement-after-migration"></a>移轉後的資料移動

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>如何以 BACPAC 檔案的形式從 Azure SQL Database 匯出和匯入資料？ 

- **匯出**：您可以從 Azure 入口網站將 Azure SQL Database 匯出為 BACPAC 檔案。

  ![匯出為 BACPAC](./media/sql-database-export/database-export.png)

- **匯入**：您可以使用 Azure 入口網站以 BACPAC 檔案的形式匯入資料庫。

  ![匯入 BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>如何同步處理 Azure SQL Database SQL Server 2016 / 2012 之間的資料？
[資料同步](sql-database-sync-data.md) 功能可協助您在多個內部部署 SQL Server 資料庫與 Azure SQL Database 之間雙向同步處理資料。 不過，由於這以觸發程序為基礎，所以會保證最終一致性 (不會遺失資料)，但不保證交易一致性。 

## <a name="next-steps"></a>後續步驟
了解 [Azure SQL Database](sql-database-technical-overview.md)。
