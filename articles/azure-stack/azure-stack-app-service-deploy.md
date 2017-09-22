---
title: "部署 App Service：Azure Stack | Microsoft Docs"
description: "部署 Azure Stack 中之 App Service 的詳細指導方針"
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
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 4b4f978f008dbcd8a7424f285198535cf133d7e2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>將 App Service 資源提供者新增到 Azure Stack

如果您想要讓租用戶使用其 Azure Stack 訂用帳戶建立 Web、行動裝置版及 API 應用程式，您必須將 [Azure App Service 資源提供者](azure-stack-app-service-overview.md)新增到您的 Azure Stack 部署。 依照此文章中的步驟執行。

## <a name="download-the-required-components"></a>下載必要元件

1. 下載 [Azure Stack 上的 App Service 預覽安裝程式](http://aka.ms/appsvconmasrc1installer)。

2. 下載 [Azure Stack 上的 App Service 部署協助程式指令碼](http://aka.ms/appsvconmasrc1helper)。

3. 從協助程式指令碼 zip 檔案解壓縮檔案。 隨即出現下列檔案與資料夾結構：

   - Create-AppServiceCerts.ps1
   - Create-IdentityApp.ps1
   - 模組
      - AzureStack.Identity.psm1
      - GraphAPI.psm1
   
## <a name="create-certificates-required-by-app-service-on-azure-stack"></a>建立 Azure Stack 上之 App Service 所需的憑證

這第一個指令碼會搭配 Azure Stack 憑證授權單位運作，以建立 App Service 所需的三個憑證。 在 Azure Stack 主機上執行指令碼，並確定您是以 azurestack\AzureStackAdmin 身分執行 PowerShell。

1. 在以 azurestack\AzureStackAdmin 身分執行的 PowerShell 工作階段中，從您解壓縮協助程式指令碼所在的資料夾執行 **Create-AppServiceCerts.ps1** 指令碼。 此指令碼會在與 App Service 所需之建立憑證指令碼相同的資料夾中建立三個憑證。

2. 輸入密碼來保護 .pfx 檔案，並記下密碼。 您將需要在 Azure Stack 上的 App Service 安裝程式中輸入此密碼。

### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 參數

| 參數 | 必要/選用 | 預設值 | 描述 |
| --- | --- | --- | --- |
| pfxPassword | 必要 | Null | 用來保護憑證私密金鑰的密碼 |
| DomainName | 必要 | local.azurestack.external | Azure Stack 區域和網域尾碼 |
| CertificateAuthority | 必要 | AzS-CA01.azurestack.local | 憑證授權單位端點 |

## <a name="use-the-installer-to-download-and-install-app-service-on-azure-stack"></a>使用安裝程式，下載並安裝 Azure Stack 上的 App Service

Appservice.exe 安裝程式將：

* 提示您接受 Microsoft 和協力廠商「軟體授權條款」。
* 收集 Azure Stack 部署資訊。
* 在指定的 Azure Stack 儲存體帳戶中建立 Blob 容器。
* 下載安裝 App Service 資源提供者所需的檔案。
* 準備安裝，以便在 Azure Stack 環境中部署 App Service 資源提供者。
* 將檔案上傳到 App Service 儲存體帳戶。
* 部署 App Service 資源提供者。
* 建立適用於 App Service 的 DNS 區域與項目。
* 註冊 App Service 資源提供者。
* 註冊 App Service 資源庫項目。

下列步驟會逐步引導您完成安裝階段。

> [!NOTE]
> 您必須使用已提高權限的帳戶 (本機或網域系統管理員) 來執行安裝程式。 如果您以 azurestack\azurestackuser 身分登入，則系統會提示您提供已提高權限的認證。

1. 以 azurestack\AzureStackAdmin 身分執行 appservice.exe。

2. 按一下 [在您的 Azure Stack 雲端上部署 App Service]。

    ![Azure Stack 上的 App Service 安裝程式][1]

3. 檢閱並接受 Microsoft 軟體發行前版本授權條款，然後按一下 [下一步]。

4. 檢閱並接受協力廠商授權條款，然後按一下 [下一步]。

5. 檢閱 App Service 雲端設定資訊，然後按一下 [下一步]。

    ![Azure Stack App Service 上的 App Service 雲端設定][2]

    > [!NOTE]
    > Azure Stack 上的 App Service 安裝程式提供適用於單一節點 Azure Stack 安裝的預設值。 如果您已在部署 Azure Stack 時自訂選項 (例如，網域尾碼)，就需要據以編輯此視窗中的值。 例如，如果您使用網域尾碼 mycloud.com，則您的管理員 Azure Resource Manager 端點必須變更為 adminmanagement.[region].mycloud.com。

6. 按一下 [Azure Stack 訂用帳戶] 方塊旁邊的 [連線] 按鈕。

   - 如果您使用 Azure Active Directory (Azure AD)，就必須輸入您的 Azure AD 管理帳戶和密碼。 按一下 [登入] 。 您*必須*輸入您在部署 Azure Stack 時提供的 Azure AD 帳戶。
   - 如果您使用 Active Directory 同盟服務 (AD FS)，就必須提供您的管理帳戶，例如 azurestackadmin@azurestack.local。 輸入您的密碼，然後按一下 [登入]。

7. 在 [Azure Stack 訂用帳戶] 方塊中，選取您的訂用帳戶。

8. 在 [Azure Stack 位置] 方塊中，選取對應到您要部署之區域的位置。 例如，選取 [本機]。 按一下 [下一步]。

    ![Azure Stack 上的 App Service 訂用帳戶選取項目][3]

9. 針對您的 App Service 部署輸入**資源群組名稱**。 根據預設值，它是設定為 **APPSERVICE-LOCAL**。

10. 輸入您想要 App Service 在安裝期間建立的**儲存體帳戶名稱**。 根據預設值，它是設定為 **appsvclocalstor**。

11. 針對用於裝載 App Service 資源提供者資料庫的執行個體，輸入 SQL Server 詳細資料。 按一下 [下一步]，安裝程式即會驗證 SQL 連線屬性。

    ![Azure Stack 上的 App Service 資源群組、儲存體和 SQL Server 資訊][4]

12. 按一下 [App Service 預設 SSL 憑證檔案] 方塊旁邊的 [瀏覽] 按鈕。 移至[稍早建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的 **_.appservice.local.AzureStack.external** 憑證。 如果您指定的位置和網域尾碼與您建立憑證時的不同，請選取對應的憑證。

13. 輸入您建立憑證時所設定的憑證密碼。

14. 按一下 [資源提供者 SSL 憑證檔案] 方塊旁邊的 [瀏覽] 按鈕。 移至[稍早建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的 **api.appservice.local.AzureStack.external** 憑證。 如果您指定的位置和網域尾碼與您建立憑證時的不同，請選取對應的憑證。

15. 輸入您建立憑證時所設定的憑證密碼。

16. 按一下 [資源提供者根憑證檔案] 方塊旁邊的 [瀏覽] 按鈕。 移至[稍早建立](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps)的 **AzureStackCertificationAuthority** 憑證。

17. 按一下 [下一步]。 安裝程式會確認所提供的憑證密碼。

    ![Azure Stack 上的 App Service 憑證詳細資料][5]

18. 檢閱 App Service 角色設定。 預設值會使用適用於每個角色的最低建議執行個體 SKU 來填入。 系統會提供核心和記憶體需求的摘要來協助規劃您的部署。 進行選擇之後，按一下 [下一步]。

    - **控制器**：預設會選取一個標準 A1 執行個體。 這是我們建議的最小值。 控制器角色負責管理和維護 App Service 雲端的健康情況。
    - **管理**：預設會選取一個標準 A2 執行個體。 為了提供容錯移轉，我們建議兩個執行個體。 管理角色負責 App Service Azure Resource Manager 和 API 端點、入口網站擴充功能 (管理員、租用戶、Functions 入口網站)，以及資料服務。
    - **發行者**：預設會選取一個標準 A1 執行個體。 這是我們建議的最小值。 發行者角色負責透過 FTP 和 Web 部署來發行內容。
    - **前端**：預設會選取一個標準 A1 執行個體。 這是我們建議的最小值。 前端角色負責將要求路由傳送到 App Service 應用程式。
    - **共用背景工作**：預設會選取一個標準 A1 執行個體，但您可能想要新增更多。 身為系統管理員，您可以定義您的供應項目，並選擇任何 SKU 層。 各層必須具有至少一個核心。 共用背景工作角色負責主控 Web、行動裝置版或 API 應用程式及 Azure Functions 應用程式。

    ![Azure Stack 上的 App Service 角色設定][6]

    > [!NOTE]
    > 在技術預覽中，App Service 資源提供者安裝程式也會部署要作為簡單檔案伺服器運作的標準 A1 執行個體，以支援 Azure Resource Manager。 這會針對單一節點開發套件加以保留。 對於生產工作負載，在公開推出時，App Service 安裝程式會讓高可用性檔案伺服器可供使用。

19. 從可以在適用於 App Service 雲端的運算資源提供者中的可用映像中，選擇您的部署 **Windows Server 2016** VM 映像。 按一下 [下一步]。

    ![Azure Stack 上的 App Service VM 映像選取項目][7]

20. 針對 App Service 雲端中設定的背景工作角色，輸入使用者名稱和密碼。 針對所有其他的 App Service 角色，輸入使用者名稱和密碼。 按一下 [下一步]。

    ![Azure Stack 上的 App Service 認證項目][8]

21. 在摘要畫面上，確認您所做的選擇。 若要進行變更，請返回畫面並修改您的選擇。 如果設定符合您的需求，請選取核取方塊。 若要開始部署，請按一下 [下一步]。

    ![Azure Stack 上的 App Service 選取項目摘要][9]

22. 追蹤安裝進度。 根據預設的選取項目而定，部署 Azure Stack 上的 App Service 大約需要 45 到 60 分鐘。

    ![Azure Stack 上的 App Service 安裝進度][10]

23. 當安裝程式順利完成之後，請按一下 [結束]。

## <a name="validate-the-app-service-on-azure-stack-installation"></a>確認 Azure Stack 上的 App Service 安裝

1. 在 Azure Stack 管理入口網站中，瀏覽到安裝程式所建立的資源群組。 根據預設值，此群組是 **APPSERVICE-LOCAL**。

2. 找出 **CN0-VM**。 若要連線到 VM，請按一下 [虛擬機器] 刀鋒視窗上的 [連線]。

3. 在此 VM 的桌面上，按兩下 [Web 雲端管理主控台]。

4. 移至 [受管理伺服器]。

5. 當所有電腦都針對一或多個背景工作顯示 [就緒] 時，請繼續進行步驟 6。

6. 關閉遠端桌面電腦，並返回您執行 App Service 安裝程式的電腦。

    > [!NOTE]
    > 您不需要等候一或多個背景工作顯示 [就緒]，就能完成 Azure Stack 上的 App Service 安裝。 不過，您需要至少一個背景工作已就緒可部署 Web、行動裝置版或 API 應用程式，或是 Azure Functions。

    ![Azure Stack 上的 App Service 受管理伺服器狀態][11]

## <a name="configure-an-azure-ad-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>針對背景工作層的虛擬機器擴展集整合設定 Azure AD 服務主體，並針對 Azure Functions 入口網站設定 SSO 和進階開發人員工具

>[!NOTE]
> 這些步驟只適用於 Azure AD 保護的 Azure Stack 環境。

系統管理員必須設定 SSO 來執行下列動作：

* 啟用 App Service (Kudu) 內的進階開發人員工具。
* 讓 Azure Functions 入口網站體驗可供使用。

依照下列步驟執行：

1. 以 azurestack\azurestackadmin 身分開啟 PowerShell 執行個體。

2. 移至在[先決條件步驟](#Download-Required-Components)中下載並解壓縮的指令碼位置。

3. [安裝](azure-stack-powershell-install.md)及[設定 Azure Stack PowerShell 環境](azure-stack-powershell-configure-admin.md)。

4. 在同一個 PowerShell 工作階段中，執行 **CreateIdentityApp.ps1** 指令碼。 當系統提示您提供 Azure AD 租用戶識別碼時，請輸入您針對 Azure Stack 部署使用的 Azure AD 租用戶識別碼，例如 myazurestack.onmicrosoft.com。

5. 在 [認證] 視窗中，輸入您的 Azure AD 服務管理帳戶和密碼。 按一下 [確定] 。

6. 輸入[稍早建立的憑證](# Create certificates to be used by App Service on Azure Stack)的憑證檔案路徑和憑證密碼。 根據預設值，針對此步驟建立的憑證是 sso.appservice.local.azurestack.external.pfx。

7. 指令碼會在租用戶 Azure AD 中建立新的應用程式，並產生名為 **UpdateConfigOnController.ps1** 的新 PowerShell 指令碼。

    >[!NOTE]
    > 請記下 PowerShell 輸出中傳回的**應用程式識別碼**。 您在步驟 12 中需要使用此資訊進行搜尋。

8. 使用遠端桌面工作階段，將身分識別應用程式憑證檔案和產生的指令碼複製到 **CN0-VM**。

9. 開啟新的瀏覽器視窗，並以 **Azure Active Directory 服務管理員**身分登入 Azure 入口網站 (portal.azure.com)。

10. 開啟 Azure AD 資源提供者。

11. 按一下 [應用程式註冊]。

12. 搜尋步驟 7 傳回的**應用程式識別碼**。 隨即列出 App Service 應用程式。

13. 按一下清單中的 [應用程式]，並開啟 [金鑰] 刀鋒視窗。

14. 加入含有**描述 - Functions 入口網站**的新金鑰，並將 [到期日] 設為 [永不過期]。

15. 按一下 [儲存]，然後複製所產生的金鑰。

    >[!NOTE]
    > 產生此金鑰時，請務必加以記錄或複製。 儲存該金鑰之後，您便無法再次檢視，而您需要重新產生新的金鑰。

    ![Azure Stack 上的 App Service 應用程式金鑰][12]

16. 返回 Azure AD 中的 [應用程式註冊]。

17. 按一下 [必要權限] > [授與權限] > [是]。

    ![Azure Stack 上的 App Service SSO 授與][13]

18. 返回 **CN0-VM**，然後再度開啟 [Web 雲端管理主控台]。

19. 選取左窗格中的 [設定] 節點，然後尋找 **ApplicationClientSecret** 設定。

20. 以滑鼠右鍵按一下並選取 [編輯]。 貼上步驟 15 產生的金鑰，然後按一下 [確定]。

    ![Azure Stack 上的 App Service 應用程式金鑰][14]

21. 開啟系統管理員的 PowerShell 視窗。 瀏覽至步驟 7 中複製指令碼檔案與憑證的目錄。

22. 執行指令碼檔案。 此指令碼檔案會在 Azure Stack 上的 App Service 設定中輸入屬性，並在所有前端與管理角色上起始修復作業。

| 參數 | 必要/選用 | 預設值 | 描述 |
| --- | --- | --- | --- |
| DirectoryTenantName | 強制 | Null | Azure AD 租用戶識別碼。 提供 GUID 或字串，例如 myazureaaddirectory.onmicrosoft.com |
| TenantAzure Resource ManagerEndpoint | 強制 | management.local.azurestack.external | 租用戶 Azure Resource Manager 端點 |
| AzureStackCredential | 強制 | Null | Azure AD 系統管理員 |
| CertificateFilePath | 強制 | Null | 稍早產生之身分識別應用程式憑證檔案的路徑 |
| CertificatePassword | 強制 | Null | 用來保護憑證私密金鑰的密碼 |
| DomainName | 必要 | local.azurestack.external | Azure Stack 區域和網域尾碼 |
| AdfsMachineName | 選用 | 若是 Azure AD 部署即會忽略，但在 AD FS 部署中為必要的。 AD FS 電腦名稱，例如 AzS-ADFS01.azurestack.local |

## <a name="configure-an-ad-fs-service-principal-for-virtual-machine-scale-set-integration-on-worker-tiers-and-sso-for-the-azure-functions-portal-and-advanced-developer-tools"></a>針對背景工作層上的虛擬機器擴展集整合設定 AD FS 服務主體，以及針對 Azure Functions 入口網站設定 SSO 和進階開發人員工具

>[!NOTE]
> 這些步驟只適用於 AD FS 保護的 Azure Stack 環境。

系統管理員必須設定 SSO 來執行下列動作：

* 針對背景工作層上的虛擬機器擴展集整合設定服務主體。
* 啟用 App Service (Kudu) 內的進階開發人員工具。
* 讓 Azure Functions 入口網站體驗可供使用。 

請遵循下列步驟：

1. 以 azurestack\azurestackadmin 身分開啟 PowerShell 執行個體。

2. 移至在[先決條件步驟](#Download-Required-Components)中下載並解壓縮的指令碼位置。

3. [安裝](azure-stack-powershell-install.md)及[設定 Azure Stack PowerShell 環境](azure-stack-powershell-configure-admin.md)。

4. 在同一個 PowerShell 工作階段中，執行 **CreateIdentityApp.ps1** 指令碼。 當系統提示您提供 Azure AD 租用戶識別碼時，請輸入 **ADFS**。

5. 在 [認證] 視窗中，輸入您的 AD FS 服務管理帳戶和密碼。 按一下 [確定] 。

6. 提供[稍早建立之憑證](# Create certificates to be used by App Service on Azure Stack)的憑證檔案路徑和憑證密碼。 根據預設值，針對此步驟建立的憑證是 sso.appservice.local.azurestack.external.pfx。

7. 指令碼會在租用戶 Azure AD 中建立新的應用程式，並產生名為 **UpdateConfigOnController.ps1** 的新 PowerShell 指令碼。

8. 使用遠端桌面工作階段，將身分識別應用程式憑證檔案和產生的指令碼複製到 **CN0-VM**。

9. 返回 **CN0-VM**。

10. 開啟系統管理員的 PowerShell 視窗，然後瀏覽到步驟 7 中複製指令碼檔案與憑證的目錄。

11. 執行指令碼檔案。 此指令碼檔案會在 Azure Stack 上的 App Service 設定中輸入屬性，並在所有前端與管理角色上起始修復作業。

| 參數 | 必要/選用 | 預設值 | 描述 |
| --- | --- | --- | --- |
| DirectoryTenantName | 強制 | Null | 對 AD FS 環境使用 **ADFS** |
| TenantAzure Resource ManagerEndpoint | 強制 | management.local.azurestack.external | 租用戶 Azure Resource Manager 端點 |
| AzureStackCredential | 強制 | Null | AD FS 服務管理帳戶 |
| CertificateFilePath | 強制 | Null | 稍早產生之身分識別應用程式憑證檔案的路徑 |
| CertificatePassword | 強制 | Null | 用來保護憑證私密金鑰的密碼 |
| DomainName | 必要 | local.azurestack.external | Azure Stack 區域和網域尾碼 |
| AdfsMachineName | 選用 | AD FS 電腦名稱，例如 AzS-ADFS01.azurestack.local |

## <a name="test-drive-app-service-on-azure-stack"></a>測試 Azure Stack 上的 App Service

當您部署並註冊 App Service 資源提供者之後，請測試它，以確定租用戶可以部署 Web、行動裝置版及 API 應用程式。

> [!NOTE]
> 您需要在方案內建立含有 Microsoft.Web 命名空間的供應項目。 然後，您需要具有訂閱此供應項目的租用戶訂用帳戶。 如需詳細資訊，請參閱[建立供應項目](azure-stack-create-offer.md)和[建立方案](azure-stack-create-plan.md)。
>
您*必須*具有租用戶訂用帳戶，才能建立使用 Azure Stack 上之 App Service 的應用程式。 服務管理員只能在管理入口網站內完成的功能，與 App Service 的資源提供者管理有關。 這些功能包括新增容量、設定部署來源，以及新增背景工作層和 SKU。
>
從第三個技術預覽開始，若要建立 Web、行動裝置版、API 及 Azure Functions 應用程式，您必須使用租用戶入口網站，並具有租用戶訂用帳戶。 

1. 在 Azure Stack 租用戶入口網站中，按一下 [新增] > [Web + 行動] > [Web 應用程式]。

2. 在 [Web 應用程式] 刀鋒視窗中，於 [Web 應用程式] 方塊中輸入名稱。

3. 按一下 [資源群組] 下方的 [新增]。 在 [資源群組] 方塊中輸入名稱。

4. 按一下 [App Service 方案/位置] > [新建]。

5. 在 [App Service 方案] 刀鋒視窗中，於 [App Service 方案] 方塊中輸入名稱。

6. 按一下 [定價層] > [免費 - 共用] 或 [共用 - 共用] > [選取] > [確定] > [建立]。

7. 新 Web 應用程式的磚隨即出現在儀表板上。 按一下此磚。

8. 在 [Web 應用程式] 刀鋒視窗中，按一下 [瀏覽] 以檢視此應用程式的預設網站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 網站 (選擇性)

1. 在 Azure Stack 租用戶入口網站中，按一下 [+]、移至 Azure Marketplace、部署 Django 網站，然後等待順利完成。 Django Web 平台會使用以檔案系統為基礎的資料庫。 它不需要任何額外的資源提供者，例如 SQL 或 MySQL。

2. 如果您也會部署 MySQL 資源提供者，就可以從 Marketplace 部署 WordPress 網站。 當系統提示您提供資料庫參數時，請使用您選擇的使用者名稱和伺服器名稱，以 User1@Server1 形式輸入使用者名稱。

3. 如果您也會部署 SQL Server 資源提供者，就可以從 Marketplace 部署 DNN 網站。 當系統提示您提供資料庫參數時，請選擇執行 SQL Server 且已連線到您資源提供者之電腦中的資料庫。

## <a name="next-steps"></a>後續步驟

您也可以嘗試其他[平台即服務 (PaaS) 服務](azure-stack-tools-paas-services.md)。

- [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-exe-start.png
[2]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-cloud-configuration.png
[3]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-subscription-location-populated.png
[4]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-resource-group-sql.png
[5]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-certificates.png
[6]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-configuration.png
[7]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-vm-image-selection.png
[8]: ./media/azure-stack-app-service-deploy/app-service-exe-default-entries-step-role-credentials.png
[9]: ./media/azure-stack-app-service-deploy/app-service-exe-selection-summary.png
[10]: ./media/azure-stack-app-service-deploy/app-service-exe-installation-progress.png
[11]: ./media/azure-stack-app-service-deploy/managed-servers.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sso-keys.png
[13]: ./media/azure-stack-app-service-deploy/app-service-sso-grant.png
[14]: ./media/azure-stack-app-service-deploy/app-service-application-secret.png

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

