---
title: "從 Azure Stack 管理 Windows Azure 套件虛擬機器 | Microsoft Docs"
description: "了解如何從 Azure Stack 中的使用者入口網站管理 Windows Azure 套件 (WAP) VM。"
services: azure-stack
documentationcenter: 
author: walterov
manager: byronr
editor: 
ms.assetid: 213c2792-d404-4b44-8340-235adf3f8f0b
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: walterov
ms.openlocfilehash: b07a18055d149e20cd605a892063eccecf3df8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-windows-azure-pack-virtual-machines-from-azure-stack"></a>從 Azure Stack 管理 Windows Azure 套件虛擬機器

適用於：Azure Stack 開發套件

在 Azure Stack 開發套件環境中，您可以從 Azure Stack 使用者入口網站中，啟用在 Windows Azure 套件上所執行租用戶虛擬機器的存取權。 使用者可以使用 Azure Stack 入口網站來管理其現有的 IaaS 虛擬機器和虛擬網路。 這些資源都可以透過基礎的 Service Provider Foundation (SPF) 和 Virtual Machine Manager (VMM) 元件，在 Windows Azure 套件上使用。 具體而言，使用者可以：

* 瀏覽資源
* 檢查設定值
* 停止或啟動虛擬機器
* 透過遠端桌面通訊協定 (RDP) 連線到虛擬機器
* 建立和管理檢查點
* 刪除虛擬機器和虛擬網路

此功能是由適用於 Azure Stack 的 Windows Azure 套件連接器所提供 (預覽)。 本文示範如何設定適用於單一節點 Azure Stack 環境的連接器。

針對此預覽版本的連接器，請注意以下事項：
* 請僅在測試環境中使用 Windows Azure 套件連接器 (針對 Azure Stack 和 Windows Azure 套件)，而不要在生產環境部署中使用。
* 您必須擁有 Windows Azure 套件更新彙總套件 (UR) 9.1 或更新版本，以及 System Center SPF 和 VMM UR 9 或更新版本。
* VMM 和 SPF 元件可以是 System Center 2012 R2 或 System Center 2016。
* 您必須在 Azure Stack 和 Windows Azure 套件都執行設定步驟。
* 此指示適用於非雲端平台系統 (CPS) 環境。
* 若要檢閱已知的問題，請參閱 [Microsoft Azure Stack 疑難排解](azure-stack-troubleshooting.md)。


## <a name="architecture"></a>架構
下圖顯示 Windows Azure 套件連接器的主要元件。

![Windows Azure 套件連接器元件](media/azure-stack-manage-wap/image1.png)

請注意下列詳細資料：
* Azure Stack 使用者入口網站會從這兩個雲端 (Azure Stack 和 Windows Azure 套件) 存取資源資訊。
* 使用者必須具有在這兩種環境中都有效的帳戶。
* Azure Stack 使用者入口網站必須具有在 Windows Azure 套件上所執行元件的網路存取權。
* 在圖表中的 **WAP** 區段，您可以看到新的 Windows Azure 套件連接器模組 (WAP 擴充功能和連接器) 以及包含 SPF 和 VMM 元件的現有 Windows Azure 套件租用戶 API。


## <a name="identity-management"></a>身分識別管理
Windows Azure 套件租用戶 API 必須信任 Azure Stack 安全性權杖服務 (STS)。

當使用者透過 Azure Stack 入口網站執行目標為 Windows Azure 套件資源的動作時，入口網站會使用 Windows Azure 套件租用戶 API。 因此，提供的使用者驗證權杖必須來自受信任的 STS。 請參閱下圖：

![Windows Azure 套件連接器驗證的圖表](media/azure-stack-manage-wap/image2.png)

在開發套件環境中，Windows Azure 套件和 Azure Stack 具有獨立的識別提供者。 從 Azure Stack 入口網站存取這兩種環境的使用者，在這兩個識別提供者中使用者主體名稱 (UPN) 的名稱必須相同。 例如，帳戶 *azurestackadmin@azurestack.local* 在 Windows Azure 套件的 STS 中也應存在。 若 AD FS 未設定為支援連出的信任關係，您會從 Windows Azure 套件元件 (租用戶 API) 建立對 AD FS Azure Stack 執行個體的信任。

