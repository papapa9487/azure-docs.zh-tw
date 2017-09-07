---
title: "設定使用 Azure Site Recovery 將 Hyper-V (含 System Center VMM) 複寫至 Azure 時的來源和目標 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 將 VMM 雲端中的 Hyper-V VM 複寫至 Azure 儲存體時，設定來源和目標設定時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>步驟 8：設定將 Hyper-V (含 VMM) 複寫至 Azure 時的來源與目標

[建立保存庫](vmm-to-azure-walkthrough-create-vault.md)和指定複寫的目標後，請使用本文來設定來源和目標設定，以在使用 Azure 入口網站中的 [Azure Site Recovery](site-recovery-overview.md) 服務將 System Center Virtual Machine Manager (VMM) 雲端中的內部部署 Hyper-V 虛擬機器複寫至 Azure 時套用。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="set-up-the-source-environment"></a>設定來源環境

S1. 按一下 [準備基礎結構]  >  [來源]。

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. 在 [準備來源] 中，按一下 [+ VMM] 以新增 VMM 伺服器。

    ![設定來源](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. 在 [新增伺服器] 中，檢查 [System Center VMM 伺服器] 是否出現在 [伺服器類型] 中，以及 VMM 伺服器是否符合[必要條件和 URL 需求](#prerequisites)。
4. 下載 Azure Site Recovery Provider 安裝檔案。
5. 下載註冊金鑰。 您會在執行安裝程式時用到此金鑰。 該金鑰在產生後會維持 5 天有效。

    ![設定來源](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>在 VMM 伺服器上將提供者解除安裝

1. 在 VMM 伺服器上執行 Provider 安裝檔案。
2. 在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
3. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。

    ![安裝位置](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. 安裝完成時，請按一下 [註冊] 以在保存庫中註冊 VMM 伺服器。
5. 在 [保存庫設定] 頁面中，按一下 [瀏覽] 來選取保存庫金鑰檔案。 指定 Azure Site Recovery 訂用帳戶和保存庫名稱。

    ![伺服器註冊](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. 在 [網際網路連線] 中，指定在 VMM 伺服器上執行的 Provider 透過網際網路連接到 Site Recovery 的方式。

   * 如果您想要讓 Provider 直接連線，請選取 [不使用 Proxy 直接連接至 Azure Site Recovery]。
   * 如果您現有的 Proxy 需要驗證，或您想要使用自訂 Proxy，請選取 [使用 Proxy 伺服器連接至 Azure Site Recovery]。
   * 如果您使用自訂 proxy，請指定位址、連接埠以及認證
   * 如果您使用 Proxy，您應該已經允許[必要條件](#on-premises-prerequisites)中所述的 URL。
   * 如果您使用的是自訂 proxy，則會使用指定的 proxy 認證自動建立 VMM RunAs 帳戶 (DRAProxyAccount)。 設定 proxy 伺服器，讓此帳戶可以成功進行驗證。 在 VMM 主控台中，可以修改 VMM RunAs 帳戶設定。 在 [設定] 中，展開 [安全性] > [執行身分帳戶]，然後修改 DRAProxyAccount 的密碼。 您必須重新啟動 VMM 服務，這項設定才會生效。

     ![網際網路](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. 接受或修改自動為資料加密產生的 SSL 憑證位置。 如果您在 Azure 站台復原入口網站中為 Azure 所保護的雲端啟用資料加密，則會使用此憑證。 請保護此憑證的安全。 當您執行容錯移轉至 Azure 時，如果已啟用資料加密，您需要使用它來解密。
8. 在 [伺服器名稱] 中，指定保存庫中 VMM 伺服器的易記識別名稱。 在叢集設定中，指定 VMM 叢集角色名稱。
9. 如果您想要將 VMM 伺服器上所有雲端的中繼資料與保存庫進行同步，請啟用 [同步處理雲端中繼資料]。 這個動作只需要在每個伺服器上進行一次。 如果不要同步所有雲端，您可以取消核取這項設定，再於 VMM 主控台的雲端屬性中個別同步每個雲端。 按一下 [註冊]  完成此程序。

    ![伺服器註冊](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. 註冊作業隨即開始。 註冊完成後，伺服器會顯示在 [Site Recovery 基礎結構]  >  [VMM 伺服器] 中。


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-V 主機上安裝 Azure 復原服務代理程式

1. 設定 Provider 之後，您需要下載 Azure 復原服務代理程式的安裝檔案。 在 VMM 雲端中的每部 Hyper-V 伺服器上執行安裝程式。

    ![Hyper-V 網站](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. 在 [檢查先決條件] 中，按 [下一步]。 將自動安裝任何缺少的必要元件。

    ![Prerequisites Recovery Services Agent](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. 在 [安裝設定] 中，接受或修改安裝位置和快取位置。 您可以在至少有 5 GB 可用儲存體的磁碟機上設定快取，但我們建議快取磁碟機有 600 GB 或更多可用空間。 然後按一下 [安裝] 。
4. 安裝完成後，按一下 [關閉]  即可完成。

    ![註冊 MARS 代理程式](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>命令列安裝
您可以使用下列命令，從命令列安裝 Microsoft Azure 復原服務代理程式：

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>設定從 Hyper-V 主機對 Site Recovery 的網際網路 Proxy 存取

在 Hyper-V 主機上執行的復原服務代理程式需要 Azure 的網際網路存取權才能進行 VM 複寫。 如果您透過 Proxy 存取網際網路，請如下所示設定它︰

1. 在 Hyper-V 主機上開啟 Microsoft Azure 備份 MMC 嵌入式管理單元。 根據預設，Microsoft Azure 備份的捷徑位於桌面上或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在嵌入式管理單元中，按一下 [變更屬性]。
3. 在 [Proxy 設定]  索引標籤上指定 Proxy 伺服器資訊。

    ![註冊 MARS 代理程式](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. 確定代理程式可以連到[必要條件](#on-premises-prerequisites)中所述的 URL。

## <a name="set-up-the-target-environment"></a>設定目標環境
指定要用於複寫的 Azure 儲存體帳戶，以及 Azure VM 在容錯移轉後會連接的 Azure 網路。

1. 按一下 [準備基礎結構] > [目標]，選取您想要在其中建立容錯移轉虛擬機器的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對容錯移轉虛擬機器使用的部署模型。

    ![儲存體](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

    ![儲存體](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. 如果您尚未建立儲存體帳戶而想要使用 Resource Manager 建立一個帳戶，請按一下 [+儲存體帳戶] 以內嵌方式執行該作業。  在 [建立儲存體帳戶]  刀鋒視窗中，指定帳戶名稱、類型、訂用帳戶和位置。 此帳戶應位於與復原服務保存庫相同的位置。

   ![儲存體](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * 如果您想要使用傳統模型建立儲存體帳戶，請在 Azure 入口網站中執行該作業。 [深入了解](../storage/common/storage-create-storage-account.md)
   * 如果您將進階儲存體帳戶使用於複寫的資料，則須設定其他標準儲存體帳戶來儲存複寫記錄檔，而這類記錄檔會擷取內部部署資料的進行中變更。
5. 如果您尚未建立 Azure 網路，而且想要使用 Resource Manager 建立一個，請按一下 [+網路] 以內嵌方式執行該作業。 在 [建立虛擬網路]  刀鋒視窗上，指定網路名稱、位址範圍、子網路詳細資料、訂用帳戶和位置。 此網路應位於與復原服務保存庫相同的位置。

   ![網路](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   如果您想要使用傳統模型建立網路，請在 Azure 入口網站中執行該作業。 [深入了解](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。





## <a name="next-steps"></a>後續步驟

移至[步驟 9：設定網路對應](vmm-to-azure-walkthrough-network-mapping.md)

