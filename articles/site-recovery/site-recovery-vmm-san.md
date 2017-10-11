---
title: "使用 Azure Site Recovery 以 SAN 複寫 VMM 中的 Hyper-V VM | Microsoft Docs"
description: "本文說明如何使用 SAN 複寫，搭配 Azure Site Recovery 在兩個網站之間複寫 Hyper-V 虛擬機器。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 3df38802fcdc86e4553253d38c49faff455f873e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>使用 SAN 搭配 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至次要網站


如果您想要部署 [Azure Site Recovery](site-recovery-overview.md)，在傳統入口網站中使用 Azure Site Recovery，以管理將 Hyper-V VM (在 System Center Virtual Machine Manager 雲端中管理) 複寫至次要 VMM 網站，請閱讀本文。 此案例不適用於新的 Azure 入口網站。



若有任何意見，請張貼於文末。 請至 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) 尋找技術問題的解答。


## <a name="why-replicate-with-san-and-site-recovery"></a>為什麼使用 SAN 搭配 Site Recovery 進行複寫？

* SAN 提供企業層級、可調整的複寫解決方案，使包含 Hyper-V 與 SAN 的主要網站可利用 SAN 將 LUN 複寫至次要網站。 儲存體由 VMM 管理，複寫及容錯移轉使用 Site Recovery 進行協調。
* Site Recovery 與數個 [SAN 存放裝置合作夥伴](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)合作，提供跨光纖通道及 iSCSI 存放裝置的複寫。  
* 利用您現有的 SAN 基礎結構，保護部署在 Hyper-V 叢集中的關鍵任務應用程式。 VM 可以整組複寫，使多層式架構應用程式可以一致地容錯移轉。
* SAN 複寫藉由低 RTO 與 RPO 的同步複寫，以及高彈性的非同步複寫 (視存放裝置陣列功能而定) 確保不同層應用程式的複寫一致性。  
* 您可以在 VMM 網狀架構中管理 SAN 存放裝置，並在 VMM 中使用 SMI-S 探索現有的存放裝置。  

請注意：
- 在 Azure 入口網站中無法使用網站對網站複寫搭配 SAN。 只有在傳統入口網站中才能使用。 在傳統入口網站中無法建立新的保存庫。 可以保留現有的保存庫。
- 不支援從 SAN 複寫至 Azure。
- 您無法複寫共用 VHDX 或是透過 iSCSI 或光纖通道直接連線到 VM 的邏輯單元 (LUN)。 可以複寫客體叢集。


## <a name="architecture"></a>架構

![SAN 架構](./media/site-recovery-vmm-san/architecture.png)

- **Azure**︰在 Azure 入口網站中設定 Site Recovery 保存庫。
- **SAN 存放裝置**︰在 VMM 網狀架構中管理 SAN 存放裝置。 您新增儲存體提供者、建立儲存體分類，並設定存放集區。
- **VMM 與 Hyper-V**：我們建議一個網站一部 VMM 伺服器。 設定 VMM 私人雲端，並將 Hyper-V 叢集放在這些雲端中。 部署期間，Azure Site Recovery 提供者是安裝在每部 VMM 伺服器上，伺服器則註冊於保存庫中。 提供者會與 Site Recovery 服務通訊以管理複寫、容錯移轉和容錯回復。
- **複寫**︰在 VMM 中設定儲存體並在 Site Recovery 中設定複寫之後，就會在主要和次要 SAN 存放裝置之間進行複寫。 複寫資料不會傳送至 Site Recovery。
- **容錯移轉**︰在 Site Recovery 入口網站中啟用容錯移轉。 由於複寫是同步作業，容錯移轉期間不會遺失任何資料。
- **容錯回復**︰若要容錯回復，您可以啟用反向複寫，將差異變更從次要網站傳送到主要網站。 反向複寫完成後，則執行從次要到主要的計劃性容錯移轉。 這個規劃的容錯移轉會停止次要網站上的複本 VM，並在主要網站上啟動它們。


