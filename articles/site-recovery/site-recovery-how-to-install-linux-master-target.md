---
title: "如何安裝 Linux 主要目標伺服器以從 Azure 容錯移轉至內部部署 | Microsoft Docs"
description: "在重新保護 Linux 虛擬機器之前，您必須先有 Linux 主要目標伺服器。 了解如何進行安裝。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 5341e3e56e0c366079958dd9a885f6ee3e8436cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-linux-master-target-server"></a>安裝 Linux 主要目標伺服器
您可以在容錯移轉虛擬機器之後，將虛擬機器容錯回復至內部部署網站。 若要進行容錯回復，您需要在從 Azure 到內部部署網站的過程中重新保護虛擬機器。 針對此程序，您需要內部部署的主要目標伺服器以接收流量。 

如果受保護的虛擬機器是 Windows 虛擬機器，您需要 Windows 主要目標。 若是 Linux 虛擬機器，您需要 Linux 主要目標。 請參閱下列步驟，以了解如何建立和安裝 Linux 主要目標。

> [!IMPORTANT]
> 從主要目標伺服器的 9.10.0 版本開始，最新的主要目標伺服器只能安裝在 Ubuntu 16.04 伺服器上。 CentOS6.6 伺服器上不允許安裝新的主要目標。 不過，您可以使用 9.10.0 版本繼續升級舊的主要目標伺服器。

## <a name="overview"></a>概觀
本文提供如何安裝 Linux 主要目標的指示。

在本文末尾或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中張貼意見或問題。

## <a name="prerequisites"></a>必要條件

* 若要選擇要在其中部署主要目標的主機，請確定容錯回復的目的地是現有內部部署虛擬機器還是新的虛擬機器。 
    * 若是現有虛擬機器，主要目標的主機應該要能存取虛擬機器的資料存放區。
    * 如果內部部署虛擬機器不存在，容錯回復虛擬機器會建立在作為主要目標的相同主機上。 您可以選擇任何 ESXi 主機來安裝主要目標。
* 主要目標應位於可與處理序伺服器及組態伺服器通訊的網路上。
* 主要目標的版本必須等於或早於處理序伺服器和組態伺服器的版本。 例如，若組態伺服器的版本是 9.4，則主要目標的版本可以是 9.4 或 9.3，但不能是 9.5。
* 主要目標只能是 VMware 虛擬機器，不能是實體伺服器。

## <a name="create-the-master-target-according-to-the-sizing-guidelines"></a>根據大小配置準則建立主要目標

建立符合下列大小配置準則的主要目標：
- **RAM**：6 GB 或更多
- **OS 磁碟大小**：100 GB 或更多 (以安裝 CentOS6.6)
- **用於保留磁碟機的額外磁碟大小**：1 TB
- **CPU 核心**：4 核心或更多

支援下列受支援的 Ubuntu 核心。


|核心系列  |最多支援  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>部署主要目標伺服器

### <a name="install-ubuntu-16042-minimal"></a>安裝 Ubuntu 16.04.2 極簡版

請依循下列步驟以安裝 Ubuntu 16.04.2 64 位元作業系統。

**步驟 1：**前往[下載連結](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64)，並選擇最接近的鏡像以從中下載 Ubuntu 16.04.2 極簡版 64 位元 ISO。

將 Ubuntu 16.04.2 極簡版 64 位元 ISO 放在 DVD 光碟機中，並啟動系統。

**步驟2：**選取 [English]\(英文\) 作為慣用語言，然後按 **Enter**。

