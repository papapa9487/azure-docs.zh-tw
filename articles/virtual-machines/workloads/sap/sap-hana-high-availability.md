---
title: "Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性 | Microsoft Docs"
description: "在 Azure 虛擬機器 (VM) 上，建立 SAP HANA 的高可用性。"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 951150e621d21037b0adde7287b9f985290d8d11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure 虛擬機器 (VM) 上 SAP HANA 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

在內部部署中，您可以使用 HANA 系統複寫或使用共用儲存體來建立 SAP HANA 的高可用性。
我們目前只支援在 Azure 上設定 HANA 系統複寫。 SAP HANA 複寫是由一個主要節點以及至少一個從屬節點所組成。 對主要節點上資料的變更，會以同步或非同步方式複寫到從屬節點。

本文說明如何部署虛擬機器、設定虛擬機器、安裝叢集架構、安裝和設定 SAP HANA 系統複寫。
在範例組態、安裝命令等內容中，會使用執行個體號碼 03 和 HANA 系統識別碼 HDB。

請先閱讀下列 SAP Note 和文件

* SAP Note [1928533]，其中包含：
  * SAP 軟體部署支援的 Azure VM 大小清單
  * Azure VM 大小的重要容量資訊
  * 支援的 SAP 軟體，以及作業系統 (OS) 與資料庫組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
