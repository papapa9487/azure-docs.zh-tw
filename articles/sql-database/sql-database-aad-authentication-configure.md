---
title: "設定 Azure Active Directory 驗證 - SQL | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 驗證連接到 SQL Database 與 SQL 資料倉儲。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 07/10/2017
ms.author: rickbyh
ms.openlocfilehash: f0c9578217beff22b4a322b363c7499943311d88
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2017
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>使用 SQL Database 或 SQL 資料倉儲設定和管理 Azure Active Directory 驗證

本文說明如何建立和填入 Azure AD，以及搭配 Azure SQL Database 和 SQL 資料倉儲使用 Azure AD。 如需概觀，請參閱 [Azure Active Directory 驗證](sql-database-aad-authentication.md)。

>  [!NOTE]  
>  使用 Azure Active Directory 帳戶不支援連線到 Azure VM 上執行的 SQL Server。 請改用 Active Directory 網域帳戶。

## <a name="create-and-populate-an-azure-ad"></a>建立和填入 Azure AD
建立 Azure AD 並利用使用者和群組填入。 Azure AD 可以是初始 Azure AD 受管理的網域。 Azure AD 也可以是與 Azure AD 同盟的內部部署 Active Directory 網域服務。

如需詳細資訊，請參閱[整合內部部署身分識別與 Azure Active Directory](../active-directory/active-directory-aadconnect.md)、[將您自己的網域名稱新增至 Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)、[Microsoft Azure 現在支援與 Windows Server Active Directory 同盟](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理您的 Azure AD 目錄](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[使用 Windows PowerShell 管理 Azure AD](/powershell/azure/overview?view=azureadps-2.0) 和[混合式身分識別所需的連接埠和通訊協定](../active-directory/active-directory-aadconnect-ports.md)。

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
若要將您的資料庫與貴組織的 Azure AD 目錄產生關聯，請讓目錄成為裝載資料庫之 Azure 訂用帳戶信任的目錄。 如需詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure AD 產生關聯](https://msdn.microsoft.com/library/azure/dn629581.aspx)。

**其他資訊：** 每個 Azure 訂用帳戶都會與 Azure AD 執行個體有信任關係。 這表示它信任該目錄來驗證使用者、服務和裝置。 多個訂用帳戶可以信任相同的目錄，但是一個訂用帳戶只能信任一個目錄。 您可以在 [設定]  索引標籤下的 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)看到訂用帳戶所信任的目錄。 這個訂用帳戶與目錄之間存在的信任關係不同於訂用帳戶與所有其他 Azure 資源 (網站、資料庫等) 之間的關係，後者比較像是訂用帳戶的子資源。 如果訂用帳戶已過期，則也會停止存取與該訂用帳戶相關聯的其他資源。 但目錄會保留在 Azure 中，而且您可以將其他訂用帳戶與該目錄產生關聯，並繼續管理目錄使用者。 如需有關資源的詳細資訊，請參閱 [了解 Azure 中的資源存取](https://msdn.microsoft.com/library/azure/dn584083.aspx)。

下列程序會示範如何變更特定訂用帳戶的相關聯目錄。
1. 使用 Azure 訂用帳戶管理員連接到您的 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。
2. 在左邊的橫幅中，選取 [設定] 。
3. 您的訂用帳戶會出現在 [設定] 畫面中。 如果未出現所需的訂用帳戶，請按一下頂端的 [訂用帳戶]，拉下 [依目錄篩選] 方塊並選取包含您訂用帳戶的目錄，然後按一下 [套用]。
   
    ![select subscription][4]
4. 在 [設定] 區域中，按一下您的訂用帳戶，然後按一下頁面底部的 [編輯目錄]。
   
    ![ad-settings-portal][5]
5. 在 [編輯目錄]  方塊中，選取與您的 SQL Server 或 SQL 資料倉儲相關聯的 Azure Active Directory，然後按一下箭號進行下一步。
   
    ![edit-directory-select][6]
6. 在 [確認目錄對應] 對話方塊中，確認「**將移除所有的共同管理員**」。
   
    ![edit-directory-confirm][7]
7. 按一下核取記號重新載入入口網站。

   > [!NOTE]
   > 當您變更目錄時，所有共同管理員、Azure AD 使用者和群組以及以目錄為基礎之資源使用者的存取權都會被移除，他們將不再有此訂用帳戶或其資源的存取權。 只有具備服務管理員身分的您能夠根據新的目錄設定主體的存取權。 這項變更可能需要相當長的時間才會傳播到所有資源。 變更目錄也會變更 SQL Database 和「SQL 資料倉儲」的 Azure AD 系統管理員，並且不允許任何現有 Azure AD 使用者進行資料庫存取。 必須重設 Azure AD 系統管理員 (如下所述) 且必須建立新的 Azure AD 使用者。
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>建立 Azure SQL 伺服器的 Azure AD 系統管理員
每個 Azure SQL 伺服器 (裝載 SQL Database 或「SQL 資料倉儲」) 一開始都只有一個伺服器系統管理員帳戶，也就是整個 Azure SQL 伺服器的系統管理員。 第二個 SQL Server 系統管理員必須建立，也就是 Azure AD 帳戶。 這個主體會在 master 資料庫中建立為自主資料庫使用者。 身為系統管理員，伺服器系統管理員帳戶是每個使用者資料庫中的 **db_owner** 角色成員，並且會進入每個使用者資料庫做為 **dbo** 使用者。 如需有關伺服器管理員帳戶的詳細資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。

將 Azure Active Directory 與異地複寫搭配使用時，必須為主要和次要伺服器設定 Azure Active Directory 系統管理員。 如果伺服器沒有 Azure Active Directory 系統管理員，則 Azure Active Directory 登入和使用者將會收到「無法連線至伺服器」錯誤。

> [!NOTE]
> 使用者如果不是以 Azure AD 帳戶 (包括 Azure SQL 伺服器系統管理員帳戶) 為基礎，就無法建立以 Azure AD 為基礎的使用者，因為他們不具備向 Azure AD 驗證建議之資料庫使用者的權限。
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>佈建 Azure SQL 伺服器的 Azure Active Directory 系統管理員

下列兩個程序會示範如何在 Azure 入口網站以及使用 PowerShell，佈建 Azure SQL 伺服器的 Azure Active Directory 系統管理員。

### <a name="azure-portal"></a>Azure 入口網站
1. 在 [Azure 入口網站](https://portal.azure.com/)的右上角，按一下您的連線以拉下可能的 Active Directory 清單。 選擇正確的 Active Directory 做為預設 Azure AD。 此步驟利用 Azure SQL 伺服器連結與 Active Directory 相關聯的訂用帳戶，確定 Azure AD 和 SQL Server 使用相同的訂用帳戶。 (Azure SQL 伺服器可以裝載 Azure SQL Database 或 Azure SQL 資料倉儲。)   
    ![choose-ad][8]   
    
2. 在左邊的橫幅中，選取 [SQL Server]，並選取您的 **SQL Server**，然後在 [SQL Server] 刀鋒視窗中按一下 [Active Directory 系統管理員]。   
3. 在 [Active Directory 系統管理員] 刀鋒視窗中，按一下 [設定系統管理員]。   
    ![選取 Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. 在 [新增系統管理員] 刀鋒視窗中，搜尋使用者，選取使用者或群組成為系統管理員，然後按一下 [選取]。 ([Active Directory 系統管理員] 刀鋒視窗會顯示您 Active Directory 的所有成員與群組。 呈現灰色的使用者或群組無法選取，因為他們不受支援成為 Azure AD 系統管理員。 (請在[利用 SQL Database 或 SQL 資料倉儲使用 Azure Active Directory 驗證來驗證](sql-database-aad-authentication.md)的＜Azure AD 功能和限制＞節中參閱支援的系統管理員清單。)以角色為基礎的存取控制 (RBAC) 只會套用至入口網站，並且不會傳播至 SQL Server。   
    ![選取系統管理員](./media/sql-database-aad-authentication/select-admin.png)  
    
5. 在 [Active Directory 系統管理員] 刀鋒視窗頂端，按一下 [儲存]。   
    ![儲存系統管理員](./media/sql-database-aad-authentication/save-admin.png)   

變更系統管理員的程序可能需要幾分鐘的時間。 接著，新的系統管理員就會出現在 [Active Directory 系統管理員]  方塊中。

   > [!NOTE]
   > 設定 Azure AD 系統管理員時，新的系統管理員名稱 (使用者或群組) 不可以已經存在於虛擬主要資料庫中作為 SQL Server 驗證使用者。 如果存在，Azure AD 系統管理員設定將會失敗；其中會復原其建立並指出這樣的系統管理員 (名稱) 已經存在。 由於這類 SQL Server 驗證使用者並非 Azure AD 的成員，因此使用 Azure AD 驗證來連線到伺服器的一切努力都會失敗。
   > 


若要稍後移除系統管理員，請在 [Active Directory 系統管理員] 刀鋒視窗頂端，按一下 [移除系統管理員]，然後按一下 [儲存]。

### <a name="powershell"></a>PowerShell
若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

若要佈建 Azure AD 系統管理員，請執行下列 Azure PowerShell 命令：

* Add-AzureRmAccount
* Select-AzureRmSubscription

用來佈建和管理 Azure AD 系統管理員的 Cmdlet：

| Cmdlet 名稱 | 說明 |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |佈建 Azure SQL 伺服器或 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員 (必須來自目前的訂用帳戶。 ) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |移除 Azure SQL 伺服器或 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員。 |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |傳回目前為 Azure SQL 伺服器或 Azure SQL 資料倉儲設定的 Azure Active Directory 系統管理員的相關資訊。 |

使用 PowerShell 命令 get-help 來查看這當中每個命令的詳細資料，例如 ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``。

下列指令碼會在名為 **Group-23** 的資源群組中，為 **demo_server** 伺服器佈建名為 **DBA_Group** (物件識別碼 `40b79501-b343-44ed-9ce7-da4c8cc7353f`) 的 Azure AD 系統管理員群組：

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 輸入參數可接受 Azure AD 顯示名稱或「使用者主體名稱」。 例如 ``DisplayName="John Smith"`` 和 ``DisplayName="johns@contoso.com"``。 Azure AD 群組只支援 Azure AD 顯示名稱。

> [!NOTE]
> Azure PowerShell 命令 ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` 不會阻止您為不支援的使用者佈建 Azure AD 系統管理員。 您可以佈建不支援的使用者，但是該使用者無法連線到資料庫。 
> 

下列範例使用選用的 **ObjectID**：

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> 當 **DisplayName** 並非唯一時，就需要 Azure AD **ObjectID**。 若要擷取 **ObjectID** 和 **DisplayName** 的值，請使用 Azure 傳統入口網站的 [Active Directory] 區段，然後檢視使用者或群組的屬性。
> 

下列範例會傳回 Azure SQL 伺服器的目前 Azure AD 系統管理員的相關資訊：

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

下列範例會移除 Azure AD 系統管理員：

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

您也可以使用 REST API 來佈建 Azure Active Directory 系統管理員。 如需詳細資訊，請參閱 [Azure SQL Database 之 Azure SQL Database 作業的 Service Management REST API 參考和作業](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>CLI  
您也可以呼叫下列 CLI 命令來佈建 Azure AD 系統管理員：
| 命令 | 說明 |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |佈建 Azure SQL 伺服器或 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員 (必須來自目前的訂用帳戶。 ) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |移除 Azure SQL 伺服器或 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員。 |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |傳回目前為 Azure SQL 伺服器或 Azure SQL 資料倉儲設定的 Azure Active Directory 系統管理員的相關資訊。 |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |更新 Azure SQL 伺服器或 Azure SQL 資料倉儲的 Azure Active Directory 系統管理員。 |

如需 CLI 命令的詳細資訊，請參閱 [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server)。  


## <a name="configure-your-client-computers"></a>設定用戶端電腦
在您的應用程式或使用者使用 Azure AD 身分識別連接到 Azure SQL Database 或 Azure SQL 資料倉儲的所有用戶端電腦上，您必須安裝下列軟體：

* 從 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)安裝 .NET Framework 4.6 或更新版本。
* 從下載中心的**Microsoft Active Directory Authentication Library for Microsoft SQL Server**，可以下載多種語言 (x86 和 amd64) 的 Azure Active Directory Authentication Library for SQL Server ( [ADALSQL.DLL](http://www.microsoft.com/download/details.aspx?id=48742))。

您可以符合這些需求，方法如下︰

* 安裝 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 或 [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) 皆可符合 .NET Framework 4.6 需求。
* SSMS 安裝 x86 版 **ADALSQL.DLL**。
* SSDT 會安裝 amd64 版的 **ADALSQL.DLL**。
* 來自 [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs) 的最新 Visual Studio 符合 .NET Framework 4.6 需求，但不會安裝必要的 amd64 版 **ADALSQL.DLL**。

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者

Azure Active Directory 驗證需要建立資料庫使用者做為自主資料庫使用者。 以 Azure AD 身分識別為基礎的自主資料庫使用者係指在 master 資料庫中沒有登入身分的資料庫使用者，並且此使用者會對應至 Azure AD 目錄中與資料庫關聯的身分識別。 Azure AD 身分識別可以是個別的使用者帳戶或群組。 如需有關自主資料庫使用者的詳細資訊，請參閱 [自主資料庫使用者 - 使資料庫可攜](https://msdn.microsoft.com/library/ff929188.aspx)。

> [!NOTE]
> 您無法使用入口網站來建立資料庫使用者 (系統管理員除外)。 RBAC 角色不會傳播至 SQL Server、SQL Database 或「SQL 資料倉儲」。 Azure RBAC 角色可用來管理 Azure 資源，並不會套用到資料庫權限。 例如，「SQL Server 參與者」  角色不會授與可連線到 SQL Database 或「SQL 資料倉儲」的存取權。 存取權限必須使用 Transact-SQL 陳述式直接在資料庫中授與。
>

若要建立以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請以至少具有 **ALTER ANY USER** 權限的使用者身分，使用 Azure AD 身分識別來連線到資料庫。 然後使用下列的 Transact-SQL 語法：

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

Azure_AD_principal_name 可以是 Azure AD 使用者的使用者主體名稱或 Azure AD 群組的顯示名稱。

**範例：** 建立代表 Azure AD 同盟或受管理網域使用者的自主資料庫使用者：
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

若要建立代表 Azure AD 或同盟網域群組的自主資料庫使用者，請輸入安全性群組的顯示名稱：
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

若要建立代表使用 Azure AD 權杖進行連線之應用程式的自主資料庫使用者︰

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  您無法從 Azure Active Directory 直接建立使用者，除了與您的 Azure 訂用帳戶相關聯的 Azure Active Directory 以外。 不過，在相關聯 Active Directory (稱為外部使用者) 中匯入之使用者的其他 Active Directory 成員可以新增至租用戶 Active Directory 中的 Active Directory 群組。 藉由建立該 AD 群組的自主資料庫使用者，來自外部 Active Directory 的使用者可以存取 SQL Database。   

如需有關根據 Azure Active Directory 身分識別建立自主資料庫使用者的詳細資訊，請參閱 [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)。

> [!NOTE]
> 移除 Azure SQL 伺服器的 Azure Active Directory 系統管理員可防止任何 Azure AD 驗證使用者連線到伺服器。 必要時，SQL Database 系統管理員可以手動刪除無法使用的 Azure AD 使用者。   

>  [!NOTE]
>  如果您收到**連線逾時過期**，您可能需要將 `TransparentNetworkIPResolution` 連接字串的參數設定為 false。 如需詳細資訊，請參閱 [.NET Framework 4.6.1 的連線逾時問題 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)。   

   
當您建立資料庫使用者時，該使用者會獲得 **CONNECT** 權限，而可以用 **PUBLIC** 角色的成員身分連線到該資料庫。 一開始提供給使用者的權限僅限於已授與 **PUBLIC** 角色的任何權限，或已授與其所屬任何 Azure AD 群組的任何權限。 一旦您佈建以 Azure AD 為基礎的自主資料庫使用者，您可以使用您授與權限給任何其他類型使用者的相同方式，授與該使用者額外的權限。 通常會授與權限給資料庫角色並新增使用者至角色。 如需詳細資訊，請參閱 [Database Engine 權限基本概念](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 如需有關特殊 SQL Database 角色的詳細資訊，請參閱 [管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。
以外部使用者身分匯入至管理網域的同盟網域使用者帳戶必須使用受管理的網域身分識別。

> [!NOTE]
> Azure AD 使用者會在資料庫中繼資料中標示為類型 E (EXTERNAL_USER)，而群組則標示為類型 X (EXTERNAL_GROUPS)。 如需詳細資訊，請參閱 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)。 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>使用 SSMS 或 SSDT 連線至使用者資料庫或資料倉儲  
若要確認 Azure AD 系統管理員已正確設定，請使用 Azure AD 系統管理員帳戶連接到 **master** 資料庫。
若要佈建以 Azure AD 為基礎的自主資料庫使用者 (而非擁有資料庫的伺服器系統管理員)，請利用有權存取資料庫的 Azure AD 身分識別連線到資料庫。

> [!IMPORTANT]
> Visual Studio 2015 中的 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 和 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) 提供 Azure Active Directory 驗證支援。 SSMS 的 2016 年 8 月版本也支援 Active Directory 通用驗證，讓系統管理員能夠使用電話、簡訊、含有 PIN 的智慧卡或行動應用程式通知來要求 Multi-Factor Authentication。
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>使用 Azure AD 身分識別以使用 SSMS 或 SSDT 進行連線  

下列程序會示範如何使用 SQL Server Management Studio 或 SQL Server 資料庫工具的 Azure AD 身分連接到 SQL 資料庫。

### <a name="active-directory-integrated-authentication"></a>Active Directory 整合驗證

如果您已使用 Azure Active Directory 認證從同盟網域登入 Windows，請使用這個方法。

1. 啟動 Management Studio 或 Data Tools，並在 [連線到伺服器] \(或 [連線到 Database Engine]) 對話方塊的 [驗證] 方塊中，選取 [Active Directory - 整合式]。 不需要密碼或沒有密碼可輸入，因為現有的認證將會在連接時出現。   

    ![選取 AD 整合式驗證][11]
2. 按一下 [選項] 按鈕，然後在 [連接屬性] 頁面的 [連接到資料庫] 方塊中，輸入您想要連線的使用者資料庫名稱。 ([AD 網域名稱或租用戶 ID] 選項僅對 [通用驗證搭配 MFA 連線] 選項提供支援，否則會呈現灰色。)  

    ![選取資料庫名稱][13]

## <a name="active-directory-password-authentication"></a>Active Directory 密碼驗證

使用 Azure AD 受管理網域連接到 Azure AD 主體名稱時，請使用這個方法。 您也可以將其用於沒有網域存取權的同盟帳戶，例如在遠端運作時。

如果您使用認證從未與 Azure 建立同盟的網域登入 Windows，或在使用 Azure AD 驗證時使用以初始或用戶端網域為基礎的 Azure AD，請使用這個方法。

1. 啟動 Management Studio 或 Data Tools，並在 [連線到伺服器] \(或 [連線到 Database Engine]) 對話方塊的 [驗證] 方塊中，選取 [Active Directory - 密碼]。
2. 在 [使用者名稱] 方塊中，以 **username@domain.com** 格式輸入您的 Azure Active Directory 使用者名稱。這必須是來自 Azure Active Directory 的帳戶或來自與 Azure Active Directory 建立同盟之網域的帳戶。
3. 在 [密碼]  方塊中，輸入您的 Azure Active Directory 帳戶或同盟網域帳戶的使用者密碼。

    ![選取 AD 密碼驗證][12]
4. 按一下 [選項] 按鈕，然後在 [連接屬性] 頁面的 [連接到資料庫] 方塊中，輸入您想要連線的使用者資料庫名稱。 (請參閱上一個選項中的圖形。)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>從用戶端應用程式使用 Azure AD 身分識別來連接

下列程序示範如何從用戶端應用程式使用 Azure AD 身分識別連接到 SQL 資料庫。

###  <a name="active-directory-integrated-authentication"></a>Active Directory 整合驗證

若要使用整合式 Windows 驗證，您網域的 Active Directory 必須與 Azure Active Directory 建立同盟關係。 連接到資料庫的用戶端應用程式 (或服務) 必須以使用者的網域認證在已加入網域的電腦上執行。

若要使用整合式驗證以及 Azure AD 身分識別連接至資料庫，資料庫連接字串中的驗證關鍵字必須設定為 Active Directory 整合式。 下列 C# 程式碼範例會使用 ADO.NET。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

不支援使用連接字串關鍵字 ``Integrated Security=True`` 來連接到 Azure SQL Database。 建立 ODBC 連接時，您必須移除空格，並將「驗證」設為 'ActiveDirectoryIntegrated'。

### <a name="active-directory-password-authentication"></a>Active Directory 密碼驗證

若要使用整合式驗證和 Azure AD 身分識別來連接到資料庫，Authentication 關鍵字就必須設定為 Active Directory Password。 連接字串必須包含使用者識別碼 (UID) 及密碼 (PWD) 關鍵字和值。 下列 C# 程式碼範例會使用 ADO.NET。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

深入了解使用 [Azure AD 驗證 GitHub 示範](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)上所提供之示範程式碼範例的 Azure AD 驗證方法。

## <a name="azure-ad-token"></a>Azure AD 權杖
這種驗證方法可以從 Azure Active Directory (AAD) 取得權杖，讓中介層服務連接到 Azure SQL Database 或 Azure SQL 資料倉儲。 這可容許包含憑證型驗證的複雜案例。 您必須完成四個基本步驟，才能使用 Azure AD 權杖驗證︰

1. 向 Azure Active Directory 註冊您的應用程式，並取得程式碼的用戶端識別碼。 
2. 建立代表應用程式的資料庫使用者。 (稍早在步驟 6 中已完成)。
3. 在執行應用程式的用戶端電腦上建立憑證。
4. 將憑證加入應用程式當做索引鍵。

範例連接字串︰

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

如需詳細資訊，請參閱 [SQL Server 安全性部落格](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)。 如需新增憑證的詳細資訊，請參閱[開始在 Azure Active Directory 中使用憑證式驗證](../active-directory/active-directory-certificate-based-authentication-get-started.md)。

### <a name="sqlcmd"></a>sqlcmd

下列陳述式中使用 sqlcmd 13.1 進行連線，從 [下載中心](http://go.microsoft.com/fwlink/?LinkID=825643)即可取得此版本。

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>後續步驟
- 如需 SQL Database 中存取權和控制權的概觀，請參閱 [SQL Database 的存取權和控制權](sql-database-control-access.md)。
- 如需 SQL Database 中登入、使用者和資料庫角色的概觀，請參閱[登入、使用者和資料庫角色](sql-database-manage-logins.md)。
- 如需資料庫主體的詳細資訊，請參閱[主體](https://msdn.microsoft.com/library/ms181127.aspx)。
- 如需資料庫角色的詳細資訊，請參閱[資料庫角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 如需 SQL Database 中防火牆規則的詳細資訊，請參閱 [SQL Database 防火牆規則](sql-database-firewall-configure.md)。

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

