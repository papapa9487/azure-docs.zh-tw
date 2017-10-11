---
title: "在 Site Recovery 中測試容錯移轉到 Azure | Microsoft Docs"
description: "了解執行從內部部署到 Azure 的測試容錯移轉"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: pratshar
ms.openlocfilehash: 54f62af6abcdd38254fd5379b95baa05656dc90b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="test--failover-to-azure-in-site-recovery"></a>在 Site Recovery 中測試容錯移轉到 Azure



本文提供使用 Azure 做為復原站台，對使用 Site Recovery 保護的虛擬機器與實體伺服器執行測試容錯移轉或 DR (災害復原) 訓練的相關資訊和指示。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

執行測試容錯移轉可驗證您的複寫策略，或在沒有資料遺失或停機時間的情況下執行災害復原訓練。 執行測試容錯移轉不會影響任何正在進行的複寫或生產環境。 測試容錯移轉可在虛擬機器或[復原計劃](site-recovery-create-recovery-plans.md)上執行。 觸發測試容錯移轉時，您必須指定測試虛擬機器會連線的網路。 一旦觸發測試容錯移轉，您可以在 [作業] 頁面追蹤進度。  


## <a name="supported-scenarios"></a>支援的案例
除了[從舊版 VMware 站台到 Azure](site-recovery-vmware-to-azure-classic-legacy.md) 以外的部署案例，都支援測試容錯移轉。 當虛擬機器已容錯移轉至 Azure 時也不支援測試容錯移轉。  


## <a name="run-a-test-failover"></a>執行測試容錯移轉
此程序說明如何針對復原方案執行測試容錯移轉。 或者，您也可以在單一機器上使用適當的選項來對其執行測試容錯移轉。

