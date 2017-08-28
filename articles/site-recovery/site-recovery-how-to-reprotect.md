---
title: "從 Azure 重新保護到內部部署網站 | Microsoft Docs"
description: "將 VM 容錯移轉到 Azure 之後，您可以起始將 VM 容錯回復到內部部署的作業。 了解如何在容錯回復之前重新保護。"
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
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 181ed544ae4697753490642fea8eef636322a114
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>從 Azure 重新保護至內部部署網站



## <a name="overview"></a>概觀
此文章說明如何將 Azure 虛擬機器從 Azure 重新保護到內部部署網站。 當您準備好使用已從內部部署網站容錯移轉至 Azure 的 VMware 虛擬機器或 Windows/Linux 實體伺服器容錯回復時，請依照此文章中的指示執行 (如[使用 Azure Site Recovery 將 VMware 虛擬機器和實體伺服器複寫至 Azure](site-recovery-failover.md)所述)。

> [!WARNING]
> 您[完成移轉](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration)、將虛擬機器移至另一個資源群組，或刪除 Azure 虛擬機器之後，則無法執行容錯回復。


完成重新保護且受保護的虛擬機器開始複寫之後，您可以在虛擬機器上起始容錯回復，以將它們回復到內部部署網站。

在本文末尾或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 中張貼意見或問題。

如需快速概觀，請觀看下列有關如何從 Azure 容錯移轉到內部部署網站的影片。
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>必要條件
您準備要重新保護虛擬機器時，請採取或考量下列的必要動作：

* 如果 vCenter 伺服器管理您想要容錯回復到的目標虛擬機器，您必須確定已擁有在 vCenter 伺服器上探索虛擬機器的[必要權限](site-recovery-vmware-to-azure-classic.md)。

  > [!WARNING]
  > 如果內部部署的主要目標或虛擬機器上有快照集，重新保護程序將會失敗。 您可以先刪除主要目標上的快照集，再進行重新保護。 虛擬機器上的快照集會在重新保護作業期間自動合併。

* 在容錯回復之前，先建立兩個其他元件：

  * **處理序伺服器**：[處理序伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)會從在 Azure 中受保護的虛擬機器接收資料，並將資料傳送至內部部署網站。 低延遲網路必須位於處理伺服器與受保護虛擬機器之間。 因此，如果您使用 Azure ExpressRoute 連線，或 Azure 型處理序伺服器和 VPN，則可擁有內部部署處理序伺服器。
  
  * **主要目標伺服器**：主要目標伺服器會接收容錯回復資料。 您建立的內部部署管理伺服器預設會安裝主要目標伺服器。 不過，您可能必須根據容錯回復流量的大小，另外建立一部用來容錯回復的主要目標伺服器。
    * [Linux 虛擬機器需要 Linux 主要目標伺服器](site-recovery-how-to-install-linux-master-target.md)。
    * Windows 虛擬機器需要 Windows 主要目標伺服器。 您可以再次使用內部部署處理序伺服器和主要目標電腦。

    [重新保護之前在主要目標上檢查的常見項目](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server)中，列出主要目標的其他必要條件。

* 執行容錯回復時，組態伺服器必須為內部部署。 在容錯回復期間，虛擬機器必須存在於組態伺服器資料庫中。 否則，將無法成功容錯回復。 

> [!IMPORTANT]
> 請確定您定期排程備份設定伺服器。 發生災害時，使用相同的 IP 位址還原伺服器，讓容錯回復運作。

* 在 VMware 的主要目標虛擬機器組態參數中，進行 `disk.EnableUUID=true` 設定。 如果此列不存在，請新增它。 需要此設定才能提供一致的 UUID 給虛擬機器磁碟 (VMDK)，使其可正確掛接。

* *您無法使用主要目標伺服器的儲存體 vMotion*。 這會造成容錯回復失敗。 虛擬機器無法啟動，因為磁碟不可供其使用。 若要避免這個問題，請從 vMotion 清單中排除主要目標伺服器。

* 將新的磁碟機新增至主要目標伺服器：保留磁碟機。 新增磁碟並格式化磁碟機。


