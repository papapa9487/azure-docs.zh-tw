---
title: "部署 Azure Stack 開發套件 | Microsoft Docs"
description: "了解如何準備 Azure Stack 開發套件，以及執行 PowerShell 指令碼來加以部署。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 7c320c6ba51ae0800407aab7aee92c42b2b441a7
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>部署 Azure Stack 開發套件

適用於：Azure Stack 開發套件

若要部署 [Azure Stack 開發套件](azure-stack-poc.md)，您必須完成下列步驟：

1. [下載部署封裝](https://azure.microsoft.com/overview/azure-stack/try/?v=try)以取得 Cloudbuilder.vhdx。
2. 準備 cloudbuilder.vhdx，以設定您要安裝開發套件的電腦 (開發套件主機)。 在這個步驟之後，開發套件主機將會開機進入 Cloudbuilder.vhdx。
3. 在開發套件主機上部署開發套件。

> [!NOTE]
> 為獲得最佳結果，即使您想要使用已中斷連線的 Azure Stack 環境，最好是在已連線到網際網路時部署。 如此一來，就可以在部署期間啟動開發套件安裝隨附的 Windows Server 2016 評估版。

## <a name="download-and-extract-the-development-kit"></a>下載並將開發套件解壓縮
1. 開始下載之前，請確定您的電腦符合下列必要條件：

  - 除了作業系統磁碟以外，電腦在四個個別相同的邏輯硬碟上必須有至少 60 GB 的可用磁碟空間。
  - 必須安裝 [.NET framework 4.6 (或更新版本)](https://aka.ms/r6mkiy)。

2. [移至 [開始使用] 頁面](https://azure.microsoft.com/overview/azure-stack/try/?v=try)，下載 Azure Stack 開發套件，提供您的詳細資料，然後按一下 [提交]。
3. 下載並執行 [Azure Stack 開發套件的部署檢查程式](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409)必要條件檢查程式指令碼。 此獨立指令碼會經歷 Azure Stack 開發套件設定期間所進行的先決條件檢查。 在下載較大型的 Azure Stack 開發套件之前，這可用來確認您符合硬體和軟體需求。
4. 在 [下載軟體] 底下，按一下 [Azure Stack 開發套件]。

  > [!NOTE]
  > ASDK 下載 (AzureStackDevelopmentKit.exe) 本身大約 10GB。 如果您選擇也要下載 Windows Server 2016 評估版 ISO 檔案，則下載大小會增加至大約 17GB。 在 ASDK 安裝完成之後，您可以使用該 ISO 檔案來建立 Windows Server 2016 虛擬機器映像並將它新增至 Azure Stack Marketplace。 請注意，此 Windows Server 2016 評估映像只能搭配 ASDK 使用，而且必須遵循 ASDK 授權條款。

5. 下載完成之後，按一下 [執行] 以啟動 ASDK 自我解壓縮程式 (AzureStackDevelopmentKit.exe)。
6. 檢閱並接受自我解壓縮程式精靈的 [授權合約] 頁面上顯示的授權合約，然後按 [下一步]。
7. 檢閱自我解壓縮程式精靈的 [重要聲明] 頁面上顯示的隱私權聲明資訊，然後按 [下一步]。
8. 在自我解壓縮程式精靈的 [選取目的地位置] 頁面上，選取 Azure Stack 安裝程式檔案要解壓縮至的位置，然後按 [下一步]。 預設位置是「目前資料夾」\Azure Stack 開發套件。 
9. 檢閱自我解壓縮程式精靈的 [準備解壓縮] 頁面上的目的地位置摘要，然後按一下 [解壓縮] 來解壓縮 CloudBuilder.vhdx (大約 25GB) 和 ThirdPartyLicenses.rtf 檔案。 這個程序需要一些時間才會完成。
10. 將 CloudBuilder.vhdx 檔案複製或移動至 ASDK 主機電腦上的 C:\ 磁碟機根目錄 (C:\CloudBuilder.vhdx)。

> [!NOTE]
> 將檔案解壓縮之後，您可以刪除 .EXE 和 .BIN 檔案來恢復硬碟空間。 或者，您可以備份這些檔案，而如果您需要重新部署 ASDK，就不需要下載這些檔案。

## <a name="deploy-the-asdk-using-a-guided-experience"></a>使用引導式體驗來部署 ASDK
使用藉由下載並執行 asdk-installer.ps1 PowerShell 指令碼所提供的圖形化使用者介面 (GUI) 可以部署 ASDK。

> [!NOTE]
> Azure Stack 開發套件的安裝程式使用者介面是以 WCF 和 PowerShell 為基礎的開放來源指令碼。

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>使用引導式使用者體驗來準備開發套件主機
必須先備妥 ASDK 環境，才可以在主機電腦上安裝 ASDK。
1. 以本機系統管理員的身分登入 ASDK 主機電腦。
2. 確保 CloudBuilder.vhdx 檔案已移至 C:\ 磁碟機的根目錄 (C:\CloudBuilder.vhdx)。
3. 執行下列指令碼，將開發套件安裝程式檔案 (asdk-installer.ps1) 從 [Azure Stack GitHub 工具存放庫](https://github.com/Azure/AzureStack-Tools)下載至開發套件主機電腦上的 **C:\AzureStack_Installer** 資料夾：

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 從已提升權限的 PowerShell 主控台，啟動 **C:\AzureStack_Installer\asdk-installer.ps1** 指令碼，然後按一下 [準備環境]。
5. 在安裝程式的 [選取 Cloudbuilder vhdx] 頁面上，瀏覽至您在先前步驟中下載並解壓縮的 **cloudbuilder.vhdx** 檔案並加以選取。 如果您需要在開發套件主機電腦上新增其他驅動程式，您也可以在此頁面上選擇性地啟用 [新增驅動程式] 核取方塊。  
6. 在 [選擇性設定] 頁面上，提供開發套件主機電腦的本機系統管理員帳戶。 

  > [!IMPORTANT]
  > 如果您未提供這些認證，則在設定開發套件期間重新啟動電腦之後，您需要主機的直接或 KVM 存取權。

7. 您也可以在 [選擇性設定] 頁面上提供這些選擇性設定：
    - **電腦名稱**：此選項會設定開發套件主機的名稱。 名稱必須符合 FQDN 需求，且長度必須等於或少於 15 個字元。 預設值是由 Windows 產生的隨機電腦名稱。
    - **時區**：設定開發套件主機的時區。 預設值是 (UTC-8:00) 太平洋時間 (美國和加拿大)。
    - **靜態 IP 組態**：將您的部署設定為使用靜態 IP 位址。 否則，當安裝程式重新開機進入 cloudbuilder.vhx 時，會使用 DHCP 來設定網路介面。
11. 按一下 [下一步]。
12. 如果您在上一個步驟中選擇靜態 IP 組態，您現在必須：
    - 選取網路介面卡。 請先確定您可以連線到介面卡，然後再按一下 [下一步]。
    - 請確定 [IP 位址]、[閘道] 和 [DNS] 值正確無誤，然後按一下 [下一步]。
13. 按一下 [下一步]，以開始準備程序。
14. 當準備指出 [已完成] 時，按一下 [下一步]。
15. 按一下 [立即重新開機]，以開機進入 cloudbuilder.vhdx 並繼續部署程序。


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>使用引導式體驗來部署開發套件
準備 ASDK 主機電腦之後，可以使用下列步驟將 ASDK 部署到 CloudBuilder.vhdx 映像。 
1. 在主機電腦成功開機進入 CloudBuilder.vhdx 映像之後，使用在先前步驟中指定的系統管理員認證進行登入。 
2. 開啟已提升權限的 PowerShell 主控台並執行 **\AzureStack_Installer\asdk-installer.ps1** 指令碼 (目前可能是在 Cloudbuilder.vhdx 映像中的其他磁碟機上)。 按一下 [Install] 。
3. 在 [類型] 下拉式方塊中，選取 [Azure 雲端] 或 [AD FS]。
    - **Azure 雲端**：將 Azure Active Directory (Azure AD) 設定為識別提供者。 若要使用此選項，您需有網際網路連線、Azure AD 目錄租用戶的完整名稱 (採用 *domainname*.onmicrosoft.com 形式)，以及所指定目錄的全域管理員認證。 
    - **AD FS**：預設戳記目錄服務會作為識別提供者。 用於登入的預設帳戶是 azurestackadmin@azurestack.local，而要使用的密碼是您在設定過程中所提供的密碼。
4. 在 [本機系統管理員密碼] 底下的 [密碼] 方塊中，輸入本機系統管理員密碼 (必須符合目前設定的本機系統管理員密碼)，然後按一下 [下一步]。
5. 選取要用於開發套件的網路介面卡，然後按一下 [下一步]。
6. 為 BGPNAT01 虛擬機器選取 DHCP 或靜態網路組態。
    - **DHCP** (預設值)：虛擬機器會從 DHCP 伺服器取得 IP 網路組態。
    - **靜態**：只有當 DHCP 無法為 Azure Stack 指派有效的 IP 位址來存取網際網路時，才使用此選項。 指定靜態 IP 位址 時，必須以 CIDR 格式 (例如，10.0.0.5/24) 一併指定子網路遮罩長度。
7. 或者，設定下列值：
    - **VLAN 識別碼**：設定 VLAN 識別碼。 只有當主機與 AzS-BGPNAT01 必須設定 VLAN 識別碼來存取實體網路 (以及網際網路) 時，才使用此選項。 
    - **DNS 轉寄站**：DNS 伺服器會在 Azure Stack 部署期間建立。 若要允許解決方案內的電腦解析戳記以外的名稱，請提供您現有架構 DNS 伺服器。 戳記內的 DNS 伺服器會將未知的名稱解析要求轉送至這部伺服器。
    - **時間伺服器**：此必填欄位會設定開發套件所要使用的時間伺服器。 此參數必須以有效的時間伺服器 IP 位址形式提供。 不支援伺服器名稱。
  
  > [!TIP]
  > 若要尋找時間伺服器 IP 位址，請造訪 [pool.ntp.org](http:\\pool.ntp.org) 或 ping time.windows.com。 
  
8. 按一下 [下一步] 。 
9. 在 [驗證網路介面卡內容] 頁面上，您將會看到一個進度列。 
    - 如果顯示 [無法下載更新]，請遵循頁面上的指示執行。
    - 顯示 [已完成] 時，按一下 [下一步]。
10. 在 [摘要] 頁面上，按一下 [部署]。 您也可以在此複製將用於安裝開發套件的 PowerShell 設定命令。
11. 如果您使用 Azure AD 部署，系統會在設定開始後幾分鐘，提示您輸入 Azure AD 全域系統管理員帳戶認證。
12. 部署程序可能需要幾小時的時間，在這段期間內，系統會自動重新開機一次。 當部署成功時，PowerShell 主控台會顯示：**COMPLETE: Action ‘Deployment’**。 如果部署失敗，您可以從頭[重新部署](azure-stack-redeploy.md)或使用下列 PowerShell 命令，在已提升權限的相同 PowerShell 視窗中，從上一個成功步驟重新開始部署：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > 如果您想要監視部署進度，當開發套件主機在設定期間重新啟動時，請以 azurestack\AzureStackAdmin 的身分登入。 如果您在電腦加入網域之後，以本機系統管理員的身分登入，將不會看到部署進度。 請勿重新執行部署，而是以 azurestack\AzureStackAdmin 的身分登入，以驗證執行狀態。
   

## <a name="deploy-the-asdk-using-powershell"></a>使用 PowerShell 部署 ASDK
先前的步驟已帶您完成使用引導式使用者體驗部署 ASDK。 或者，您可以依照本節中的步驟，使用 PowerShell 在開發套件主機上部署 ASDK。

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>將 ASDK 主機電腦設定為從 CloudBuilder.vhdx 開機
這些命令會將 ASDK 主機電腦設定為從已下載並解壓縮的 Azure Stack 虛擬硬碟 (CloudBuilder.vhdx) 開機。 完成這些步驟之後，重新啟動 ASDK 主機電腦。

1. 以系統管理員身分啟動命令提示字元。
2. 執行 `bcdedit /copy {current} /d "Azure Stack"`
3. 複製 (CTRL + C) 傳回的 CLSID 值，包括必要的 {}。 這個值也就是 {CLSID}，且必須在其餘步驟中貼入 (CTRL + V 或按一下滑鼠右鍵)。
4. 執行 `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. 執行 `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. 執行 `bcdedit /set {CLSID} detecthal on` 
7. 執行 `bcdedit /default {CLSID}`
8. 若要驗證開機設定，請執行 `bcdedit`。 
9. 確定 CloudBuilder.vhdx 檔案已移至 C:\ 磁碟機的根目錄 (C:\CloudBuilder.vhdx)，並重新啟動開發套件主機電腦。 ASDK 主機重新啟動後，它現在應該預設為從 CloudBuilder.vhdx VM 開機。 

### <a name="prepare-the-development-kit-host-using-powershell"></a>使用 PowerShell 來準備開發套件主機 
在開發套件主機電腦成功開機進入 CloudBuilder.vhdx 映像之後，您可開啟已提升權限的 PowerShell 主控台並在此區段中執行命令，以在開發套件主機上部署 ASDK。

> [!IMPORTANT] 
> ASDK 安裝支援正好一個網路介面卡 (NIC) 進行網路運作。 如果您有多個 NIC，在執行部署指令碼之前，請先確定僅啟用一個 NIC (並停用其他所有 NIC)。

您可使用 Azure AD 或 AD FS 作為識別提供者來部署 Azure Stack。 Azure Stack、資源提供者和其他應用程式使用這兩者的方式相同。 若要深入了解 Azure Stack 中的 AD FS 支援項目，請參閱[主要功能和概念](.\azure-stack-key-features.md)一文。

> [!TIP]
> 如果您未提供任何設定參數 (請參閱 InstallAzureStackPOC.ps1 選用參數和以下範例)，系統會提示您輸入必要參數。

### <a name="deploy-azure-stack-using-azure-ad"></a>使用 Azure AD 部署 Azure Stack 
若要**使用 Azure AD 作為識別提供者**來部署 Azure Stack，您必須具有網際網路連線能力 (直接或透過透明 Proxy)。 使用 Azure AD，執行下列 PowerShell 命令來部署開發套件：

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  進入 ASDK 安裝幾分鐘，系統會提示您輸入 Azure AD 認證。 您必須提供 Azure AD 租用戶的全域管理員認證。 

### <a name="deploy-azure-stack-using-ad-fs"></a>使用 AD FS 部署 Azure Stack 
若要**使用 AD FS 作為識別提供者**來部署開發套件，請執行下列 PowerShell 命令 (您只需要新增 -UseADFS 參數)： 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

在 AD FS 部署中，預設戳記目錄服務會作為識別提供者。 用來登入的預設帳戶是 azurestackadmin@azurestack.local，而密碼將會設定為您在 PowerShell 設定命令中提供的密碼。

部署程序可能需要幾小時的時間，在這段期間內，系統會自動重新開機一次。 當部署成功時，PowerShell 主控台會顯示：**COMPLETE: Action ‘Deployment’**。 如果部署失敗，您可以使用 -rerun 參數再次嘗試執行指令碼。 或者，您可以從頭開始[重新部署 ASDK](.\azure-stack-redeploy.md)。
> [!IMPORTANT]
> 如果您想要監視部署進度，在 ASDK 主機重新開機之後，您必須以 AzureStack\AzureStackAdmin 身分登入。 如果您在主機電腦重新啟動 (並加入 azurestack.local 網域) 之後，以本機系統管理員的身分登入，將不會看到部署進度。 請勿重新執行部署，而是以 azurestack 的身分登入來驗證執行狀態。
>

#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 部署指令碼範例
您可以為整個 Azure AD 部署轉寫指令碼。 以下是一些加上註解且包含一些選用參數的範例。

如果您的 Azure AD 身分識別只與**一個** Azure AD 目錄相關聯：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

如果您的 Azure AD 身分識別與**一個以上的** Azure AD 目錄相關聯：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<specific Azure AD directory in the form of domainname.onmicrosoft.com>" -TimeServer 52.168.138.145 #Example time server IP address.
```

如果您的環境**並未**啟用 DHCP，則您必須將下列其他參數包含在上述其中一個選項 (提供範例使用方式)： 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 選用參數
|參數|必要/選用|說明|
|-----|-----|-----|
|AdminPassword|必要|在開發套件部署過程中建立的所有虛擬機器上，設定本機系統管理員帳戶和其他所有使用者帳戶。 此密碼必須符合主機上的目前本機系統管理員密碼。|
|InfraAzureDirectoryTenantName|必要|設定租用戶目錄。 使用此參數來指定 AAD 帳戶有權限管理多重目錄的特定目錄。 AAD 目錄租用戶的完整名稱，格式為 .onmicrosoft.com。|
|TimeServer|必要|使用此參數來指定特定時間伺服器。 此參數必須以有效的時間伺服器 IP 位址形式提供。 不支援伺服器名稱。|
|InfraAzureDirectoryTenantAdminCredential|選用|設定 Azure Active Directory 使用者名稱與密碼。 這些 Azure 認證必須是組織識別碼。|
|InfraAzureEnvironment|選用|選取您要用來註冊此 Azure Stack 部署的 Azure 環境。 選項包括公用 Azure、Azure - 中國、Azure - 美國政府。|
|DNSForwarder|選用|DNS 伺服器會在 Azure Stack 部署期間建立。 若要允許解決方案內的電腦解析戳記以外的名稱，請提供您現有架構 DNS 伺服器。 戳記內的 DNS 伺服器會將未知的名稱解析要求轉送至這部伺服器。|
|NatIPv4Address|DHCP NAT 支援所需|設定 MAS-BGPNAT01 的靜態 IP 位址。 只有當 DHCP 無法指派有效的 IP 位址來存取網際網路時，才使用此參數。|
|NatIPv4Subnet|DHCP NAT 支援所需|透過 NAT 支援用於 DHCP 的 IP 子網路前置詞。 只有當 DHCP 無法指派有效的 IP 位址來存取網際網路時，才使用此參數。|
|PublicVlanId|選用|設定 VLAN 識別碼。 只有當主機與 MAS-BGPNAT01 必須設定 VLAN 識別碼來存取實體網路 (以及網際網路) 時，才使用此參數。 例如，.\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Rerun|選用|使用這個旗標來重新執行部署。 系統會使用所有先前的輸入。 不支援重新輸入先前提供的資料，因為會產生數個唯一值並使用於部署。|

## <a name="activate-the-administrator-and-tenant-portals"></a>啟用系統管理員和租用戶入口網站
使用 Azure AD 部署之後，您必須啟用 Azure Stack 系統管理員和租用戶入口網站。 此啟用同意將所有目錄使用者的正確權限 (列在同意頁面上) 提供給 Azure Stack 入口網站和 Azure Resource Manager。

- 在系統管理員入口網站中，瀏覽至 https://adminportal.local.azurestack.external/guest/signup、讀取資訊，然後按一下 [接受]。 接受之後，您即可新增同時不是目錄租用戶管理員的服務管理員。

- 在租用戶入口網站中，瀏覽至 https://portal.local.azurestack.external/guest/signup、讀取資訊，然後按一下 [接受]。 接受之後，目錄中的使用者可以登入租用戶入口網站。 

> [!NOTE] 
> 如果未啟用入口網站，則只有目錄管理員可以登入並使用入口網站。 如果任何其他使用者登入，他們會看到一項錯誤，告知系統管理員上未授與權限給其他使用者。 當系統管理員原本不屬於 Azure Stack 註冊至的目錄時，Azure Stack 目錄必須附加到啟用 URL。 例如，如果 Azure Stack 註冊至 fabrikam.onmicrosoft.com 且系統管理使用者為 admin@contoso.com，請瀏覽至 https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 來啟用入口網站。 

## <a name="reset-the-password-expiration-policy"></a>重設密碼到期原則 
為確定開發套件主機的密碼不會在評估期間結束前到期，請在部署 ASDK 之後遵循下列步驟進行。

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>若要從 Powershell 變更密碼到期原則：
從已提升權限的 PowerShell 主控台，執行以下命令：

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>若要手動變更密碼到期原則：
1. 在開發套件主機上，開啟 [群組原則管理]，並瀏覽至 [群組原則管理] – [樹系：azurestack.local] – [網域] – [azurestack.local]。
2. 以滑鼠右鍵按一下 [預設網域原則]，然後按一下 [編輯]。
3. 在 [群組原則管理編輯器] 中，瀏覽至 [電腦設定] – [原則] – [Windows 設定] – [安全性設定] – [帳戶原則] – [密碼原則]。
4. 在右窗格中，按兩下 [密碼最長使用期限]。
5. 在 [密碼最長使用期限內容] 對話方塊中，將 [密碼到期日] 值變更為 180，然後按一下 [確定]。


## <a name="next-steps"></a>後續步驟

[連接至 Azure Stack](azure-stack-connect-azure-stack.md)

[設定適用於 Azure Stack 環境的 PowerShell](azure-stack-powershell-configure-quickstart.md)

[使用您的 Azure 訂用帳戶註冊 Azure Stack](azure-stack-register.md)



