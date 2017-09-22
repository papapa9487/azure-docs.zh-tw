---
title: "在 Azure Stack 上使用 SQL 資料庫 | Microsoft Docs"
description: "了解如何將 SQL 資料庫部署為 Azure Stack 上的服務，以及了解部署 SQL Server 資源提供者配接器的快速步驟"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: JeffGo
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 018d556d52aa1a1f436460d9811c43f9b45bd440
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

# <a name="use-sql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 SQL 資料庫


使用 SQL Server 資源提供者配接器，將 SQL 資料庫公開為 Azure Stack 的服務。 在您安裝資源提供者，並將它連線到一或多個 SQL Server 執行個體之後，您和您的使用者就可以為雲端原生應用程式、以 SQL 為基礎的網站，以及以 SQL 為基礎的工作流程建立資料庫，不需要每次佈建主控 SQL Server 的虛擬機器 (VM)。

資源提供者並未支援 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 的所有資料庫管理功能。 例如，不支援彈性資料庫集區和調整資料庫效能的功能。 API 與 SQL DB 不相容。


## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server 資源提供者配接器架構
資源提供者並非以 Azure SQL Database 為基礎，也不提供 Azure SQL Database 的所有資料庫管理功能。 例如，不提供彈性資料庫集區和自動提高及降低資料庫效能的功能。 但是，資源提供者支援類似的建立、讀取、更新及刪除 (CRUD) 作業。

資源提供者由三個元件組成：

- **SQL 資源提供者配接器 VM**，這是執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，它會處理佈建要求並公開資料庫資源。
- **主控 SQL Server 的伺服器**，它提供資料庫的容量，稱為主控伺服器。 

此版本不會再建立 SQL 執行個體。 您必須建立一個 (或多個) 和/或提供外部 SQL 執行個體的存取權。 有一些選項可供您使用，包括 [Azure Stack 快速入門資源庫](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) (\英文\) 中的範本和 Marketplace 項目。 

>[!NOTE]
如果已下載任何 SQL Marketplace 項目，請確定您也同時下載了 SQL IaaS 延伸模組，否則將不會部署它們。


## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並下載 Windows Server 2016 評估版映像 (可透過 Marketplace 管理下載)。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 不再需要 .NET 3.5 執行階段。

