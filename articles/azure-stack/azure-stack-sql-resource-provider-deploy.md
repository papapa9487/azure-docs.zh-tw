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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 329970d8717053ab7126fb8fb6a4a119ccbff6b7
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 SQL 資料庫

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

使用 SQL Server 資源提供者配接器，將 SQL 資料庫公開為 [Azure Stack](azure-stack-poc.md) 的服務。 安裝資源提供者並將它連接至一或多個 SQL Server 執行個體之後，您和您的使用者可以建立：
- 雲端原生應用程式的資料庫
- 以 SQL 為基礎的網站
- 基於 SQL 的工作負載。您不需要佈建每次主控 SQL Server 的虛擬機器 (VM)。

資源提供者並未支援 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 的所有資料庫管理功能。 例如，不提供彈性資料庫集區和自動提高及降低資料庫效能的功能。 但是，資源提供者支援類似的建立、讀取、更新及刪除 (CRUD) 作業。 API 與 SQL DB 不相容。

## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server 資源提供者配接器架構
資源提供者由三個元件組成：

- **SQL 資源提供者配接器 VM**，這是執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，它會處理佈建要求並公開資料庫資源。
- **主控 SQL Server 的伺服器**，它提供資料庫的容量，稱為主控伺服器。

您必須建立一個 (或多個) SQL 伺服器和/或提供外部 SQL 執行個體的存取權。

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並透過 Marketplace 管理下載下載 Windows Server 2016 Datacenter Core 映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼來建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) - 務必選取核心選項。 不再需要 .NET 3.5 執行階段。

2. 登入可存取特殊權限端點 VM 的主機。

    a. 在 Azure Stack 開發套件 (ASDK) 安裝上，登入實體主機。

    b. 在多節點系統上，主機必須是可存取特殊權限端點的系統。

3. [下載 SQL 資源提供者二進位檔案](https://aka.ms/azurestacksqlrp)，並執行自我解壓縮程式將內容解壓縮至暫存目錄。

4. Azure Stack 根憑證是從特殊權限端點擷取。 對於 ASDK，系統會在此程序的執行過程中建立自我簽署憑證。 對於多節點，您必須提供適當的憑證。

    如果您需要提供您自己的憑證，您需要下列憑證：

    \*.dbadapter.\<region\>.\<external fqdn\> 的萬用字元憑證。 此憑證必須是受信任的，例如會由憑證授權單位發出。 也就是說，信任鏈結必須存在，而不需要中繼憑證。 單一站台憑證即可在安裝期間用於搭配明確虛擬機器名稱 [sqladapter]。


5. 開啟**新的**已提升權限 (管理) 的 PowerShell 主控台，並變更至解壓縮檔案所在的目錄。 使用新視窗，避免因為系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. [安裝 Azure PowerShell 1.2.11 版](azure-stack-powershell-install.md)。

7. 請搭配下方列出的參數執行 DeploySqlProvider.ps1 指令碼。

指令碼會執行這些步驟：

- 將憑證和其他成品上傳到您 Azure Stack 上的儲存體帳戶。
- 發佈資源庫套件，以便您可以透過資源庫部署 SQL 資料庫。
- 發佈用於裝載伺服器部署的圖庫套件
- 使用在步驟 1 中建立的 Windows Server 2016 映像部署 VM，並安裝資源提供者。
- 註冊對應到您資源提供者 VM 的本機 DNS 記錄。
- 向本機 Azure Resource Manager 註冊您的資源提供者 (使用者和管理員)。

> [!NOTE]
> 如果安裝時間超過 90 分鐘，表示安裝可能失敗，且您會在畫面上和記錄檔中看到失敗訊息，但會從失敗的步驟重新嘗試執行部署作業。 不符合建議的記憶體及核心規格的系統可能無法部署 SQL RP。
>

以下是可從 PowerShell 提示字元執行的範例 (視需要變更帳戶資訊和密碼)：

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
.$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 參數
您可以在命令列中指定這些參數。 如果未這麼做，或任何參數驗證失敗，系統會提示您提供必要參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，存取特殊權限端點所需。 | _必要_ |
| **AzCredential** | 提供 Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所使用之認證相同的認證)。 | _必要_ |
| **VMLocalCredential** | 定義 SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 提供特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證 PFX 檔案也必須放在這個目錄中。 | _選擇性_ (多節點為_強制_) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼 | _必要_ |
| **MaxRetryCount** | 定義作業失敗時重試每個作業的次數。| 2 |
| **RetryDuration** | 定義重試之間的逾時 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註) | 否 |
| **DebugMode** | 防止在失敗時自動清除 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站確認部署是否成功

> [!NOTE]
>  在安裝指令碼完成之後，將需要重新整理入口網站才會看到管理刀鋒視窗。


1. 以服務管理員身分登入管理入口網站。

2. 確認部署成功。 瀏覽 [**資源群組**]&gt;，按一下 **system.\<location\>.sqladapter** 資源群組，並確認四個部署均成功。

      ![確認 SQL RP 的部署是否成功](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>移除 SQL 配接器資源提供者

若要移除資源提供者，必須先移除任何相依性。

1. 確認您有為此資源提供者版本下載的原始部署套件。

2. 必須從資源提供者刪除所有使用者資料庫 (這不會刪除資料)。 必須由使用者本身執行此作業。

3. 系統管理員必須從 SQL 配接器刪除主控伺服器

4. 系統管理員必須刪除參照 SQL 配接器的任何方案。

5. 系統管理員必須刪除與 SQL 配接器關聯的任何 SKU 和配額。

6. 使用 -Uninstall 參數、Azure Resource Manager 端點、DirectoryTenantID 和服務管理員帳戶的認證，重新執行部署指令碼。


## <a name="next-steps"></a>後續步驟


嘗試其他 [PaaS 服務](azure-stack-tools-paas-services.md)，如 [MySQL Server 資源提供者](azure-stack-mysql-resource-provider-deploy.md)和[應用程式服務資源提供者](azure-stack-app-service-overview.md)。
