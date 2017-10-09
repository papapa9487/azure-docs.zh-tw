---
title: "部署 Azure Stack 開發套件 | Microsoft Docs"
description: "了解如何準備 Azure Stack 開發套件，以及執行 PowerShell 指令碼來加以部署。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 530a9558df2323e1aa49d9f4b974c142ee5ecf37
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-the-azure-stack-development-kit"></a>部署 Azure Stack 開發套件

「適用於：Azure Stack 開發套件」

若要部署 [Azure Stack 開發套件](azure-stack-poc.md)，您必須完成下列步驟：

1. [下載部署封裝](https://azure.microsoft.com/overview/azure-stack/try/?v=try)以取得 Cloudbuilder.vhdx。
2. 執行 asdk-installer.ps1 指令碼來設定安裝開發套件所在的電腦 (開發套件主機)，以[準備 cloudbuilder.vhdx](#prepare-the-development-kit-host)。 在這個步驟之後，開發套件主機將會開機進入 Cloudbuilder.vhdx。
3. 在開發套件主機上，[部署開發套件](#deploy-the-development-kit)。

> [!NOTE]
> 為獲得最佳結果，即使您想要使用已中斷連線的 Azure Stack 環境，最好是在已連線到網際網路時部署。 如此一來，就可以在部署期間啟動 Windows Server 2016 評估版。 如果在 10 天內未啟動 Windows Server 2016 評估版，它就會關閉。
> 
> 

## <a name="download-and-extract-the-development-kit"></a>下載並解壓縮開發套件
1. 開始下載之前，請確定您的電腦符合下列必要條件：

   * 電腦必須至少有 60 GB 的可用磁碟空間。
   * 必須安裝 [.NET framework 4.6 (或更新版本)](https://aka.ms/r6mkiy)。

2. [移至開始使用頁面](https://azure.microsoft.com/overview/azure-stack/try/?v=try)、提供您的詳細資料，然後按一下 [提交]。
3. 在 [下載軟體] 底下，按一下 [Azure Stack 開發套件]。
4. 執行下載的 AzureStackDownloader.exe 檔案。
5. 在 [Azure Stack 開發套件下載程式] 視窗中，遵循步驟 1 至 5 進行。
6. 下載完成之後，請按一下 [執行]，以啟動 MicrosoftAzureStackPOC.exe。
7. 檢閱 [自我解壓縮程式精靈] 的 [授權合約] 畫面和資訊，然後按一下 [下一步]。
8. 檢閱 [自我解壓縮程式精靈] 的 [隱私權聲明] 畫面和資訊，然後按一下 [下一步]。
9. 選取要解壓縮檔案的目的地，然後按一下 [下一步]。
   * 預設值是：<drive letter>:\<目前資料夾>\Microsoft Azure Stack
10. 檢閱 自我解壓縮程式精靈 的 目的地位置 畫面和資訊，然後按一下解壓縮 來解壓縮 CloudBuilder.vhdx (~ 25 GB) 和 ThirdPartyLicenses.rtf 檔案。 這個程序需要一些時間才會完成。

> [!NOTE]
> 將檔案解壓縮之後，您可以刪除 exe 和 bin 檔案來恢復電腦上的空間。 或者，您可以將這些檔案移到其他位置。如此一來，如果您需要重新部署，就不必重新下載這些檔案。
> 
> 

## <a name="prepare-the-development-kit-host"></a>準備開發套件主機
1. 請確定您可以實際連線到開發套件主機，或者可以存取實體主控台 (例如 KVM)。 在下面的步驟 13 中重新啟動開發套件主機之後，您必須以這類方式來存取。
2. 請確定開發套件主機符合[最低需求](azure-stack-deploy.md)。 您可以使用 [Azure Stack 適用的部署檢查程式](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)來確認您的需求。
3. 以本機系統管理員的身分來登入開發套件主機。
4. 將 CloudBuilder.vhdx 檔案複製或移動至 C:\ 磁碟機的根目錄 (C:\CloudBuilder.vhdx)。
5. 執行下列指令碼，將開發套件安裝程式檔案 (asdk-installer.ps1) 下載至開發套件主機上的 c:\AzureStack_Installer 資料夾。
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. 開啟提升權限的 PowerShell 主控台 > 執行 C:\AzureStack_Installer\asdk-installer.ps1 指令碼 > 按一下 [準備 vhdx]。
7. 在安裝程式的 [選取 Cloudbuilder vhdx] 頁面上，瀏覽至您在先前步驟中下載的 cloudbuilder.vhdx 檔案並加以選取。
8. 選擇性：選取 [新增驅動程式] 方塊，以指定包含主機上所要其他驅動程式的資料夾。
9. 在 [選擇性設定] 頁面上，提供開發套件主機的本機系統管理員帳戶。 如果沒有提供這些認證，則在下方的安裝程序期間，您必須能夠以 KVM 來存取主機。
10. 此外，在 [選擇性設定] 頁面上，您可以選擇設定下列項目：
    - **電腦名稱**：此選項會設定開發套件主機的名稱。 名稱必須符合 FQDN 需求，且長度必須等於或少於 15 個字元。 預設值是由 Windows 產生的隨機電腦名稱。
    - **時區**：設定開發套件主機的時區。 預設值是 (UTC-8:00) 太平洋時間 (美國和加拿大)。
    - **靜態 IP 組態**：將您的部署設定為使用靜態 IP 位址。 否則，當安裝程式重新開機進入 cloudbuilder.vhx 時，會使用 DHCP 來設定網路介面。
11. 按一下 [下一步] 。
12. 如果您在上一個步驟中選擇靜態 IP 組態，您現在必須：
    - 選取網路介面卡。 請先確定您可以連接到介面卡，然後再按一下 [下一步]。
    - 請確定 [IP 位址]、[閘道] 和 [DNS] 值正確無誤，然後按一下 [下一步]。
13. 按一下 [下一步]，以開始準備程序。
14. 當準備指出 [已完成] 時，按一下 [下一步]。
15. 按一下 [立即重新開機]，以開機進入 cloudbuilder.vhdx 並繼續部署程序。

## <a name="deploy-the-development-kit"></a>部署開發套件
1. 以本機系統管理員的身分來登入開發套件主機。 使用在先前步驟中指定的認證。

    > [!IMPORTANT]
    > 若是 Azure Active Directory 部署，Azure Stack 需要能夠直接或透過 Transparent Proxy 來存取網際網路。 部署僅支援一個 NIC，以供網路功能使用。 如果您有多個 NIC，請確定僅啟用一個 NIC (且停用其他所有 NIC)，再執行下一節中的部署指令碼。
    
2. 開啟提升權限的 PowerShell 主控台 > 執行 \AzureStack_Installer\asdk-installer.ps1 指令碼 (這可能是在 Cloudbuilder.vhdx 中的其他磁碟機上) > 按一下 [安裝]。
3. 在 [類型] 方塊中，選取 [Azure 雲端] 或 [ADFS]。
    - **Azure 雲端**：Azure Active Directory 為識別提供者。 使用此參數可指定 AAD 帳戶具有全域系統管理員權限的特定目錄。 AAD 目錄租用戶的完整名稱，格式為 .onmicrosoft.com。 
    - **ADFS**：預設戳記目錄服務為識別提供者、登入身分的預設帳戶是 azurestackadmin@azurestack.local，且要使用的密碼是您在安裝時所提供的密碼。
4. 在 本機系統管理員密碼 底下的 密碼 方塊中，輸入本機系統管理員密碼 (必須符合目前設定的本機系統管理員密碼)，然後按一下下一步。
5. 選取要用於開發套件的網路介面卡，然後按一下 [下一步]。
6. 為 BGPNAT01 虛擬機器選取 DHCP 或靜態網路組態。
    - **DHCP** (預設值)：虛擬機器會從 DHCP 伺服器取得 IP 網路組態。
    - **靜態**：只有當 DHCP 無法為 Azure Stack 指派有效的 IP 位址來存取網際網路時，才使用此選項。 指定靜態 IP 位址 時，必須一併指定子網路遮罩長度 (例如，10.0.0.5/24)。
7. 或者，設定下列值：
    - **VLAN 識別碼**：設定 VLAN 識別碼。 只有當主機與 AzS-BGPNAT01 必須設定 VLAN 識別碼來存取實體網路 (以及網際網路) 時，才使用此選項。 
    - **DNS 轉寄站**：DNS 伺服器會在 Azure Stack 部署期間建立。 若要允許解決方案內的電腦解析戳記以外的名稱，請提供您現有架構 DNS 伺服器。 戳記內的 DNS 伺服器會將未知的名稱解析要求轉送至這部伺服器。
    - **時間伺服器**：設定特定的時間伺服器。 
8. 按一下 [下一步] 。 
9. 在 [驗證網路介面卡內容] 頁面上，您將會看到一個進度列。 
    - 如果顯示 [無法下載更新]，請遵循頁面上的指示執行。
    - 顯示 [已完成] 時，按一下 [下一步]。
10. 在 [摘要] 頁面上，按一下 [部署]。
11. 如果您使用的是 Azure Active Directory 部署，系統會要求您輸入 Azure Active Directory 全域系統管理員帳戶認證。
12. 部署程序可能需要幾小時的時間，在這段期間內，系統會自動重新開機一次。
   
   > [!IMPORTANT]
   > 如果您想要監視部署進度，請以 azurestack\AzureStackAdmin 的身分登入。 如果您在電腦加入網域之後，以本機系統管理員的身分登入，將不會看到部署進度。 請勿重新執行部署，而是以 azurestack\AzureStackAdmin 的身分登入，以驗證執行狀態。
   > 
   > 
   
    當部署成功時，PowerShell 主控台會顯示：**COMPLETE: Action ‘Deployment’**。
   
如果部署失敗，您可以使用下列 PowerShell，從相同提升權限的 PowerShell 視窗重新執行指令碼：

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

此指令碼將會從最後一個成功的步驟重新開始部署。

或者，您可以從頭開始[重新部署](azure-stack-redeploy.md)。


## <a name="reset-the-password-expiration-to-180-days"></a>將密碼到期日重設為 180 天

為確定開發套件主機的密碼不會太快到期，請在部署之後遵循下列步驟進行：

1. 在開發套件主機上，開啟 [群組原則管理]，並瀏覽至 [群組原則管理] – [樹系：azurestack.local] – [網域] – [azurestack.local]。
2. 以滑鼠右鍵按一下 [MemberServer]，然後按一下 [編輯]。
3. 在 [群組原則管理編輯器] 中，瀏覽至 [電腦設定] – [原則] – [Windows 設定] – [安全性設定] – [帳戶原則] – [密碼原則]。
4. 在右窗格中，按兩下 [密碼最長使用期限]。
5. 在 [密碼最長使用期限內容] 對話方塊中，將 [密碼到期日] 值變更為 180，然後按一下 [確定]。


## <a name="next-steps"></a>後續步驟
[使用您的 Azure 訂用帳戶註冊 Azure Stack](azure-stack-register.md)

[連接至 Azure Stack](azure-stack-connect-azure-stack.md)