## <a name="prerequisites"></a>必要條件

### <a name="download-the-windows-azure-pack-connector"></a>下載 Windows Azure 套件連接器
在 [Microsoft 下載中心](https://aka.ms/wapconnectorazurestackdlc)下載 .exe 檔案，並將其解壓縮到本機電腦。 稍後，您要將內容複製到可以存取您 Windows Azure 套件環境的電腦。

### <a name="deployment-option-requirement"></a>部署選項需求
為了與 Windows Azure 套件整合，您可以使用 AD FS 選項或 Azure Active Directory 選項來部署 Azure Stack。

### <a name="connectivity-requirements"></a>連線能力需求
1. 請從可存取 Azure Stack 使用者入口網站的電腦，確定您可以透過網頁瀏覽器來存取 Windows Azure 套件租用戶入口網站。
2. Azure Stack 上的 AzS-WASP01 虛擬機器必須能夠連線到 Windows Azure 套件租用戶入口網站的電腦。 請使用 Ping.exe 驗證網路連線能力。 
3.  您必須擁有新連接器服務的有效憑證。 這些 SSL 憑證必須由受信任的憑證授權單位 (CA) 發行。 您不得使用自我簽署的憑證。 Azure Stack (尤其是 AzS-WASP01 VM) 和任何其他租用戶可用來存取 Azure Stack 使用者入口網站的電腦，都必須信任該 SSL 憑證。
 
    >[!NOTE]
    由於 AzS-WASP01 會執行包含伺服器核心安裝選項的 Windows Server，因此您可以使用命令列工具 (例如 Certutil.ext) 來匯入憑證。 例如，您可將 .cer 檔案複製到 AzS-WASP01 的 c:\temp 上，並執行命令 **certutil -addstore "CA" "c:\temp\certname.cer"**。

4.  若要透過 Azure Stack 入口網站建立到 Windows Azure 套件租用戶虛擬機器的 RDP 連線，Windows Azure 套件環境必須允許到租用戶虛擬機器的遠端桌面流量。
5.  針對 Azure Stack 疊租用戶虛擬機器與 Windows Azure 套件租用戶虛擬機器之間的連線，它們的外部 IP 位址必須可跨兩個環境路由傳送。 這種連線能力也可能包括建立 DNS 伺服器的關聯，以解析在環境之間的虛擬機器名稱。

### <a name="iis-requirements"></a>IIS 需求
裝載 Windows Azure 套件租用戶入口網站的電腦 (這可能是實體主機、一部虛擬機器或多部虛擬機器) 必須安裝 URL 重寫 IIS 擴充功能。 如果尚未安裝，您可以從[這裡](https://www.iis.net/downloads/microsoft/url-rewrite)下載。 若有多部虛擬機器裝載租用戶入口網站，請在每部虛擬機器都上安裝擴充功能。

## <a name="configure-azure-stack"></a>設定 Azure Stack
設定 Windows Azure 套件連接器之前，您必須先在 Azure Stack 使用者入口網站中啟用多重雲端模式。 此模式可讓使用者選取要存取其資源的雲端。

若要啟用多重雲端模式，您必須在部署 Azure Stack 後執行 Add-AzurePackConnector.ps1 指令碼。 下表描述指令碼參數。


|  參數 | 說明 | 範例 |   
| -------- | ------------- | ------- |  
| AzurePackClouds | Windows Azure 套件連接器的 URI。 這些 URI 應該對應到 Windows Azure 套件租用戶入口網站。 | @{CloudName = "AzurePack1"; CloudEndpoint = "https://waptenantportal1:40005"},@{CloudName = "AzurePack2"; CloudEndpoint = "https://waptenantportal2:40005"}<br><br>  (依預設，此連接埠值為 40005。) |  
| AzureStackCloudName | 代表本機 Azure Stack 雲端的標籤。| "AzureStack" |
| DisableMultiCloud | 停用多重雲端模式的開關。| N/A |
| | |

您可在部署之後緊接著執行 Add-AzurePackConnector.ps1 指令碼，也可以稍後再執行。 若要在部署後緊接著執行指令碼，請使用與完成 Azure Stack 部署相同的 Windows PowerShell 工作階段。 否則，您也可用系統管理員身分 (登入為 azurestackadmin 帳戶) 開啟新的 Windows PowerShell 工作階段。

1. 使用下列命令 (具有您環境專屬的值) 執行 Add-AzurePackConnector.ps1 指令碼。 請注意 Add-AzurePackConnector 指令碼可讓您新增多個 Windows Azure 套件連接器端點。
 
 ```powershell
    $cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local", `
    (ConvertTo-SecureString -String "<password>" -AsPlainText -Force))
    $session = New-PSSession -ComputerName 'azs-ercs01' `
     -Credential $cred `
     -ConfigurationName PrivilegedEndpoint `
     -Authentication Credssp

    # Enable Multicloud
    Invoke-Command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
    @{CloudName = "AzurePack_1"; CloudEndpoint = "https://waptenantportal1:40005"},`
    @{CloudName = "AzurePack_2"; CloudEndpoint = "https://waptenantportal2:40005" } `
    -AzureStackCloudName "AzureStack" }

 ```
> [!NOTE]
> 在目前的組建中有個問題，就是 Add-AzurePackConnector 指令碼結束後，會停留在輪詢迴圈中一段很長的時間 (數分鐘)，然後才會結束。 您看到 **VERBOSE：步驟「設定 Azure 套件連接器」狀態：「成功」**訊息後，就可以停止指令碼，或等到它自己停止。 由於已設定成功，因此兩者將不會有所差別。

2. 請針對您指定的每個 Windows Azure 套件環境，記下此指令碼所產生的輸出檔 (每個環境各一個)。 檔案位於：\\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput。 這些檔案包含設定目標 Windows Azure 套件環境所需的資訊。 稍後在這些指示中，您會將這個檔案作為參數傳遞給指令碼。 這個檔案包含下列資訊：

    * **AzurePackConnectorEndpoint**：包含 Windows Azure 套件連接器服務的端點。
    * **AuthenticationIdentityProviderPartner**：包含下列值組：
        * Windows Azure 套件租用戶 API 需要信任的驗證權杖簽署憑證，以便接受來自 Azure Stack 入口網站擴充功能的呼叫。

        * 與簽署憑證相關聯的「領域」。 例如：https://adfs.local.azurestack.global.external/adfs/c1d72562-534e-4aa5-92aa-d65df289a107/。

3.  瀏覽至包含輸出檔的資料夾 (\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput)，並將檔案複製到本機電腦。 檔案會看起來像這樣：AzurePack-06-27-15-50.txt。

4.  測試設定。

    a. 開啟瀏覽器並登入 Azure Stack 使用者入口網站 (https://portal.local.azurestack.external/)。
    
    b. 以租用戶身分登入且入口網站載入後，您將會看到無法從 Azure 套件雲端擷取訂用帳戶或擴充功能的錯誤訊息。 按一下 [確定] 以關閉訊息。 (在您設定 Windows Azure 套件後這些錯誤訊息就會消失。)

    c. 請注意入口網站左上角的 [雲端] 下拉式清單。

    ![Azure Stack 使用者介面中的雲端選取器](media/azure-stack-manage-wap/image3.png)

## <a name="configure-windows-azure-pack"></a>設定 Windows Azure 套件
只有此預覽版本連接器需要手動設定 Windows Azure 套件。

>[!IMPORTANT]
針對此預覽版本，請僅在測試環境中使用 Windows Azure 套件連接器，而不要在生產環境部署中使用。

1.  在 Windows Azure 套件租用戶入口網站虛擬機器上安裝連接器 MSI 檔案，並安裝憑證。 (若您有多部租用戶入口網站虛擬機器，則必須在每部虛擬機器上都完成此步驟。)

    a. 在 [檔案總管] 中，將 (您之前下載的) **WAPConnector** 資料夾複製到租用戶入口網站虛擬機器上名為 **c:\temp** 的資料夾中。

    b. 開啟對租用戶入口網站虛擬機器的主控台或 RDP 連線。

    c. 將目錄變更為 **c:\temp\wapconnector\setup\scripts**，並執行 **Install-Connector.ps1** 指令碼來安裝三個 MSI 檔案。 不需要任何參數。

     ```powershell
    cd C:\temp\wapconnector\setup\scripts\

    .\Install-Connector.ps1
    ```
     d. 將目錄變更為 **c:\inetpub**，並確認已安裝這三個新網站：

       * MgmtSvc-Connector

       * MgmtSvc-ConnectorExtension

       * MgmtSvc-ConnectorController

    e. 從相同的 **c:\temp\wapconnector\setup\scripts** 資料夾中，執行 **Configure-Certificates.ps1** 指令碼來安裝憑證。 依預設，會使用 Windows Azure 套件租用戶入口網站中可用的相同憑證。 請確定這是有效的憑證 (受到 Azure Stack AzS-WASP01 虛擬機器和任何存取 Azure Stack 入口網站的用戶端電腦信任)。 否則，通訊將無法運作。 (或者，您也可以使用 -Thumbprint 參數，明確地傳遞憑證指紋作為參數。)

     ```powershell
        cd C:\temp\wapconnector\setup\scripts\

        .\Configure-Certificates.ps1
    ```

    f. 若要完成這三項服務的設定，請執行 **Configure-WapConnector.ps1** 指令碼以更新 Web.config 檔案參數。

    |  參數 | 說明 | 範例 |   
    | -------- | ------------- | ------- |  
    | TenantPortalFQDN | Windows Azure 套件租用戶入口網站 FQDN。 | tenant.contoso.com | 
    | TenantAPIFQDN | Windows Azure 套件租用戶 API FQDN。 | tenantapi.contoso.com  |
    | AzureStackPortalFQDN | Azure Stack 使用者入口網站 FQDN。 | portal.local.azurestack.external |
    | | |
    
     ```powershell
    .\Configure-WapConnector.ps1 -TenantPortalFQDN "tenant.contoso.com" `
         -TenantAPIFQDN "tenantapi.contoso.com" `
         -AzureStackPortalFQDN "portal.local.azurestack.external"
    ```
    g. 若您有多部租用戶入口網站虛擬機器，請在每部虛擬機器上重複步驟 1。

2. 在每部 Windows Azure 套件租用戶 API 虛擬機器上安裝新的租用戶 API MSI。

    a. 如果負載平衡器正在使用中，您可能會想要將虛擬機器標示為離線。

    b. 在 [檔案總管] 中，將 **WAPConnector** 資料夾複製到每部租用戶 API 機器上名為 **c:\temp** 的資料夾中。

    c. 將您稍早儲存的 AzurePackConnectorOutput.txt 檔案複製到 **c:\temp\WAPConnector**。

    d. 開啟對 (您將檔案複製到的) 租用戶 API VM 的主控台或 RDP 連線。
    
    e. 將目錄變更為 **c:\temp\wapconnector\setup\scripts**，並執行 **Update-TenantAPI.ps1**。 這個新版本的 WAP 租用戶 API 包含變更，不只可啟用與目前 STS 間的信任關係，還可以啟用與 Azure Stack AD FS 執行個體間的信任關係。

     ```powershell
    cd C:\temp\wapconnector\setup\packages\

    .\Update-TenantAPI.ps1
    ```

    f.  在每部執行租用戶 API 的虛擬機器上重複步驟 2。
3. **只從其中一部**租用戶 API VM 上，執行 Configure-TrustAzureStack.ps1 指令碼，以新增與租用戶 API 和 Azure Stack 上 AD FS 執行個體之間的信任關係。 您必須使用對 Microsoft.MgmtSvc.Store 資料庫具有系統管理員存取權的帳戶。 此指令碼具有下列參數︰

    | 參數 | 說明 | 範例 |
    | --------- | ------------| ------- |
   | SqlServer | 包含 Microsoft.MgmtSvc.Store 資料庫的 SQL Server 名稱。 這是必要參數。 | SQLServer | 
   | DataFile | 此輸出檔是稍早在設定 Azure Stack 多重雲端模式期間所產生的。 這是必要參數。 | AzurePack-06-27-15-50.txt | 
   | PromptForSqlCredential | 表示指令碼應該以互動方式提示您，要連線到 SQL Server 執行個體時所使用的 SQL 驗證認證。 指定的認證必須有足夠的權限，可解除安裝資料庫、結構描述及刪除使用者登入。 如果未提供，指令碼會假設目前的使用者內容具有存取權。 | 不需要任何值。 |
   |  |  |

    如果您不知道要使用的 SQL 伺服器，可以進行探索。 請連線到租用戶 API 的電腦，使用 Unprotect-MgmtSvc 命令取消保護租用戶 API Web.config 檔案，並尋找連接字串中的伺服器名稱。 請記得再次執行 Protect-MgmtSvc 以保護租用戶 API Web.config 檔案。

  ```powershell
  cd C:\temp\wapconnector\setup\scripts\

 .\Configure-TrustAzureStack.ps1 -SqlServer "SQLServer" `
       -DataFile "C:\temp\wapconnector\AzurePackConnectorOutput.txt"
  ```

## <a name="example"></a>範例
下列範例會示範在單一節點 Azure Stack 設定下完整的 Windows Azure 套件連接器部署，以及兩種 Windows Azure 套件快速安裝。 (每種快速安裝都是在單一電腦上，範例名稱為 *wapcomputer1* 和 *wapcomputer2*。)

```powershell
# Run the following script on the Azure Stack host
$cred = New-Object System.Management.Automation.PSCredential("cloudadmin@azurestack.local",`
     (ConvertTo-SecureString -String "p@ssw0rd" -AsPlainText -Force))
$session = New-PSSession -ComputerName 'azs-ercs01' -Credential $cred `
     -ConfigurationName PrivilegedEndpoint -Authentication Credssp
 
# Enable Multicloud
invoke-command -Session $session -ScriptBlock { Add-AzurePackConnector -AzurePackClouds `
     @{CloudName = "AzurePack_1"; CloudEndpoint = "https://wapcomputer1.contoso.com:40005"},`
     @{CloudName = "AzurePack_2"; CloudEndpoint = "https://wapcomputer2.contoso.com:40005"}`
     -AzureStackCloudName "AzureStack" }  

```
從 [Microsoft 下載中心](https://aka.ms/wapconnectorazurestackdlc)下載 .exe 檔案、將其解壓縮，並將 WAPConnector 資料夾複製到 Windows Azure 套件電腦上的 **c:\temp** 資料夾中。 將先前指令碼中所產生的輸出檔案 (位於 \\\su1fileserver\SU1_Infrastructure_1\AzurePackConnectorOutput) 複製到 **c:\temp\WAPConnector** 資料夾中。 (檔案會看起來像這樣：AzurePack-06-27-15-50.txt。)然後執行下列指令碼 (每次一個 Windows Azure 套件執行個體)：

 ```powershell
# Install Connector components
cd C:\temp\WAPConnector\Setup\Scripts
.\Install-Connector.ps1

# Configure Certificates for the new Connector services
.\Configure-Certificates.ps1

# Configure the Connector services
.\Configure-WapConnector.ps1 -TenantPortalFQDN "wapcomputer1.contoso.com" `
     -TenantAPIFQDN "wapcomputer1.contoso.com" `
     -AzureStackPortalFQDN "portal.local.azurestack.external"

# Install the updated TenantAPI
.\Update-TenantAPI.ps1

# Establish trust with the Azure Stack AD FS
.\Configure-TrustAzureStack.ps1 -SqlServer "wapcomputer1" `
     -DataFile "C:\temp\wapconnector\AzurePack-06-27-15-50.txt" 

```
## <a name="troubleshooting-tips"></a>疑難排解秘訣
1.  確保 Azure Stack 與 Windows Azure 套件之間有網路連線能力。 在任何存取 Azure Stack 入口網站的租用戶電腦，與執行新連接器服務的 Windows Azure 套件租用戶入口網站虛擬機器之間，都應具有連線能力。
2.  確保所有指定的 FQDN 都正確。
3.  確保 Azure Stack (尤其是 AzS-WASP01 VM) 和任何其他租用戶可用來存取 Azure Stack 使用者入口網站的電腦，都必須信任用於新連接器服務上的 SSL 憑證。
4. 若要檢閱已知的問題，請參閱 [Microsoft Azure Stack 疑難排解](azure-stack-troubleshooting.md)。


## <a name="next-steps"></a>後續步驟
[使用 Azure Stack 中系統管理員和使用者的入口網站](azure-stack-manage-portals.md)