* SAP Note [2015553] 列出 Azure 中 SAP 支援的 SAP 軟體部署先決條件。
* SAP Note [2205917] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的建議 OS 設定
* SAP Note [1944799] 包含適用於 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 指導方針
* SAP Note [2178632] 包含在 Azure 中針對 SAP 回報的所有監視計量詳細資訊。
* SAP Note [2191498] 包含 Azure 中 Linux 所需的 SAP Host Agent 版本。
* SAP Note [2243692] 包含 Azure 中 Linux 上的 SAP 授權相關資訊。
* SAP Note [1984787] 包含 SUSE LINUX Enterprise Server 12 的一般資訊。
* SAP Note [1999351] 包含 Azure Enhanced Monitoring Extension for SAP 的其他疑難排解資訊。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含 Linux 所需的所有 SAP Note。
* [適用於 SAP on Linux 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器部署 (本文)][deployment-guide]
* [適用於 SAP on Linux 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* [SAP HANA SR 效能最佳化案例][suse-hana-ha-guide]：此指南包含設定內部部署 SAP HANA 系統複寫的所有必要資訊。 請使用此指南做為基礎。

## <a name="deploying-linux"></a>部署 Linux

SAP HANA 的資源代理程式隨附於 SUSE Linux Enterprise Server for SAP Applications 中。
Azure Marketplace 包含 SUSE Linux Enterprise Server for SAP Applications 12 (含 BYOS (使用您自己的訂用帳戶)) 的映像，讓您可用來部署新的虛擬機器。

### <a name="manual-deployment"></a>手動部署

1. 建立資源群組
1. 建立虛擬網路
1. 建立兩個儲存體帳戶
1. 建立可用性設定組  
   設定更新網域上限
1. 建立負載平衡器 (內部)  
   選取上述步驟的 VNET
1. 建立虛擬機器 1  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   選取儲存體帳戶 1  
   選取可用性設定組  
1. 建立虛擬機器 2  
   https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES For SAP Applications 12 SP1 (BYOS)  
   選取儲存體帳戶 2   
   選取可用性設定組  
1. 新增資料磁碟
1. 設定負載平衡器
    1. 建立前端 IP 集區
        1. 開啟負載平衡器、選取前端 IP 集區，然後按一下 [新增]
        1. 輸入新前端 IP 集區 的名稱 (例如 hana-frontend)
       1. Click OK
        1. 建立新的前端 IP 集區之後，記下它的 IP 位址
    1. 建立後端集區
        1. 開啟負載平衡器、選取後端集區，然後按一下 [新增]
        1. 輸入新後端集區的名稱 (例如 hana-backend)
        1. 按一下 [新增虛擬機器]
        1. 選取您稍早建立的可用性設定組
        1. 選取 SAP HANA 叢集的虛擬機器
        1. Click OK
    1. 建立健康狀態探查
       1. 開啟負載平衡器、選取健康狀態探查，然後按一下 [新增]
        1. 輸入新健康狀態探查的名稱 (例如 hana-hp)
        1. 選取 TCP 當做通訊協定、連接埠 625**03**，保留間隔 5 和狀況不良閾值 2
        1. Click OK
    1. 建立負載平衡規則
        1. 開啟負載平衡器、選取負載平衡規則，然後按一下 [新增]
        1. 輸入新負載平衡器規則的名稱 (例如 hana-lb-3**03**15)
        1. 選取前端 IP 位址、後端集區，以及您稍早建立的健康狀態探查 (例如 hana-frontend)
        1. 保留通訊協定 TCP，輸入連接埠 3**03**15
        1. 將閒置逾時增加為 30 分鐘
       1. **務必啟用浮動 IP**
        1. Click OK
        1. 針對連接埠 3**03**17 重複上述步驟

### <a name="deploy-with-template"></a>透過範本進行部署
您可以使用 Github 上的其中一個快速入門範本來部署所有必要資源。 範本會部署虛擬機器、負載平衡器、可用性設定組等。請遵循下列步驟來部署範本：

1. 在 Azure 入口網站上開啟[資料庫範本][template-multisid-db]或[交集範本][template-converged]。資料庫範本只會建立資料庫的負載平衡規則，而交集範本還會建立 ASCS/SCS 和 ERS (僅限 Linux) 執行個體的負載平衡規則。 如果您打算安裝 SAP NetWeaver 架構的系統，而且也想要在同一部電腦上安裝 ASCS/SCS 執行個體，請使用[交集範本][template-converged]。
1. 輸入下列參數
    1. SAP 系統識別碼  
       輸入您想要安裝之 SAP 系統的 SAP 系統識別碼。 此識別碼將用來做為已部署資源的前置詞。
    1. 堆疊類型 (只有在您使用交集範本時才適用)  
       選取 SAP NetWeaver 堆疊類型
    1. OS 類型  
       選取一個 Linux 發行版本。 在此範例中，請選取 SLES 12 BYOS
    1. DB 類型  
       選取 HANA
    1. SAP 系統大小  
       新系統將提供的 SAP 數量。 如果您不確定系統將需要多少 SAP，請詢問您的 SAP 技術合作夥伴或系統整合者。
    1. 系統可用性  
       選取 HA
    1. 管理員使用者名稱和管理員密碼  
       建立可用來登入電腦的新使用者。
    1. 新的或現有的子網路  
       決定應該建立新的虛擬網路和子網路，或是應該使用現有子網路。 如果您已經有連接到內部部署網路的虛擬網路，請選取現有虛擬網路。
    1. 子網路識別碼  
    虛擬機器應該連接的子網路識別碼。 選取將虛擬機器連接到內部部署網路之 VPN 或快速路由虛擬網路的子網路。 識別碼通常看起來像 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>設定 Linux HA

下列項目會加上下列其中一個前置詞：[A] - 適用於所有節點、[1] - 僅適用於節點 1 或 [2] - 僅適用於節點 2。

1. [A] 僅限 SLES for SAP BYOS - 註冊 SLES，使它能夠使用存放庫
1. [A] 僅限 SLES for SAP BYOS - 新增公用雲端模組
1. [A] 更新 SLES
    ```bash
    sudo zypper update

    ```

1. [1] 啟用 SSH 存取
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] 啟用 SSH 存取
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] 啟用 SSH 存取
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] 安裝 HA 擴充功能
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] 設定磁碟配置
    1. LVM  
    我們通常建議針對儲存資料和記錄檔的磁碟區使用 LVM。 下列範例假設虛擬機器已連接四個資料磁碟，這些資料磁碟應該用來建立兩個磁碟區。
        * 針對您想要使用的所有磁碟建立實體磁碟區。
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * 建立一個適用於資料檔案的磁碟區群組、一個用於記錄檔的磁碟區群組，以及一個用於 SAP HANA 共用目錄的磁碟區群組
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * 建立邏輯磁碟區
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * 建立掛接目錄，並複製所有邏輯磁碟區的 UUID
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the id of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * 針對這三個邏輯磁碟區建立 fstab 項目
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    將這行插入至 /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * 掛接新的磁碟區
    <pre><code>
    sudo mount -a
    </code></pre>
    1. 一般磁碟  
       針對小型或示範系統，您可以將 HANA 資料和記錄檔放在一個磁碟上。 下列命令會在 /dev/sdc 上建立磁碟分割，並使用 xfs 來將它格式化。
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>記下 /dev/sdc1 的識別碼
    sudo /sbin/blkid  sudo vi /etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] 設定適用於所有主機的主機名稱解析  
    您可以使用 DNS 伺服器，或修改所有節點上的 /etc/hosts。 這個範例示範如何使用 /etc/hosts 檔案。
   取代下列命令中的 IP 位址和主機名稱
    ```bash
    sudo vi /etc/hosts
    ```
    將下列幾行插入至 /etc/hosts。 變更 IP 位址和主機名稱以符合您的環境    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] 安裝叢集
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] 將節點新增至叢集
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] 將 hacluster 密碼變更為同一個密碼
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] 設定 corosync 以使用其他傳輸，並新增節點清單。 否則叢集將無法運作。
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    將下列粗體內容新增至檔案。
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    然後重新啟動 corosync 服務

    ```bash
    sudo service corosync restart
    
    ```

