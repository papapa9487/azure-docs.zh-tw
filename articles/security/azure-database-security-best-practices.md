---
title: "Azure 資料庫安全性最佳做法 | Microsoft Docs"
description: "本文章提供一組 Azure 資料庫安全性的最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: b3a9befe5c6607c108e11b583f8b67c483710021
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="azure-database-security-best-practices"></a>Azure 資料庫安全性最佳做法

安全性是管理資料庫時的最重要考量，而且向來一直是 Azure SQL Database 的優先考量。 您的資料庫可嚴加保護，有助於符合大多數法規或安全性需求，包括 HIPAA、ISO 27001/27002 和 PCI DSS Level 1 等等。 [Microsoft 信任中心網站](http://azure.microsoft.com/support/trust-center/services/)提供目前的安全性合規性認證清單。 您也可以法規要求作為基礎，選擇將資料庫放在特定的 Azure 資料中心。

本文將討論 Azure 資料庫安全性最佳做法的集合。 這些最佳做法衍生自我們的 Azure 資料庫安全性以及如您本身的客戶體驗。

針對每個最佳做法，我們會說明︰

-   最佳作法是什麼
-   您為何想要啟用該最佳作法
-   如果無法啟用最佳作法，結果可能為何
-   如何學習啟用最佳作法

這篇「Azure 資料庫安全性最佳做法」是以共識意見以及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 作為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 資料庫安全性最佳做法包括︰

-   使用防火牆規則來限制資料庫存取
-   啟用資料庫驗證
-   使用加密來保護您的資料
-   保護傳輸中的資料
-   啟用資料庫稽核
-   啟用資料庫威脅偵測


## <a name="use-firewall-rules-to-restrict-database-access"></a>使用防火牆規則來限制資料庫存取

Microsoft Azure SQL Database 為 Azure 和其他網際網路式應用程式提供關聯式資料庫服務。 為了提供存取安全性，SQL Database 會透過以下機制來控制存取：依 IP 位址限制連線能力的防火牆規則、要求使用者證明其身分識別的驗證機制，以及將使用者限制在特定動作和資料的授權機制。 防火牆會防止對您資料庫伺服器的所有存取，直到您指定哪些電腦擁有權限。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。

![防火牆規則](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。 若要從您的電腦存取 SQL Database，請務必確認您的用戶端電腦防火牆允許 TCP 連接埠 1433 上的傳出 TCP 通訊。 如果其他應用程式不需要，請使用防火牆規則來封鎖 TCP 通訊埠 1433 的傳入連線。

連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。 連接埠號碼的範圍從 11000 到 11999。 如需 TCP 連接埠的詳細資訊，請參閱[適用於 ADO.NET 4.5 及 SQL Database 的 1433 以外的連接埠](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)。

> [!Note]
> 如需 SQL Database 中防火牆規則的詳細資訊，請參閱 [SQL Database 防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

## <a name="enable-database-authentication"></a>啟用資料庫驗證
SQL Database 支援兩種類型的驗證，SQL 驗證和 Azure Active Directory 驗證 (Azure AD 驗證)。

下列情況下，建議使用 **SQL 驗證**：

-   它可讓 SQL Azure 支援具有混合作業系統的環境，其中所有使用者都未獲得 Windows 網域驗證。
-   允許 SQL Azure 支援舊版的應用程式，以及需要 SQL Server 驗證之第三方所提供的應用程式。
-   可讓使用者從未知或未受信任的網域進行連線。 例如，可供既有客戶與指派之 SQL Server 登入連線，以接收訂單狀態的應用程式。
-   允許 SQL Azure 支援 Web 架構應用程式，讓使用者可在其中建立自己的識別。
-   可讓軟體開發人員散發其應用程式，方法是使用以已知預設的 SQL Server 登入作為基礎的複雜權限階層。

> [!Note]
> 不過，SQL Server 驗證無法使用 Kerberos 安全性通訊協定。

如果您是使用 **SQL 驗證**，就必須：

-   自行管理強式認證。
-   保護連接字串中的認證。
-   (可能) 保護透過網路從 Web 伺服器傳遞至資料庫的認證。 如需詳細資訊，請參閱[如何：使用 ASP.NET 2.0 中的 SQL 驗證連線到 SQL Server](https://msdn.microsoft.com/library/ms998300.aspx)。

**Azure Active Directory 驗證**是 Azure Active Directory (Azure AD) 中使用身分識別連線到 Microsoft Azure SQL Database 和 [SQL 資料倉儲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)的機制。 您可以使用 Azure AD 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央識別碼管理提供單一位置以管理資料庫使用者並簡化權限管理。 包括以下優點：

-   它提供 SQL Server 驗證的替代方案。
-   協助停止跨資料庫伺服器使用過多的使用者身分識別。
-   允許在單一位置變換密碼。
-   客戶可以管理使用外部 (AAD) 群組的資料庫權限。
-   它可以藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
-   Azure AD 驗證會使用自主資料庫使用者，在資料庫層級驗證身分。
-   Azure AD 針對連線到 SQL Database 的應用程式支援權杖型驗證。
-   Azure AD 驗證本機 Azure Active Directory 的 ADFS (網域同盟) 或原生使用者/密碼驗證，而不需進行網域同步處理。
-   Azure AD 支援來自 SQL Server Management Studio 的連線，其中使用包含 Multi-Factor Authentication (MFA) 的 Active Directory 通用驗證。 MFA 包含增強式驗證功能，其中提供一系列簡易的驗證選項，例如電話、簡訊、含有 PIN 的智慧卡或行動應用程式通知。 如需詳細資訊，請參閱 [適用於與 SQL Database 和 SQL 資料倉儲搭配使用之 Azure AD MFA 的 SSMS 支援](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)。

設定步驟包括以下設定和使用 Azure Active Directory 驗證的程序。

-   建立和填入 Azure AD。
-   選用：和目前與您 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更。
-   建立 Azure SQL 伺服器或 [Azure SQL 資料倉儲](https://azure.microsoft.com/services/sql-data-warehouse/)的 Azure Active Directory 系統管理員。
- 設定用戶端電腦。
-   在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者。
-   使用 Azure AD 身分識別連接到您的資料庫。

您可以在[這裡](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)找到詳細資訊。

## <a name="protect-your-data-using-encryption"></a>使用加密來保護您的資料

[Azure SQL Database 的透明資料加密 (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。 TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。

即使整個儲存體都已加密，也一定要您的資料庫本身加密。 這是資料保護的深度防禦方法實作。 如果您是使用 Azure SQL Database，而且需要保護敏感性資料 (例如信用卡或身分證號碼)，可以使用 FIPS 140-2 驗證的 256 位元 AES 加密來加密資料庫，其符合許多產業標準 (例如 HIPAA、PCI) 的需求。

請務必了解使用 TDE 加密資料庫時，[緩衝集區延伸 (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) 相關檔案並不會加密。 您必須對 BPE 相關檔案使用檔案系統等級的加密工具，像是 [BitLocker](https://technet.microsoft.com/library/cc732774) 或 [加密檔案系統 (EFS)](https://technet.microsoft.com/library/cc700811.aspx)。

因為經過授權的使用者 (如安全性系統管理員或資料庫管理員) 可以存取資料，所以即使已使用 TDE 將資料庫加密，您也應該遵循下列建議︰

-   啟用資料庫等級的 SQL 驗證。
-   使用 [RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)的 Azure AD 驗證。
-   使用者和應用程式應使用不同的帳戶進行驗證。 如此一來，您可以限制授與使用者和應用程式的權限，並降低惡意活動的風險。
-   使用固定的資料庫角色 (例如 db_datareader 或 db_datawriter) 實作資料庫等級安全性，或者您可以為應用程式建立自訂角色，以授與明確的權限給選取的資料庫物件。

如需其他的資料加密方式，請考慮：

-   [儲存格層級加密](https://msdn.microsoft.com/library/ms179331.aspx) ，可利用不同的加密金鑰來加密特定的資料行或甚至是資料儲存格。
-   使用 Always Encrypted 的使用中加密：[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) 可讓用戶端在用戶端應用程式中將敏感性資料加密，且永遠不會對資料庫引擎 (SQL Database 或 SQL Server) 顯示加密金鑰。 因此，Always Encrypted 可將資料擁有者 (和可檢視者) 及資料管理者 (但無法存取資料) 分隔開來。
-   使用資料列等級安全性：資料列等級安全性讓客戶能夠以執行查詢之使用者的特性 (例如，群組成員資格或執行內容) 作為基礎，來控制資料庫資料表中的資料列存取。 如需詳細資訊，請參閱[資料列層級安全性](https://msdn.microsoft.com/library/dn765131)。

## <a name="protect-data-in-transit"></a>保護傳輸中的資料
保護傳輸中的資料應該是您的資料保護策略中不可或缺的部分。 由於資料會從許多位置來回移動，一般會建議您一律使用 SSL/TLS 通訊協定來交換不同位置的資料。 在某些情況下，建議您使用虛擬私人網路 (VPN)，隔離您的內部部署與雲端基礎結構之間的整個通訊通道。

對於在內部部署基礎結構與 Azure 之間移動的資料，您應該考慮適當的防護措施，例如 HTTPS 或 VPN。

對於需要從位於內部部署的多個工作站安全存取 Azure 的組織而言，請使用 [Azure 站對站 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create)。

對於需要從位於內部部署或外部部署的個別工作站安全存取 Azure 的組織而言，請考慮使用[點對站 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create)。

較大的資料集可以透過專用的高速 WAN 連結 (例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)) 移動。 如果您選擇使用 ExpressRoute，您也可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他通訊協定，在應用程式層級加密資料，以提供額外的保護。

如果您透過 Azure 入口網站與 Azure 儲存體互動，則所有交易都會透過 HTTPS 發生。 透過 HTTPS 的[儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 也可用來與 [Azure 儲存體](https://azure.microsoft.com/services/storage/)和 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 互動。

無法保護傳輸中資料的組織比較容易遭受[攔截攻擊](https://technet.microsoft.com/library/gg195821.aspx)、[竊聽](https://technet.microsoft.com/library/gg195641.aspx)及工作階段攔截。 這些攻擊可能是取得機密資料存取權的第一步。

若要深入了解 Azure VPN 選項，請閱讀[規劃與設計 VPN 閘道](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design)一文。

## <a name="enable-database-auditing"></a>啟用資料庫稽核
稽核 SQL Server 資料庫引擎或個別資料庫的執行個體，會牽涉到追蹤和記錄資料庫引擎中所發生之事件。 SQL Server 稽核可讓您建立伺服器稽核，其中可能包含伺服器等級事件的伺服器稽核規格，以及資料庫等級事件的資料庫稽核規格。 稽核的事件可以寫入事件記錄或稽核檔案。

根據管制或安裝的標準需求而定，會有數個 SQL Server 的稽核等級。 您必須擁有 SQL Server 稽核提供的工具和程序，才能啟用、儲存及檢視各種伺服器和資料庫物件上的稽核。

[Azure SQL Database 稽核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)會追蹤資料庫事件，並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。

稽核可協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

稽核會啟用及推動遵循法規標準，但不保證符合法規。

若要深入了解資料庫稽核以及如何加以啟用，請閱讀[在 Azure 資訊安全中心的 SQL Server 上啟用稽核與威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)一文。

## <a name="enable-database-threat-detection"></a>啟用資料庫威脅偵測
SQL 威脅偵測可讓您在發生異常活動時提供安全性警訊，讓客戶偵測並回應潛在威脅。 一旦有可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式發生時，您將會收到警示。 SQL 威脅偵測警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。

例如，SQL 插入式攻擊是網際網路上常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，破壞或修改資料庫中的資料。

若要了解如何使用 Azure 入口網站為資料庫設定威脅偵測，請參閱 [SQL Database 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。

此外，SQL 威脅偵測將自有的警示與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合。 敬邀您免費試用 60 天。

若要深入了解資料庫威脅偵測以及如何加以啟用，請閱讀[在 Azure 資訊安全中心的 SQL Server 上啟用稽核與威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)一文。

## <a name="conclusion"></a>結論
Azure 資料庫是強固的資料庫平台，具有完整的安全性功能，能符合許多組織及法務相容性需求。 您可以協助保護資料，方法是控制實體存取您的資料，以及透過透明資料加密、資料格等級加密或資料列等級安全性，使用檔案、資料行或資料列等級的各種資料安全性選項。 Always Encrypted 也會啟用針對加密資料的作業，從而簡化應用程式更新的程序。 接著，存取 SQL Database 活動的稽核記錄可為您提供所需要的資訊，讓您知道存取資料的方式及時間。

## <a name="next-steps"></a>後續步驟
- 若要深入了解防火牆規則，請參閱[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。
- 若要了解使用者和登入，請參閱[管理登入](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。
- 如需教學課程，請參閱[保護 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)。
