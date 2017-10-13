---
title: "使用 Azure Site Recovery 將 Hyper-V VM 複寫至次要站台 | Microsoft Docs"
description: "說明如何使用 Azure 入口網站將 VMM 雲端中的 Hyper-V VM 複寫至次要 VMM 網站。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: b33a1922-aed6-4916-9209-0e257620fded
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: 777bddea6b1cb325a6f8ede00196b18e1746d80c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>使用 Azure 入口網站將位於 VMM 雲端中的 Hyper-V 虛擬機器複寫至次要 VMM 站台
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmm-to-vmm.md)
> * [傳統入口網站](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - 資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md)，將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器複寫至次要網站。 深入了解此[案例架構](site-recovery-components.md)。

在閱讀本文之後，請在下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼任何意見。


## <a name="prerequisites"></a>必要條件

**必要條件** | **詳細資料**
--- | ---
**Azure** | 您需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 [深入了解](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery 價格。
**內部部署 VMM** | 我們建議您在主要網站和次要網站中各有一部 VMM 伺服器。<br/><br/> 您可以在單一 VMM 伺服器上的雲端之間進行複寫。<br/><br/> VMM 伺服器至少應執行含有最新更新的 System Center 2012 SP1。<br/><br/> VMM 伺服器需要網際網路存取。
**VMM 雲端** | 每部 VMM 伺服器都必須有一或多個雲端，而所有雲端都必須設定 Hyper-V 容量設定檔。 <br/><br/>雲端必須包含一或多個 VMM 主機群組。<br/><br/> 如果您只有一部 VMM 伺服器，則需要至少有兩個雲端，才能作為主要和次要網站。
**Hyper-V** | Hyper-V 伺服器必須至少執行具備 Hyper-V 角色並已安裝最新更新的 Windows Server 2012。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/>  Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，請安裝[更新 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> 如果您在 Windows Server 2012 上的叢集中執行 Hyper-V，當您的叢集是靜態 IP 位址型叢集時，並不會自動建立叢集訊息代理程式。 您必須手動設定叢集訊息代理程式。 [閱讀更多資訊](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。<br/><br/> Hyper-V 伺服器需要網際網路存取。
**URL** | VMM 伺服器和 Hyper-V 主機必須能夠觸達這些 URL：<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

## <a name="deployment-steps"></a>部署步驟

以下是您要執行的動作：

1. 驗證必要條件。
2. 準備 VMM 伺服器和 Hyper-V 主機。
3. 建立復原服務保存庫。 保存庫包含組態設定，並協調複寫。
4. 指定來源、目標和複寫設定。
5. 將行動服務部署在您想要複寫的 VM 上。
6. 準備進行複寫，並啟用 Hyper-V VM 的複寫。
7. 執行測試容錯移轉，確定一切都沒問題。

## <a name="prepare-vmm-servers-and-hyper-v-hosts"></a>準備 VMM 伺服器和 Hyper-V 主機

準備部署：

1. 請確定 VMM 伺服器和 Hyper-V 主機符合上述必要條件，而且可以觸達所需的 URL。
2. 設定 VMM 網路，以便設定[網路對應](#network-mapping-overview)。

    - 確認來源 Hyper-V 主機伺服器上的 VM 已連接到 VMM VM 網路。 該網路應該連結到與雲端相關聯的邏輯網路。
    確認您用於復原的次要雲端已設定相對應的 VM 網路。 該 VM 網路應該連結到與次要雲端相關聯的邏輯網路。

3. 如果您想要在相同 VMM 伺服器上的雲端之間複寫 VM，請準備[單一伺服器部署](#single-vmm-server-deployment)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [新增] > [管理] > [復原服務]。
3. 在 [名稱] 中，指定保存庫的易記識別名稱。 如果您有多個訂用帳戶，請選取其中一個。
4. [建立資源群組](../azure-resource-manager/resource-group-template-deploy-portal.md)，或選取現有的資源群組。 指定 Azure 區域。 電腦會複寫至此區域。 若要查看支援的地區，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)
5. 如果您想要從儀表板快速存取保存庫，請按一下 [釘選到儀表板] > [建立保存庫]。

    ![新增保存庫](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

新的保存庫會出現在 [儀表板] 的[所有資源] 上，以及主要 [復原服務保存庫] 刀鋒視窗上。


## <a name="choose-a-protection-goal"></a>選擇保護目標

選取您要複寫的項目以及您要複寫到的位置。

2. 按一下 [Site Recovery] > [步驟 1: 準備基礎結構] > [保護目標]。
3. 選取 [到復原網站]，然後選取 [是，利用 Hyper-V]。
4. 選取 [是]，表示您使用 VMM 來管理 Hyper-V 主機。
5. 如果您有次要 VMM 伺服器，請選取 [是]。 如果您要在單一 VMM 伺服器上的雲端之間部署複寫，請按一下 [否] 。 然後按一下 [確定] 。

    ![選擇目標](./media/site-recovery-vmm-to-vmm/choose-goals.png)

## <a name="set-up-the-source-environment"></a>設定來源環境

在 VMM 伺服器上安裝 Azure Site Recovery Provider，並在保存庫中探索和註冊伺服器。

1. 按一下 [步驟 1：準備基礎結構]  >  [來源]。

    ![設定來源](./media/site-recovery-vmm-to-vmm/goals-source.png)
2. 在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。

    ![設定來源](./media/site-recovery-vmm-to-vmm/set-source1.png)
3. 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中，以及 VMM 伺服器是否符合[必要條件](#prerequisites)。
4. 下載 Azure Site Recovery Provider 安裝檔案。
5. 下載註冊金鑰。 您會在執行安裝程式時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

    ![設定來源](./media/site-recovery-vmm-to-vmm/set-source3.png)
6. 您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 您不需要明確地在 Hyper-V 主機伺服器上安裝任何項目。


### <a name="install-the-azure-site-recovery-provider"></a>安裝 Azure Site Recovery 提供者

1. 在每部 VMM 伺服器上執行 Provider 安裝檔案。 如果 VMM 部署在叢集中，請在第一次安裝時執行下列作業︰
    -  將提供者安裝在作用中的節點上，並完成安裝以在保存庫中註冊該 VMM 伺服器。
    - 然後在其他節點上安裝 Provider。 叢集節點全都應該執行相同版本的 Provider。
2. 安裝程式會執行幾項必要條件檢查，並要求停止 VMM 服務的權限。 當安裝完成之後，VMM 服務將會自動重新啟動。 如果您安裝在 VMM 叢集上，您會收到停止叢集角色的提示。
3. 在 [Microsoft Update] 中，可以選擇根據您的 Microsoft Update 原則來安裝提供者更新。
4. 在 [安裝] 中，接受或修改預設安裝位置，然後按一下 [安裝]。

    ![安裝位置](./media/site-recovery-vmm-to-vmm/provider-location.png)
5. 安裝完成之後，按一下 [註冊] 以在保存庫中註冊伺服器。

    ![安裝位置](./media/site-recovery-vmm-to-vmm/provider-register.png)
6. 在 [保存庫名稱] 中，確認要註冊伺服器的保存庫名稱。 按 [下一步] 。

    ![伺服器註冊](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)
7. 在 [網際網路連線] 中，指定 VMM 伺服器上執行的提供者連接至 Azure 的方式。

    ![網際網路設定](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

   - 您可以指定提供者應該直接或透過 proxy 連接至網際網路。
   - 指定 proxy 設定 (如有需要)。
   - 如果您使用 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 您可以在 VMM 控主台 > [設定] > [安全性] > [執行身分帳戶]中修改 RunAs 帳戶設定。 重新啟動 VMM 服務以更新變更。
8. 在 [註冊金鑰] 中，選取您從 Azure Site Recovery 下載並複製到 VMM 伺服器的金鑰。
9. 只有在您正在將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 時，才會使用這項加密設定。 如果您是在複寫至次要站台，則不會使用它。
10. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。 在叢集設定中，指定 VMM 叢集角色名稱。
11. 在 [同步處理雲端中繼資料] 中，選取您是否要將 VMM 伺服器上所有雲端的中繼資料與保存庫同步。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。
12. 按 [下一步]  ，完成此程序。 註冊後，Azure Site Recovery 即可從 VMM 伺服器擷取中繼資料。 此伺服器會顯示於保存庫中 [伺服器] 頁面的 [VMM 伺服器] 索引標籤上。

    ![伺服器](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)
13. 當伺服器可以在 Site Recovery 主控台中使用之後，於 [來源]  >  中選取 VMM 伺服器，然後選取 Hyper-V 主機所在的雲端。 然後按一下 [確定] 。

您也可以從命令列安裝提供者：

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>設定目標環境

選取目標 VMM 伺服器和雲端。

1. 按一下 [準備基礎結構] > [目標]，然後選取您想要使用的目標 VMM 伺服器。
2. 隨即顯示伺服器上與 Site Recovery 同步處理的雲端。 選取目標雲端。

   ![目標](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="set-up-replication-settings"></a>設定複寫設定

- 當您建立複寫原則時，所有使用該原則的主機都必須有相同的作業系統。 VMM 雲端中可以包含執行不同 Windows Server 版本的 Hyper-V 主機，但在此情況下，您需要多個複寫原則。
- 您可以離線執行初始複寫。 [深入了解](#prepare-for-initial-offline-replication)

1. 若要建立新的複寫原則，請按一下 [準備基礎結構]  >  [複寫設定]  >  [+建立及關聯]。

    ![網路](./media/site-recovery-vmm-to-vmm/gs-replication.png)
2. 在 [建立及關聯原則] 中指定原則名稱。 來源和目標類型應該是 **Hyper-V**。
3. 在 [Hyper-V 主機版本] 中，選取在主機上執行的作業系統。
4. 在 [驗證類型] 和 [驗證連接埠] 中，指定主要與復原 Hyper-V 主機伺服器之間流量的驗證方式。 除非您有有效的 Kerberos 環境，否則請選取 [憑證]。 Azure Site Recovery 會自動設定用於 HTTPS 驗證的憑證。 您不需要手動執行任何動作。 根據預設，連接埠 8083 和 8084 (用於憑證) 將在 Hyper-V 主機伺服器上的 Windows 防火牆中開啟。 如果您確實選取 [Kerberos]，Kerberos 票證將會用於主機伺服器的相互驗證。 請注意，這項設定只有在 Hyper-V 主機伺服器是執行於 Windows Server 2012 R2 時才有重要性。
5. 在 [複製頻率] 中，指定您要在初始複寫後複寫差異資料的頻率 (每隔 30 秒、5 或 15 分鐘)。
6. 在 [復原點保留] 中，針對每個復原點指定保留週期的長度 (以小時為單位)。 受保護的機器可以復原到週期內的任意點。
7. 在 [應用程式一致快照頻率] 中，指定建立包含應用程式一致快照之復原點的頻率 (1-12 小時)。 Hyper-V 使用兩種類型的快照，一個是標準快照，提供整個虛擬機器的增量快照，另一個是應用程式一致快照，會建立虛擬機器內應用程式資料的時間點快照。 應用程式一致快照會使用「磁碟區陰影複製服務」(VSS) 來確保建立快照時，應用程式是處於一致狀態。 如果您啟用應用程式一致快照，它會影響在來源虛擬機器上執行的應用程式效能。 確認您設定的值低於您設定的其他復原點數目。
8. 在 [資料傳輸壓縮] 中，指定是否應該將傳輸的已複寫資料壓縮。
9. 選取 [刪除複本 VM]，以指定如果您停用對來源 VM 的保護，則應刪除複本虛擬機器。 如果啟用此設定，當您停用對來源 VM 的保護時，便會從 Site Recovery 主控台中加以移除、在 VMM 主控台中移除 VMM 的 Site Recovery 設定，並刪除複本。
10. 在 [初始複寫方法] 中，如果您要透過網路進行複寫，請指定是否要啟動初始複寫，或將它排程。 若要節省網路頻寬，您可能要將它排程在離峰時間執行。 然後按一下 [確定] 。

     ![複寫原則](./media/site-recovery-vmm-to-vmm/gs-replication2.png)
11. 當您建立新的原則時，該原則會自動與 VMM 雲端產生關聯。 在 [複寫原則] 中，按一下 [確定]。 您可以在 [複寫] > 原則名稱 > [關聯 VMM 雲端] 中，將其他 VMM 雲端 (及其中的 VM) 與此複寫原則產生關聯。

     ![複寫原則](./media/site-recovery-vmm-to-vmm/policy-associate.png)


### <a name="configure-network-mapping"></a>設定網路對應

- 開始之前，深入了解[網路對應](#prepare-for-network-mapping)。
- 確認 VMM 伺服器上的虛擬機器已連線到 VM 網路。


1. 在 [Site Recovery 基礎結構] > [網路對應] > [網路對應]，按一下 [+網路對應]。

    ![網路對應](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. 在 [新增網路對應] 索引標籤中，選取來源和目標 VMM 伺服器。 隨即會擷取與 VMM 伺服器相關聯的 VM 網路。
3. 在 [來源網路] 中，從與主要 VMM 伺服器相關聯的 VM 網路清單中，選取您要使用的網路。
4. 在 [目標網路] 中，選取您要在次要 VMM 伺服器上使用的網路。 然後按一下 [確定] 。

    ![網路對應](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

以下是網路對應開始時發生的事情︰

* 對應到來源 VM 網路的任何現有複本虛擬機器都將連線到目標 VM 網路。
* 連線到來源 VM 網路的新虛擬機器都會在複寫之後連線到目標對應的網路。
* 如果您修改與新網路的現有對應，複本虛擬機器將使用新設定進行連線。
* 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在之子網路名稱相同，複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。

### <a name="configure-storage-mapping"></a>設定儲存體對應。

新的 Azure 入口網站中不支援[儲存體對應](#prepare-for-storage-mapping)。 不過，可以使用 Powershell 來啟用這項功能。 [深入了解](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-7-configure-storage-mapping)。

## <a name="step-5-capacity-planning"></a>步驟 5︰容量規劃

您現已設定您的基本基礎結構，請思考容量規劃並找出您是否需要額外的資源。

- 下載並執行 [Azure Site Recovery Capacity Planner](site-recovery-capacity-planner.md)，以收集複寫環境的相關資訊，包括 VM、每個 VM 的磁碟和每個磁碟的儲存體。
- 收集即時複寫資訊之後，您可以修改 NetQos 原則來控制 VM 的複寫頻寬。 請至 Thomas Maurer 部落格深入了解[節流處理 Hyper-V 複本流量](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)。 取得 [New-NetQosPolicy Cmdlet](https://technet.microsoft.com/library/hh967468.aspx.) 的詳細資訊。

## <a name="enable-replication"></a>啟用複寫

1. 按一下 [步驟 2: 複寫應用程式]  >  [來源]。 第一次啟用複寫之後，請按一下保存庫中的 [+複寫]  ，以對其他機器啟用複寫。

    ![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication1.png)
2. 在 [來源] 中，選取 VMM 伺服器和您要複寫的 Hyper-V 主機所在的雲端。 然後按一下 [確定] 。

    ![啟用複寫](./media/site-recovery-vmm-to-vmm/enable-replication2.png)
3. 在 [目標] 中，確認次要 VMM 伺服器和雲端。
4. 在 [虛擬機器] 中，從清單中選取您要保護的 VM。

    ![啟用虛擬機器保護](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

您可以在 [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 動作的進度。 在**完成保護**作業完成之後，虛擬機器即可進行容錯移轉。

請注意：

- 您也可以在 VMM 主控台中啟用虛擬機器的保護。 在虛擬機器屬性 > [Azure Site Recovery] 索引標籤中的工具列上按一下 [啟用保護]。
- 啟用複寫之後，您就可以在 [複寫的項目] 中檢視 VM 的屬性。 在 [基本資訊] 儀表板中，您可以看到關於 VM 的複寫原則及其狀態的資訊。 如需詳細資訊，請按一下 [屬性]  。

### <a name="onboard-existing-virtual-machines"></a>建立現有的虛擬機器
如果 VMM 中目前已經有使用 Hyper-V 複本複寫的虛擬機器，您可以使用下列方式加入它們以提供 Azure Site Recovery 複寫：

1. 確認裝載現有 VM 的 Hyper-V 伺服器位於主要雲端，且裝載複本虛擬機器的 Hyper-V 伺服器位於次要雲端。
2. 確定已針對主要 VMM 雲端設定複寫原則。
3. 針對主要虛擬機器啟用複寫。 Azure Site Recovery 和 VMM 將確保會偵測到相同的複本主機和虛擬機器，且 Azure Site Recovery 會使用指定的設定，重複使用及重新建立複寫。

## <a name="test-your-deployment"></a>測試您的部署

若要測試部署，您可以針對單一虛擬機器執行[測試容錯移轉](site-recovery-test-failover-vmm-to-vmm.md)，或建立[復原計劃](site-recovery-create-recovery-plans.md)來包含一或多部虛擬機器。



## <a name="prepare-for-offline-initial-replication"></a>準備進行離線初始複寫

您可以針對初始資料複本進行離線複寫。 您可以如下所示方式進行準備︰

* 在來源伺服器上，您會指定要從中匯出資料的路徑位置。 在匯出路徑上指派 NTFS 和共用權限的完整控制權給 VMM 服務。 在目標伺服器上，您會指定要從中匯入資料的路徑位置。 在這個匯入路徑上指派相同的權限。
* 如果共用匯入或匯出路徑，請在共用所在的遠端桌面上，指派系統管理員、進階使用者、列印操作員或伺服器操作員群組成員資格給 VMM 伺服器帳戶。
* 如果您使用任何執行身分帳戶加入主機，請在匯入和匯出路徑上指派讀取和寫入權限給 VMM 中的執行身分帳戶。
* 匯入和匯出共用不得位於任何做為 Hyper-V 主機伺服器的電腦，因為 Hyper-V 不支援回送設定。
* 在 Active Directory 的每部包含您想要保護之虛擬機器的 Hyper-V 主機伺服器上，啟用與設定限制委派，以信任匯入與匯出路徑所在的遠端電腦，如下所示：
  1. 在網域控制站上，開啟 [Active Directory 使用者和電腦] 。
  2. 在主控台樹狀目錄中，按一下 [DomainName] > [電腦]。
  3. 在 Hyper-V 主機伺服器名稱上按一下滑鼠右鍵 > [屬性]。
  4. 在 [委派] 索引標籤上，按一下 [信任這台電腦，但只委派指定的服務]。
  5. 按一下 [使用任何驗證通訊協定] 。
  6. 按一下 [新增]  > 提出技術問題。
  7. 輸入裝載匯出路徑之電腦的名稱 > [確定]。 從可用服務清單，按住 CTRL 鍵，然後按一下 [cifs] > [確定]。 為裝載匯入路徑的電腦名稱重複動作。 視需要為其他 Hyper-V 主機伺服器重複動作。



## <a name="prepare-for-network-mapping"></a>準備網路對應
網路對應會在主要和次要 VMM伺服器上的 VMM VM 網路之間進行對應：

* 容錯移轉之後，選擇性地在次要 Hyper-V 主機上放置複本 VM。
* 容錯移轉之後將複本 VM 連接至適當的 VM 網路。

請注意：
- 您可以在兩部 VMM 伺服器的 VM 網路之間設定網路對應，如果兩個站台由相同的伺服器管理，則可以在單一 VMM 伺服器上設定網路對應。
- 正確設定對應並啟用複寫時，位於主要位置的 VM 將會連接至網路，且其位於目標位置的複本將會連接至其對應的網路。
- 如果網路已在 VMM 中正確設定，當您在網路對應期間選取目標 VM 網路時，將會顯示使用來源 VM 網路的 VMM 來源雲端，以及用於保護的目標雲端上的可用目標 VM 網路。
- 如果目標網路具有多個子網路，且其中一個子網路的名稱和來源虛擬機器所在的子網路名稱相同，則複本虛擬機器將會在容錯移轉之後連線到該目標子網路。 如果沒有目標子網路具有相符的名稱，虛擬機器將會連線到網路中的第一個子網路。


以下是說明這項機制的範例。 讓我們以具有兩個位置 (紐約和芝加哥) 的組織為例。

| **位置** | **VMM 伺服器** | **VM 網路** | **對應至** |
| --- | --- | --- | --- |
| 紐約 |VMM-NewYork |VMNetwork1-NewYork |對應至 VMNetwork1-Chicago |
| VMNetwork2-NewYork |未對應 | | |
| 芝加哥 |VMM-Chicago |VMNetwork1-Chicago |對應至 VMNetwork1-NewYork |
| VMNetwork2-Chicago |未對應 | | |

在此範例中：

* 為連線至 VMNetwork1-NewYork 的任何虛擬機器建立複本虛擬機器時，它將會連線至 VMNetwork1-Chicago。
* 為 VMNetwork2-NewYork 或 VMNetwork2-Chicago 建立複本虛擬機器時，它將不會連線至任何網路。

以下是如何在我們的範例組織中設定 VMM 雲端，以及與雲端相關聯的邏輯網路。

### <a name="cloud-protection-settings"></a>雲端保護設定
| **受保護的雲端** | **保護雲端** | **邏輯網路 (紐約)** |
| --- | --- | --- |
| GoldCloud1 |GoldCloud2 | |
| SilverCloud1 |SilverCloud2 | |
| GoldCloud2 |<p>NA</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |
| SilverCloud2 |<p>NA</p><p></p> |<p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p> |

### <a name="logical-and-vm-network-settings"></a>邏輯和 VM 網路設定
| **位置** | **邏輯網路** | **相關聯的 VM 網路** |
| --- | --- | --- |
| 紐約 |LogicalNetwork1-NewYork |VMNetwork1-NewYork |
| 芝加哥 |LogicalNetwork1-Chicago |VMNetwork1-Chicago |
| LogicalNetwork2Chicago |VMNetwork2-Chicago | |

### <a name="target-networks"></a>目標網路
根據這些設定，當您選取目標 VM 網路時，下表會顯示可用的選擇。

| **選取** | **受保護的雲端** | **保護雲端** | **可用的目標網路** |
| --- | --- | --- | --- |
| VMNetwork1-Chicago |SilverCloud1 |SilverCloud2 |可用 |
| GoldCloud1 |GoldCloud2 |可用 | |
| VMNetwork2-Chicago |SilverCloud1 |SilverCloud2 |尚未提供 |
| GoldCloud1 |GoldCloud2 |可用 | |


### <a name="failback"></a>容錯回復
若要了解容錯回復 (反向複寫) 時發生的狀況，讓我們假設 VMNetwork1-NewYork 對應到 VMNetwork1-Chicago，並包含下列設定。

| **虛擬機器** | **已連線至 VM 網路** |
| --- | --- |
| VM1 |VMNetwork1-Network |
| VM2 (VM1 的複本) |VMNetwork1-Chicago |

讓我們使用這些設定，檢閱幾個可能的案例中發生的情況。

| **案例** | **結果** |
| --- | --- |
| 在容錯移轉之後，VM-2 的網路屬性沒有變更。 |VM 1 仍然連線至來源網路。 |
| 在容錯移轉並中斷連線之後，VM-2 的網路屬性有所變更。 |VM-1 已中斷連線。 |
| 在容錯移轉並連線至 VMNetwork2-Chicago 之後，VM-2 的網路屬性有所變更。 |如果未對應 VMNetwork2-Chicago，將會中斷 VM-1 連線。 |
| VMNetwork1-Chicago 的網路對應已變更。 |VM-1 現在會連線到對應至 VMNetwork1-Chicago 的網路。 |


## <a name="prepare-for-single-server-deployment"></a>準備進行單一伺服器部署


如果您只有單一 VMM 伺服器，可以在 VMM 雲端中，將 Hyper-V 主機上的 VM 複寫到 [Azure](site-recovery-vmm-to-azure.md) 或次要 VMM 雲端。 我們建議採用第一個選項，因為在雲端之間複寫並不順暢。 如果您不想要在雲端之間複寫，您可以使用單一的獨立 VMM 伺服器進行複寫，或以延伸 Windows 叢集中部署的單一 VMM 伺服器進行複寫

### <a name="standalone-vmm-server"></a>獨立 VMM 伺服器

在這個案例中，您會將單一 VMM 伺服器部署為主要站台中的虛擬機器，並使用 Site Recovery 與 Hyper-V 複本將此 VM 複寫到次要站台。

1. **在 Hyper-V VM 上設定 VMM**。 建議您將 VMM 使用的 SQL Server 執行個體共置在相同的 VM 上。 因為只需建立一個 VM，這樣就能節省時間。 如果您想要使用遠端 SQL Server 執行個體卻發生中斷，則必須先復原該執行個體後再復原 VMM。
2. **確定 VMM 伺服器至少已設定兩個雲端**。 其中一個雲端包含您想要複寫的 VM，另一個雲端做為次要位置。 包含要保護 VM 的雲端必須遵守 [必要條件](#prerequisites)。
3. 以本文所述的方式設定 Site Recovery。 在保存庫中建立並註冊 VMM 伺服器、設定複寫原則，然後啟用複寫。 來源和目標 VMM 會有相同的名稱。 指定透過網路執行初始複寫。
4. 當您設定網路對應時，要將主要雲端的 VM 網路對應到次要雲端的 VM 網路。
5. 使用 Hyper-V 管理員主控台，在包含 VMM VM 的 Hyper-V 主機上啟用 Hyper-V 複本，並啟用 VM 的複寫功能。 請不要將 VMM 虛擬機器加入受 Site Recovery 保護的雲端，以確保 Site Recovery 不會覆寫 Hyper-V 複本設定。
6. 如果您建立適用於容錯移轉的復原方案，便須使用同一部 VMM 伺服器做為來源與目標。
7. 在完全中斷運作的情況下，您進行容錯移轉和復原的方式如下︰

   1. 在次要網站的 Hyper-V 管理員主控台中，執行非計劃性容錯移轉，將主要 VMM VM 容錯移轉至次要網站。
   2. 確認 VMM VM 已啟動並執行，然後在保存庫中執行非計劃性容錯移轉，將 VM 從主要雲端容錯移轉至次要雲端。 認可容錯移轉，並視需要選取替代復原點。
   3. 在非計劃性的容錯移轉完成之後，將可透過主要站台再次存取所有資源。
   4. 當主要網站恢復運作時，請在次要網站的 Hyper-V 管理員主控台中，對 VMM VM 啟用反向複寫。 這會開始將 VM 從次要網站複寫至主要網站。
   5. 在次要網站的 Hyper-V 管理員主控台中，執行計劃性容錯移轉，將 VMM VM 容錯移轉至主要網站。 認可容錯移轉。 然後啟用反向複寫，以再次將 VMM VM 從主要網站複寫至次要網站。
   6. 在復原服務保存庫中，針對工作負載 VM 啟用反轉複寫方向，以開始將它們從次要站台複寫到主要站台。
   7. 在復原服務保存庫中，執行計劃的容錯移轉以將工作負載 VM 容錯移轉回主要站台。 認可容錯移轉以完成它。 然後啟用反轉複寫方向，以開始將工作負載 VM 從主要站台複寫到次要站台。

### <a name="stretched-vmm-cluster"></a>延伸的 VMM 叢集

您不需要將獨立的 VMM 伺服器部署為複寫至次要站台的 VM，而可將它部署為 Windows 容錯移轉叢集中的 VM，讓 VMM 發揮高可用性。 這可以提供工作負載彈性及硬體故障的防護。 若要使用 Site Recovery 來部署，應將 VMM VM 部署至跨地理不同位置之站台的延伸叢集中。 作法：

1. 在 Windows 容錯移轉叢集中的虛擬機器上安裝 VMM，然後選取在安裝期間以高度可用性執行伺服器的選項。
2. VMM 所使用的 SQL Server 執行個體應以 SQL Server AlwaysOn 可用性群組來複寫，這樣一來次要站台就會有資料庫的複本。
3. 請遵循本文中的指示建立保存庫、註冊伺服器及設定保護。 您必須在復原服務保存庫的叢集中註冊每部 VMM 伺服器。 若要這樣做，您必須在作用中的節點上安裝 Provider，並註冊 VMM 伺服器。 然後在其他節點上安裝提供者。
4. 如果發生中斷，VMM 伺服器及其對應的 SQL Server 資料庫會容錯移轉並可從次要站台存取。



## <a name="prepare-for-storage-mapping"></a>準備儲存體對應


您可以透過對應來源和目標 VMM 伺服器上的儲存體分類來設定儲存體對應，以執行下列操作：

  * **識別複本虛擬機器的目標儲存體**— 來源 VM 硬碟會複寫至目標位置中您指定的儲存體 (SMB 共用或叢集共用磁碟區 (CSV))。
  * **複本虛擬機器放置**— 儲存體對應用於以最佳方式將複本虛擬機器放在 Hyper-V 主機伺服器上。 複本虛擬機器將會放在可以存取對應的儲存體分類的主機上。
  * **無儲存體對應**— 如果您沒有設定儲存體對應，虛擬機器將會複寫至與複本虛擬機器相關聯之 Hyper-V 主機伺服器上指定的預設儲存體位置。

請注意：
- 您可以在單一伺服器上設定兩個 VMM 雲端之間的對應。
- 儲存體分類必須可用於來源與目標雲端中的主機群組。
- 分類不需要具有相同類型的儲存體。 例如，您可以將包含 SMB 共用的來源分類對應至包含 CSV 的目標分類。

### <a name="example"></a>範例
如果在 VMM 中正確設定分類，當您在儲存體對應期間選取來源和目標 VMM 伺服器時，將會顯示來源與目標分類。 以下是儲存體檔案共用和分類的範例，以供擁有兩個位置 (紐約和芝加哥) 的組織使用。

| **位置** | **VMM 伺服器** | **檔案共用 (來源)** | **分類 (來源)** | **對應至** | **檔案共用 (目標)** |
| --- | --- | --- | --- | --- | --- |
| 紐約 |VMM_Source |SourceShare1 |GOLD |GOLD_TARGET |TargetShare1 |
| SourceShare2 |SILVER |SILVER_TARGET |TargetShare2 | | |
| SourceShare3 |BRONZE |BRONZE_TARGET |TargetShare3 | | |
| 芝加哥 |VMM_Target | |GOLD_TARGET |未對應 | |
|  |SILVER_TARGET |未對應 | | | |
|  |BRONZE_TARGET |未對應 | | | |

在此範例中：

* 在 GOLD 儲存體 (SourceShare1) 上為任何虛擬機器建立複本虛擬機器時，它會複寫至 GOLD_TARGET 儲存體 (TargetShare1)。
* 在 SILVER 儲存體 (SourceShare2) 上為任何虛擬機器建立複本虛擬機器時，它會複寫至 SILVER_TARGET (TargetShare2) 儲存體，依此類推。

### <a name="multiple-storage-locations"></a>多個儲存體位置
如果將目標分類指派給多個 SMB 共用或 CSV，將會在虛擬機器受到保護時，自動選取最佳的儲存體位置。 如果沒有適合的目標儲存體可搭配指定的分類使用，則會使用 Hyper-V 主機上指定的預設儲存體位置來放置複本虛擬硬碟。

下表顯示儲存體分類和叢集共用磁碟區在我們的範例中設定的方式。

| **位置** | **分類** | **相關聯的儲存體** |
| --- | --- | --- |
| 紐約 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> |
| SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p> | |
| 芝加哥 |GOLD_TARGET |<p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p> |
| SILVER_TARGET |<p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p> | |

下表摘要說明您在此範例環境中為虛擬機器 (VM1 - VM5) 啟用保護時的行為。

| **虛擬機器** | **來源儲存體** | **來源分類** | **對應的目標儲存體** |
| --- | --- | --- | --- |
| VM1 |C:\ClusterStorage\SourceVolume1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>兩個 GOLD_TARGET</p> |
| VM2 |\\FileServer\SourceShare1 |GOLD |<p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>兩個 GOLD_TARGET</p> |
| VM3 |C:\ClusterStorage\SourceVolume2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p> |
| VM4 |\FileServer\SourceShare2 |SILVER |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>兩個 SILVER_TARGET</p> |
| VM5 |C:\ClusterStorage\SourceVolume3 |N/A |沒有對應，因此會使用 Hyper-V 主機的預設儲存體位置 |



### <a name="data-privacy-overview"></a>資料隱私權概觀

下表摘要說明此案例中儲存資料的方式︰

- - -
| 動作 | **詳細資料** | **收集的資料** | **使用** | **必要** |
| --- | --- | --- | --- | --- |
| **註冊** | 您在復原服務保存庫中註冊 VMM 伺服器。 如果您稍後想要取消註冊伺服器，可以從 Azure 入口網站刪除該伺服器資訊來達成此目的。 | 註冊 VMM 伺服器之後，Site Recovery 即會收集、處理和傳輸有關 VMM 伺服器的中繼資料以及 Site Recovery 偵測到的 VMM 雲端名稱。 | 資料可用來識別適當的 VMM 伺服器並進行通訊，以及設定適用於 VMM 雲端的設定。 | 此為必要功能。 如果您不想將此資訊傳送到 Site Recovery，就不應該使用 Site Recovery 服務。 |
| **啟用複寫** | 安裝在 VMM 伺服器上的 Azure Site Recovery Provider 是用來和 Site Recovery 服務通訊的管道。 提供者是裝載在 VMM 程序中的動態連結程式庫 (DLL)。 安裝 Provider 之後，「Datacenter Recovery」功能就會在 VMM 系統管理員主控台中啟用。 新的和現有的 VM 可以啟用此功能來啟用對 VM 的保護。 |設定這個屬性，Provider 就能將 VM 的名稱和識別碼傳送到 Site Recovery。  複寫是透過 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複本來啟用。 虛擬機器資料會從一個 Hyper-V 主機複寫至另一個 (通常位於不同的「復原」資料中心)。 |Site Recovery 會使用中繼資料來填入 Azure 入口網站中的 VM 資訊。 | 此功能是服務不可或缺的一部分，而且無法關閉。 如果您不想傳送此資訊，請勿針對 VM 啟用 Site Recovery 保護。 請注意，由 Provider 傳送到 Site Recovery 的所有資料都是透過 HTTPS 傳送。 |
| **復原計畫** | 復原方案可協助您建立復原資料中心的協調流程計劃。 您可以定義 VM 或虛擬機器群組應在復原站台上啟動的順序。 您也可以指定在每個 VM 復原時任何要執行的自動化指令碼，或任何要採取的手動動作。 容錯移轉通常會在協調復原的復原方案層級觸發。 | Site Recovery 會收集、處理和傳輸復原方案的中繼資料，包含虛擬機器中繼資料，以及任何自動化指令碼和手動動作備註的中繼資料。 |中繼資料可用來建置 Azure 入口網站中的復原方案。 |此功能是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送到 Site Recovery，請勿建立復原方案。 |
| **網路對應** | 將網路資訊從主要資料中心對應至復原資料中心。 在復原站台上復原 VM 時，網路對應可協助建立網路連線。 |Site Recovery 會為各站台 (主要與資料中心) 收集、處理並傳輸其邏輯網路的中繼資料。 |中繼資料可用來填入網路設定，讓您能夠對應網路資訊。 | 此功能是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送到 Site Recovery，請勿使用網路對應。 |
| **容錯移轉 (計劃性/非計劃性/測試)** | 容錯移轉會將 VM 從某一個 VMM 管理的資料中心容錯移轉到另一個。 容錯移轉動作在 Azure 入口網站中是手動觸發的。 |VMM 伺服器上的 Provider 會收到來自 Site Recovery 有關容錯移轉事件的通知，並在 Hyper-V 主機上透過 VMM 介面執行容錯移轉動作。 VM 的實際容錯移轉是從某一部 Hyper-V 主機到另一部，並透過 Windows Server 2012 或 Windows Server 2012 R2 Hyper-V 複本來處理。 Site Recovery 會使用傳送的資訊，在 Azure 入口網站上填入容錯移轉動作資訊的狀態。 | 此功能是服務不可或缺的一部分，而且無法關閉。 如果您不想將此資訊傳送到 Site Recovery，請勿使用容錯移轉。 |

## <a name="next-steps"></a>後續步驟

測試部署之後，接著可深入了解其他類型的[容錯移轉](site-recovery-failover.md)
