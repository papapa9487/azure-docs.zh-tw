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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: badaefb4986f573362babea81d704bf2be067d6b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 MySQL 資料庫

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

您可以在 Azure Stack 上部署 MySQL 資源提供者。 部署資源提供者後，您可以透過 Azure Resource Manager 部署範本建立 MySQL 伺服器和資料庫，並提供 MySQL 資料庫即服務。 網站上常用的 MySQL 資料庫支援許多網站平台。 例如，部署資源提供者後，您可以從 Azure Stack 的 Azure Web Apps 平台即服務 (PaaS) 附加元件中建立 WordPress 網站。

若要在沒有網際網路存取權的系統上部署 MySQL 提供者，您可以將 [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) 檔複製到本機共用。 然後，出現提示時提供該共用名稱。 您也必須安裝 Azure 和 Azure Stack PowerShell 模組。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL Server 資源提供者介面卡架構

資源提供者由三個元件組成：

- **MySQL 資源提供者介面卡 VM**，為執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，其處理佈建要求並公開資料庫資源。
- **主控 MySQL Server 的伺服器**，其提供資料庫的容量，稱為主控伺服器。

此版本不會再建立 MySQL 執行個體。 您必須建立它們和/或提供外部 SQL 執行個體的存取權。 瀏覽 [Azure Stack 快速入門資源庫](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)，以取得可以執行下列動作的範例範本：
- 為您建立 MySQL 伺服器
- 從 Marketplace 下載和部署 MySQL Server。

![NOTE] 安裝在多節點 Azure Stack 上的任何主控伺服器，必須透過租用戶訂用帳戶建立。 無法從預設提供者訂用帳戶加以建立。 也就是說，必須透過租用戶入口網站或透過具有適當登入的 PowerShell 工作階段來加以建立。 所有主控伺服器都是可計費的 VM，而且必須具有適當的授權。 服務管理員可以是該訂用帳戶的擁有者。

### <a name="required-privileges"></a>需要的權限
系統帳戶必須具有下列權限：

1.  資料庫：建立、卸除
2.  登入：建立、設定、卸除、授與、撤銷

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並透過 Marketplace 管理下載下載 Windows Server 2016 Datacenter Core 映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼來建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) - 務必選取核心選項。 不再需要 .NET 3.5 執行階段。


2. 登入可存取特殊權限端點 VM 的主機。

    a. 在 Azure Stack 開發套件 (ASDK) 安裝上，登入實體主機。

    b. 在多節點系統上，主機必須是可存取特殊權限端點的系統。

