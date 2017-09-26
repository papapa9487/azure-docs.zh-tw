---
title: "在 Azure Stack 上使用 MySQL 資料庫做為 PaaS | Microsoft Docs"
description: "瞭解如何部署 MySQL 資源提供者，並提供 MySQL 資料庫作為 Azure Stack 上的服務"
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
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 45c7edcc645e82107805b3e62d87655a830fb22a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 MySQL 資料庫


您可以在 Azure Stack 上部署 MySQL 資源提供者。 部署資源提供者後，您可以透過 Azure Resource Manager 部署範本建立 MySQL 伺服器和資料庫，並提供 MySQL 資料庫即服務。 網站上常用的 MySQL 資料庫支援許多網站平台。 例如，部署資源提供者後，您可以從 Azure Stack 的 Azure Web Apps 平台即服務 (PaaS) 附加元件中建立 WordPress 網站。

若要在沒有網際網路存取權的系統上部署 MySQL 提供者，您可以將 [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) 檔複製到本機共用，並在系統提示時提供該共用名稱 (請參閱下文)。 您也必須安裝 Azure 和 Azure Stack PowerShell 模組。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 資源提供者介面卡架構

資源提供者由三個元件組成：

- **MySQL 資源提供者介面卡 VM**，為執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，其處理佈建要求並公開資料庫資源。
- **主控 MySQL Server 的伺服器**，其提供資料庫的容量，稱為主控伺服器。 

此版本不會再建立 MySQL 執行個體。 您必須建立它們和/或提供外部 SQL 執行個體的存取權。 您可以造訪 [Azure Stack 快速入門圖庫](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)，取得範本範例來建立 MySQL 伺服器，或從 Marketplace下載並部署 MySQL Server。

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並下載 Windows Server 2016 Datacenter - 可透過 Marketplace 管理下載 Eval 映像。 您也可以使用指令碼來建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)。

