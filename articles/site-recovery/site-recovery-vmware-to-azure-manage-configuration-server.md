---
title: " 在 Azure Site Recovery 中管理組態伺服器 | Microsoft Doc"
description: "本文說明如何設定和管理組態伺服器。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: ba236ad1327a7f3419d7c8cf7effc889a90dde61
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---

# <a name="manage-a-configuration-server"></a>管理組態伺服器

組態伺服器做為 Site Recovery 服務和您的內部部署基礎結構之間的協調者。 本文說明如何安裝、設定及管理組態伺服器。

> [!NOTE]
> [容量規劃](site-recovery-capacity-planner.md)是確保以符合負載需求的設定來部署組態伺服器的重要步驟。 深入了解[組態伺服器的大小需求](#sizing-requirements-for-a-configuration-server)。


## <a name="prerequisites"></a>必要條件
下列為安裝組態伺服器所需的最基本硬體、軟體及網路設定。
> [!IMPORTANT]
> 部署用於保護 VMWare 虛擬機器的組態伺服器時，我們建議您將其部署為**高可用 (HA)** 虛擬機器。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>下載組態伺服器軟體

1. 登入 Azure 入口網站並瀏覽至您的復原服務保存庫。
2. 瀏覽至 [Site Recovery 基礎結構] > [組態伺服器] \(位於 [適用於 VMware 和實體機器] 底下)。

  ![新增伺服器頁面](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. 按一下 [+伺服器] 按鈕。
4. 在 [新增伺服器] 頁面上，按一下 [下載] 按鈕，下載註冊金鑰。 在組態伺服器安裝期間，您需要使用此金鑰向 Azure Site Recovery 服務註冊它。
5. 按一下 [下載 Microsoft Azure Site Recovery 整合安裝] 連結，以下載最新版本的組態伺服器。

  ![下載頁面](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  您可以直接從 [Microsoft 下載中心下載頁面](http://aka.ms/unifiedsetup)，下載最新版本的組態伺服器

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>從 GUI 安裝和註冊組態伺服器
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>使用命令列安裝和註冊組態伺服器

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>範例用法
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>組態伺服器安裝程式命令列引數。
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>建立 MySql 認證檔案
MySQLCredsFilePath 參數使用檔案做為輸入。 使用下列格式建立檔案，並將它以輸入 MySQLCredsFilePath 參數傳遞。
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>建立 Proxy 設定組態檔案
ProxySettingsFilePath 參數使用檔案做為輸入。 使用下列格式建立檔案，並將它以輸入 ProxySettingsFilePath 參數傳遞。

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>修改組態伺服器的 Proxy 設定
1. 登入您的組態伺服器。
2. 使用捷徑啟動 cspsconfigtool.exe。
3. 按一下 [保存庫註冊] 索引標籤。
4. 從入口網站下載新的保存庫註冊檔案，並當做輸入提供給工具。

  ![註冊組態伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. 提供新的 Proxy 伺服器詳細資料，然後按一下 [註冊] 按鈕。
6. 開啟系統管理 PowerShell 命令視窗。
7. 執行下列命令
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  如果您有連結至此組態伺服器的相應放大處理序伺服器，您需要在部署中[修正所有相應放大處理序伺服器上的 Proxy 設定](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server)。

## <a name="modify-user-accounts-and-passwords"></a>修改使用者帳戶和密碼

CSPSConfigTool.exe 是用於管理用來「自動探索 VMware 虛擬機器」的使用者帳戶，以及用來執行「受保護機器上之行動服務推送安裝」。 

1. 登入您的設定伺服器。
2. 按一下桌面上的捷徑來啟動 CSPSConfigtool.exe。
3. 按一下 [管理帳戶] 索引標籤。
4. 選取要修改密碼的帳戶，然後按一下 [編輯] 按鈕。
5. 輸入新的密碼，然後按一下 [確定]


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>向相同的復原服務保存庫註冊組態伺服器
  1. 登入您的組態伺服器。
  2. 使用桌面上的捷徑啟動 cspsconfigtool.exe。
  3. 按一下 [保存庫註冊] 索引標籤。
  4. 從入口網站下載新的註冊檔案，並當做輸入提供給工具。
        ![註冊組態伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. 提供 Proxy 伺服器詳細資料，然後按一下 [註冊] 按鈕。  
  6. 開啟系統管理 PowerShell 命令視窗。
  7. 執行下列命令

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  如果您有連結至此組態伺服器的相應放大處理序伺服器，您需要在部署中[重新註冊所有相應放大處理序伺服器](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server)。

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>向不同的復原服務保存庫註冊組態伺服器。

> [!WARNING]
> 下列步驟會解除設定伺服器和目前保存庫之間的關聯，且設定伺服器底下所有受保護虛擬機器的複寫都會停止。

1. 登入您的組態伺服器。
2. 從系統管理命令提示字元中，執行命令

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. 使用捷徑啟動 cspsconfigtool.exe。
4. 按一下 [保存庫註冊] 索引標籤。
5. 從入口網站下載新的註冊檔案，並當做輸入提供給工具。

    ![註冊組態伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. 提供 Proxy 伺服器詳細資料，然後按一下 [註冊] 按鈕。  
7. 開啟系統管理 PowerShell 命令視窗。
8. 執行下列命令
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="updating-a-configuration-server"></a>更新組態伺服器

> [!WARNING]
> 更新只支援到 N 系列第 4 代的版本。 例如，若市面上的最新版本是 9.11，則您可從 9.10、9.9、9.8 或 9.7 版更新到 9.11 版。 但若您使用的是任何小於或等於 9.6 的版本，則您必須先至少更新到 9.7 版，才能將最新的更新套用到組態伺服器。 您可以在 [Azure Site Recovery 服務更新](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) \(英文\) 底下找到舊版本的下載連結

1. 將更新安裝程式下載到組態伺服器上。
2. 按兩下安裝程式將其啟動。
3. 安裝程式會偵測機器上的 Site Recovery 元件版本，並提示您進行確認。 
4. 按一下 [確定] 按鈕加以確認，並繼續進行升級。


## <a name="decommissioning-a-configuration-server"></a>解除委任組態伺服器
在開始解除委任組態伺服器之前，請確認下列事項。
1. 停用保護此組態伺服器下的所有虛擬機器。
2. 解除所有複寫原則與組態伺服器的關聯。
3. 刪除與組態伺服器相關聯的所有 VCenters 伺服器/vSphere 主機。

### <a name="delete-the-configuration-server-from-azure-portal"></a>從 Azure 入口網站刪除組態伺服器
1. 在 Azure 入口網站中，從 [保存庫] 功能表瀏覽至 [Site Recovery 基礎結構] > [組態伺服器]。
2. 按一下您想要解除委任的組態伺服器。
3. 在組態伺服器的詳細資料頁面上，按一下 [刪除] 按鈕。

  ![刪除組態伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. 按一下 [是] 以確認刪除伺服器。

  >[!WARNING]
  如果您有任何虛擬機器、複寫原則或 vCenter 伺服器/vSphere 主機與此組態伺服器相關聯，則無法刪除伺服器。 嘗試刪除保存庫之前，請先刪除這些實體。

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>解除安裝組態伺服器軟體和其相依性
  > [!TIP]
  如果您打算再次搭配 Azure Site Recovery 來重複使用組態伺服器，您可以直接跳至步驟 4

1. 以系統管理員身分登入組態伺服器。
2. 開啟 [控制台] > [程式集] > [解除安裝程式]
3. 依下列順序將程式解除安裝：
  * Microsoft Azure 復原服務代理程式
  * Microsoft Azure Site Recovery Mobility Service/主要目標伺服器
  * Microsoft Azure Site Recovery Provider
  * Microsoft Azure Site Recovery 組態伺服器/處理序伺服器
  * Microsoft Azure Site Recovery 組態伺服器相依性
  * MySQL Server 5.5
4. 從系統管理命令提示字元中，執行下列命令。
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>更新組態伺服器安全通訊端層 (SSL) 憑證
組態伺服器有內建的 Web 伺服器，可協調連線至組態伺服器的行動服務、處理序伺服器和主要目標伺服器的活動。 組態伺服器的 Web 伺服器使用 SSL 憑證來驗證其用戶端。 此憑證三年期滿，可隨時使用下列方法更新︰

> [!WARNING]
只有在 9.4.XXXX.X 或更新版本上才會履行憑證到期日。 開始「更新憑證」工作流程之前，請升級所有 Azure Site Recovery 元件 (組態伺服器、處理序伺服器、主要目標伺服器、行動服務)。

1. 在 Azure 入口網站上，瀏覽至 [保存庫] > [Site Recovery 基礎結構] > [組態伺服器]。
2. 按一下您需要更新 SSL 憑證的組態伺服器。
3. 在 [組態伺服器健全狀況] 下，您可以看到 SSL 憑證的到期日。
4. 按一下 [更新憑證] 動作來更新憑證，如下圖所示︰

  ![刪除組態伺服器](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>安全通訊端層憑證到期警告

> [!NOTE]
對於 2016 年 5 月之前完成的所有安裝，SSL 憑證的有效性設定為一年。 您已開始看到 Azure 入口網站中顯示憑證到期通知。

1. 如果組態伺服器的 SSL 憑證將於接下來兩個月之內到期，服務會開始透過 Azure 入口網站和電子郵件來通知使用者 (您需要訂閱 Azure Site Recovery 通知)。 您開始在保存庫的資源頁面上看到通知橫幅。

  ![憑證通知](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. 按一下橫幅，以取得憑證到期日的其他詳細資料。

  ![憑證詳細資料](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  如果您不是看到 [立即更新] 按鈕，而是 [立即升級] 按鈕， 這表示您的環境中有些元件尚未升級至 9.4.xxxx.x 或更新版本。

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>在安全通訊端層 (SSL) 憑證過期的情況下恢復設定伺服器

1. 將設定伺服器更新至[最新版本](http://aka.ms/unifiedinstaller)
2. 如果您有任何相應放大處理序伺服器、容錯回復主要目標伺服器，或容錯回復處理序伺服器，請將它們更新至最新版本
3. 將所有受保護虛擬機器上的行動服務更新至最新版本。
4. 登入設定伺服器並開啟具有系統管理員權限的命令提示字元。
5. 瀏覽至 %ProgramData%\ASR\home\svsystems\bin 資料夾
6. 執行 RenewCerts.exe 以更新設定伺服器上的 SSL 憑證。
7. 如果程序成功，您應該會看見「憑證更新為成功」的訊息


## <a name="sizing-requirements-for-a-configuration-server"></a>組態伺服器的大小需求

| **CPU** | **記憶體** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU (2 個插槽 * 4 核心 @ 2.5GHz) |16 GB |300 GB |500 GB 或更少 |複寫少於 100 部電腦。 |
| 12 個 vCPU (2 個插槽 * 6 核心 @ 2.5GHz) |18 GB |600 GB |500 GB 至 1 TB |複寫 100-150 部機器。 |
| 16 個 vCPU (2 個插槽 * 8 核心 @ 2.5GHz) |32 GB |1 TB |1 TB 至 2 TB |複寫 150-200 部機器。 |

  >[!TIP]
  如果您每日資料變換量超過 2 TB，或您打算複寫 200 個以上的虛擬機器，建議部署額外的處理序伺服器，以平衡複寫流量的負載。 深入了解如何部署相應放大處理序伺服器。


## <a name="common-issues"></a>常見問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

