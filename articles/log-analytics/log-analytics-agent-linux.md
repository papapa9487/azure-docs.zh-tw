---
title: "將 Linux 電腦連線至 Operations Management Suite (OMS) | Microsoft Docs"
description: "本文說明如何使用 OMS Agent for Linux，將在 Azure、其他雲端或內部部署中託管的 Linux 電腦連線至 OMS。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c9902e1b8644c2b0a894f9cde98f2056564775c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>將 Linux 電腦連線至 Operations Management Suite (OMS) 

透過 Microsoft Operations Management Suite (OMS)，您可以收集從下列位置產生的資料，並對資料採取動作：從 Linux 電腦；存放在內部部署資料中心做為實體伺服器或虛擬機器的容器解決方案，如 Docker；如 Amazon Web Services (AWS) 或 Microsoft Azure 等雲端託管服務中的虛擬機器。 您也可以使用 OMS 中可用的管理解決方案 (例如「變更追蹤」) 來識別組態變更，並使用「更新管理」管理軟體更新，以主動管理 Linux VM 的生命週期。 

OMS Agent for Linux 會透過 TCP 通訊埠 443 對外與 OMS 服務通訊，如果電腦連線至防火牆或 Proxy 伺服器以透過網際網路通訊，請檢閱[設定代理程式以搭配 HTTP Proxy 伺服器或 OMS 閘道使用](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway)，以了解必須套用哪些組態變更。  如果您正在監視的電腦已安裝 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2，則該電腦為多重主目錄，並採用 OMS 服務來收集資料及轉寄至該服務，且仍然受到 Operations Manager 監視。  Linux 電腦會由與 OMS 整合的 Operations Manager 管理群組所監視，其不會收到資料來源的組態，也不會透過該管理群組轉寄收集到的資料。  OMS 代理程式無法設定為多個工作區的報表。  

如果 IT 安全性原則不允許您網路上的電腦連線到網際網路，則可以將代理程式設定為連線到 OMS 閘道，以根據您已啟用的解決方案來接收組態資訊和傳送收集到的資料。 如需如何設定 OMS Linux Agent 以透過 OMS 閘道與 OMS 服務進行通訊的詳細資訊和步驟，請參閱[使用 OMS 閘道將電腦連線到 OMS](log-analytics-oms-gateway.md)。  

下圖說明代理程式管理的 Linux 電腦與 OMS 之間的連線，包括方向與連接埠。

