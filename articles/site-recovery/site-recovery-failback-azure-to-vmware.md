---
title: "將 VMware VM 從 Azure 容錯回復到內部部署 | Microsoft Docs"
description: "了解在將 VMware VM 和實體伺服器容錯移轉至 Azure 後，如何將其容錯回復到內部部署網站。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: ruturajd
ms.openlocfilehash: dde0bb6b4f6bc10afdd7d40adc6689d42b37de81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>將 VMWare 虛擬機器和實體伺服器容錯回復至內部部署網站


本文說明如何將 Azure 虛擬機器從 Azure 容錯回復到內部部署網站。 當您準備好在使用此[參考](site-recovery-how-to-reprotect.md)重新保護機器後，將 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請依照這裡的指示執行。

>[!NOTE]
>如果您使用傳統 Azure 入口網站，請參閱[這裡](site-recovery-failback-azure-to-vmware-classic.md)所說的指示來增強 VMware 到 Azure 的架構，若為舊版架構，則請參閱[這裡](site-recovery-failback-azure-to-vmware-classic-legacy.md)

## <a name="overview"></a>概觀
本節中的圖表說明此案例的容錯回復架構。

當處理伺服器為內部部署，而且您要使用 Azure ExpressRoute 連接時，請使用此架構：

![Expressroute 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

當處理伺服器在 Azure 上，而且您有 VPN 或 ExpressRoute 連接時，請使用此架構：

![VPN 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

若要查看完整的連接埠清單及容錯回復架構圖，請參閱下面的影像：

![所有連接埠的容錯移轉-容錯回復清單](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

在容錯移轉至 Azure 之後，您可以透過三個階段來容錯回復至內部部署網站：

* **階段 1**：重新保護 Azure VM，使其開始複寫回在內部部署網站中執行的 VMware VM。
* **階段 2**：Azure VM 複寫到內部部署網站後，就要執行容錯移轉以從 Azure 容錯回復。
* **階段 3**：容錯回復資料之後，您必須重新保護已容錯回復到的內部部署 VM，使其開始複寫至 Azure。

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>容錯回復到原始位置或替代位置
容錯移轉 VMware VM 之後，在它仍存在於內部部署環境的前提下，您可以將它容錯回復到相同的來源 VM。 在此案例中，只會容錯回復差異部分。

如果您容錯移轉實體伺服器，則一律會容錯回復至新的 VMware VM。 在容錯回復實體機器之前，請注意：
* 當從 Azure 容錯移轉回 VMware 時，受保護的實體機器會回復成虛擬機器。 Windows Server 2008 R2 SP1 實體機器如果受保護且容錯移轉至 Azure，則無法容錯回復。 已啟動為內部部署虛擬機器的 Windows Server 2008 R2 SP1 機器將能夠容錯回復。
* 請確認您連同需要容錯回復的必要 ESX/ESXi 主機，已探索至少一部主要目標伺服器。

如果您要容錯回復到原始 VM，則必須符合下列條件：

* 如果 VM 是由 vCenter 伺服器管理，主要目標的 ESX 主機應該可以存取 VM 資料存放區。
* 如果 VM 位於 ESX 主機上，但不受 vCenter 管理，則 VM 的硬碟必須位於可由主要目標主機存取的資料存放區中。
* 如果 VM 位於 ESX 主機上，但未使用 vCenter，則應該先完成主要目標 ESX 主機的探索後，才能重新保護。 如果您要容錯回復實體伺服器，則也適用這一條件。
* 另一個選項 (如果內部部署 VM 存在) 是先將它刪除，然後才執行容錯回復。 接著，容錯回復會在與主要目標 ESX 主機相同的主機上建立新的 VM。

當您容錯回復至替代位置時，資料將會復原到內部部署主要目標伺服器所使用的相同資料存放區和相同 ESX 主機上。

## <a name="prerequisites"></a>必要條件
* 若要容錯回復 VMware VM 和實體伺服器，您需要 VMware 環境。 不支援容錯回復至實體伺服器。
* 若要容錯回復，您必須在最初設定保護時建立 Azure 網路。 要進行容錯回復，就需要有從 Azure 網路 (Azure VM 所在網路) 連往內部部署網站的 VPN 或 ExpressRoute 連線。
* 如果您想要容錯回復到的目標 VM 是由 vCenter 伺服器所管理，您必須確定已擁有在 vCenter 伺服器上探索 VM 的必要權限。 如需詳細資訊，請參閱[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md)。
* 如果 VM 上有快照，重新保護程序將會失敗。 您可以刪除快照或磁碟。
* 進行容錯回復之前，請建立這些元件：
  * **在 Azure 中建立處理伺服器**。 此元件是您在容錯回復期間建立並保持運作的 Azure VM。 容錯回復完成後，便可以刪除該 VM。
  * **建立主要目標伺服器**：主要目標伺服器會傳送及接收容錯回復資料。 您在內部部署環境中建立的管理伺服器預設會安裝主要目標伺服器。 不過，您可能必須根據容錯回復流量的大小，另外建立一部用來容錯回復的主要目標伺服器。
  * 若要另外建立一部在 Linux 上執行的主要目標伺服器，您必須先設定 Linux VM，然後才安裝主要目標伺服器，稍後將說明作法。
* 執行容錯回復時，組態伺服器必須為內部部署。 在容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中。 若組態伺服器資料庫未包含任何 VM，則容錯回復無法成功。 因此，請確定您定期備份伺服器。 發生災害時，您需要使用相同的 IP 位址進行還原，讓容錯回復運作。
* 在 VMware 中設定主要目標 VM 之**組態參數**中的 disk.enableUUID=true 設定。 如果此列不存在，請新增它。 需要此設定才能提供一致的通用唯一識別碼 (UUID) 給虛擬機器磁碟 (VMDK) 檔案以便正確地掛載它。
* 請注意「主要目標伺服器不能是 vMotioned 儲存體」條件，因為這會導致容錯回復失敗。 該 VM 無法開機，因為尚未將磁碟提供給 VM。
* 在主要目標伺服器上新增名為保留磁碟機的磁碟機。 新增磁碟並格式化磁碟機。

## <a name="failback-policy"></a>容錯回復原則
若要複寫回內部部署，您需要容錯回復原則。 當您建立轉送方向原則時，會自動建立該原則，而且它會自動與組態伺服器關聯。 您無法編輯它。 該原則具有下列複寫設定：

* RPO 臨界值 = 15 分鐘
* 復原點保留 = 24 小時
* 應用程式一致快照頻率 = 60 分鐘

 ![容錯回復原則的複寫設定](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>在 Azure 中設定處理伺服器
在 Azure 中安裝處理伺服器，讓 Azure VM 可以將資料傳送回內部部署主要目標伺服器。

如果您已經將您的虛擬機器做為傳統資源保護 (也就是在 Azure 中復原的 VM 是傳統部署模型建立的 VM)，那麼您需要 Azure 中的處理伺服器。 若您已經在使用 Azure Resource Manager 做為部署類型的情況下復原 VM，Process Server 必須使用 Resource Manager 做為部署類型。 部署類型是由您在其中部署處理伺服器的 Azure 虛擬網路所選取。

1. 在 [保存庫] > [設定] > [Site Recovery 基礎結構] \(在 [管理] 下) > [組態伺服器] \(在 [適用於 VMware 與虛擬機器] 下)，選取組態伺服器。
2. 按一下 [處理伺服器]。

  ![[處理伺服器] 按鈕](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. 選擇將處理伺服器部署為「在 Azure 中部署容錯回復處理伺服器」。
4. 選取您已在其中復原 VM 的訂用帳戶。
5. 選取您已在其中復原 VM 的 Azure 網路。 處理伺服器必須位於相同的網路中，復原的 VM 與處理伺服器才能夠通訊。
6. 若已選取「傳統部署模型」網路，請透過 Azure Marketplace 建立 VM，然後在其中安裝處理伺服器。

 ![[新增處理伺服器] 視窗](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 當您建立處理伺服器時，請注意下列事項：
 * 映像的名稱是 *Microsoft Azure Site Recovery Process Server V2*。 選取 [傳統] 做為部署模型。

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * 根據[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md)中的指示安裝處理伺服器。
7. 如果您選取 [Resource Manager] Azure 網路，請提供下列資訊來部署處理伺服器：

  * 您想要在其中部署伺服器的資源群組名稱
  * 伺服器的名稱
  * 用於登入伺服器的使用者名稱與密碼
  * 要將伺服器部署到其中的儲存體帳戶
  * 要連接的目標子網路與網路介面
   >[!NOTE]
   >當您部署處理伺服器時，您必須建立自己的[網路介面](../virtual-network/virtual-networks-multiple-nics.md) (NIC) 並選取它。

    ![在 [新增處理伺服器] 對話方塊中輸入資訊](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. 按一下 [確定] 。 此動作會觸發在處理伺服器安裝期間建立 Resource Manager 部署類型虛擬機器的作業。 若要將該伺服器登錄到組態伺服器，請依照[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md) 中的指示在 VM 內執行安裝程式。 將會一併觸發部署處理伺服器的作業。

  處理伺服器會列在 [組態伺服器] >  [關聯的伺服器] >  [處理伺服器] 索引標籤上。

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > 處理伺服器不會顯示在 [VM 內容] 下。 它只會顯示在所登錄之管理伺服器的 [伺服器] 索引標籤上。 處理伺服器需要 10 到 15 分鐘才會出現。


## <a name="set-up-the-master-target-server-on-premises"></a>在內部部署環境中設定主要目標伺服器
主要目標伺服器會接收容錯回復資料。 伺服器會自動安裝在內部部署管理伺服器，但如果要容錯回復大量資料，則可能需要另外設定一部主要目標伺服器。 若要在內部部署環境中設定主要目標伺服器，請執行下列動作：

> [!NOTE]
> 若要在 Linux 上設定主要目標伺服器，請跳到下一個程序。 只使用 CentOS 6.6 基本作業系統做為主要目標 OS。

1. 若要在 Windows 上設定主要目標伺服器，請從要在其上安裝主要目標伺服器的 VM 開啟快速入門頁面。
2. 下載「Azure Site Recovery 統一安裝程式」精靈的安裝檔案。
3. 執行安裝程式，並在 [開始之前] 中選取 [新增額外處理伺服器以相應放大部署]。
4. 以您 [設定管理伺服器](site-recovery-vmware-to-azure-classic.md)時的相同方式完成精靈。 在 [組態伺服器詳細資料] 頁面上，指定主要目標伺服器的 IP 位址並輸入複雜密碼來存取 VM。

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>將 Linux VM 設定為主要目標伺服器
若要設定管理伺服器並以 Linux VM 執行主要目標伺服器，請安裝 CentOS 6.6 基本作業系統。 接著，擷取每個 SCSI 硬碟的 SCSI 識別碼，安裝一些其他套件，並套用一些自訂變更。

#### <a name="install-centos-66"></a>安裝 CentOS 6.6

1. 在管理伺服器 VM 上安裝 CentOS 6.6 最小作業系統。 在 DVD 光碟機中放入 ISO，並將系統開機。 跳過媒體測試。 選取 [美式英文] 做為語言，選取 [基本存放裝置]，確認硬碟上沒有任何重要資料，然後按一下 [是] 以捨棄所有資料。 輸入管理伺服器的主機名稱，然後選取伺服器網路介面卡。  在 [編輯系統] 對話方塊中，選取 [自動連線]，然後新增靜態 IP 位址、網路和 DNS 設定。 指定時區。 若要存取管理伺服器，請輸入 root 密碼。
2. 當系統要求您指定安裝類型時，請選取 [建立自訂配置] 做為磁碟分割。 按一下 [下一步] 。 選取 可用，然後按一下建立。 使用 [FS 類型：ext4] 來建立 **/**、**/var/crash** 和 **/home** 磁碟分割。 將交換磁碟分割建立為 [FS 類型：swap]。
3. 如果找到現存裝置，將會出現警告訊息。 按一下 [格式化]  以使用磁碟分割設定將該磁碟機格式化。 按一下 [將變更寫入磁碟]  以套用磁碟分割變更。
4. 選取 [安裝開機載入器]  >  [下一步] 以在根磁碟分割上安裝開機載入器。
5. 安裝完成時，按一下 [重新開機]。

#### <a name="retrieve-the-scsi-ids"></a>擷取 SCSI 識別碼

1. 在安裝後，擷取 VM 中每個 SCSI 硬碟的 SCSI 識別碼。 若要這樣做，請將管理伺服器 VM 關機。 在 VMware 的 VM 屬性中，以滑鼠右鍵按一下 VM 項目 > [編輯設定]  > [選項]。
2. 選取 進階 > 一般項目，然後按一下組態參數。 當機器正在執行時，此選項無法使用。 若要讓此選項可用，必須將機器關機。
3. 執行下列其中一個動作：
 * 如果 **disk.EnableUUID** 列存在，請確定其值已設定為 [True] \(區分大小寫)。 如果值已設定為 [True]，您可以取消，然後在機器開機之後，於客體作業系統內部測試 SCSI 命令。
 * 如果 **disk.EnableUUID** 列不存在，請按一下 [新增列]，並以 [True] 值新增它。 請勿使用雙引號。

#### <a name="install-additional-packages"></a>安裝其他套件
下載並安裝其他套件。

1. 確定主要目標伺服器已連線到網際網路。
2. 若要從 CentOS 存放庫下載並安裝 15 個套件，請執行此命令：`# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`。
3. 如果所要保護的來源機器是執行 Linux 並針對根目錄或開機裝置使用 Reiser 或 XFS 檔案系統，請下載並安裝下列額外套件：

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \# rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \# wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \# rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath (這是在主要目標伺服器上啟用多重路徑套件的必要做法)

#### <a name="apply-custom-changes"></a>套用自訂變更
完成安裝後步驟並安裝套件之後，請執行下列動作以套用自訂變更：

1. 將 RHEL 6-64 整合代理程式二進位檔複製到 VM。 若要將二進位檔解壓縮，請執行此命令：`tar –zxvf <file name>`。
2. 若要授與權限，請執行此命令：`# chmod 755 ./ApplyCustomChanges.sh`。
3. 執行下列指令碼：`# ./ApplyCustomChanges.sh`。 只要執行一次。 在指令碼順利執行後，請將伺服器重新開機。

## <a name="run-the-failback"></a>執行容錯回復
### <a name="reprotect-the-azure-vms"></a>重新保護 Azure VM
1. 在 [保存庫] 的 [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的 VM，然後選取 [重新保護]。
2. 在刀鋒視窗上，您可以看到已選取保護方向 [Azure 至內部部署]。
3. 在 [主要目標伺服器] 和 [處理伺服器] 中，選取內部部署主要目標伺服器和 Azure VM 處理伺服器。
4. 選取您想要將內部部署磁碟復原至該位置的資料存放區。 當內部部署 VM 已被刪除且您需要建立新的磁碟時，請使用此選項。 如果磁碟已存在，但您仍然需要指定值的話，請忽略此選項。
5. 當 VM 複寫回內部部署時，可以使用保留磁碟機來停止時間點。 這裡列出保留磁碟機的一些條件。 若沒有這些條件，將不會針對主要目標伺服器列出磁碟機。

  * 磁碟區不能使用於其他用途 (做為複寫目標等)。
  * 磁碟區不能處於鎖定模式。
  * 磁碟區不能是快取磁碟區。 (該磁碟區上不能有主要目標安裝。 處理伺服器加上主要目標自訂安裝磁碟區不符合做為保留磁碟區的資格。 在這裡，已安裝的處理伺服器加上主要目標磁碟區是主要目標的快取磁碟區。)
  * 磁碟區檔案系統類型不能是 FAT 和 FAT32。
  * 磁碟區容量不能為零。
  * Windows 的預設保留磁碟區為 R 磁碟區。
  * Linux 的預設保留磁碟區為 /mnt/retention。

6. 系統會自動選取容錯回復原則。
7. 在您按一下 [確定] 以開始重新保護之後，就會開始執行將 VM 從 Azure 複寫到內部部署網站的作業。 您可以在 [作業]  索引標籤上追蹤進度。

如果您想要復原到替代位置，請選取為主要目標伺服器設定的保留磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計劃中的 VMware VM 會使用與主要目標伺服器相同的資料存放區。 如果您想要將複本 Azure VM 復原到相同的內部部署 VM，則內部部署 VM 必須已位於與主要目標伺服器相同的資料存放區內。 如果內部部署環境中沒有任何 VM，則會在重新保護期間建立一個新的 VM。

![在下拉式功能表中選取 [Azure 到內部部署]](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

您也可以在復原計劃層級重新保護。 如果您有一個複寫群組，則只能使用復原方案來重新保護它。 當您使用復原方案來重新保護時，請為每部受保護的機器使用先前的值。

> [!NOTE]
> 複寫群組應該使用相同的主要目標重新保護。 如果使用不同的主要目標伺服器重新保護，則無法針對它們判斷一般時間點。

### <a name="run-a-failover-to-the-on-premises-site"></a>執行容錯移轉至內部部署網站
重新保護 VM 後，您可以起始從 Azure 至內部部署的容錯移轉。

1. 在 [已複寫的項目] 頁面上，以滑鼠右鍵按一下虛擬機器，然後選取 [非計劃性容錯移轉]。
2. 在 [確認容錯移轉] 中，確認容錯移轉方向 (以 Azure 為來源)，並選取您想要用來進行容錯移轉的復原點 (最近的復原點或最近的應用程式一致復原點)。 應用程式一致復原點發生在最近的時間點之前，而且它將會導致一些資料遺失。
3. 在容錯移轉期間，Site Recovery 會將 Azure VM 關機。 在確認容錯回復已如預期完成後，您可以確認 Azure VM 是否已如預期般關閉。

### <a name="reprotect-the-on-premises-site"></a>重新保護內部部署網站
容錯回復完成之後，請認可虛擬機器，以確保會刪除在 Azure 中復原的 VM。 若要這樣做，請以滑鼠右鍵按一下受保護的項目，然後按一下認可。 此動作將會觸發在 Azure 中移除先前復原之虛擬機器的作業。

在認可完成後，內部部署網站上的資料會恢復，但不會受到保護。 若要再次開始複寫至 Azure，請執行下列動作：

1. 在 保存庫 的 設定 >  已複寫的項目 中，選取已容錯回復的 VM，然後按一下重新保護。
2. 提供必須用來將資料傳送回 Azure 之處理伺服器的值。
3. 按一下 [確定] 。

重新保護完成之後，VM 會複寫回 Azure，而且您可以執行容錯移轉。

### <a name="resolve-common-failback-issues"></a>解決常見容錯回復問題
* 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，這樣會成功且容錯回復可作用。 在重新保護期間，容錯回復會失敗，因為無法探索資料存放區。 徵兆是重新保護期間不會列出資料存放區。 若要解決此問題，您可以使用有適當權限的帳戶更新 vCenter 認證並重試作業。 如需詳細資訊，請參閱[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md)
* 當您將 Linux VM 容錯回復並在內部部署執行它時，您會看到 Network Manager 套件已從該機器解除安裝。 發生此解除安裝的原因是，當在 Azure 中復原 VM 時，Network Manager 套件已被移除。
* 當 VM 是以靜態 IP 位址設定且容錯移轉至 Azure 時，IP 位址是透過 DHCP 取得。 當您容錯移轉回內部部署時，該 VM 會繼續使用 DHCP 取得 IP 位址。 視需要手動登入機器，並將 IP 位址設定回靜態位址。
* 如果您是使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 若要啟用容錯回復，請升級到上述程式的評估授權。
* 若無法從處理伺服器連線到組態伺服器，請嘗試使用 Telnet 連線到組態伺服器的連接埠 443，以檢查與組態伺服器之間的連線。 您也可以嘗試從處理伺服器機器 Ping 組態伺服器。 當處理伺服器已連線到組態伺服器時，應該會有活動訊號。
* 如果您要嘗試容錯回復至替代的 vCenter，請確定您的新 vCenter 已被探索到，而且主要目標伺服器也已被探索到。 一般的徵兆是，您會發現在 [重新保護] 對話方塊中無法存取/看到資料存放區。
* 做為實體內部部署電腦保護的 WS2008R2SP1 電腦無法從 Azure 容錯回復到內部部署。

## <a name="fail-back-with-expressroute"></a>透過 ExpressRoute 進行容錯回復
您可以透過 VPN 連線或 ExpressRoute 連線進行容錯回復。 如果您想要使用 ExpressRoute 連線，請注意下列事項：

* ExpressRoute 連線應該設定在來源機器所要容錯移轉到的 Azure 虛擬網路上，以及容錯移轉發生時 Azure VM 所位於的 Azure 虛擬網路上。
* 資料會複寫至公用端點上的 Azure 儲存體帳戶。 若要使用 ExpressRoute 連線，請在 ExpressRoute 中設定與目標資料中心的公用對等互連以進行 Site Recovery 複寫才。
