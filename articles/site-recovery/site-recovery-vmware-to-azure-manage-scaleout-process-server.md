---
title: " 在 Azure Site Recovery 中管理相應放大處理序伺服器 | Microsoft Doc"
description: "本文說明如何在 Azure Site Recovery 中設定並管理相應放大處理序伺服器。"
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
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-scale-out-process-server"></a>管理相應放大處理序伺服器

相應放大處理序伺服器是 Site Recovery 服務和您的內部部署基礎結構之間的資料傳輸協調者。 本文說明如何設定、配置及管理相應放大處理序伺服器。

## <a name="prerequisites"></a>必要條件
下列為設定相應放大處理序伺服器所需的建議硬體、軟體及網路組態。

> [!NOTE]
> [容量計劃](site-recovery-capacity-planner.md)是確保能以符合負載需求的設定部署相應放大處理序伺服器的重要步驟。 請在[相應放大處理序伺服器的調整特性](#sizing-requirements-for-a-configuration-server)中閱讀更多資訊。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>下載相應放大處理序伺服器軟體
1. 登入 Azure 入口網站並瀏覽至您的復原服務保存庫。
2. 瀏覽至 **Site Recovery 基礎結構** > **設定伺服器** \(位於 [適用於 VMware 和實體機器] 底下)。
3. 選取您的設定伺服器以切入設定伺服器的詳細資料頁面。
4. 按一下 [+ 處理序伺服器] 按鈕。
5. 在 [新增處理序伺服器] 頁面中，選取 [選擇您要部署處理序伺服器的位置] 下拉式清單中的 [於內部部署部署相應放大處理序伺服器] 選項。

  ![新增伺服器頁面](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. 按一下 [下載 Microsoft Azure Site Recovery 整合安裝] 連結，以下載相應放大處理序伺服器安裝的最新版本。

  > [!WARNING]
  您相應放大處理序伺服器的版本，應該要等於或小於您環境中執行的設定伺服器版本。 一個確保版本相容性的簡單方式，便是使用和您最近用來安裝/更新設定伺服器的安裝程式具有相同位元數的安裝程式。

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>從 GUI 安裝並註冊相應放大處理序伺服器
如果您必須將您的部署相應放大至超過 200 部來源機器，或是總計超過 2 TB 的每日變換率，您便需要額外的處理序伺服器來處理流量。

檢查[處理伺服器的大小建議](#size-recommendations-for-the-process-server)，然後遵循這些指示來設定處理序伺服器。 設定伺服器之後，請移轉來源機器以便使用它。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>使用命令列安裝並註冊相應放大處理序伺服器

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>範例用法
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>相應放大處理序伺服器安裝程式命令列引數。
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>建立 Proxy 設定組態檔案
ProxySettingsFilePath 參數使用檔案做為輸入。 使用下列格式建立檔案，並將它以輸入 ProxySettingsFilePath 參數傳遞。
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>修改相應放大處理序伺服器的 Proxy 設定
1. 登入您的相應放大處理序伺服器。
2. 開啟系統管理 PowerShell 命令視窗。
3. 執行下列命令
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. 接下來，請瀏覽至目錄 **%PROGRAMDATA%\ASR\Agent**，並執行下列命令
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>重新註冊相應放大處理序伺服器
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* 接下來，開啟系統管理命令提示字元。
* 瀏覽至目錄 **%PROGRAMDATA%\ASR\Agent**，並執行命令

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>升級相應放大處理序伺服器
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>將相應放大處理序伺服器解除委任
1. 請確定：
  - 設定伺服器的連線狀態在 Azure 入口網站中顯示為「已連線」
  - 處理序伺服器仍然可以和設定伺服器通訊。
2. 以系統管理員身分登入處理序伺服器
3. 開啟 [控制台] > [程式] > [解除安裝程式]
4. 以下列順序將程式解除安裝：
  * Microsoft Azure Site Recovery 設定伺服器/處理序伺服器
  * Microsoft Azure Site Recovery 設定伺服器相依性
  * Microsoft Azure 復原服務代理程式

處理序伺服器的刪除可能需要花費 15 分鐘的時間才會顯示於 Azure 入口網站上。

  > [!NOTE]
  如果處理序伺服器無法與設定伺服器通訊 (入口網站中的 [連線狀態] 為「已中斷連線」)，您必須遵循下列步驟來將它從設定伺服器中清除。

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>從設定伺服器中將已中斷連線的相應放大處理序伺服器取消註冊

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>相應放大處理序伺服器的調整大小需求

| **額外處理序伺服器** | **快取磁碟大小** | **資料變更率** | **受保護的機器** |
| --- | --- | --- | --- |
|4 個 vCPU (2 個通訊端 * 雙核心 @ 2.5 GHz)，8 GB 記憶體 |300 GB |250 GB 或更少 |複寫 85 部或更少的機器。 |
|8 個 vCPU (2 個通訊端 * 四核心 @ 2.5 GHz)，12 GB 記憶體 |600 GB |250 GB 至 1 TB |複寫 85-150 部機器。 |
|12 個 vCPU (2 個通訊端 * 六核心 @ 2.5 GHz)，24 GB 記憶體 |1 TB |1 TB 至 2 TB |複寫 150-225 部機器。 |