![代理程式直接與 OMS 通訊的圖表](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>系統需求
開始之前請檢閱下列詳細資料，以確認符合必要條件。

### <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統
以下為正式支援的 Linux 散發套件。  不過，OMS Agent for Linux 也可能在未列出的其他散發套件上執行。

* Amazon Linux 2012.09 至 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、15.04、15.10、16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

### <a name="network"></a>網路
下列資訊列出 Linux 代理程式與 OMS 通訊所需的 Proxy 和防火牆組態資訊。 流量會從您的網路輸出至 OMS 服務。 

|代理程式資源| 連接埠 |  
|------|---------|  
|*.ods.opinsights.azure.com | 連接埠 443|   
|*.oms.opinsights.azure.com | 連接埠 443|   
|*.blob.core.windows.net/ | 連接埠 443|   
|*.azure-automation.net | 連接埠 443|  

### <a name="package-requirements"></a>封裝需求

 **必要封裝**   | **說明**   | **最低版本**
--------------------- | --------------------- | -------------------
Glibc | GNU C 程式庫   | 2.5-12 
Openssl | OpenSSL 程式庫 | 0.9.8e 或 1.0
Curl | cURL Web 用戶端 | 7.15.5
Python-ctypes | | 
PAM | 插入式驗證模組 | 

> [!NOTE]
>  需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應安裝和設定為取代 sysklog。 

代理程式包含多個封裝。 發行檔案包含下列封裝 (搭配執行殼層套件組合與 `--extract` 即可取得)：

**Package** | **版本** | **說明**
----------- | ----------- | --------------
omsagent | 1.4.1 | Operations Management Suite Agent for Linux
omsconfig | 1.1.1 | OMS 代理程式的組態代理程式
omi | 1.2.0 | 開放式管理基礎結構 (OMI) - 輕量型 CIM 伺服器
scx | 1.6.3 | 作業系統效能計量的 OMI CIM 提供者
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 伺服器效能監視提供者。 在偵測到 Apache HTTP 伺服器時安裝。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 伺服器效能監視提供者。 在偵測到 MySQL/MariaDB 伺服器時安裝。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供者。 在偵測到 Docker 時安裝。

### <a name="compatibility-with-system-center-operations-manager"></a>與 System Center Operations Manager 的相容性
OMS Agent for Linux 會與 System Center Operations Manager 代理程式共用代理程式二進位檔。 如果您在 Operations Manager 目前所管理的系統上安裝 OMS Agent for Linux，它會將電腦上的 OMI 和 SCX 套件升級到較新版本。 在此版本中，OMS 與 Linux 適用的 System Center 2016 - Operations Manager/Operations Manager 2012 R2 代理程式相容。 

> [!NOTE]
> System Center 2012 SP1 和舊版本目前與 OMS Agent for Linux 不相容或不受其支援。<br>
> 如果 OMS Agent for Linux 安裝到 Operations Manager 目前未監視的電腦，且您之後想要使用 Operations Manager 監視該電腦，您必須先修改 [OMI 組態](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager)，再探索電腦。 **如果先安裝 Operations Manager 代理程式，再安裝 OMS Agent for Linux，則*不*需要這個步驟。**

### <a name="system-configuration-changes"></a>系統組態變更
安裝 OMS Agent for Linux 封裝之後，會套用下列額外的全系統組態變更。 解除安裝 omsagent 封裝時，會移除這些構件。

* 會建立名為 `omsagent` 的非特殊權限使用者。 omsagent 精靈會以此帳戶的身分執行。
* sudoers “include” 檔案建立在 /etc/sudoers.d/omsagent。 這個檔案會授權 omsagent 重新啟動 syslog 與 omsagent 精靈。 如果已安裝的 sudo 版本不支援 sudo “include” 指示詞，這些項目會寫入 /etc/sudoers。
* syslog 組態修改成將事件子集轉送給代理程式。 如需詳細資訊，請參閱下面**設定資料收集**一節。

### <a name="upgrade-from-a-previous-release"></a>從舊版升級
此版本支援從 1.0.0-47 之前的版本升級。 使用 `--upgrade` 命令執行安裝，會將代理程式的所有元件升級為最新版本。

## <a name="installing-the-agent"></a>安裝代理程式

本節說明如何以套件組合安裝 OMS Agent for Linux，其中包含各代理程式元件的 Debian 與 RPM 封裝。  代理程式可以直接安裝，或是經擷取將個別封裝取出。  

您首先需要您的 OMS 工作區識別碼和金鑰，這可以透過切換至 [OMS 傳統入口網站](https://mms.microsoft.com)找到。  在 [概觀] 頁面上，從頂端功能表選取 [設定]，然後巡覽至 [連接的來源]\[Linux 伺服器]。  您會看到 [工作區識別碼] 和 [主索引鍵] 右邊的值。  將兩者複製並貼到您最愛的編輯器。    

1. 在 GitHub 下載最新的 [OMS Ageent for Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x64.sh) 或 [OMS Agent for Linux (x86)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x86.sh)。  
2. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
3. 使用 `--install` 或 `--upgrade` 引數安裝該套件組合。 

    > [!NOTE]
    > 若已安裝任何現有封裝，例如已安裝適用於 Linux 的 System Center Operations Manager 時，則使用 `--upgrade` 引數。 若要在安裝期間連線到 Operations Management Suite，請提供 `-w <WorkspaceID>` 和 `-s <Shared Key>` 參數。


#### <a name="to-install-and-onboard-directly"></a>直接安裝並上架
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>若要升級代理程式封裝
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>安裝並上架到美國政府雲端
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>設定代理程式以搭配 Proxy 伺服器或 OMS 閘道使用
適用於 Linux 的 OMS 代理程式支援使用 HTTPS 通訊協定，透過 Proxy 伺服器或 OMS 閘道，與 OMS 服務進行通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  

### <a name="proxy-configuration"></a>Proxy 組態
Proxy 組態值的語法如下︰

`[protocol://][user:password@]proxyhost[:port]`

屬性|說明
-|-
通訊協定|https
user|用於驗證 Proxy 的選擇性使用者名稱
password|用於驗證 Proxy 的選擇性密碼
proxyhost|Proxy 伺服器/OMS 閘道的位址或 FQDN
port|Proxy 伺服器/OMS 閘道的選擇性連接埠號碼

例如：`https://user01:password@proxy01.contoso.com:30443`

可在安裝期間指定或在安裝後透過修改 proxy.conf 組態檔案指定的 Proxy 伺服器。   

### <a name="specify-proxy-configuration-during-installation"></a>安裝期間指定 Proxy 組態
omsagent 安裝套件組合的 `-p` 或 `--proxy` 引數可指定要使用的 Proxy 組態。 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>在檔案中定義 Proxy 組態
可在檔案 `/etc/opt/microsoft/omsagent/proxy.conf` 和 `/etc/opt/microsoft/omsagent/conf/proxy.conf ` 中設定 Proxy 組態。 可以直接建立或編輯檔案，但必須更新其權限，才能對 omiuser 使用者授與檔案的讀取權限。 例如：
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>移除 Proxy 組態
若要移除先前定義的 Proxy 組態並還原至直接連線，請移除 proxy.conf 檔案：
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-operations-management-suite"></a>透過 Operations Management Suite 上架
如果套件組合安裝期間未提供工作區識別碼與金鑰，則之後必須向 Operations Management Suite 註冊代理程式。

### <a name="onboarding-using-the-command-line"></a>使用命令列上架
透過提供您工作區的工作區識別碼與金鑰來執行 omsadmin.sh 命令。 此命令必須以根身分 (具有 sudo 提升權限) 執行：
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>使用檔案上架
1.  建立檔案 `/etc/omsagent-onboard.conf`。 此檔案必須可讓根使用者讀取與寫入。
`sudo vi /etc/omsagent-onboard.conf`
2.  在檔案中插入以下幾行，並以您的工作區識別碼與共用金鑰取代：

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  執行下列命令以上架至 OMS：`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  此檔案會在成功上架時刪除。

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>啟用 OMS Agent for Linux 以向 System Center Operations Manager 報告
執行下列步驟設定 OMS Agent for Linux 向 System Center Operations Manager 管理群組報告。  

1. 編輯 `/etc/opt/omi/conf/omiserver.conf`
2. 確認開頭為 **httpsport=** 的行定義連接埠 1270。 例如：`httpsport=1270`
3. 重新啟動 OMI 伺服器：`sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>代理程式記錄檔
OMS Agent for Linux 的記錄檔位於：`/var/opt/microsoft/omsagent/<workspace id>/log/` omsconfig (代理程式組態) 程式的記錄檔位於：`/var/opt/microsoft/omsconfig/log/` OMI 與 SCX 元件 (可提供效能計量資料) 的記錄檔位於：`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>記錄輪替組態##
omsagent 的記錄輪替組態位於：`/etc/logrotate.d/omsagent-<workspace id>`

預設設定值為： 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>解除安裝 OMS Agent for Linux
可以藉由使用 `--purge` 引數執行搭售的 .sh 檔案，以解除安裝代理程式封裝，將代理程式及其組態從電腦中完全移除。   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>疑難排解

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>問題︰ 無法透過 Proxy 連線至 OMS

#### <a name="probable-causes"></a>可能的原因
* 上架期間指定的 Proxy 不正確
* OMS 服務端點未列入您資料中心的允許清單 

#### <a name="resolutions"></a>解決方式
1. 將下列命令搭配已啟用的 `-v` 選項使用，以透過 OMS Agent for Linux 重新上架至 OMS 服務。 這個 settubg 可讓透過 Proxy 連線到 OMS 服務的代理程式產生詳細資訊輸出。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 檢閱[設定代理程式以搭配 Proxy 伺服器或 OMS 閘道使用](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway)一節，以確認您已適當地設定代理程式透過 Proxy 伺服器通訊。    
* 再次檢查以確認下列 OMS 服務端點已在允許清單中：

    |代理程式資源| 連接埠 |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 連接埠 443|   
    |*.oms.opinsights.azure.com | 連接埠 443|   
    |ods.systemcenteradvisor.com | 連接埠 443|   
    |*.blob.core.windows.net/ | 連接埠 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>問題：您在嘗試上架時收到 403 錯誤

#### <a name="probable-causes"></a>可能的原因
* Linux 伺服器上的日期與時間不正確 
* 使用的工作區識別碼和工作區金鑰不正確

#### <a name="resolution"></a>解決方案

1. 使用命令日期檢查 Linux 伺服器上的時間。 如果時間為自目前時間起的 + /-15 分鐘，則上架失敗。 若要修正此問題，請更新 Linux 伺服器的日期和/或時區。 
2. 確認您已安裝最新版的 OMS Agent for Linux。  最新版本現在會通知您時間差異是否造成上架失敗。
3. 使用正確的工作區識別碼和工作區金鑰並遵循本主題中前面的安裝指示重新上架。

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>問題︰上架後您隨即在記錄檔中看到 500 與 404 錯誤
這個錯誤是已知第一次將 Linux 資料上傳至 OMS 工作區時會發生的問題。 這個錯誤不會影響正在傳送的資料或服務體驗。

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>問題︰您在 OMS 入口網站中看不到任何資料

#### <a name="probable-causes"></a>可能的原因

- 上架至 OMS 服務失敗
- 對 OMS 服務的連線遭到封鎖
- OMS Agent for Linux 資料已備份

#### <a name="resolutions"></a>解決方式
1. 確認 OMS 服務是否成功上架，做法是檢查下列檔案是否存在：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. 使用 `omsadmin.sh` 命令列指示重新上架
3. 如果使用 Proxy，請參閱稍早所提供的 Proxy 解決步驟。
4. 在某些情況下，當 OMS Agent for Linux 無法與 OMS 服務通訊時，系統會將整個緩衝區大小 (亦即 50 MB) 的資料加入佇列。 應該執行下列命令重新啟動 OMS Agent for Linux：`/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`。 

    >[!NOTE]
    >此問題已在代理程式 1.1.0-28 版和更新版本中修正。