2. [下載 MySQL 資源提供者二進位檔案](https://aka.ms/azurestackmysqlrp)，並在開發套件主機上解壓縮該檔案。

3. 登入開發套件主機，並將 MySQL RP 安裝程式檔案解壓縮至暫存目錄。

4. 擷取 Azure Stack 根憑證，並在此程序的過程中建立自我簽署憑證。 

    __選擇性：__如果需要提供自己的憑證，請準備好憑證並將它複製到本機目錄 (如果您想要自訂憑證以傳遞到安裝指令碼)。 您需要下列項目：

    a. *.dbadapter.\<地區\>.\<外部 FQDN\>的萬用字元憑證。 此憑證必須受到信任，例如由憑證授權單位簽發 (也就是信任鏈結必須存在而不需要中繼憑證。) (單一站台憑證可搭配您在安裝期間提供的明確 VM 名稱使用。)

    b. Azure Resource Manager 為您的 Azure Stack 執行個體所使用的根憑證。 如果找不到，則將擷取根憑證。

5. 開啟**新的**已提升權限的 PowerShell 主控台，並變更至解壓縮檔案所在的目錄。 使用新視窗，避免因為系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. 如果已安裝 1.2.9 或 1.2.10 以外之任何版本的 AzureRm 或 AzureStack PowerShell 模組，系統會提示您將它們移除，否則不會繼續安裝。 這包括 1.3 或更高版本。

7. 執行 DeployMySqlProvider.ps1。

此指令碼執行下列步驟：

* 如有必要，請下載相容版本的 Azure PowerShell。
* 下載 MySQL 連接器二進位檔 (可離線提供)。
* 將憑證和所有其他成品上傳到 Azure Stack 儲存體帳戶。
* 發佈資源庫套件，讓您可以透過資源庫部署 MySQL 資料庫。
* 部署主控資源提供者的虛擬機器 (VM)。
* 註冊會對應至您的資源提供者 VM 的本機 DNS 記錄。
* 向本機 Azure Resource Manager 註冊您的資源提供者。

請在命令列上至少指定必要的參數，或是如果不含任何參數執行，系統會提示您輸入它們。 

以下是可從 PowerShell 提示執行的範例 (視需要變更帳戶資訊和入口網站端點)：


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
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ("admin@mydomain.onmicrosoft.com", $AdminPass)

# change this as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files 
# and adjust the endpoints
<extracted file directory>\DeployMySQLProvider.ps1 -DirectoryTenantID $tenantID -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -ResourceGroupName "MySqlRG" -VmName "MySQLRP" -ArmEndpoint "https://adminmanagement.local.azurestack.external" -TenantArmEndpoint "https://management.local.azurestack.external" -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath
 ```

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 參數

您可以在命令列中指定這些參數。 如果未這麼做，或任何參數驗證失敗，系統會提示您提供必要參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **DirectoryTenantID** | Azure 或 ADFS Directory ID (guid) | _必要_ |
| **ArmEndpoint** | Azure Stack 系統管理 Azure Resource Manager 端點 | _必要_ |
| **TenantArmEndpoint** | Azure Stack 租用戶 Azure Resource Manager 端點 | _必要_ |
| **AzCredential** | Azure Stack 服務管理員帳戶認證 (使用用於部署 Azure Stack 的相同帳戶) | _必要_ |
| **VMLocalCredential** | MySQL 資源提供者 VM 的本機系統管理員帳戶 | _必要_ |
| **ResourceGroupName** | 此指令碼所建立之項目的資源群組 |  _必要_ |
| **VmName** | 持有資源提供者的 VM 名稱 |  _必要_ |
| **AcceptLicense** | 略過提示以接受 GPL 授權 (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |
| **DependencyFilesLocalPath** | 包含 [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi) 的本機共用路徑。 如果您提供它們，也必須將憑證檔案放在這個目錄。 | _選用_ |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼 | _必要_ |
| **MaxRetryCount** | 失敗時會重試每個作業 | 2 |
| **RetryDuration** | 重試之間以秒為單位的逾時 | 120 |
| **解除安裝** | 移除資源提供者 | 否 |
| **DebugMode** | 防止在失敗時自動清除 | 否 |


根據系統效能和下載速度，安裝可能需要少至 20 分鐘或長達幾小時的時間。 如果無法使用 MySQLAdapter 刀鋒視窗，您必須重新整理管理員入口網站。

> [!NOTE]
> 如果安裝需要超過 90 分鐘，它可能會失敗，並在畫面和記錄檔中看到失敗訊息。 部署會從失敗的步驟開始重試。 不符合建議的記憶體及核心規格的系統可能無法部署 MySQL RP。


## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>透過連線到 MySQL 主控伺服器以提供容量

1. 作為服務管理員登入 Azure Stack 入口網站。

2. 按一下**資源提供者** &gt; **MySQLAdapter** &gt; **主控伺服器** &gt; **+新增**。

    您可使用**MySQL 主控伺服器**刀鋒視窗，將 MySQL Server 資源提供者連線到 MySQL Server 的實際執行個體以作為資源提供者的後端。

    ![主控伺服器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 在表單中填寫 MySQL Server 執行個體的連線詳細資料。 提供完整網域名稱 (FQDN) 或有效的 IPv4 位址，而不是簡短的 VM 名稱。 這項安裝不會再提供預設 MySQL 執行個體。 提供的大小可協助資源提供者管理資料庫容量。 它應該接近資料庫伺服器的實體容量。

    > [!NOTE]
    > 只要租用戶和管理 Azure Resource Manager 可以存取 MySQL 執行個體，資源提供者就可以控制它。 MySQL 執行個體__必須__配置為 RP 專屬。

4. 新增伺服器時，您必須將它們指派給新的或現有的 SKU，以允許服務產品的差異化。 例如，您可以具有提供資料庫容量和自動備份的企業執行個體、保留高效能的伺服器供個別部門使用等等。SKU 名稱應反映屬性，讓租用戶可適當地安置其資料庫，且 SKU 中的所有主控伺服器都應具有相同的功能。


>[!NOTE]
最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。


## <a name="create-your-first-mysql-database-to-test-your-deployment"></a>建立第一個 MySQL 資料庫以測試您的部署


1. 作為服務管理員登入 Azure Stack 入口網站。

2. 按一下**+ 新增**按鈕&gt;**資料+儲存體** &gt; **MySQL Database (預覽版)**。

3. 在表單中填寫資料庫的詳細資訊。

    ![建立測試 MySQL 資料庫](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 選取 SKU。

    ![選取 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 建立登入設定。 可重複使用，或建立一個新的登入設定。 這包含資料庫的使用者名稱和密碼。

    連接字串包含實際的資料庫伺服器名稱。 從入口網站複製它。

    ![取得 MySQL 資料庫的連接字串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> MySQL 5.7 中的使用者名稱長度不得超過 32 個字元，較早的版本則為 16 個字元。 這是 MySQL 實作的限制。


## <a name="add-capacity"></a>新增容量

在 Azure Stack 入口網站中新增額外的 MySQL 伺服器來新增容量。 如果您想要使用 MySQL 的另一個執行個體，按一下**資源提供者** &gt; **MySQLAdapter** &gt; **MySQL 主控伺服器** &gt; **+新增**。


## <a name="making-mysql-databases-available-to-tenants"></a>將 MySQL 資料庫提供給租用戶使用
建立方案和產品，讓租用戶使用 MySQL 資料庫。 新增 Microsoft.MySqlAdapter 服務、新增配額等等。

![建立方案和產品來加入資料庫](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>移除 MySQL 介面卡資源提供者

若要移除資源提供者，必須先移除任何相依性。

1. 確認已下載此資源提供者版本的原始部署套件。

2. 必須從資源提供者刪除所有租用戶資料庫 (這不會刪除資料)。 必須由租用戶本身執行此作業。

3. 必須從命名空間中取消註冊租用戶。

4. 系統管理員必須從 MySQL 介面卡刪除主控伺服器

5. 系統管理員必須刪除參照 MySQL 介面卡的任何方案。

6. 系統管理員必須刪除 MySQL 介面卡所關聯的任何配額。

7. 使用 -Uninstall 參數、Azure Resource Manager 端點、DirectoryTenantID 和服務管理員帳戶的認證，重新執行部署指令碼。




## <a name="next-steps"></a>後續步驟


嘗試其他 [PaaS 服務](azure-stack-tools-paas-services.md)，如 [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)和[應用程式服務資源提供者](azure-stack-app-service-overview.md)。

