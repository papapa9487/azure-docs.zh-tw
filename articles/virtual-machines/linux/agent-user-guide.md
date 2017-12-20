---
title: "Azure Linux VM 代理程式概觀 | Microsoft Docs"
description: "了解如何安裝和設定 Linux 代理程式 (waagent)，來管理虛擬機器與 Azure 網狀架構控制器之間的互動。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: szark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59266c6d6452eeff56b05e60389ac14f0b2c3f1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>了解與使用 Azure Linux 代理程式
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>簡介
Microsoft Azure Linux 代理程式 (waagent) 管理 Linux 與 FreeBSD 佈建，以及 VM 與 Azure 網狀架構控制器之間的互動。 它為 Linux 和 FreeBSD IaaS 部署提供下列功能：

> [!NOTE]
> 如需詳細資訊，請參閱 Azure Linux 代理程式[讀我檔案](https://github.com/Azure/WALinuxAgent/blob/master/README.md)。
> 
> 

* **映像佈建**
  
  * 建立使用者帳戶
  * 設定 SSH 驗證類型
  * 部署 SSH 公開金鑰和金鑰組
  * 設定主機名稱
  * 將主機名稱發佈至平台 DNS
  * 將 SSH 主機金鑰和指紋報告給平台
  * 資源磁碟管理
  * 格式化和掛接資源磁碟
  * 設定交換空間
* **網路功能**
  
  * 管理路由以提高平台 DHCP 伺服器的相容性
  * 確保網路介面名稱的穩定性
* **核心**
  
  * 設定虛擬 NUMA (核心 < 2.6.37 時停用)
  * 取用 /dev/random 的 Hyper-V Entropy
  * 設定根裝置 (可能在遠端) 的 SCSI 逾時
* **診斷**
  
  * 將主控台重新導向至序列埠
* **SCVMM 部署**
  
  * 在 System Center Virtual Machine Manager 2012 R2 環境中執行時偵測和啟動 Linux 的 VMM 代理程式
* **VM 延伸模組**
  
  * 將 Microsoft 和合作夥伴所撰寫的元件插入 Linux VM (IaaS)，以啟用軟體和設定自動化 
  * VM 延伸模組參考實作，網址為 [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>通訊
資訊經由兩個管道從平台流向代理程式：

* 在 IaaS 部署中，開機時連接的 DVD。 此 DVD 包含 OVF 相容組態檔，內含實際 SSH 金鑰組以外的所有佈建資訊。
* TCP 端點，公開可用來取得部署和拓撲組態的 REST API。

## <a name="requirements"></a>需求
下列系統已經過測試，且已知可與 Azure Linux 代理程式一同運作：

> [!NOTE]
> 這份清單可能與 Microsoft Azure 平台上官方的支援系統清單不同，如下所述：[http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

其他支援的系統：

* FreeBSD 10+ (Azure Linux 代理程式 v2.0.10+)

Linux 代理程式需要一些系統封裝才能正確運作：

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* 檔案系統公用程式：sfdisk、fdisk、mkfs、parted
* 密碼工具：chpasswd、sudo
* 文字處理工具：sed、grep
* 網路工具：ip-route
* 掛接 UDF 檔案系統的核心支援。

## <a name="installation"></a>安裝
安裝和升級 Azure Linux 代理程式時，建議使用散發套件的封裝儲存機制所提供的 RPM 或 DEB 封裝來安裝。 所有[認可的散發套件提供者](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)都會將 Azure Linux 代理程式套件整合於本身的映像和儲存機制中。

如需了解進階安裝選項，例如從來源安裝或安裝到自訂的位置或前置詞，請參閱 [GitHub 上 Azure Linux 代理程式存放庫](https://github.com/Azure/WALinuxAgent)中的文件。

## <a name="command-line-options"></a>命令列選項
### <a name="flags"></a>旗標
* verbose：提高指定命令的詳細程度
* force：略過某些命令的互動式確認

### <a name="commands"></a>命令
* help：列出支援的命令和旗標。
* deprovision：嘗試清理系統，使之適合重新佈建。 此作業會刪除下列項目：
  
  * 所有 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')
  * /etc/resolv.conf 中的名稱伺服器設定
  * /etc/shadow 中的根密碼 (如果組態檔中的 Provisioning.DeleteRootPassword 是 'y')
  * 快取的 DHCP 用戶端租用
  * 將主機名稱重設為 localhost.localdomain

> [!WARNING]
> 取消佈建不能保證映像檔中的所有機密資訊都清除完畢而適合再度散發。
> 
> 

* deprovision+user：執行 -deprovision (上述) 下的一切動作，並刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) 和相關聯的資料。 此參數是為了取消佈建 Azure 上先前佈建的映像檔，以便擷取並重複使用。
* version：顯示 waagent 的版本
* serialconsole：設定 GRUB，以將 ttyS0 (第一個序列埠) 標示為開機主控台。 這可確保將核心開機記錄傳送至序號埠且可用於偵錯。
* daemon：以精靈方式執行 waagent 來管理與平台之間的互動。 此引數是在 waagent init 指令碼中指定給 waagent。
* 開始︰以背景處理序方式執行 waagent

## <a name="configuration"></a>組態
組態檔 (/etc/waagent.conf) 控制 waagent 的動作。 範例組態檔如下所示：

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y

以下詳細說明各種組態選項。 組態選項可分成三種：布林、字串或整數。 布林組態選項可指定為 "y" 或 "n"。 特殊關鍵字 "None" 可用於某些字串類型組態項目，詳述如下。

**Provisioning.Enabled：**  
型別︰布林值  
預設：y

這可讓使用者啟用或停用代理程式的佈建功能。 有效值為 "y" 或 "n"。 如果停用佈建，則會保留映像檔中的 SSH 主機金鑰和使用者金鑰，並忽略 Azure 佈建 API 中指定的任何組態。

> [!NOTE]
> 針對使用 cloud-init 執行佈建工作的 Ubuntu 雲端映像，`Provisioning.Enabled` 參數的預設值為 "n"。
> 
> 

**Provisioning.DeleteRootPassword：**  
型別︰布林值  
預設：n

如果設定，則佈建過程中會清除 /etc/shadow 檔案中的根密碼。

**Provisioning.RegenerateSshHostKeyPair：**  
型別︰布林值  
預設：y

如果設定，則佈建過程會從 /etc/ssh/ 中刪除所有 SSH 主機金鑰組 (ecdsa、dsa 和 rsa)。 還會產生單一全新的金鑰組。

全新金鑰組的加密類型可由 Provisioning.SshHostKeyPairType 項目來設定。 請注意，重新啟動 SSH 精靈時 (例如重新開機時)，某些散發套件會針對任何遺失的加密類型而重建 SSH 金鑰組。

**Provisioning.SshHostKeyPairType：**  
類型：字串  
預設：rsa

這可設為虛擬機器上的 SSH 精靈所支援的加密演算法類型。 支援的值通常為 "rsa"、"dsa" 和 "ecdsa"。 請注意，Windows 的 "putty.exe" 不支援 "ecdsa"。 因此，如果想要在 Windows 上使用 putty.exe 來連線至 Linux 部署，請使用 "rsa" 或 "dsa"。

**Provisioning.MonitorHostName：**  
型別︰布林值  
預設：y

如果設定，waagent 會監視 Linux 虛擬機器的主機名稱有無變動 (由 "hostname" 命令傳回)，並自動更新映像檔中的網路組態來反映此變動。 為了將名稱變更發送至 DNS 伺服器，虛擬機器中將重新啟動網路功能。 這會導致網際網路連線短暫中斷。

**Provisioning.DecodeCustomData**  
型別︰布林值  
預設：n

如果設定，waagent 會從 CustomData 將 Base64 解碼。

**Provisioning.ExecuteCustomData**  
型別︰布林值  
預設：n

如果設定，waagent 將在佈建之後執行 CustomData。

**Provisioning.AllowResetSysUser** 類型：布林值的預設值：n

此選項可允許重設系統使用者的密碼，預設為停用。

**Provisioning.PasswordCryptId**  
類型：字串  
預設值：6

產生密碼雜湊時由 crypt 使用的演算法。  
 1 - MD5  
 2a - Blowfish  
 5-SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
類型：字串  
預設值：10

產生密碼雜湊時使用的隨機 salt 長度。

**ResourceDisk.Format：**  
型別︰布林值  
預設：y

如果設定，如果使用者在 "ResourceDisk.Filesystem" 中要求的檔案系統類型不是 "ntfs"，則 waagent 會格式化並掛接平台所提供的資源磁碟。 磁碟上將會有 Linux (83) 類型的單一磁碟分割。 請注意，如果可順利掛接此磁碟分割，則不會格式化。

**ResourceDisk.Filesystem：**  
類型：字串  
預設：ext4

這指定資源磁碟的檔案系統類型。 支援的值隨 Linux 散發套件而不同。 如果字串為 X，則 Linux 映像檔上應該會出現 mkfs.X。 SLES 11 映像檔通常會使用 'ext3'。 在此，FreeBSD 映像檔應該是使用 'ufs2'。

**ResourceDisk.MountPoint：**  
類型：字串  
預設值：/mnt/resource 

這指定資源磁碟的掛接路徑。 請注意，資源磁碟是 *暫存* 磁碟，可能會在 VM 取消佈建時清空。

**ResourceDisk.MountOptions**  
類型：字串  
預設值︰無

指定要傳遞至 mount -o 指令的磁碟掛接選項。 這是以逗號分隔的值清單，例如： 'nodev,nosuid'。 如需詳細資訊，請參閱 Mount(8)。

**ResourceDisk.EnableSwap：**  
型別︰布林值  
預設：n

如果設定，則會在資源磁碟上建立交換檔 (/swapfile) 並加入至系統交換空間。

**ResourceDisk.SwapSizeMB：**  
型別︰整數  
預設值：0

交換檔的大小 (MB)。

**Logs.Verbose：**  
型別︰布林值  
預設：n

如果設定，則記錄詳細程度會提高。 Waagent 會記錄到 /var/log/waagent.log，並利用系統 logrotate 功能來輪換記錄檔。

**OS.EnableRDMA**  
型別︰布林值  
預設：n

如果設定，該代理程式將嘗試安裝並載入 RDMA 核心驅動程式，這個驅動程式與基礎硬體上的軔體版本相符。

**OS.RootDeviceScsiTimeout：**  
型別︰整數  
預設值︰300

這會在 OS 磁碟和資料磁碟機上設定 SCSI 逾時 (以秒為單位)。 如果未設定，則會使用系統預設值。

**OS.OpensslPath：**  
類型：字串  
預設值︰無

這可用來指定加密作業使用的 openssl 二進位檔的替代路徑。

**HttpProxy.Host, HttpProxy.Port**  
類型：字串  
預設值︰無

如果設定，代理程式將使用此 Proxy 伺服器存取網際網路。 

**AutoUpdate.Enabled**類型：布林值的預設值：y

啟用或停用目標狀態處理的自動更新；預設為啟用。



## <a name="ubuntu-cloud-images"></a>Ubuntu 雲端映像
請注意，Ubuntu 雲端映像會利用 [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) 來執行許多組態工作，否則會由 Azure Linux 代理程式來管理。  請注意下列差異：

* **Provisioning.Enabled** 會在 Ubuntu 雲端映像上預設為 "n"，其會使用 cloud-init 來執行佈建工作。
* 下列組態參數不會在使用 cloud-init 來管理資源磁碟和交換空間的 Ubuntu 雲端映像上產生任何作用：
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**
* 請參閱下列資源，以便在佈建期間，於 Ubuntu 雲端映像上設定資源磁碟掛接和交換空間：
  
  * [Ubuntu Wiki：設定交換資料分割](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [將自訂資料插入 Azure 虛擬機器](../windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

