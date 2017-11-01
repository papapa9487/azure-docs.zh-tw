---
title: "在 Azure Stack 上部署 App Service 之前 | Microsoft Docs"
description: "在 Azure Stack 上部署 App Service 之前必須完成的步驟"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: f2e7b5b96b70333ae4ee92d24c354960008c7f00
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>開始使用 Azure Stack 上的 App Service 之前

Azure App Service on Azure Stack 有一些必須在部署之前完成的先決條件步驟：

- 下載 Azure App Service on Azure Stack 協助程式指令碼
- 高可用性
- Azure App Service on Azure Stack 所需的憑證
- 準備檔案伺服器
- 準備 SQL Server
- 建立 Azure Active Directory 應用程式
- 建立 Active Directory 同盟服務應用程式

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>下載 Azure App Service on Azure Stack 安裝程式和協助程式指令碼

1. 下載 [Azure Stack 上的 App Service 部署協助程式指令碼](https://aka.ms/appsvconmashelpers)。
2. 下載 [Azure App Service on Azure Stack 安裝程式](https://aka.ms/appsvconmasinstaller)。
3. 從協助程式指令碼 zip 檔案解壓縮檔案。 隨即出現下列檔案與資料夾結構：
  - Common.ps1
  - Create-AADIdentityApp.ps1
  - Create-ADFSIdentityApp.ps1
  - Create-AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Remove-AppService.ps1
  - 模組
    - GraphAPI.psm1
    
## <a name="high-availability"></a>高可用性

Azure App Service on Azure Stack 目前無法提供高可用性，因為 Azure Stack 僅會將工負載部署至一個單一個容錯網域中。

若要讓 AAzure App Service on Azure Stack 準備好提供高可用性，請務必在 [高可用性] 配置中部署必要的檔案伺服器和 SQL Server。 若 Azure Stack 支援多個容錯網域，我們會提供如何在高可用性配置中啟用 Azure App Service on Azure Stack 的方法。


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack 所需的憑證

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Azure Stack 開發套件所需的憑證

這第一個指令碼會搭配 Azure Stack 憑證授權單位運作，以建立 App Service 所需的四個憑證。

| 檔案名稱 | 使用 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service 預設 SSL 憑證 |
| Api.appservice.local.azurestack.external.pfx | App Service API SSL 憑證 |
| ftp.appservice.local.azurestack.external.pfx | App Service 發行者 SSL 憑證 |
| Sso.appservice.local.azurestack.external.pfx | App Service 身分識別應用程式憑證 |

在 Azure Stack 開發套件主機上執行指令碼，並確定您是以 azurestack\CloudAdmin 身分執行 PowerShell。

1. 在以 azurestack\CloudAdmin 身分執行的 PowerShell 工作階段中，從您解壓縮協助程式指令碼所在的資料夾執行 Create-AppServiceCerts.ps1 指令碼。 此指令碼會在與 App Service 所需之建立憑證指令碼相同的資料夾中建立四個憑證。
2. 輸入密碼來保護 .pfx 檔案，並記下密碼。 您必須在 App Service on Azure Stack 安裝程式中輸入此密碼。

#### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 參數

| 參數 | 必要/選用 | 預設值 | 說明 |
| --- | --- | --- | --- |
| pfxPassword | 必要 | Null | 用來保護憑證私密金鑰的密碼 |
| DomainName | 必要 | local.azurestack.external | Azure Stack 區域和網域尾碼 |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack 的實際執行部署所需的憑證

若要在生產環境中操作資源提供者，您必須提供下列四個憑證：

#### <a name="default-domain-certificate"></a>預設網域憑證

預設網域憑證放在「前端」角色。 使用者應用程式會將它用於對 Azure App Service 的萬用字元或預設網域要求。 憑證也用於原始檔控制作業 (KUDU)。

憑證必須是 .pfx 格式，而且應該是雙主體的萬用字元憑證。 這使得一個憑證即可同時涵蓋用於原始檔控制作業的預設網域和 SCM 端點。

| 格式 | 範例 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 憑證

API 憑證會放置在管理角色上，並且由資源提供者用來保護 API 呼叫。 用於發佈的憑證必須包含符合 API DNS 項目的主體：

| 格式 | 範例 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>發行憑證

發行者角色的憑證會在應用程式擁有者上傳內容時，保護其 FTPS 流量。  用於發佈的憑證必須包含符合 FTPS DNS 項目的主體。

| 格式 | 範例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>身分識別憑證

身分識別應用程式的憑證可讓：
- AAD/ADFS 目錄、Azure Stack 和 App Service 之間整合，以支援與計算資源提供者的整合
- Azure App Service on Azure Stack 中進階開發人員工具的單一登入案例。
身分識別的憑證必須包含符合下列格式的主體：

| 格式 | 範例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

#### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>擷取 Azure Stack Azure Resource Manager 根憑證

在以 azurestack\CloudAdmin 身分執行的 PowerShell 工作階段中，從您解壓縮協助程式指令碼所在的資料夾執行 Get-AzureStackRootCert.ps1 指令碼。 此指令碼會在與 App Service 所需之建立憑證指令碼相同的資料夾中建立四個憑證。

| Get-AzureStackRootCert.ps1 參數 | 必要/選用 | 預設值 | 說明 |
| --- | --- | --- | --- |
| PrivelegedEndpoint | 必要 | AzS-ERCS01 | 特殊權限的端點。 |
| CloudAdminCredential | 必要 | AzureStack\CloudAdmin | Azure Stack 雲端管理網域帳戶的認證 |


## <a name="prepare-the-file-server"></a>準備檔案伺服器

Azure App Service 需要使用檔案伺服器。 在實際執行的部署中，必須將檔案伺服器設定為高度可用，且能夠處理失敗。

僅針對與 Azure Stack 開發套件部署搭配使用，您可以使用此範例 Azure Resource Manager 部署範本來部署設定的單一節點檔案伺服器：https://aka.ms/appsvconmasdkfstemplate。

### <a name="provision-groups-and-accounts-in-active-directory"></a>在 Active Directory 中佈建群組和帳戶

>[!NOTE]
> 在系統管理員命令提示字元工作階段中設定檔案伺服器時，請執行下列所有的命令。  **請勿使用 PowerShell。**

1. 建立下列 Active Directory 全域安全性群組：
    - FileShareOwners
    - FileShareUsers
2. 建立下列 Active Directory 帳戶做為服務帳戶：
    - FileShareOwner
    - FileShareUser

    基於安全性最佳作法，這些帳戶 (以及所有 Web 角色) 的使用者，應該彼此相異並擁有強式使用者名稱和密碼。
    使用下列條件設定密碼：
     - 啟用 [密碼永久有效]。
     - 啟用 [使用者不可變更密碼]。
     - 停用 [使用者必須在下次登入時變更密碼]。
3. 將帳戶新增至群組成員資格，如下所示：
    - 將 **FileShareOwner** 新增至 **FileShareOwners** 群組。
    - 將 **FileShareUser** 新增至 **FileShareUsers** 群組。

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>在工作群組中佈建群組和帳戶

在工作群組上，執行 net 和 WMIC 命令來佈建群組和帳戶。

1. 執行下列命令來建立 FileShareOwner 與 FileShareUser 帳戶。 以您自己的值取代 <password>。
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. 執行以下 WMIC 命令，將帳戶的密碼設定為永不過期：
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. 建立本機群組 FileShareUsers 和 FileShareOwners，並在第一個步驟中為其新增帳戶。
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>佈建內容共用

內容共用包含租用戶網站內容。 在單一檔案伺服器上佈建內容共用的程序與在 Active Directory 和工作群組環境中均相同，但是對於 Active Directory 中的容錯移轉叢集則不同。

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>在單一檔案伺服器上 (AD 或工作群組) 佈建內容共用

在單一檔案伺服器上，請在提升權限的命令提示字元中執行下列命令。 以您的環境中的對應路徑，取代 <C:\WebSites> 的值。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=<C:\WebSites>
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>若要啟用 WinRM，請將 FileShareOwners 群組新增至本機 Administrators 群組

為了讓「Windows 遠端管理」正常運作，您必須將 FileShareOwners 群組新增至本機 Administrators 群組。

#### <a name="active-directory"></a>Active Directory

在檔案伺服器上或每個檔案伺服器容錯移轉叢集節點上提高權限的命令提示字元中，執行下列命令。 您要使用的網域名稱取代 <DOMAIN> 的值。

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>工作群組

在檔案伺服器上提高權限的命令提示字元中，執行下列命令。

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>設定共用的存取控制

在檔案伺服器上或檔案伺服器容錯移轉叢集節點 (目前的叢集資源擁有者) 上提高權限的命令提示字元中，執行下列命令。 以環境特有的值取代斜體的值。

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>工作群組
```DOS
set WEBSITES_FOLDER=<C:\WebSites>
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>準備 SQL Server

針對 Azure App Service on Azure Stack 託管和計量資料庫，您必須準備 SQL Server，以用來存放 Azure App Service 資料庫。

若要搭配使用 Azure Stack 開發套件，您可以使用 SQL Express 2014 SP2 或更新版本。

若要用於生產環境及高可用性，您應該使用完整版本的 SQL 2014 SP2 或更新版本，啟用混合模式驗證，並在[高可用性配置](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)中部署。

Azure App Service on Azure Stack SQL Server 必須能夠從所有 App Service 角色存取。 您可以在 Azure Stack 中的預設提供者訂用帳戶中部署 SQL Server。 或者，您可以使用組織中現有的基礎結構 (請確認可以連線到 Azure Stack)。

針對任何 SQL Server 角色，您可以使用預設執行個體或具名執行個體。 不過，如果您使用具名執行個體，請務必手動啟動 SQL Browser 服務並開啟連接埠 1434。

## <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

設定 Azure AD 服務主體，以支援下列項目：
- 背景工作層上的虛擬機器擴展集整合。
- 對 Azure Functions 入口網站和進階開發人員工具使用 SSO。

這些步驟只適用於 Azure AD 保護的 Azure Stack 環境。

系統管理員必須設定 SSO 來執行下列動作：
- 啟用 App Service (Kudu) 內的進階開發人員工具。
- 讓 Azure Functions 入口網站體驗可供使用。

請遵循下列步驟：

1. 以 azurestack\cloudadmin 身分開啟 PowerShell 執行個體。
2. 移至在[先決條件步驟](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)中下載並解壓縮的指令碼位置。
3. [安裝 Azure Stack PowerShell](azure-stack-powershell-install.md)。
4. 執行 **Create-AADIdentityApp.ps1** 指令碼。 當系統提示您提供 Azure AD 租用戶識別碼時，請輸入您針對 Azure Stack 部署使用的 Azure AD 租用戶識別碼，例如 myazurestack.onmicrosoft.com。
5. 在 [認證] 視窗中，輸入您的 Azure AD 服務管理帳戶和密碼。 按一下 [確定] 。
6. 輸入[稍早建立的憑證](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)的憑證檔案路徑和憑證密碼。 根據預設值，針對此步驟建立的憑證是 sso.appservice.local.azurestack.external.pfx。
7. 此指令碼會在租用戶 Azure AD 中建立新的應用程式。 請記下 PowerShell 輸出中傳回的應用程式識別碼。 安裝期間會需要這項資訊。
8. 開啟新的瀏覽器視窗，並以 **Azure Active Directory 服務管理員**身分登入 Azure 入口網站 (portal.azure.com)。
9. 開啟 Azure AD 資源提供者。
10. 按一下 [應用程式註冊]。
11. 搜尋步驟 7 傳回的**應用程式識別碼**。 隨即列出 App Service 應用程式。
12. 按一下清單中的 [應用程式]
13. 按一下 [必要權限] > [授與權限] > [是]。

| Create-AADIdentityApp.ps1 的參數 | 必要/選用 | 預設值 | 說明 |
| --- | --- | --- | --- |
| DirectoryTenantName | 必要 | Null | Azure AD 租用戶識別碼。 提供 GUID 或字串，例如 myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | 必要 | Null | 管理員的 Azure Resource Manager 端點，例如 adminmanagement.local.azurestack.external |
| TenantARMEndpoint | 必要 | Null | 租用戶的 Azure Resource Manager 端點，例如 management.local.azurestack.external |
| AzureStackAdminCredential | 必要 | Null | Azure AD Service 管理員的認證 |
| CertificateFilePath | 必要 | Null | 稍早產生之身分識別應用程式憑證檔案的路徑。 |
| CertificatePassword | 必要 | Null | 用來保護憑證私密金鑰的密碼。 |

## <a name="create-an-active-directory-federation-services-application"></a>建立 Active Directory 同盟服務應用程式

對於受到 AD FS 保護的 Azure Stack 環境，您必須設定 AD FS 服務主體，以支援下列項目：
- 背景工作層上的虛擬機器擴展集整合。
- 對 Azure Functions 入口網站和進階開發人員工具使用 SSO。

系統管理員必須設定 SSO 來執行下列動作：
- 針對背景工作層上的虛擬機器擴展集整合設定服務主體。
- 啟用 App Service (Kudu) 內的進階開發人員工具。
- 讓 Azure Functions 入口網站體驗可供使用。

請遵循下列步驟：

1. 以 azurestack\azurestackadmin 身分開啟 PowerShell 執行個體。
2. 移至在[先決條件步驟](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)中下載並解壓縮的指令碼位置。
3. [安裝 Azure Stack PowerShell](azure-stack-powershell-install.md)。
4.  執行 **Create-ADFSIdentityApp.ps1** 指令碼。
5.  在 [認證] 視窗中，輸入您的 AD FS 雲端管理帳戶和密碼。 按一下 [確定] 。
6.  提供[稍早建立之憑證](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)的憑證檔案路徑和憑證密碼。 根據預設值，針對此步驟建立的憑證是 sso.appservice.local.azurestack.external.pfx。

| Create-ADFSIdentityApp.ps1 的參數 | 必要/選用 | 預設值 | 說明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必要 | Null | 管理員 Azure Resource Manager 端點。 例如，adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 必要 | Null | 特殊權限的端點。 例如 AzS-ERCS01。 |
| CloudAdminCredential | 必要 | Null | Azure Stack cloudadmin 網域帳戶認證。 例如，Azurestack\CloudAdmin。 |
| CertificateFilePath | 必要 | Null | 識別應用程式憑證 PFX 檔案的路徑。 |
| CertificatePassword | 必要 | Null | 用來保護憑證私密金鑰的密碼。 |


## <a name="next-steps"></a>後續步驟

[安裝 App Service 資源提供者](azure-stack-app-service-deploy.md)。
