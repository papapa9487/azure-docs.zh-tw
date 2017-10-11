---
title: "在傳統入口網站中從 Azure 容錯回復 VMware VM | Microsoft Docs"
description: "了解在將 VMware VM 和實體伺服器容錯移轉至 Azure 後，如何將其容錯回復到內部部署網站。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 82d5eb7fd13b1e9700a3e9bc2d30775e9c129749
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>將 VMWare 虛擬機器和實體伺服器容錯回復至內部部署網站 (傳統入口網站)
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-failback-azure-to-vmware.md)
> * [Azure 傳統入口網站](site-recovery-failback-azure-to-vmware-classic.md)
> * [Azure 傳統入口網站 (舊版)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

本文說明如何將 Azure 虛擬機器從 Azure 容錯回復至內部部署網站。 當您準備好使用本 [教學課程](site-recovery-vmware-to-azure-classic.md)，將已從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請遵循本文中的指示。

## <a name="overview"></a>Overview
下圖說明此案例的容錯回復架構。

當處理序伺服器是內部部署，而且您使用 ExpressRoute，請使用此架構。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

當處理序伺服器在 Azure 上，而且您有 VPN 或 ExpressRoute 連接，請使用此架構。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

若要查看完整的連接埠清單及容錯回復架構圖，請參閱下圖

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

容錯回復的運作方式如下：

* 在容錯移轉至 Azure 之後，您可以透過幾個階段來容錯回復至內部部署網站：
  * **階段 1**：重新保護 Azure VM，使其開始複寫回到在內部部署網站執行的 VMware VM。 啟用重新保護時，VM 會移入最初在建立容錯移轉保護群組時所自動建立的容錯回復保護群組。 如果您已將容錯移轉保護群組新增至復原計畫，則也會自動將容錯回復保護群組新增至該計畫。  在重新保護期間，您將指定如何規劃容錯回復。
  * **階段 2**：Azure VM 複寫到內部部署網站後，就要執行容錯移轉以從 Azure 容錯回復。
  * **階段 3**：容錯回復資料之後，您必須重新保護已容錯回復到的內部部署 VM，使其開始複寫至 Azure。


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>容錯回復到原始位置或替代位置
如果您已容錯移轉 VMware VM，在其仍存在於內部部署環境的前提下，您可以將其容錯回復到相同的來源 VM。 在此案例中，只會容錯回復差異變更。 請注意：

* 如果您容錯移轉實體伺服器，則一律會容錯回復至新的 VMware VM。
  * 在容錯回復實體機器之前，請注意：
  * 當從 Azure 容錯移轉回復至 VMware 時，受保護的實體機器會回復成虛擬機器。
  * 請確認連同您需要容錯回復的必要 ESX/ESXi 主機，您探索至少一部主要目標伺服器。
* 如果您要容錯回復到原始 VM，則必須符合下列條件：

  * 如果 VM 是由 vCenter 伺服器管理，主要目標的 ESX 主機應該可以存取 VM 資料存放區。
  * 如果 VM 位於 ESX 主機上，但不受 vCenter 管理，則 VM 的硬碟必須位於可由主要目標主機存取的資料存放區內。
  * 如果 VM 位於 ESX 主機上，但未使用 vCenter，則應該先完成主要目標 ESX 主機的探索後，才能重新保護。 如果您要容錯回復實體伺服器，則也適用這一條件。
  * 另一個選項 (如果內部部署 VM 存在) 是先將其刪除，然後才執行容錯回復。 如此一來，容錯回復便會在與主要目標 ESX 主機相同的主機上建立新的 VM。
* 當您容錯回復至替代位置時，資料將會復原到內部部署主要目標伺服器所使用的相同資料存放區和相同 ESX 主機上。

## <a name="prerequisites"></a>必要條件
* 您需要擁有 VMware 環境才能容錯回復 VMware VM 和實體伺服器。 不支援容錯回復至實體伺服器。
* 若要容錯回復，您應該在最初設定保護時就已建立 Azure 網路。 要進行容錯回復，就需要有從 Azure 網路 (Azure VM 所在網路) 連往內部部署網站的 VPN 或 ExpressRoute 連線。
* 如果您想要容錯回復到的目標 VM 是由 vCenter 伺服器進行管理，您必須確定已擁有在 vCenter 伺服器上探索 VM 的必要權限。 [閱讀更多資訊](site-recovery-vmware-to-azure-classic.md)。
* 如果 VM 上有快照，重新保護程序將會失敗。 您可以刪除快照或磁碟。
* 在容錯回復之前，您需要先建立幾個元件：
  * **在 Azure 中建立處理序伺服器**。 在容錯回復期間，您必須建立這個 Azure VM 並使其保持運作。 容錯回復完成後，便可以刪除該機器。
  * **建立主要目標伺服器**：主要目標伺服器會傳送及接收容錯回復資料。 您在內部部署環境中建立的管理伺服器預設會安裝主要目標伺服器。 不過，您可能必須根據容錯回復流量的大小，另外建立一個用來容錯回復的主要目標伺服器。
  * 如果您想要另外建立一個在 Linux 上執行的主要目標伺服器，您必須先設定 Linux VM，然後才安裝主要目標伺服器，詳情如下所述。
* 執行容錯回復時，組態伺服器必須為內部部署。 容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中，而失敗的容錯回復將不會成功。 因此，請確定您定期排定備份伺服器。 發生災害時，您需要使用相同的 IP 位址進行還原，讓容錯回復運作。

## <a name="set-up-the-process-server-in-azure"></a>在 Azure 中設定處理序伺服器
您必須在 Azure 中安裝處理序伺服器，讓 Azure VM 可以將資料傳回內部部署的主要目標伺服器。

1. 在 [Site Recovery 入口網站] > [組態伺服器] 中，選擇新增處理序伺服器。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. 指定處理序伺服器名稱，然後輸入要用來以系統管理員身分連接到 Azure VM 的名稱和密碼。 在 [組態伺服器]  中，選取內部部署管理伺服器，並指定應該在其中部署處理序伺服器的 Azure 網路。 這應該是 Azure VM 所在的網路。 從選取子網路指定唯一的 IP 位址並開始部署。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   將觸發部署處理序伺服器的作業

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   在 Azure 中部署處理序伺服器之後，您就能使用指定的認證來登入該伺服器。 第一次登入時，會執行處理序伺服器對話方塊。 請輸入內部部署管理伺服器的 IP 位址和它的複雜密碼。 保留預設連接埠 443 設定。 您也可以保留預設連接埠 9443 來進行資料複寫，除非您在設定內部部署管理伺服器時已特別修改此設定。

   > [!NOTE]
   > 該伺服器不會顯示在 [VM 屬性] 下。　 該伺服器只會顯示在它已註冊之管理伺服器的 [伺服器]  索引標籤底下。 處理序伺服器大約需要 10-15 分鐘才會出現。
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>在內部部署環境中設定主要目標伺服器
主要目標伺服器會接收容錯回復資料。 主要目標伺服器會自動安裝在內部部署管理伺服器，但如果要容錯回復大量資料，則可能需要另外設定一個主要目標伺服器。 以下列方式來執行此動作：

> [!NOTE]
> 如果您想要在 Linux 上安裝主要目標伺服器，請遵循下一個程序中的指示。
>
>

1. 如果您要在 Windows 上安裝主要目標伺服器，請從要安裝主要目標伺服器的 VM 開啟 [快速啟動] 頁面，並下載 Azure Site Recovery 整合安裝精靈的安裝檔案。
2. 執行安裝程式，並在 [開始之前] 中選取 [新增額外處理序伺服器以相應放大部署]。
3. 以您 [設定管理伺服器](site-recovery-vmware-to-azure-classic.md)時的相同方式完成精靈。 在 [組態伺服器詳細資料]  頁面上，指定這個主要目標伺服器的 IP 位址和複雜密碼來存取 VM。

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>將 Linux VM 設定為主要目標伺服器
若要將執行主要目標伺服器的管理伺服器設定為 Linux VM，您必須安裝 CentOS 6.6 最小作業系統、擷取每個 SCSI 硬碟的 SCSI 識別碼、額外安裝某些封裝，並套用一些自訂變更。

#### <a name="install-centos-66"></a>安裝 CentOS 6.6
1. 在管理伺服器 VM 上安裝 CentOS 6.6 最小作業系統。 在 DVD 光碟機中放入 ISO，並啟動系統。 略過媒體測試，於語言選取美式英文，選取 [基本存放裝置]確認硬碟機上沒有重要資料，然後按一下 [是] 以捨棄其中的所有資料。 輸入管理伺服器的主機名稱，然後選取伺服器的網路介面卡。  在 [編輯系統] 對話方塊中，選取 [自動連線]，然後新增靜態 IP 位址、網路和 DNS 設定。 指定時區，以及用來存取管理伺服器的根密碼。
2. 當系統要求您指定安裝類型時，請選取 [建立自訂配置] 做為磁碟分割。 按一下 [下一步] 之後，請選取 [免費] 並按一下 [建立]。 使用 [FS 類型：ext4] 來建立 **/**、**/var/crash** 和 **/home** 磁碟分割。 以 [FS 類型：swap] 的形式建立交換磁碟分割。
3. 如果找到現存裝置，將會出現警告訊息。 按一下 [格式化]  以使用磁碟分割設定將該磁碟機格式化。 按一下 [將變更寫入磁碟]  以套用磁碟分割變更。
4. 選取 [安裝開機載入器]  >  [下一步] 以在根磁碟分割上安裝開機載入器。
5. 安裝完成時，按一下 [重新開機] 。

#### <a name="retrieve-the-scsi-ids"></a>擷取 SCSI 識別碼
1. 在安裝後，擷取 VM 中每個 SCSI 硬碟的 SCSI 識別碼。 若要執行此作業，請將管理伺服器 VM 關機，然後在 VMware 的 VM 屬性中以滑鼠右鍵按一下 VM 項目 > [編輯設定]  >  [選項]。
2. 選取 [進階]  >  [一般項目]，然後按一下 [組態參數]。 機器在執行時會停用此選項。 若要啟用，則必須將機器關閉。
3. 如果 **disk.EnableUUID** 資料列存在，請確定其值已設定為 [True] \(有區分大小寫)。 如果已如此設定，則可加以取消，然後在機器開機之後，於客體作業系統內部測試 SCSI 命令。
4. 如果該資料列不存在，請按一下 [新增資料列]，並以 [True] 值來新增它。 請勿使用雙引號。

#### <a name="install-additional-packages"></a>安裝其他封裝
您必須另外下載並安裝一些封裝。

1. 確定主要目標伺服器已連線到網際網路。
2. 執行下列命令以從 CentOS 儲存機制下載並安裝 15 個套件： **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**。
3. 如果所要保護之來源機器的根目錄或開機裝置是執行 Linux wit Reiser 或 XFS 檔案系統，則應該下載並安裝下列額外封裝：

   * # <a name="cd-usrlocal"></a>cd /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>套用自訂變更
完成後續安裝步驟並已安裝封裝之後，請執行下列動作以套用自訂變更：

1. 將 RHEL 6-64 整合代理程式二進位檔複製到 VM。 執行下列命令來解壓縮二進位檔：**tar –zxvf <file name>**
2. 執行此命令來授與權限： **# chmod 755 ./ApplyCustomChanges.sh**
3. 執行指令碼： **# ./ApplyCustomChanges.sh**。 指令碼只應執行一次。 在指令碼順利執行後，請重新啟動伺服器。

## <a name="run-the-failback"></a>執行容錯回復
### <a name="reprotect-the-azure-vms"></a>重新保護 Azure VM
1. 在 Site Recovery 入口網站 > [機器] 索引標籤中，選取已容錯移轉的 VM，然後按一下 [重新保護]。
2. 在 [主要目標伺服器] 和 [處理序伺服器] 中，選取內部部署主要目標伺服器和 Azure VM 處理序伺服器。
3. 選取設定用來連線到 VM 的帳戶。
4. 選取保護群組的容錯回復版本。 例如，如果是在 PG1 中保護 VM，則必須選取 PG1_Failback。
5. 如果您想要復原到替代位置，請選取為主要目標伺服器所設定的保留磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware VM 會使用與主要目標伺服器相同的資料存放區。 如果您想要將複本 Azure VM 復原到相同的內部部署 VM，則內部部署 VM 必須已位於與主要目標伺服器相同的資料存放區內。 如果內部部署環境中沒有任何 VM，則會在重新保護期間建立一個新的 VM。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. 在您按一下 [確定]  以開始重新保護之後，就會開始執行將 VM 從 Azure 複寫到內部部署網站的作業。 您可以在 [作業]  索引標籤上追蹤進度。

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>執行容錯移轉至內部部署網站
在重新保護之後，VM 會轉換為其保護群組的容錯回復版本，並且會自動新增到用於容錯移轉至 Azure 的復原計畫中 (如果已有計畫)。

1. 在 [復原方案] 頁面中，選取包含容錯回復群組的復原方案，然後按一下 [容錯移轉]  >  [非計劃性容錯移轉]。
2. 在 [確認容錯移轉]  中，確認容錯移轉方向 (至 Azure)，然後選取您要用於容錯移轉的復原點 (最新)。 如果您已在設定複寫屬性時啟用 [多部 VM]  ，您便可以復原到最新的應用程式或與當機時保持一致的復原點。 按一下核取記號以啟動容錯移轉。
3. 在容錯移轉期間，Site Recovery 會關閉 Azure VM。 在確認容錯回復已如預期完成後，您可以確認 Azure VM 是否已如預期般關閉。

### <a name="reprotect-the--on-premises-site"></a>重新保護內部部署網站
在容錯回復完成後，內部部署網站上的資料會恢復，但不會受到保護。 若要再次啟動複寫至 Azure，請執行下列動作：

1. 在 Site Recovery 入口網站 > [機器] 索引標籤中，選取已容錯回復的 VM，然後按一下 [重新保護]。
2. 確認複寫至 Azure 已正常運作之後，您可以在 Azure 中刪除已容錯回復的 Azure VM (目前已未執行)。

### <a name="common-issues-in-failback"></a>容錯回復的常見問題
1. 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，這樣會成功且容錯回復可作用。 在重新保護的時候則會失敗，因為探索不到資料存放區。 徵兆是重新保護的時候不會列出資料存放區。 若要解決此問題，您可以使用有適當權限的帳戶更新 vCenter 認證並重試作業。 [閱讀更多資訊](site-recovery-vmware-to-azure-classic.md)
2. 當您將 Linux VM 容錯回復並在內部執行它時，您會看到 Network Manager 封裝已從該機器解除安裝。 這是因為 VM 在 Azure 中復原時，Network Manager 封裝遭移除。
3. 當 VM 是以靜態 IP 位址設定且容錯移轉至 Azure 時，IP 位址則是透過 DHCP 取得。 當您容錯移轉至內部時，該 VM 會繼續使用 DHCP 取得 IP 位址。 如有需要，則您必須手動登入該機器並將 IP 位址設為原本的靜態位址。
4. 如果您是使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 您將需要升級為評估授權才能啟用容錯回復。

## <a name="failing-back-with-expressroute"></a>透過 ExpressRoute 容錯回復
您可以透過 VPN 連線或 Azure ExpressRoute 進行容錯回復。 如果您想要使用 ExpressRoute，請注意下列事項：

* ExpressRoute 應該設定在來源機器所要容錯移轉到的 Azure 虛擬網路上，以及容錯移轉發生時 Azure VM 所位於的 Azure 虛擬網路上。
* 資料會複寫至公用端點上的 Azure 儲存體帳戶。 您應該在 ExpressRoute 中設定與目標資料中心的公用對等互連，這樣一來，Site Recovery 複寫才能使用 ExpressRoute。