### <a name="frequently-asked-questions"></a>常見問題集

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>為什麼需要 S2S VPN 或 ExpressRoute 連線，才能將資料複寫回到內部部署網站？
從內部部署複寫到 Azure 的動作可以透過網際網路或具有公用對等互連的 ExpressRoute 連線來執行，重新保護和容錯回復需要站台對站台 (S2S) VPN 以複寫資料。 提供網路，讓 Azure 中已容錯移轉的虛擬機器可以觸達 (偵測) 內部部署組態伺服器。 您也可能會在容錯移轉虛擬機器的 Azure 網路中部署處理序伺服器。 此處理序伺服器也應該能夠與內部部署組態設定伺服器進行通訊。

#### <a name="when-should-i-install-a-process-server-in-azure"></a>何時應該在 Azure 中安裝處理序伺服器？


您要在 Azure 上重新保護的虛擬機器會將複寫資料傳送至處理序伺服器。 設定網路，以便 Azure 上的虛擬機器可以連線到處理序伺服器。

您可以在 Azure 中部署處理伺服器，或使用在容錯移轉期間使用的現有處理伺服器。 要考量的重點是將資料從 Azure 上的虛擬機器傳送到處理伺服器的延遲。

如果您已設定 ExpressRoute 連線，可使用內部部署處理序伺服器來傳送資料，因為虛擬機器和處理序伺服器之間的延遲很低。

 ![Expressroute 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



不過若您只有 S2S VPN，我們建議您在 Azure 中部署處理伺服器。

 ![VPN 的架構圖](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


請記住，複寫只能透過 S2S VPN 或您 ExpressRoute 網路的私人對等互連。 請確定該網路通道上有足夠的可用頻寬。

如需安裝 Azure 型處理序伺服器的資訊，請參閱[管理在 Azure 中執行的處理伺服器](site-recovery-vmware-setup-azure-ps-resource-manager.md)。

> [!TIP]
> 我們建議在容錯回復期間使用 Azure 型處理序伺服器。 如果處理序伺服器比較接近複寫的虛擬機器 (在 Azure 中容錯移轉的機器)，則複寫效能較高。 不過，在概念證明 (POC) 或示範的期間，您可以透過私人對等互連，使用內部部署處理序伺服器以及 ExpressRoute 更快完成 POC。



#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>在不同的元件上應該開啟哪些連接埠，重新保護才能運作？

![容錯移轉和容錯回復的端點](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>哪一個主要目標伺服器應用於重新保護？
必須有內部部署主要目標伺服器才可接收來自處理伺服器的資料，並接著將資料寫入到內部部署內部部署的 VMDK。 如果您要保護 Windows 虛擬機器，您需要 Windows 主要目標伺服器。 您可以再次使用內部部署處理序伺服器和主要目標伺服器 <!-- !todo component -->。 針對 Linux 虛擬機器，您需要設定其他內部部署 Linux 主要目標。


如需安裝主要目標伺服器的資訊，請參閱：

* [如何安裝 Windows 主要目標伺服器](site-recovery-vmware-to-azure.md)
* [如何安裝 Linux 主要目標伺服器](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>在容錯回復期間，內部部署 ESXi 主機支援哪些資料存放區類型？

目前，Azure Site Recovery 僅支援虛擬機器檔案系統 (VMFS) 或 vSAN 資料存放區的容錯回復。 不支援 NFS 資料存放區。 由於這項限制，重新保護畫面的資料存放區選取範圍輸入在 NFS 資料存放區中將找不到任何資料，或是仍會顯示 vSAN 資料存放區但在作業期間卻不會顯示。 如果您想要容錯回復，可建立內部部署的 VMFS 資料存放區，也可容錯回復到 VMFS 資料存放區。 此容錯回復會導致完整下載 VMDK。

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>完成主要目標伺服器安裝後必須檢查的常見項目

* 若虛擬機器存在於內部部署 vCenter 伺服器上，主要目標伺服器必須能夠存取內部部署虛擬機器的 VMDK。 需要存取才可將複寫的資料寫入虛擬機器的磁碟。 請確定內部部署內部部署的資料存放區已掛接在主要目標的主機上並已設定讀寫權限。

* 如果虛擬機器不在 vCenter 伺服器上的內部部署環境，Site Recovery 服務需要在重新保護期間建立新的虛擬機器。 您必須在建立主要目標的 ESX 主機上建立此虛擬機器。 請謹慎選擇 ESX 主機，以便在您要的主機上建立容錯回復虛擬機器。

* 您無法使用主要目標伺服器的儲存體 vMotion。 這會造成容錯回復失敗。 虛擬機器無法啟動，因為磁碟不可供其使用。

  > [!WARNING]
  > 如果主要目標在重新保護後經歷儲存體 vMotion 工作，連結到主要目標的受保護虛擬機器磁碟將移轉到 vMotion 工作的目標。 如果您嘗試在此之後進行容錯回復，則中斷磁碟連結會由於找不到磁碟而失敗。 後來就很難在您的儲存體帳戶中找到磁碟。 您必須手動找到這些磁碟並附加到虛擬機器之上。 而後，就可以啟動內部部署虛擬機器。

* 將新的磁碟機新增至現有的 Windows 主要目標伺服器。 新增磁碟並格式化磁碟機。 保留磁碟機可用來停止虛擬機器複寫回到內部部署站台時的時間點。 以下是保留磁碟機的一些條件。 若沒有這些條件，將不會針對主要目標伺服器列出磁碟機。

   * 磁碟區不能使用於其他用途，例如做為複寫目標。

   * 磁碟區不處於鎖定模式。

   * 磁碟區不是快取磁碟區。 該磁碟區上不能有主要目標安裝。 處理伺服器和主要目標的自訂安裝磁碟區不符合做為保留磁碟區的資格。 當處理序伺服器和主要目標安裝在磁碟區時，磁碟區是主要目標的快取磁碟區。

   * 磁碟區的檔案系統類型不能是 FAT 或 FAT32。

   * 磁碟區容量不是零。

   * Windows 的預設保留磁碟區為 R 磁碟區。

   * Linux 的預設保留磁碟區為 /mnt/retention。

  > [!IMPORTANT]
  > 如果您使用現有的處理序伺服器/組態伺服器電腦，或使用級別或處理序伺服器/主要目標伺服器電腦，需要新增磁碟機。 新的磁碟機應該符合前述需求。 如果保留磁碟機不存在，則不會出現在入口網站上的選取項目下拉式清單中。 將磁碟機新增至內部部署主要目標之後，磁碟機最多需要 15 分鐘才會出現在入口網站上的選取項目中。 如果磁碟機未在 15 分鐘之後出現，您也可以重新整理設定伺服器。

* Linux 容錯移轉虛擬機器需要 Linux 主要目標伺服器。 Windows 容錯移轉虛擬機器需要 Windows 主要目標伺服器。

* 在主要目標伺服器上安裝 VMware 工具。 若沒有 VMware 工具，將無法偵測主要目標之 ESXi 主機上的資料存放區。

* 使用 vCenter 屬性在主要目標虛擬機器上啟用 `disk.EnableUUID=true` 參數。 <!-- !todo Needs link. -->

* 主要目標應該至少連結一個 VMFS 資料存放區。 如果沒有的話，重新保護頁面上的**資料存放區**輸入會空白，您將無法繼續。

* 主要目標伺服器在磁碟上不能有快照集。 如果有快照集，重新保護和容錯回復會失敗。

* 主要目標不能有 Paravirtual SCSI 控制器。 控制器只能是 LSI Logic 控制器。 如果沒有 LSI Logic 控制器，重新保護會失敗。

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>重新保護的步驟

> [!NOTE]
> 在 Azure 虛擬機器啟動之後，需要經過一些時間，代理程式才會註冊回到組態伺服器中 (最多 15 分鐘)。 在這段時間，重新保護會失敗，且有錯誤訊息指出未安裝代理程式。 請等候幾分鐘，然後再次嘗試重新保護。



1. 在 [保存庫] > [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的虛擬機器，然後選取 [重新保護]。 您也可以按一下機器，並從命令按鈕中選取**重新保護**。
2. 在刀鋒視窗中，您可以看到已選取保護方向 [Azure 至內部部署]。
3. 在 [主要目標伺服器] 和 [處理伺服器] 中，選取內部部署主要目標伺服器和處理伺服器。
4. 對於 [資料存放區]，選取您想要將內部部署磁碟復原至該位置的資料存放區。 當內部部署虛擬機器已被刪除且您需要建立新的磁碟時，請使用此選項。 如果磁碟已存在，則會忽略此選項，但您仍然需要指定一個值。
5. 選擇保留磁碟機。
6. 系統會自動選取容錯回復原則。
7. 按一下 [確定] 以開始重新保護。 隨即開始執行將虛擬機器從 Azure 複寫到內部部署網站的作業。 您可以在 [作業]  索引標籤上追蹤進度。

如果您想要復原到替代位置 (當內部部署虛擬機器已被刪除時)，請選取為主要目標伺服器所設定的保留磁碟機和資料存放區。 當您容錯回復到內部部署網站時，容錯回復保護計畫中的 VMware 虛擬機器會使用與主要目標伺服器相同的資料存放區。 接著會在 vCenter 中建立新的虛擬機器。

若要將 Azure 上的虛擬機器復原到現有的內部部署虛擬機器，請在主要目標伺服器的 ESXi 主機上掛接內部部署虛擬機器的資料存放區並設定讀寫權限。
    ![重新保護對話方塊](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

您也可以在復原計劃層級重新保護。 只能透過復原方案重新保護複寫群組。 當您使用復原方案來重新保護時，您必須為每部受保護機器提供值。

> [!NOTE]
> 使用相同的主要目標伺服器來重新保護複寫群組。 如果使用不同的主要目標伺服器重新保護複寫群組，則伺服器無法提供共同的時間點。

> [!NOTE]
> 執行重新保護的期間，內部部署虛擬機器將會關閉。 這有助於確保資料在複寫期間的一致性。 完成重新保護之後，請勿開啟虛擬機器。

重新保護成功之後，虛擬機器將進入受保護狀態。

## <a name="next-steps"></a>後續步驟

在虛擬機器進入受保護狀態後，您就可以[起始容錯回復](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)。 

容錯回復會關閉 Azure 中的虛擬機器，並啟動內部部署虛擬機器。 預期應用程式停機一段時間。 選擇應用程式允許停機時的容錯回復時間。

## <a name="common-problems"></a>常見問題

* 如果您使用範本來建立虛擬機器，請確定每部虛擬機器有磁碟本身的 UUID。 如果內部部署虛擬機器和主要目標的 UUID 衝突 (因為兩者都是從相同的範本建立)，則重新保護會失敗。 部署另一個不是從相同範本建立的主要目標。

* 如果您執行唯讀的使用者 vCenter 探索並保護虛擬機器，保護會成功且容錯回復可作用。 在重新保護期間，容錯回復會失敗，因為無法探索資料存放區。 重新保護期間未列出資料存放區是容錯失敗的徵兆。 若要解決此問題，您可以使用有適當權限的帳戶更新 vCenter 認證並重試作業。 如需詳細資訊，請參閱[使用 Azure Site Recovery 將 VMWare 虛擬機器和實體伺服器複寫至 Azure](site-recovery-vmware-to-azure-classic.md)。

* 當您將 Linux 虛擬機器容錯回復並在內部部署執行它時，您會看到 Network Manager 封裝已從該機器解除安裝。 發生此解除安裝的原因是，當在 Azure 中復原虛擬機器時，Network Manager 套件已被移除。

* 當 Linux 虛擬機器已設定靜態 IP 位址且容錯移轉至 Azure 時，就會從 DHCP 取得 IP 位址。 當您容錯移轉至內部部署時，該虛擬機器會繼續使用 DHCP 取得 IP 位址。 視需要手動登入機器，並將 IP 位址設定回靜態位址。 Windows 虛擬機器可以再次取得其靜態 IP。

* 如果您是使用 ESXi 5.5 免費版或 vSphere 6 Hypervisor 免費版，則容錯移轉會成功，但容錯回復不會成功。 若要啟用容錯回復，請升級到上述程式的評估授權。

* 若無法從處理伺服器連線到組態伺服器，請使用 Telnet 檢查連線到組態伺服器連接埠 443 的連線。 您也可以嘗試從處理伺服器 Ping 組態伺服器。 當處理伺服器已連線到組態伺服器時，應該會有活動訊號。

* 如果您要嘗試容錯回復至替代的 vCenter，請確定您的新 vCenter 和主要目標伺服器均已被探索到。 一般的徵兆是，您會發現在 [重新保護] 對話方塊中無法存取/看到資料存放區。

* 做為實體內部部署伺服器保護的 Windows Server 2008 R2 SP1 伺服器無法從 Azure 容錯回復到內部部署網站。