## <a name="before-you-start"></a>開始之前


**必要條件** | **詳細資料**
--- | ---
**Azure** |您需要 [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。 建立 Azure Site Recovery 保存庫來設定和管理複寫及容錯移轉。
**VMM** | 您可以使用單一 VMM 伺服器，並在不同的雲端之間進行複寫，但我們建議在主要網站和次要網站中各部署一個 VMM。 VMM 可部署為實體或虛擬獨立伺服器，或是一個叢集。 <br/><br/>VMM 伺服器應執行附帶最新累計更新的 System Center 2012 R2 或更新版本。<br/><br/> 您需要至少有一個雲端設定在您想要保護的主要 VMM 伺服器上，一個雲端要設定在您想要用於容錯移轉的次要 VMM 伺服器上。<br/><br/> 來源雲端必須包含一或多個 VMM 主機群組。<br/><br/> 所有的 VMM 雲端都必須設定 Hyper-V 容量設定檔。<br/><br/> 如需設定 VMM 雲端的相關資訊，請參閱[部署私人 VM 雲端](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview)。
**Hyper-V** | 在主要和次要 VMM 雲端中，您需要一或多個 Hyper-V 叢集。<br/><br/> 來源 Hyper-V 叢集必須包含一或多部 VM。<br/><br/> 主要和次要網站中的 VMM 主機群組必須包含至少一個 Hyper-V 叢集。<br/><br/>主機和目標 Hyper-V 伺服器必須執行 Hyper-V 角色的 Windows Server 2012 或更新版本，並且安裝最新更新。<br/><br/> 如果您在叢集中執行 Hyper-V，且您具有靜態 IP 位址叢集，則不會自動建立叢集訊息代理程式。 您必須手動進行設定。 如需詳細資訊，請參閱[準備 Hyper-V 複本的主機叢集](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)。
**SAN 儲存體** | 您可以藉由 iSCSI 或光纖通道存放裝置，或藉由使用共用虛擬硬碟 (vhdx)，複寫客體叢集化虛擬機器。<br/><br/> 您需要兩個 SAN 陣列：一個在主要網站中，一個在次要網站中。<br/><br/> 網路基礎結構應設定在陣列之間。 應該設定對等互連和複寫。 應該根據存放裝置陣列需求設定複寫授權。<br/><br/>在 Hyper-V 主機伺服器與存放裝置陣列之間設定網路功能，讓主機可以使用 iSCSI 或光纖通道與存放裝置 LUN 進行通訊。<br/><br/> 請參閱[支援的儲存體陣列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)。<br/><br/> 應該安裝存放裝置陣列製造商所提供的 SMI-S 提供者，並且應該由此提供者管理 SAN 陣列。 依照製造商指示設定提供者。<br/><br/>確定陣列的 SMI-S 提供者所在的伺服器，是 VMM 伺服器可透過網路以 IP 位址或 FQDN 存取的伺服器。<br/><br/> 每個 SAN 陣列都應該要有一或多個可用的存放集區。<br/><br/> 主要 VMM 伺服器應該管理主要陣列，而次要 VMM 伺服器應該管理次要陣列。
**網路對應** | 設定網路對應，以確保在容錯移轉後，會以最佳方式將已複寫的虛擬機器置於次要 Hyper-V 主機伺服器上，使這些虛擬機器可以連線到適當的 VM 網路。 如果您沒有設定網路對應，複本 VM 將不會在容錯移轉之後連接到任何網路。<br/><br/> 請確定 VMM 網路設定正確，才能在 Site Recovery 部署期間設定網路對應。 在 VMM 中，來源 Hyper-V 主機上的 VM 應連線到 VMM VM 網路。 該網路應該連結到與雲端相關聯的邏輯網路。<br/><br/> 目標雲端應該設定相對應的 VM 網路，而該網路應該連結到與目標雲端相關聯的相對應邏輯網路。<br/><br/>。

## <a name="step-1-prepare-the-vmm-infrastructure"></a>步驟 1：準備 VMM 基礎結構
若要準備您的 VMM 基礎結構，您需要：

1. 確認 VMM 雲端。
2. 將 VMM 中的 SAN 存放裝置整合並分類。
3. 建立 LUN 並配置存放裝置。
4. 建立複寫群組。
5. 設定 VM 網路。

### <a name="verify-vmm-clouds"></a>確認 VMM 雲端

開始 Site Recovery 部署之前，請確定您的 VMM 雲端已妥善設定。

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>將 VMM 網狀架構中的 SAN 存放裝置整合並分類

1. 在 VMM 主控台，移至 [網狀架構] > [儲存體] > [新增資源] > [存放裝置]。
2. 在 [新增存放裝置]精靈中，選取 [選取存放裝置提供者類型]，然後選取 [由 SMI-S 提供者探索到及管理的 SAN 和 NAS 裝置]。

    ![提供者類型](./media/site-recovery-vmm-san/provider-type.png)

3. 在 [指定儲存 SMI-S 提供者的通訊協定與位址] 頁面上，選取 [SMI-S CIMXML]，並指定用來連線到提供者的設定。
4. 在 [提供者 IP 位址或 FQDN] 和 [TCP/IP 連接埠] 中，指定用來連線到提供者的設定。 您只能針對 SMI-S CIMXML 使用 SSL 連線。

    ![提供者連接](./media/site-recovery-vmm-san/connect-settings.png)
5. 在 [執行身分帳戶] 中，指定一個可以存取提供者的 VMM 執行身分帳戶，或建立帳戶。
6. 在 [收集資訊]  頁面上，VMM 會自動嘗試探索並匯入存放裝置資訊。 若要重新嘗試探索，請按一下 [掃描提供者] 。 如果探索程序成功，頁面上就會列出探索到的存放裝置陣列、存放集區、製造商、型號及容量。

    ![探索儲存體](./media/site-recovery-vmm-san/discover.png)
7. 在 [選取要列入管理的存放集區並指派分類] 中，選取 VMM 將管理的存放集區，並為它們指派一個分類。 LUN 資訊會從存放集區匯入。 請根據您需要保護的應用程式、其容量需求，以及您對於哪些項目要一起複寫的需求，建立 LUN。

    ![分類儲存體](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>建立 LUN 並配置存放裝置

請根據您需要保護的應用程式、容量需求，以及您對於哪些項目要一起複寫的需求，建立 LUN。

1. 存放裝置出現在 VMM 網狀架構之後，您可以[佈建 LUN](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm)。

     > [!NOTE]
     > 請勿將已啟用複寫的 VM 的 VHD 新增到 LUN。 如果這些 LUN 不在 Site Recovery 複寫群組中，Site Recovery 不會偵測到它們。
     >

2. 配置儲存容量給 Hyper-V 主機叢集，讓 VMM 可以將虛擬機器資料部署到已佈建的存放裝置上：

   * 將存放裝置配置給叢集之前，您必須先將其配置給叢集所在的 VMM 主機群組。 如需詳細資訊，請參閱[如何將存放裝置邏輯單元配置給 VMM 中的主機群組](https://technet.microsoft.com/library/gg610686.aspx)及[如何將存放集區配置給 VMM 中的主機群組](https://technet.microsoft.com/library/gg610635.aspx)。
   * 依照[如何在 VMM 中設定 Hyper-V 主機叢集上的存放裝置](https://technet.microsoft.com/library/gg610692.aspx)中所述，配置儲存容量給叢集。

    ![提供者類型](./media/site-recovery-vmm-san/provider-type.png)
3. 在 [請指定儲存 SMI-S 提供者的通訊協定與位址]中，選取 [SMI-S CIMXML]。 指定連接到提供者的設定。 您只能針對 SMI-S CIMXML 使用 SSL 連線。

    ![提供者連接](./media/site-recovery-vmm-san/connect-settings.png)
4. 在 [執行身分帳戶] 中，指定一個可以存取提供者的 VMM 執行身分帳戶，或建立帳戶。
5. 在 [收集資訊]  中，VMM 會自動嘗試探索並匯入存放裝置資訊。 若需要重試，請按一下 [掃描提供者]。 當探索程序成功時，頁面上就會列出存放裝置陣列、存放集區、製造商、型號及容量。

    ![探索儲存體](./media/site-recovery-vmm-san/discover.png)
7. 在 [選取要列入管理的存放集區並指派分類] 中，選取 VMM 將管理的存放集區，並為它們指派一個分類。 LUN 資訊會從存放集區匯入。

    ![分類儲存體](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>建立複寫群組

建立複寫群組，其中包含所有需要一起進行複寫的 LUN。

1. 在 VMM 主控台中，開啟存放裝置陣列屬性的 [複寫群組] 索引標籤，然後按一下 [新增]。
2. 建立複寫群組。

    ![SAN 複寫群組](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>設定網路

如果您想要設定網路對應，請執行下列操作：

1. 請參閱 Site Recovery 網路對應。
2. 在 VMM 中準備 VM 網路：

   * [設定邏輯網路](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks)。
   * [設定 VM 網路](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks)。


## <a name="step-2-create-a-vault"></a>步驟 2：建立保存庫

1. 從您想要在保存庫中註冊的 VMM 伺服器登入 [Azure 入口網站](https://portal.azure.com)。
2. 展開 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建]  >  [快速建立]。
4. 在 [ **名稱**] 中，輸入保存庫的易記識別名稱。
5. 在 [區域] 中，選取保存庫的地理區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 按一下 [建立保存庫] 。

    ![新增保存庫](./media/site-recovery-vmm-san/create-vault.png)

檢查狀態列，以確認是否順利建立保存庫。 保存庫在主要 [復原服務] 頁面上會列為 [使用中]。

### <a name="register-the-vmm-servers"></a>註冊 VMM 伺服器

1. 從 [復原服務] 頁面開啟 [快速啟動] 頁面。 您也可以選擇圖示隨時開啟 [快速啟動]。

    ![快速啟動圖示](./media/site-recovery-vmm-san/quick-start-icon.png)
2. 在下拉式清單方塊中，選取 [在使用陣列複寫的 Hyper-V 內部部署站台之間]。

    ![註冊金鑰](./media/site-recovery-vmm-san/select-san.png)
3. 在 [準備 VMM 伺服器] 中，下載最新版本的 Azure Site Recovery 提供者安裝檔案。
4. 在來源 VMM 伺服器上執行此檔案。 如果 VMM 部署在叢集中，且您是第一次安裝提供者，請將提供者安裝在作用中節點上，並完成安裝以在保存庫中註冊 VMM 伺服器。 然後在其他節點上安裝提供者。 如果您要升級提供者，您必須在所有節點上升級，使它們有相同的提供者版本。
5. 安裝程式會檢查需求，並要求停止 VMM 服務的權限，以便開始安裝提供者。 當安裝完成之後，服務將會自動重新啟動。 在 VMM 叢集上，您會收到停止叢集角色的提示。
6. 在 [Microsoft Update] 中，您可以選擇進行更新，將會根據您的 Microsoft Update 原則安裝提供者更新。

    ![Microsoft Update](./media/site-recovery-vmm-san/ms-update.png)

7. 根據預設，提供者的安裝位置為 <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin。 按一下 [安裝] 以開始。

    ![安裝位置](./media/site-recovery-vmm-san/install-location.png)

8. 安裝提供者之後，請按一下 [註冊]，在保存庫中註冊 VMM 伺服器。

    ![安裝完成](./media/site-recovery-vmm-san/install-complete.png)

9. 在 [網際網路連線] 中，指定提供者連線到網際網路的方式。 如果您想使用伺服器上的預設網際網路連線設定，請選取 [使用預設的系統 Proxy 設定]。

    ![網際網路設定](./media/site-recovery-vmm-san/proxy-details.png)

   * 如果您想要使用自訂的 Proxy，請在安裝提供者之前先設定它。 當您進行自訂 Proxy 設定時，會執行一項測試以檢查 Proxy 連線。
   * 如果您使用自訂 proxy，或者您的預設 proxy 需要驗證，您應輸入 Proxy 詳細資料，包含位址和連接埠。
   * 必須能夠從 VMM 伺服器存取必要的 URL。
   * 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 Proxy 伺服器，讓此帳戶可以進行驗證。 您可以在 VMM 主控台修改 RunAs 帳戶設定 ([設定]  > [安全性] > [執行身分帳戶] > [DRAProxyAccount])。 您必須重新啟動 VMM 服務，變更才會生效。
10. 在 [註冊金鑰] 中，選取您從入口網站下載並複製到 VMM 伺服器的金鑰。
11. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。

    ![伺服器註冊](./media/site-recovery-vmm-san/vault-creds.png)
12. 加密設定僅用於 VMM 至 Azure 的複寫。 您可以忽略它。

    ![伺服器註冊](./media/site-recovery-vmm-san/encrypt.png)
13. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。 在叢集設定中，指定 VMM 叢集角色名稱。
14. 在 [初始雲端中繼資料同步] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料同步。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。

    ![伺服器註冊](./media/site-recovery-vmm-san/friendly-name.png)

15. 按 [下一步]  ，完成此程序。 註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。 伺服器將顯示於保存庫中的 [伺服器]  >  [VMM伺服器] 中。

### <a name="command-line-installation"></a>命令列安裝

您也可以使用下列命令列來安裝 Azure Site Recovery 提供者。 這個方法可以用來在適用於 Windows Server 2012 R2 的伺服器核心上安裝提供者。

1. 將提供者安裝檔案和註冊金鑰下載至資料夾。 例如，C:\ASR。
2. 停止 VMM 服務。
3. 解壓縮提供者安裝程式。 以系統管理員身分執行這些命令︰

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. 安裝提供者：

        C:\ASR> setupdr.exe /i
5. 註冊提供者：

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

參數：

* **/Credentials**：必要參數，代表註冊金鑰檔案所在的位置。  
* **/FriendlyName**：必要參數，代表 Hyper-V 主機伺服器的名稱，該伺服器會出現在 Azure Site Recovery 入口網站中。
* **/EncryptionEnabled**︰選用參數，只會在從 VMM 複寫到 Azure 時使用。
* **/proxyAddress**：指定 Proxy 伺服器位址的選用參數。
* **/proxyport**：指定 Proxy 伺服器連接埠的選用參數。
* **/proxyUsername**：選用參數，指定 Proxy 使用者名稱 (如果 Proxy 需要驗證)。
* **/proxyPassword**：選用參數，指定用以驗證 Proxy 伺服器的密碼 (如果 Proxy 需要驗證)。

## <a name="step-3-map-storage-arrays-and-pools"></a>步驟 3：對應存放裝置陣列和集區

對應主要和次要陣列，以指定哪一個次要存放集區要從主要集區接收複寫資料。 請在設定複寫之前先對應存放裝置，因為在您為複寫群組啟用保護時，會使用此對應資訊。

在開始之前，請先確定 VMM 雲端有出現在保存庫中。 當您在安裝提供者期間同步處理所有雲端時，或在 VMM 主控台中同步處理特定雲端時，就會偵測雲端。

1. 按一下 [資源]  >  [伺服器存放裝置]  >  [對應來源和目標陣列]。
    ![伺服器註冊](./media/site-recovery-vmm-san/storage-map.png)

2. 選取主要網站上的存放裝置陣列，然後將它們對應至次要網站上的存放裝置陣列。 在 [存放集區] 中，選取要對應的來源和目標存放集區。

    ![伺服器註冊](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>步驟 4︰設定複寫設定

註冊 VMM 伺服器之後，設定雲端保護設定。

1. 在 [快速啟動] 頁面上，按一下 [設定 VMM 雲端的保護]。
2. 在 [受保護的項目] 索引標籤上，選取雲端 [組態]。
3. 在 [目標] 中，選取 [VMM]。
4. 在 [目標位置] 中，選取管理您要用於復原之雲端的 VMM 伺服器。
5. 在 [目標雲端] 中，選取您想要用於 VM 容錯移轉的目標雲端。
   * 建議您，選取的目標雲端要符合您所保護之虛擬機器的復原需求。
   * 雲端只能屬於單一雲端組 -- 當作主要雲端或目標雲端。
6. Site Recovery 會確認雲端能夠存取 SAN 存放裝置，並確認存放裝置陣列已對應。
7. 如果驗證成功，請在 [複寫類型] 中，選取 [SAN]。

儲存設定之後會建立一個作業，供您在 [作業] 索引標籤上監視。 在 [設定] 索引標籤上可修改設定。 如果您要修改目標位置或目標雲端，則必須移除雲端組態，然後重新設定雲端。

## <a name="step-5-enable-network-mapping"></a>步驟 5：啟用網路對應

1. 在 [快速入門] 頁面上，按一下 [對應網路]。
2. 選取來源 VMM 伺服器，然後選取網路將對應的目標 VMM 伺服器。 隨即會顯示來源網路的清單和與其相關聯的目標網路。 對於未對應的網路，將顯示空白值。 按一下來源和目標網路名稱旁邊的資訊圖示，以檢視每個網路的子網路。
3. 在 [來源上的網路] 中選取網路，然後按一下 [對應]。 服務會偵測目標伺服器上的 VM 網路並加以顯示。

    ![SAN 架構](./media/site-recovery-vmm-san/network-map1.png)
4. 從目標 VMM 伺服器選取其中一個 VM 網路。

    ![SAN 架構](./media/site-recovery-vmm-san/network-map2.png)

5. 當您選曲目標網路時，隨即會顯示使用來源網路的受保護雲端。 也會顯示可用的目標網路。 建議您選取所有用於複寫之雲端都可用的目標網路。
6. 按一下打勾記號完成對應程序。 此時會啟動一個作業來追蹤進度。 您可以在 [工作]  索引標籤上檢視它。

## <a name="step-6-enable-replication-for-replication-groups"></a>步驟 6：為複寫群組啟用複寫

您必須先為存放裝置複寫群組啟用複寫，才能為虛擬機器啟用保護。

1. 在 Site Recovery 入口網站中，請在主要雲端的 [屬性] 頁面開啟 [虛擬機器] 索引標籤，然後按一下 [新增複寫群組]。
2. 選取一個或多個與雲端關聯的 VMM 複寫群組、確認來源和目標陣列，然後指定複寫頻率。

Site Recovery、VMM 和 SMI-S 提供者會佈建目標網站存放裝置 LUN，並啟用存放裝置複寫。 如果複寫群組已經被複寫，Site Recovery 便會重複使用現有的複寫關係並更新資訊。

## <a name="step-7-enable-protection-for-virtual-machines"></a>步驟 7：對虛擬機器啟用保護


當存放裝置群組在複寫時，請使用下列其中一個方法，在 VMM 主控台中為 VM 啟用保護：

* **新增虛擬機器**：當您建立 VM 時，啟用複寫並將該 VM 與複寫群組建立關聯。
  使用此選項時，VMM 會使用智慧型放置，以最佳方式將 VM 存放裝置放置在複寫群組的 LUN 上。 Site Recovery 會在次要網站上協調建立陰影 VM 並配置容量，以便能夠在容錯移轉後啟動複本 VM。
* **現有的虛擬機器**：如果 VMM 中已經部署虛擬機器，您可以啟用複寫，並將存放裝置移轉至複寫群組。 完成之後，VMM 和 Site Recovery 會偵測新的虛擬機器，並開始在 Site Recovery 中管理它。 建立影子 VM 時，會在次要網站上建立並配置容量，以便能夠在容錯移轉後啟動複本 VM。

![啟用保護。](./media/site-recovery-vmm-san/enable-protect.png)

VM 啟用複寫後，就會顯示在 Site Recovery 主控台。 您可以檢視 VM 內容、追蹤狀態，以及追蹤包含多個 VM 的容錯移轉複寫群組。 在 SAN 複寫中，與複寫群組關聯的所有 VM 必須都一起進行容錯移轉。 這是因為容錯移轉會先發生在儲存層。 請務必正確地組成您的複寫群組，只將相關聯的 VM 放在一起。

> [!NOTE]
> 啟用 VM 的複寫之後，請勿將它的 VHD 新增至 LUN，除非這些 VHD 位於 Site Recovery 複寫群組中。 Site Recovery 只能偵測位於 Site Recovery 複寫群組中的 VHD。
>
>

您可以在 [作業] 索引標籤追蹤進度，包括初始複寫。 執行「完成保護」工作之後，虛擬機器即準備好進行容錯移轉。

![虛擬機器保護工作](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>步驟 8：測試部署

測試您的部署，以確定 VM 容錯移轉如預期般運作。 若要這樣做，請建立復原方案並執行測試容錯移轉。

1. 在 [復原方案] 索引標籤上，按一下 [建立復原方案]。
2. 指定復原計劃的名稱，並選取來源和目標 VMM 伺服器。 來源伺服器必須有已啟用容錯移轉和復原功能的 VM。 選取 [SAN]  ，僅檢視為 SAN 複寫設定的雲端。

    ![建立復原計畫](./media/site-recovery-vmm-san/r-plan.png)

3. 在 [選取虛擬機器] 中，選取複寫群組。 與群組相關聯的所有 VM 都會加入復原方案。 這些 VM 將新增到復原方案的預設群組 (群組 1)。 您可以視需要新增更多群組。 複寫之後，VM 會根據復原計劃群組的順序來編號。

    ![選取虛擬機器](./media/site-recovery-vmm-san/r-plan-vm.png)
4. 建立復原方案之後，它會出現在 [復原方案] 索引標籤上的清單中。 選取方案，然後選擇 [測試容錯移轉]。
5. 在 [確認測試容錯移轉] 頁面上，選取 [無]。 啟用此選項時，容錯移轉的複本 VM 將不會連線到任何網路。 這會測試 VM 容錯移轉是否如預期般運作，但不會測試網路環境。 如需其他網路功能選項的相關資訊，請參閱 [Site Recovery 容錯移轉](site-recovery-failover.md)。

    ![選取測試網路](./media/site-recovery-vmm-san/test-fail1.png)

6. 將會在複本 VM 所在的相同主機上建立測試 VM。 它不會新增至複本 VM 所在的雲端。
2. 複寫之後，複本 VM 會有不同於主要虛擬機器的 IP 位址。 如果您是從 DHCP 發出位址，則會自動更新位址。 如果您不是使用 DHCP 而想要相同位址，則需要執行數個指令碼。
3. 執行此指令碼以擷取 IP 位址：

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. 執行此範例指令碼以更新 DNS。 指定您擷取的 IP 位址。

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>後續步驟

在您執行測試容錯移轉以檢查您的環境是否如預期般運作之後，請參閱 [Site Recovery 容錯移轉](site-recovery-failover.md)，以深入了解不同類型的容錯移轉。