2. [下載 SQL 資源提供者二進位檔案](https://aka.ms/azurestacksqlrp)，並在開發套件主機上解壓縮該檔案。

3. 登入開發套件主機，並將 SQL RP 安裝程式檔案解壓縮到暫存目錄。

4. 系統會在此程序的執行過程中，擷取 Azure Stack 根憑證並建立自我簽署憑證。 

    __選擇性：__如果需要提供自己的憑證，請準備好憑證並將它複製到本機目錄 (如果您想要自訂憑證以傳遞到安裝指令碼)。 您需要下列憑證︰

    a. *.dbadapter.\<地區\>.\<外部 FQDN\>的萬用字元憑證。 此憑證必須受到信任，例如由憑證授權單位簽發 (也就是信任鏈結必須存在而不需要中繼憑證。) (單一站台憑證可搭配您在安裝期間提供的明確 VM 名稱使用。)

    b. Azure Resource Manager 為您的 Azure Stack 執行個體所使用的根憑證。 如果找不到，則將擷取根憑證。


5. 開啟**新的**已提升權限的 PowerShell 主控台，並變更至解壓縮檔案所在的目錄。 使用新視窗，避免因為系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. 如果已安裝 1.2.9 或 1.2.10 以外之任何版本的 AzureRm 或 AzureStack PowerShell 模組，系統會提示您將它們移除，否則不會繼續安裝。 這包括 1.3 或更高版本。

7. 請搭配下方列出的參數執行 DeploySqlProvider.ps1 指令碼。

指令碼會執行這些步驟：

* 如有必要，請下載相容版本的 Azure PowerShell。
* 將憑證和其他成品上傳到您 Azure Stack 上的儲存體帳戶。
* 發佈資源庫套件，以便您可以透過資源庫部署 SQL 資料庫。
* 使用在步驟 1 中建立的 Windows Server 2016 映像部署 VM，並安裝資源提供者。
* 註冊對應到您資源提供者 VM 的本機 DNS 記錄。
* 向本機 Azure Resource Manager 註冊您的資源提供者 (租用戶和管理員)。

> [!NOTE]
> 如果安裝時間超過 90 分鐘，表示安裝可能失敗，且您會在畫面上和記錄檔中看到失敗訊息，但會從失敗的步驟重新嘗試執行部署作業。 不符合建議的記憶體及核心規格的系統可能無法部署 SQL RP。
>

以下是可從 PowerShell 提示字元執行的範例 (視需要變更帳戶資訊和入口網站端點)：

```
# Install the AzureRM.Bootstrapper module
Install-Module -Name AzureRm.BootStrapper -Force

# Installs and imports the API Version Profile required by Azure Stack into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.10 -Force

# Download the Azure Stack Tools from GitHub and set the environment
cd c:\
Invoke-Webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip
Expand-Archive master.zip -DestinationPath . -Force

# This endpoint may be different for your installation
Import-Module C:\AzureStack-Tools-master\Connect\AzureStack.Connect.psm1
Add-AzureRmEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external" 

# For AAD, use the following
$tenantID = Get-AzsDirectoryTenantID -AADTenantName "<your directory name>" -EnvironmentName AzureStackAdmin

# For ADFS, replace the previous line with
# $tenantID = Get-AzsDirectoryTenantID -ADFS -EnvironmentName AzureStackAdmin

$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeploySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "SqlRPRG" -VmName "SqlVM" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 參數
您可以在命令列中指定這些參數。 如果未這麼做，或任何參數驗證失敗，系統會提示您提供必要參數。

| 參數名稱 | 描述 | 註解或預設值 |
| --- | --- | --- |
| **DirectoryTenantID** | Azure 或 ADFS Directory 識別碼 (GUID)。 | _必要_ |
| **AzCredential** | 提供 Azure Stack 服務管理帳戶的認證。 您必須使用與部署 Azure Stack 時所使用之認證相同的認證)。 | _必要_ |
| **VMLocalCredential** | 定義 SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 此密碼也會用於 SQL **sa** 帳戶。 | _必要_ |
| **ResourceGroupName** | 定義資源群組的名稱，此指令碼所建立的項目將儲存在此資源群組中。 例如，*SqlRPRG*。 |  _必要_ |
| **VmName** | 定義虛擬機器的名稱，資源提供者將安裝在此虛擬機器上。 例如，*SqlVM*。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證檔案也必須放在這個目錄中。 | _選用_ |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼 | _必要_ |
| **MaxRetryCount** | 定義作業失敗時重試每個作業的次數。| 2 |
| **RetryDuration** | 定義重試之間的逾時 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註) | 否 |
| **DebugMode** | 防止在失敗時自動清除 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站確認部署是否成功

> [!NOTE]
>  在安裝指令碼完成之後，將需要重新整理入口網站才會看到管理刀鋒視窗。


1. 在主控台 VM 桌面上，按一下 [Microsoft Azure Stack 入口網站]，然後以服務管理員身分登入入口網站。

2. 確認部署成功。 按一下 [資源群組] &gt; 按一下您使用的資源群組，然後確定刀鋒視窗的程式集部分 (上半部) 顯示「_日期_ (成功」**)**。

      ![確認 SQL RP 的部署是否成功](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="provide-capacity-by-connecting-to-a-hosting-sql-server"></a>透過連線到主控 SQL 伺服器以提供容量

1. 以服務管理員身分登入 Azure Stack 管理入口網站

2. 建立 SQL 虛擬機器 (除非您已經有 SQL 虛擬機器)。 「Marketplace 管理」提供一些用於部署 SQL VM 的選項。

3. 按一下 [資源提供者] &gt;[SQLAdapter] &gt;[主控伺服器] &gt;[+新增]。

    您可以使用 [SQL 主控伺服器] 刀鋒視窗，將 SQL Server 資源提供者連線到作為資源提供者後端的 SQL Server 實際執行個體。

    ![主控伺服器](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.PNG)

4. 在表單中填寫 SQL Server 執行個體的連線詳細資料。

    ![新的主控伺服器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.PNG)

    > [!NOTE]
    > 只要租用戶和管理 Azure Resource Manager 可以存取 SQL 執行個體，資源提供者就可以控制它。 SQL 執行個體__必須__配置為 RP 專屬。

5. 新增伺服器時，必須將它們指派給新的或現有的 SKU，以將服務產品差異化。 例如，您可以擁有提供資料庫容量和自動備份功能的 SQL 企業執行個體、保留高效能的伺服器供個別部門使用等等。SKU 名稱應反映屬性，讓租用戶可適當地安置其資料庫，且 SKU 中的所有主控伺服器都應具有相同的功能。

    例如：

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。


## <a name="create-your-first-sql-database-to-test-your-deployment"></a>建立第一個 SQL 資料庫以測試您的部署

1. 以服務管理員身分登入 Azure Stack 管理入口網站。

2. 按一下 [+新增] &gt;[資料+儲存體] &gt;[SQL Server 資料庫 (預覽)] &gt;[新增]。

3. 在表單中填入資料庫詳細資料，包括**資料庫名稱**、**大小上限**，並視需要變更其他參數。 系統會要求您為資料庫挑選一個 SKU。 新增主控伺服器時，系統會指派一個 SKU 給它們，並且會在構成 SKU 之主控伺服器的該集區中建立資料庫。

    ![新資料庫](./media/azure-stack-sql-rp-deploy/newsqldb.png)


4. 填入登入設定：[資料庫登入] 和 [密碼]。 這是僅為供您存取此資料庫而建立的 SQL 驗證認證。 此登入使用者名稱必須是全域唯一的。 建立新的登入設定或選取現有的設定。 您可以將登入設定重複用於其他使用相同 SKU 的資料庫。

    ![建立新的資料庫登入](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. 提交表單並等候部署完成。

    請在產生的刀鋒視窗中注意 [連接字串] 欄位。 您可以在 Azure Stack 中，於需要 SQL Server 存取權的任何應用程式 (例如 Web 應用程式) 中使用該字串。

    ![擷取連接字串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="add-capacity"></a>新增容量

透過在 Azure Stack 入口網站中新增額外的 SQL 主機來新增容量，並將它們與適當的 SKU 關聯。 如果不想使用提供者 VM 上安裝的執行個體，而想要使用其他 SQL 執行個體，請按一下 [資源提供者] &gt; [SQLAdapter] &gt; [SQL 主控伺服器] &gt; [+新增]。

## <a name="making-sql-databases-available-to-tenants"></a>將 SQL 資料庫提供給租用戶使用

建立方案和供應項目，將 SQL 資料庫提供給租用戶使用。 您必須建立方案、將 Microsoft.SqlAdapter 服務加到方案，並新增現有的配額或建立新的配額。 如果建立配額，就可以指定容量以允許租用戶使用。
    ![建立方案和供應項目以包含資料庫](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="tenant-usage-of-the-resource-provider"></a>資源提供者的租用戶使用量

自助服務資料庫是透過租用戶入口網站體驗所提供的。

## <a name="removing-the-sql-adapter-resource-provider"></a>移除 SQL 配接器資源提供者

若要移除資源提供者，必須先移除任何相依性。

1. 確認您有為此資源提供者版本下載的原始部署套件。

2. 必須從資源提供者刪除所有租用戶資料庫 (這不會刪除資料)。 必須由租用戶本身執行此作業。

3. 系統管理員必須從 SQL 配接器刪除主控伺服器

4. 系統管理員必須刪除參照 SQL 配接器的任何方案。

5. 系統管理員必須刪除與 SQL 配接器關聯的任何 SKU 和配額。

6. 使用 -Uninstall 參數、Azure Resource Manager 端點、DirectoryTenantID 和服務管理員帳戶的認證，重新執行部署指令碼。



## <a name="next-steps"></a>後續步驟


嘗試其他 [PaaS 服務](azure-stack-tools-paas-services.md)，如 [MySQL Server 資源提供者](azure-stack-mysql-resource-provider-deploy.md)和[應用程式服務資源提供者](azure-stack-app-service-overview.md)。

