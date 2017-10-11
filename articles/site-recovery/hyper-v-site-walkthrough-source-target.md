---
title: "使用 Azure Site Recovery 設定將 Hyper-V VM 複寫至 Azure (不含 System Center VMM) 時的來源和目標 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 針對將 Hyper-V VM 複寫至 Azure 儲存體設定來源和目標設定時所需的步驟"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>步驟 8：針對將 Hyper-V 複寫至 Azure 設定來源與目標

本文說明如何在 Azure 入口網站中使用 [Azure Site Recovery](site-recovery-overview.md) 服務，設定將內部部署 Hyper-V 虛擬機器 (不含 System Center VMM) 複寫至 Azure 時的來源和目標設定。

請在本文下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼意見或問題。


## <a name="set-up-the-source-environment"></a>設定來源環境

設定 Hyper-V 網站、在 Hyper-V 主機上安裝 Azure Site Recovery Provider 和 Azure 復原服務代理程式，並在保存庫註冊網站。

1. 在 [準備基礎結構] 中，按一下 [來源]。 若要加入新的 Hyper-V 網站作為 Hyper-V 主機或叢集的容器，請按一下 [+Hyper-V 網站] 。

    ![設定來源](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. 在 [建立 Hyper-V 網站] 中，指定網站的名稱。 然後按一下 [確定] 。 現在，選取您建立的網站，然後按一下 [+Hyper-V Server]，將伺服器新增至網站。

    ![設定來源](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. 在 [新增伺服器] > [伺服器類型] 中，檢查是否已顯示 [Hyper-V 伺服器]。

    - 確定您想要新增的 Hyper-V 伺服器符合[必要條件](#on-premises-prerequisites)，而且能夠存取指定的 URL。
    - 下載 Azure Site Recovery Provider 安裝檔案。 您將執行這個檔案，在每個 Hyper-V 主機上安裝 Provider 和復原服務代理程式。

    ![設定來源](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>安裝 Provider 和代理程式

1. 在您已加入 Hyper-V 網站中的每個主機上執行 Provider 安裝程式檔案。 如果您安裝在 Hyper-V 叢集上，請在每個叢集節點上執行安裝程式。 安裝和註冊每個 Hyper-V 叢集節點，可確保 VM 即使跨節點移轉，都還是會受保護。
2. 在 [Microsoft Update] 中，您可以選擇進行更新，以便根據您的 Microsoft Update 原則安裝 Provider 更新。
3. 在 [安裝] 中接受或修改預設 Provider 安裝位置，然後按一下 [安裝]。
4. 在 [保存庫設定] 中，按一下 [瀏覽] 來選取您已下載的保存庫金鑰檔案。 指定 Azure Site Recovery 訂用帳戶、保存庫名稱，以及 Hyper-V 伺服器所屬的 Hyper-V 網站。

    ![伺服器註冊](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. 在 [Proxy 設定] 中，指定在 Hyper-V 主機上執行的 Provider 要如何透過網際網路連線到 Azure Site Recovery。

    * 如果您想要讓 Provider 直接連線，請選取 [不使用 Proxy 直接連接至 Azure Site Recovery] 。
    * 如果您現有的 Proxy 需要驗證，或您想要使用自訂 Proxy 進行 Provider 連線，請選取 [使用 Proxy 伺服器連線至 Azure Site Recovery]。
    * 如果您使用 Proxy：
        - 請指定位址、連接埠以及認證
        - 請確定已允許[必要條件](#prerequisites)中所述的 URL 通過 Proxy。

    ![網際網路](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. 安裝完成之後，按一下 [註冊] 以在保存庫中註冊該伺服器。

    ![安裝位置](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. 註冊完成之後，Azure Site Recovery 便會抓取來自 Hyper-V 伺服器的中繼資料，而該伺服器會顯示在 [站台復原基礎結構] > [Hyper-V 主機] 中。


## <a name="set-up-the-target-environment"></a>設定目標環境

指定要用於複寫的 Azure 儲存體帳戶，以及 Azure VM 在容錯移轉後會連線的 Azure 網路。

1. 按一下 [準備基礎結構] > [目標]。
2. 選取您想要在容錯移轉後，在其中建立 Azure VM 的訂用帳戶和資源群組。 選擇您想要在 Azure (傳統或資源管理) 中，針對 VM 使用的部署模型。

3. Site Recovery 會檢查您是否有一或多個相容的 Azure 儲存體帳戶和網路。

    - 如果您沒有儲存體帳戶，請按一下 [+儲存體]，建立以 Resource Manager 為基礎的內嵌帳戶。 
    - 如果您沒有 Azure 網路，請按一下 [+網路]，建立以 Resource Manager 為基礎的內嵌網路。






## <a name="next-steps"></a>後續步驟

移至[步驟 9：設定複寫原則](hyper-v-site-walkthrough-replication.md)