![Test Failover](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. 選取 [復原方案]  >  *recoveryplan_name*。 按一下 [測試容錯移轉] 。
1. 選取要容錯移轉到的**復原點**。 您可以使用下列其中一個選項：
    1.  **最新處理**︰這個選項會將復原方案的所有虛擬機器容錯移轉至 Site Recovery 服務已處理的最新復原點。 當您進行虛擬機器的測試容錯移轉時，也會顯示最新處理復原點的時間戳記。 如果您正在執行某個復原方案的容錯移轉，您可以移至個別的虛擬機器，並查看 [最新復原點] 圖格以取得此資訊。 因為沒有花時間處理未處理的資料，此選項提供低 RTO (復原時間目標) 容錯移轉選項。
    1.  **最新的應用程式一致**︰這個選項會將復原方案的所有虛擬機器容錯移轉至 Site Recovery 服務已處理的最新應用程式一致復原點。 當您進行虛擬機器的測試容錯移轉時，也會顯示最新應用程式一致復原點的時間戳記。 如果您正在執行某個復原方案的容錯移轉，您可以移至個別的虛擬機器，並查看 [最新復原點] 圖格以取得此資訊。
    1.  **最新**︰此選項會先處理已傳送到 Site Recovery 服務的所有資料，先為每個虛擬機器建立復原點後再進行容錯移轉。 此選項提供最低的 RPO (復原點目標)，因為在容錯移轉後建立的虛擬機器會包含觸發容錯移轉時已複寫到 Site Recovery 服務的所有資料。
    1.  **已處理最近的多部虛擬機器**：此選項僅適用於至少一部虛擬機器已啟動多部虛擬機器一致性的復原計劃。 屬於最近一般多部虛擬機器一致復原點的複寫群組容錯移轉一部份的虛擬機器。 對於最近處理的復原點進行的其他虛擬機器容錯移轉。  
    1.  **最近的多部虛擬機器應用程式一致**：此選項僅適用於至少一部虛擬機器已啟動多部虛擬機器一致性的復原計劃。 屬於最近一般多部虛擬機器應用程式一致復原點的複寫群組容錯移轉一部份的虛擬機器。 對於最近的應用程式一致復原點進行的其他虛擬機器容錯移轉。 
    1.  **自訂**︰如果您要執行虛擬機器的測試容錯移轉，您可以使用此選項以容錯移轉至特定復原點。
1. 選取一個 **Azure 虛擬網路**︰提供一個 Azure 虛擬網路，測試虛擬機器會建立於該處。 Site Recovery 會嘗試在名稱相同的子網路中建立測試虛擬機器，並使用虛擬機器的 [計算與網路] 設定中提供的 IP。 如果針對測試容錯移轉提供的 Azure 虛擬網路中沒有名稱相同的子網路，則會在依字母順序的第一個子網路中建立測試虛擬機器。 如果子網路中沒有相同的 IP，虛擬機器會取得子網路中另一個可用的 IP 位址。 請參閱此節以[瞭解更多詳細資訊](#creating-a-network-for-test-failover)
1. 如果您正在容錯移轉到 Azure 且已啟用資料加密，請在 [加密金鑰] 中，選取當您在提供者安裝期間啟用資料加密時所發出的憑證。 如果您尚未在虛擬機器上啟用加密，您可以忽略此步驟。
1. 在 [工作]  索引標籤上追蹤容錯移轉進度。 您應該可以在 Azure 入口網站中看到測試複本機器。
1. 若要在虛擬機器上初始化 RDP 連線，您必須在容錯移轉的虛擬機器的網路介面上[新增公用 IP](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。 如果您要容錯移轉到傳統虛擬機器，您必須在連接埠 3389 上[新增端點](../virtual-machines/windows/classic/setup-endpoints.md)
1. 完成後，在復原方案上按一下 [清除測試容錯移轉]。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。 這會刪除在測試容錯移轉期間所建立的虛擬機器。


> [!TIP]
> Site Recovery 會嘗試在名稱相同的子網路中建立測試虛擬機器，並使用虛擬機器的 [計算與網路] 設定中提供的 IP。 如果針對測試容錯移轉提供的 Azure 虛擬網路中沒有名稱相同的子網路，則會在依字母順序的第一個子網路中建立測試虛擬機器。 如果目標 IP 是所選子網路的一部分，則 Site Recovery 會使用目標 IP 嘗試建立測試容錯移轉虛擬機器。 如果目標 IP 不屬於所選子網路的一部分，則會使用所選子網路中的任何可用 IP 建立測試容錯移轉虛擬機器。
>
>

## <a name="test-failover-job"></a>測試容錯移轉作業

![Test Failover](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

觸發測試容錯移轉時會執行下列步驟︰

1. 先決條件檢查︰這個步驟可確保符合容錯移轉所需的所有條件
1. 容錯移轉︰這個步驟會處理並備妥資料，以用來建立 Azure 虛擬機器。 如果您已選擇 [最新] 復原點，此步驟會從已傳送至服務的資料建立復原點。
1. 開始︰這個步驟會使用上一個步驟中所處理的資料建立 Azure 虛擬機器。

## <a name="time-taken-for-failover"></a>容錯移轉所花費的時間

在某些情況下，虛擬機器的容錯移轉會需要其他中繼步驟，通常會費時大約 8 到 10 分鐘才能完成。 這些情況如下所示︰

* VMware 虛擬機器使用的版本比行動服務版本 9.8 更早
* 實體伺服器
* VMware Linux 虛擬機器
* 如同實體伺服器般受到保護的 Hyper-V 虛擬機器
* 沒有以下驅動程式作為開機驅動程式的 VMware 虛擬機器
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 沒有啟用 DHCP 服務的 VMware 虛擬機器，無論其是否正在使用 DHCP 或靜態 IP 位址

在其他所有情況下則不需要此中繼步驟，且容錯移轉所花費的時間非常少。


## <a name="creating-a-network-for-test-failover"></a>建立測試容錯移轉的網路
當您進行測試容錯移轉時，建議您選擇與您在虛擬機器的 [計算與網路] 設定中提供的實際執行復原站台網路隔離的網路。 根據預設，當您建立 Azure 虛擬網路時，它會與其他網路隔離。 此網路應模擬您的實際執行網路︰

1. 測試網路應含有和您的實際執行網路相同的子網路數目，且名稱應和您的實際執行網路中的子網路相同。
1. 測試網路應使用和實際執行網路相同的 IP 範圍。
1. 將測試網路的 DNS 更新為您在 [計算與網路] 設定下提供做為 DNS 虛擬機器的目標 IP 的 IP。 如需詳細資訊，請參閱 [Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#test-failover-considerations) 一節。


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>在復原站台上測試容錯移轉到實際執行網路
當您進行測試容錯移轉時，建議您選擇與在虛擬機器的 [計算與網路] 設定中提供的實際執行復原站台網路不同的網路。 但是，如果您真的想驗證容錯移轉的虛擬機器中的端對端網路連線，請注意下列幾點︰

1. 進行測試容錯移轉時，請確定主要虛擬機器已關機。 如果不這麼做，相同的網路中將有兩部虛擬機器以相同的身分識別執行，因而可能造成不良的後果。
1. 當您清除測試容錯移轉虛擬機器時，您對測試容錯移轉虛擬機器所做的任何變更將會遺失。 這些變更不會複寫回主要虛擬機器。
1. 這種測試會導致實際執行應用程式的停機時間。 進行 DR 訓練時，應要求應用程式的使用者不要使用應用程式。  



## <a name="prepare-active-directory-and-dns"></a>準備 Active Directory 和 DNS
若要執行測試容錯移轉以進行應用程式測試，您需要在測試環境中具有 Active Directory 生產環境的複本。 如需詳細資訊，請參閱 [Active Directory 測試容錯移轉考量](site-recovery-active-directory.md#test-failover-considerations) 一節。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>準備在容錯移轉後連接到 Azure VM

如果您想要在容錯移轉後使用 RDP 連接到 Azure VM，請務必執行表格中摘要說明的動作。

**容錯移轉** | **位置** | **動作**
--- | --- | ---
**執行 Windows 的 Azure VM** | 在容錯移轉前的內部部署機器上 | 若要透過網際網路存取 Azure VM，請啟用 RDP，並確定已針對 [公用] 新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。<br/><br/> 若要透過站對站連線進行存取，請在機器上啟用 RDP，並確定在 [Windows 防火牆]  ->  [允許的應用程式和功能] 中已針對 [網域] 和 [私人] 網路允許 RDP。<br/><br/>  確定作業系統的 SAN 原則已設為 **OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。<br/><br/> 觸發容錯移轉時，請確定虛擬機器上沒有任何暫止的 Windows 更新。 容錯移轉時，可能會啟動 Windows 更新，必須等到更新完成，才能登入虛擬機器。 <br/><br/>
**執行 Windows 的 Azure VM** | 在容錯移轉後的 Azure VM 上 | 若是傳統虛擬機器，請為 RDP 通訊協定 (連接埠 3389) [新增公用端點](../virtual-machines/windows/classic/setup-endpoints.md)<br/><br/>  若是在 Resource Manager 虛擬機器上，請[新增公用 IP](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。<br/><br/> 已容錯移轉的 VM 上的網路安全性群組規則以及它所連線的 Azure 子網路必須允許 RDP 連接埠的連入連線。<br/><br/> 若是 Resource Manager 虛擬機器，您可以檢查 [開機診斷]，查看虛擬機器的螢幕擷取畫面<br/><br/> 如果您無法連線，請檢查 VM 是否正在執行，然後查看這些[疑難排解秘訣](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。<br/><br/>
**執行 Linux 的 Azure VM** | 在容錯移轉前的內部部署機器上 | 確定 Azure VM 上的安全殼層服務已設定為在系統開機時自動啟動。<br/><br/> 請檢查防火牆規則是否允許 SSH 連線。
**執行 Linux 的 Azure VM** | 容錯移轉後的 Azure VM | 已容錯移轉的 VM 上的網路安全性群組規則以及它所連接的 Azure 子網路必須允許 SSH 連接埠的連入連線。<br/><br/> 若是傳統虛擬機器，應建立[新增公用端點](../virtual-machines/windows/classic/setup-endpoints.md)，以允許 SSH 連接埠 (預設為 TCP 通訊埠 22) 上的連入連線。<br/><br/> 若是在 Resource Manager 虛擬機器上，請[新增公用 IP](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。<br/><br/> 若是 Resource Manager 虛擬機器，您可以檢查 [開機診斷]，查看虛擬機器的螢幕擷取畫面<br/><br/>



## <a name="next-steps"></a>後續步驟
一旦您成功嘗試測試容錯移轉之後，您就可以嘗試執行[容錯移轉](site-recovery-failover.md)。