1. [A] 安裝 HANA HA 套件  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>安裝 SAP HANA

請依照 [SAP HANA SR 效能最佳化案例指南 (英文)][suse-hana-ha-guide] 的第 4 章來安裝 SAP HANA 系統複寫。

1. [A] 從 HANA DVD 執行 hdblcm
    * 選擇安裝 -> 1
    * 選取要安裝的其他元件 -> 1
    * 輸入安裝路徑 [/hana/shared]：-> ENTER
    * 輸入本機主機名稱 [..]：-> ENTER
    * 您是否要將其他主機新增至系統？ (y/n) [n]：-> ENTER
    * 輸入 SAP HANA 系統識別碼︰<SID of HANA e.g. HDB>
    * 輸入執行個體號碼 [00]：   
  HANA 執行個體號碼。 如果您使用了 Azure 範本或遵循上述範例，請使用 03
    * 選取資料庫模式 / 輸入索引 [1]：-> ENTER
    * 選取系統使用量 / 輸入索引 [4]：  
  選取系統使用量
    * 輸入資料磁碟區的位置 [/hana/data/HDB]：-> ENTER
    * 輸入記錄檔磁碟區的位置 [/hana/log/HDB]：-> ENTER
    * 是否限制記憶體配置上限？ [n]：-> ENTER
    * 輸入主機的憑證主機名稱 '...' [...]：-> ENTER
    * 輸入 SAP 主機代理程式使用者 (sapadm) 密碼：
    * 確認 SAP 主機代理程式使用者 (sapadm) 密碼：
    * 輸入系統管理員 (hdbadm) 密碼：
    * 確認系統管理員 (hdbadm) 密碼：
    * 輸入系統管理員主目錄 [/usr/sap/HDB/home]：-> ENTER
    * 輸入系統管理員登入殼層 [/bin/sh]：-> ENTER
    * 輸入系統管理員使用者識別碼 [1001]：-> ENTER
    * 輸入使用者群組 (sapsys) 的識別碼 [79]：-> ENTER
    * 輸入資料庫使用者 (SYSTEM) 密碼：
    * 確認資料庫使用者 (SYSTEM) 密碼：
    * 是否在電腦重新開機後重新啟動系統？ [n]：-> ENTER
    * 是否要繼續？ (y/n)：  
  驗證摘要並輸入 y 繼續
1. [A] 升級 SAP 主機代理程式  
  從 [SAP 軟體中心 (英文)][sap-swcenter] 下載最新的 SAP 主機代理程式封存檔，然後執行下列命令來升級代理程式。 取代封存檔的路徑以指向您所下載的檔案。
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] 建立 HANA 複寫 (以 root 身分執行)  
    執行下列命令。 請務必使用 SAP HANA 安裝的值來取代粗體字串 (HANA 系統識別碼 HDB 和執行個體號碼 03)。
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] 建立金鑰儲存區項目 (以 root 身分執行)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] 備份資料庫 (以 root 身分執行)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] 切換至 sapsid 使用者 (例如 hdbadm)，並建立主要站台。
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] 切換至 sapsid 使用者 (例如 hdbadm)，並建立次要站台。
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>設定叢集架構

變更預設設定

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>現在我們將檔案載入到叢集
sudo crm configure load update crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>建立 STONITH 裝置