3. [下載 MySQL 資源提供者二進位檔案](https://aka.ms/azurestackmysqlrp)，並執行自我解壓縮程式將內容解壓縮至暫存目錄。

4.  Azure Stack 根憑證是從特殊權限端點擷取。 對於 ASDK，系統會在此程序的執行過程中建立自我簽署憑證。 對於多節點，您必須提供適當的憑證。

    如果您需要提供您自己的憑證，您需要下列憑證：

    \*.dbadapter.\<region\>.\<external fqdn\> 的萬用字元憑證。 此憑證必須是受信任的，例如會由憑證授權單位發出。 也就是說，信任鏈結必須存在，而不需要中繼憑證。 單一站台憑證即可在安裝期間用於搭配使用明確虛擬機器名稱 [mysqladapter]。


5. 開啟**新的**已提升權限 (管理) 的 PowerShell 主控台，並變更至解壓縮檔案所在的目錄。 使用新視窗，避免因為系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. [安裝 Azure PowerShell 1.2.11 版](azure-stack-powershell-install.md)。

7. 執行 DeploySqlProvider.ps1 指令碼。

指令碼會執行這些步驟：

* 下載 MySQL 連接器二進位檔 (可離線提供)。
* 將憑證和其他成品上傳到您 Azure Stack 上的儲存體帳戶。
* 發佈資源庫套件，以便您可以透過資源庫部署 SQL 資料庫。
* 發佈用於裝載伺服器部署的圖庫套件
* 使用在步驟 1 中建立的 Windows Server 2016 映像部署 VM，並安裝資源提供者。
* 註冊對應到您資源提供者 VM 的本機 DNS 記錄。
* 向本機 Azure Resource Manager 註冊您的資源提供者 (租用戶和管理員)。


您可以：
- 在命令列上指定最低的必要參數
- 或者，如果您執行時不搭配任何參數，請在提示時輸入。

以下是可從 PowerShell 提示字元執行的範例 (視需要變更帳戶資訊和密碼)：


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = 'AzureStack'
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
.$tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint '10.10.10.10' `
  -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 參數

您可以在命令列中指定這些參數。 如果未這麼做，或任何參數驗證失敗，系統會提示您提供必要參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，存取特殊權限端點所需。 | _必要_ |
| **AzCredential** | 提供 Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所使用之認證相同的認證)。 | _必要_ |
| **VMLocalCredential** | 定義 MySQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 提供特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 包含 [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi) 的本機共用路徑。 如果您有提供，也必須將憑證檔案放在這個目錄。 | _選擇性_ (多節點為_強制_) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼 | _必要_ |
| **MaxRetryCount** | 定義作業失敗時重試每個作業的次數。| 2 |
| **RetryDuration** | 定義重試之間的逾時 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註) | 否 |
| **DebugMode** | 防止在失敗時自動清除 | 否 |
| **AcceptLicense** | 略過提示以接受 GPL 授權 (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


根據系統效能和下載速度，安裝可能需要少至 20 分鐘或長達幾小時的時間。 如果無法使用 MySQLAdapter 刀鋒視窗，請重新整理管理員入口網站。

> [!NOTE]
> 如果安裝需要超過 90 分鐘，它可能會失敗，並在畫面和記錄檔中看到失敗訊息。 部署會從失敗的步驟開始重試。 不符合建議的記憶體及核心規格的系統可能無法部署 MySQL RP。



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站確認部署是否成功

> [!NOTE]
>  在安裝指令碼完成之後，將需要重新整理入口網站才會看到管理刀鋒視窗。


1. 以服務管理員身分登入管理入口網站。

2. 確認部署成功。 瀏覽 [資源群組]&gt;，按一下 **system.\<location\>.mysqladapter** 資源群組，並確認四個部署均成功。

      ![確認 MySQL RP 的部署是否成功](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>透過連線到 MySQL 主控伺服器以提供容量

1. 作為服務管理員登入 Azure Stack 入口網站。

2. 瀏覽至 [管理資源] &gt; [MySQL 主控伺服器] &gt; [+新增]。

    您可使用**MySQL 主控伺服器**刀鋒視窗，將 MySQL Server 資源提供者連線到 MySQL Server 的實際執行個體以作為資源提供者的後端。

    ![主控伺服器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 在表單中填寫 MySQL Server 執行個體的連線詳細資料。 提供完整網域名稱 (FQDN) 或有效的 IPv4 位址，而不是簡短的 VM 名稱。 這項安裝不會再提供預設 MySQL 執行個體。 提供的大小可協助資源提供者管理資料庫容量。 它應該接近資料庫伺服器的實體容量。

    > [!NOTE]
    > 只要租用戶和管理 Azure Resource Manager 可以存取 MySQL 執行個體，資源提供者就可以控制它。 MySQL 執行個體__必須__配置為 RP 專屬。

4. 新增伺服器時，您必須將它們指派給新的或現有的 SKU，以允許服務產品的差異化。
  例如，您可以有一個企業執行個體，提供下列：
    - 資料庫容量
    - 自動備份
    - 為個別部門保留高效能伺服器
    - 依此類推。
    SKU 名稱應反映屬性，讓租用戶可適當地安置其資料庫。 且 SKU 中的所有主控伺服器都應具有相同的功能。

    ![建立 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>若要測試您的部署，請建立第一個 MySQL 資料庫


1. 作為服務管理員登入 Azure Stack 入口網站。

2. 按一下 [+新增] 按鈕 &gt; [資料 + 儲存體] &gt; [MySQL 資料庫]。

3. 在表單中填寫資料庫的詳細資訊。

    ![建立測試 MySQL 資料庫](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 選取 SKU。

    ![選取 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 建立登入設定。 可重複使用，或建立一個新的登入設定。 此設定包含資料庫的使用者名稱和密碼。

    ![建立新的資料庫登入](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    連接字串包含實際的資料庫伺服器名稱。 從入口網站複製它。

    ![取得 MySQL 資料庫的連接字串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> MySQL 5.7 中的使用者名稱長度不得超過 32 個字元，較早的版本則為 16 個字元。


## <a name="add-capacity"></a>新增容量

在 Azure Stack 入口網站中新增額外的 MySQL 伺服器來新增容量。 可以將其他伺服器新增至新的或現有的 SKU。 確定伺服器特性都相同。


## <a name="making-mysql-databases-available-to-tenants"></a>將 MySQL 資料庫提供給租用戶使用
建立方案和產品，讓租用戶使用 MySQL 資料庫。 新增 Microsoft.MySqlAdapter 服務、新增配額等等。

![建立方案和產品來加入資料庫](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="updating-the-administrative-password"></a>更新系統管理密碼
您可以先在 MySQL 伺服器執行個體上變更它來修改密碼。 瀏覽至 [管理資源] &gt; [MySQL 主控伺服器] &gt; 然後按一下主控伺服器。 在 [設定] 面板中，按一下 [密碼]。

![更新管理員密碼](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="removing-the-mysql-adapter-resource-provider"></a>移除 MySQL 介面卡資源提供者

若要移除資源提供者，必須先移除任何相依性。

1. 確認您有為此資源提供者版本下載的原始部署套件。

2. 必須從資源提供者刪除所有租用戶資料庫 (這不會刪除資料)。 必須由租用戶本身執行此作業。

3. 必須從命名空間中取消註冊租用戶。

4. 系統管理員必須從 MySQL 介面卡刪除主控伺服器

5. 系統管理員必須刪除參照 MySQL 介面卡的任何方案。

6. 系統管理員必須刪除 MySQL 介面卡所關聯的任何配額。

7. 使用 -Uninstall 參數、Azure Resource Manager 端點、DirectoryTenantID 和服務管理員帳戶的認證，重新執行部署指令碼。




## <a name="next-steps"></a>後續步驟


嘗試其他 [PaaS 服務](azure-stack-tools-paas-services.md)，如 [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)和[應用程式服務資源提供者](azure-stack-app-service-overview.md)。