![選取語言](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**步驟 3：**選取 [Install Ubuntu Server]\(安裝 Ubuntu 伺服器\)，然後按 **Enter**。

![選取安裝 Ubuntu Server](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**步驟 4：**選取 [English]\(英文\) 作為慣用語言，然後按 **Enter**。

![選取 English 作為慣用語言](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**步驟 5：**從 [Time Zone]\(時區\) 選項清單中選取適當的選項，然後按 **Enter**。

![選取正確的時區](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**步驟 6：**選取 [No]\(否\)(預設選項)，然後按 **Enter**。


![設定鍵盤](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**步驟 7：**選取 [English (US)]\(英文 (美國)\) 作為鍵盤的國家/地區，然後按 **Enter**。

![選取美國作為國家/地區](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**步驟 8：**選取 [English (US)]\(英文 (美國)\) 作為鍵盤配置，然後按 **Enter**。

![選取美式英文作為鍵盤配置](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**步驟 9：**在 [Hostname]\(主機名稱\) 方塊中輸入伺服器的主機名稱，然後選取 [Continue]\(繼續\)。

![輸入伺服器的主機名稱](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**步驟 10：**若要建立使用者帳戶，請輸入使用者名稱，然後選取 [Continue]\(繼續\)。

![建立使用者帳戶](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**步驟 11：**輸入新使用者帳戶的密碼，然後選取 [Continue]\(繼續\)。

![輸入密碼](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**步驟 12：**確認新使用者的密碼，然後選取 [Continue]\(繼續\)。

![確認密碼](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**步驟 13：**選取 [No]\(否\) (預設選項)，然後按 **Enter**。

![設定使用者和密碼](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**步驟 14：**如果顯示的時區是正確的，選取 [Yes]\(是\) (預設選項)，然後按 **Enter**。

若要重新設定您的時區，選取 [No]\(否\)。

![設定時鐘](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**步驟 15：**從磁碟分割方法選項中選取 [Guided - Use entire disk]\(引導式 - 使用整個磁碟\)，然後按 **Enter**。

![選取資料分割方法選項](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**步驟 16：**從 [select disk to partition]\(選取要分割的磁碟\) 選項中選擇適當的磁碟，然後按 **Enter**。


![選取磁碟](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**步驟 17：**選取 [Yes]\(是\) 以將變更寫入至磁碟，然後按 **Enter**。

![將變更寫入磁碟](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**步驟 18：**選取預設選項，選取 [Continue]\(繼續\)，然後按 **Enter**。

![選取預設選項](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**步驟 19：**選取適當的選項來管理您系統上的升級，然後按 **Enter**。

![選取如何管理升級](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> 由於 Azure Site Recovery 主要目標伺服器需要非常特定版本的 Ubuntu，因此您需要確認已停用虛擬機器核心升級。 如果啟用，任何一般升級都將導致主要目標伺服器故障。 請確定您選取 [No automatic updates]\(不自動更新\) 選項。


**步驟 20：**選取預設選項。 如果您想要使用 openSSH 進行 SSH 連線，請選取 [OpenSSH server]\(OpenSSH 伺服器\) 選項，然後選取 [Continue]\(繼續\)。

![選取軟體](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**步驟 21：**選取 [Yes](是\)，然後按 **Enter**。

![安裝 GRUB 開機載入器](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**步驟 22：**選取適當的裝置以安裝開機載入器 (最好是 **/dev/sda**)，然後按 **Enter**。

![選取裝置以安裝開機載入器](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**步驟 23：**選取 [Continue]\(繼續\)，然後按 **Enter** 以完成安裝。

![完成安裝](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

安裝完成後，請使用新的使用者認證登入 VM。 (如需詳細資訊，請參閱**步驟 10**。)

請依循下列螢幕擷取畫面中的描述，設定根使用者的密碼。 然後以根使用者的身分登入。

![設定根使用者的密碼](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="prepare-the-machine-for-configuration-as-a-master-target-server"></a>準備要設定為主要目標伺服器的機器
接下來，準備要設定為主要目標伺服器的機器。

若要取得 Linux 虛擬機器中每個 SCSI 硬碟的識別碼，請啟用 **disk.EnableUUID = TRUE** 參數。

若要啟用此參數，請依循下列步驟︰

1. 關閉虛擬機器。

2. 以滑鼠右鍵按一下左窗格中的虛擬機器項目，然後選取 [Edit Settings] /(編輯設定/)。

3. 選取 [選項] 索引標籤。

4. 在左窗格中，選取 [進階]  >  [一般]，然後選取畫面右下方的 [組態參數] 按鈕。

    ![[選項] 索引標籤](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    [組態參數] 選項在機器執行時無法使用。 若要啟用此索引標籤，請關閉虛擬機器。

5. 查看含有 [disk.EnableUUID] 的資料列是否已經存在。

    - 如果該值存在，而且設定為 [False]，請將該值變更為 [True]。 (值不區分大小寫。)

    - 如果該值存在，而且設定為 [True]，請按一下 [取消]。

    - 如果該值不存在，請選取 [新增資料列]。

    - 在名稱欄位中新增 **disk.EnableUUID**，然後將值設定為 [TRUE]。

    ![檢查 disk.EnableUUID 是否已存在](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>停用核心升級

Azure Site Recovery 主要目標伺服器需要非常特定版本的 Ubuntu，因此請確認已停用虛擬機器的核心升級。

如果啟用核心升級，則任何一般升級都將導致主要目標伺服器故障。

#### <a name="download-and-install-additional-packages"></a>下載並安裝其他套件

> [!NOTE]
> 請確定您有網際網路連線以便下載並安裝其他套件。 若沒有網際網路連線，則必須以手動方式尋找這些 RPM 套件並加以安裝。

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

### <a name="get-the-installer-for-setup"></a>取得安裝程式

如果您的主要目標有網際網路連線，您可以使用下列步驟下載安裝程式。 否則，您可以從處理序伺服器複製安裝程式並加以安裝。

#### <a name="download-the-master-target-installation-packages"></a>下載主要目標安裝套件

[下載最新的 Linux 主要目標安裝位元](https://aka.ms/latestlinuxmobsvc)。

若要使用 Linux 下載它，請輸入：

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

請務必在主目錄中下載並解壓縮安裝程式。 如果您解壓縮至 **/usr/Local**，安裝將會失敗。


#### <a name="access-the-installer-from-the-process-server"></a>從處理序伺服器存取安裝程式

1. 在處理序伺服器上，移至 **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**。

2. 從處理序伺服器複製必要的安裝程式檔案，並在主目錄中將其儲存為 **latestlinuxmobsvc.tar.gz**。


### <a name="apply-custom-configuration-changes"></a>套用自訂組態變更

若要套用自訂組態變更，請使用下列步驟︰


1. 執行下列命令來解壓縮二進位檔。
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![所執行命令的螢幕擷取畫面](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. 執行下列命令來授與權限。
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. 執行下列命令來執行指令碼。
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> 只需在伺服器上執行指令碼一次。 關閉伺服器。 然後，如下節中所述新增磁碟後，重新啟動伺服器。

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>將保留磁碟新增至 Linux 主要目標虛擬機器

使用下列步驟來建立保留磁碟：

1. 將新的 1-TB 磁碟連結至 Linux 主要目標虛擬機器，然後啟動機器。

2. 使用 **multipath -ll** 命令以便得知保留磁碟的多重路徑識別碼。

    ```
    multipath -ll
    ```
    ![保留磁碟的多重路徑識別碼](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. 格式化磁碟機，並在新的磁碟機上建立檔案系統。

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![在磁碟機上建立檔案系統](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. 建立檔案系統之後，掛接保留磁碟。
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![掛接保留磁碟](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. 建立 **fstab** 項目，以便在每次啟動系統時掛接保留磁碟機。
    ```
    vi /etc/fstab
    ```
    按 **Insert** 鍵來開始編輯檔案。 建立新的一行，並插入下列文字。 根據前一個命令中醒目提示的多重路徑識別碼，編輯磁碟多重路徑識別碼。

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    按 **Esc** 鍵，然後輸入 **:wq** (寫入和結束)，以關閉編輯器視窗。

### <a name="install-the-master-target"></a>安裝主要目標

> [!IMPORTANT]
> 主要目標伺服器的版本必須等於或早於處理序伺服器和組態伺服器的版本。 如果不符合此條件，重新保護會成功，但複寫會失敗。


> [!NOTE]
> 在安裝主要目標伺服器之前，請確認虛擬機器上的 **/etc/hosts** 檔案包含會將本機主機名稱對應到所有網路介面卡相關 IP 位址的項目。

1. 在組態伺服器上從 **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** 複製複雜密碼。 然後執行下列命令，將其儲存在同個本機目錄中的 **passphrase.txt**：

    ```
    echo <passphrase> >passphrase.txt
    ```
    範例： 
    
    ```
    echo itUx70I47uxDuUVY >passphrase.txt
    ```

2. 記下組態伺服器的 IP 位址。 您在下一步需要用到它。

3. 執行下列命令來安裝主要目標伺服器，並向組態伺服器註冊伺服器。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    範例： 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

    等候指令碼完成。 如果主要目標註冊成功，主要目標會列在入口網站的 [Site Recovery 基礎結構] 頁面上。


#### <a name="install-the-master-target-by-using-interactive-installation"></a>使用互動式安裝來安裝主要目標

1. 執行下列命令來安裝主要目標。 選擇 [Master Target]\(主要目標\) 作為代理程式角色。

    ```
    ./install
    ```

2. 選擇預設安裝位置，然後按 **Enter** 以繼續。

    ![選擇主要目標的預設安裝位置](./media/site-recovery-how-to-install-linux-master-target/image17.png)

安裝完成之後，使用命令列來註冊組態伺服器。

1. 記下組態伺服器的 IP 位址。 您在下一步需要用到它。

2. 執行下列命令來安裝主要目標伺服器，並向組態伺服器註冊伺服器。

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    範例： 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

   等候指令碼完成。 如果主要目標註冊成功，主要目標會列在入口網站的 [Site Recovery 基礎結構] 頁面上。


### <a name="upgrade-the-master-target"></a>升級主要目標

執行安裝程式。 它會自動偵測主要目標上是否已安裝代理程式。 選取 [Y]\(是\) 進行升級。安裝完成之後，您可以使用下列命令來檢查所安裝的主要目標版本。

    ```
    cat /usr/local/.vx_version
    ```

您可以看到 [VERSION]\(版本\) 欄位中提供的主要目標版本號碼。

### <a name="install-vmware-tools-on-the-master-target-server"></a>在主要目標伺服器上安裝 VMware 工具

您必須將 VMware 工具安裝在主要目標上，以便其探索資料存放區。 如果未安裝工具，重新保護畫面就不會列出資料存放區。 安裝 VMware 工具之後，您必須重新開機。

## <a name="next-steps"></a>後續步驟
在主要目標完成安裝和註冊之後，您可以看到主要目標出現在 [Site Recovery 基礎結構] 中的 [主要目標] 區段 (在組態伺服器概觀下)。

您現在可以繼續[重新保護](site-recovery-how-to-reprotect.md)，接著進行容錯回復。

## <a name="common-issues"></a>常見問題

* 請確定您未在任何管理元件 (例如主要目標) 上啟動 Storage vMotion。 如果在成功重新保護之後主要目標有移動，則無法中斷連結虛擬機器磁碟 (VMDK)。 在此情況下，容錯回復會失敗。

* 主要目標在虛擬機器上不應該有任何快照集。 如果有快照集，容錯回復會失敗。

* 由於某些客戶會有一些自訂 NIC 組態，因此啟動期間會停用網路介面，導致主要目標代理程式無法初始化。 請確定已正確設定下列屬性。 在乙太網路卡檔案的 /etc/sysconfig/network-scripts/ifcfg-eth* 中檢查這些屬性。
    * BOOTPROTO=dhcp
    * ONBOOT=yes