STONITH 裝置會使用服務主體來對 Microsoft Azure 授權。 請遵循下列步驟來建立服務主體。

1. 移至 <https://portal.azure.com>
1. 開啟 [Azure Active Directory] 刀鋒視窗  
   移至 [屬性]，並記下目錄識別碼。這是**租用戶識別碼**。
1. 按一下 [應用程式註冊]
1. 按一下 [新增]
1. 輸入名稱、選取應用程式類型 [Web 應用程式/API]、輸入登入 URL (例如 http://localhost )，然後按一下 [建立]
1. 登入 URL 並未使用，而且可以是任何有效的 URL
1. 選取新的應用程式，然後按一下 [設定] 索引標籤中的金鑰
1. 輸入新金鑰的說明、選取 [永不過期]，然後按一下 [儲存]
1. 記下值。 此值會用來做為服務主體的**密碼**
1. 記下應用程式識別碼。此識別碼會用來做為服務主體的使用者名稱 (以下步驟中的 **login id**)

服務主體預設沒有存取您 Azure 資源的權限。 您需要為服務主體提供權限來啟動和停止 (解除配置) 叢集的所有虛擬機器。

1. 移至 https://portal.azure.com
1. 開啟 [所有資源] 刀鋒視窗
1. 選取虛擬機器
1. 選取 [存取控制 (IAM)]
1. 按一下 [新增]
1. 選取 [擁有者] 角色
1. 輸入您先前建立的應用程式名稱
1. Click OK

當您編輯虛擬機器的權限之後，就可以在叢集中設定 STONITH 裝置。

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription id, resource group, tenant id, service principal id and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription id</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant id</b>" login="<b>login id</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>現在我們將檔案載入到叢集
sudo crm configure load update crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>建立 SAP HANA 資源

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system id
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>現在我們將檔案載入到叢集
sudo crm configure load update crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system id and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>現在我們將檔案載入到叢集
sudo crm configure load update crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>測試叢集設定
下一章將說明如何測試您的設定。 每一個測試都假設您是 root 身分，而 SAP HANA 主要節點是在虛擬機器 saphanavm1 上執行。

#### <a name="fencing-test"></a>隔離測試

您可以藉由停用節點 saphanavm1 上的網路介面，來測試隔離代理程式的設定。

<pre><code>
sudo ifdown eth0
</code></pre>

根據您的叢集組態，虛擬機器現在應該會重新啟動或停止。
如果您將 stonith-action 設為 off，虛擬機器將會停止，並將資源移轉到執行中的虛擬機器。

當您重新啟動虛擬機器之後，如果您設定 AUTOMATED_REGISTER="false"，SAP HANA 資源將無法啟動為次要。 在此情況下，您需要執行下列命令來將 HANA 執行個體設為次要：

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>測試手動容錯移轉

您可以藉由停止節點 saphanavm1 上的 pacemaker 服務來測試手動容錯移轉。
<pre><code>
service pacemaker stop
</code></pre>

容錯移轉之後，您可以重新啟動服務。 如果您設定 AUTOMATED_REGISTER="false"，saphanavm1 上的 SAP HANA 資源將無法啟動為次要。 在此情況下，您需要執行下列命令來將 HANA 執行個體設為次要：

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>測試移轉

您可以執行下列命令來移轉 SAP HANA 主要節點
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

這應該會將 SAP HANA 主要節點以及包含虛擬 IP 位址的群組移轉到 saphanavm2。
如果您設定 AUTOMATED_REGISTER="false"，saphanavm1 上的 SAP HANA 資源將無法啟動為次要。 在此情況下，您需要執行下列命令來將 HANA 執行個體設為次要：

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

移轉會建立需要再次刪除的位置條件約束。

<pre><code>
crm configure edited

# delete location contraints that are named like the following contraint. You should have two contraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

您也必須清除次要節點資源的狀態

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>後續步驟
* [適用於 SAP 的 Azure 虛擬機器規劃和實作][planning-guide]
* [適用於 SAP 的 Azure 虛擬機器部署][deployment-guide]
* [適用於 SAP 的 Azure 虛擬機器 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性並為 Azure 上的 SAP HANA (大型執行個體) 規劃災害復原，請參閱 [SAP HANA (大型執行個體) 在 Azure 上的高可用性和災害復原](hana-overview-high-availability-disaster-recovery.md)。 
