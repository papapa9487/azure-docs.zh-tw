---
title: "使用 Azure Site Recovery 保護檔案伺服器"
description: "本文說明如何使用 Azure Site Recovery 保護檔案伺服器"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 8c9d8dadcd6181d9894ab6ee7110841afdec5708
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>使用 Azure Site Recovery 保護檔案伺服器 

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括各種工作負載的複寫、容錯移轉和復原。

本文說明如何使用 Azure Site Recovery 保護檔案伺服器，以及因應不同環境的其他建議。     

- [複寫 Azure IaaS 檔案伺服器機器](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [使用 Azure Site Recovery 複寫內部部署檔案伺服器](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>檔案伺服器架構
開放分散式檔案共用系統的目的是要提供一種環境，讓一群地理位置分散的使用者可以共同作業以有效率地處理檔案，而且保證能夠強制達到檔案完整性的需求。 典型的內部部署檔案伺服器生態系統，支援大量並行使用者和大量內容項目，使用分散式檔案系統複寫 (DFSR) 來進行複寫排程和頻寬節流。 DFSR 使用稱為「遠端差異壓縮 (RDC)」的壓縮演算法，可用來在頻寬有限的網路上有效率地更新檔案。 此演算法會偵測檔案中的資料發生插入、移除與重新排列，當檔案更新時，讓 DFSR 只複寫變更的檔案區塊。 另外還有檔案伺服器環境，在非尖峰時間才進行每日備份，可滿足災害需求且不實作 DFSR。

下列拓撲說明實作 DFSR 的檔案伺服器環境。
                
![dfsr 架構](media/site-recovery-file-server/dfsr-architecture.JPG)

在上面的參考中，多個檔案伺服器是成員，主動參與複寫整個複寫群組中的檔案。 即使其中一個成員離線，向其中一個成員傳送要求的所有用戶端還是可以取得複寫資料夾的內容。

## <a name="disaster-recovery-recommendation-for-file-servers"></a>檔案伺服器的災害復原建議：

1.  使用 Azure Site Recovery 複寫檔案伺服器：使用 Azure Site Recovery 可將檔案伺服器複寫至 Azure。 當無法存取一或多個內部部署的檔案伺服器時，可以在 Azure 中執行復原 VM，只要有站對站的 VPN 連線能力，且在 Azure 中設定了 Active Directory，就可以處理內部部署用戶端的要求。 這在設定了 DFSR 的環境或沒有 DFSR 的簡單檔案伺服器環境中就可以進行。 

2.  將 DFSR 延伸至 Azure Iaas VM：在實作 DFSR 的叢集檔案伺服器環境中，其中一個建議的方法是將內部部署 DFSR 延伸至 Azure。 然後啟用 Azure 虛擬機器以執行檔案伺服器角色。 

    一旦處理好站對站 VPN 連線能力與 Active Directory 的相依性，且 DFSR 已準備就緒，當一或多個內部部署的檔案伺服器無法存取時，用戶端仍可連線到可處理要求的 Azure VM。

    如果您的 VM 具有 Azure Site Recovery 不支援的設定 (例如：在檔案伺服器環境中有時常用的共用叢集磁碟)，建議使用此方法。  DFSR 也適用於具中等變換率的低頻寬環境。 但是，這也必須負擔讓 Azure VM 隨時啟動並執行的額外成本。  

3.  使用 Azure 檔案同步服務複寫您的檔案：如果您正準備轉移到雲端，或已經在使用 Azure VM，我們建議使用 Azure 檔案同步服務，此服務可同步雲端中完全受管理的檔案共用 (可透過業界標準的[伺服器訊息區](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) \(英文\) (SMB) 通訊協定存取)。 然後 Windows、Linux 和 macOS 的雲端部署或內部部署就可同時掛接 Azure 檔案共用。 

下圖提供的圖解，可讓您在決定為檔案伺服器環境使用哪種策略時更輕鬆。

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>進行災害復原至 Azure 的決策時所要考量的因素

|Environment  |建議  |考慮事項 |
|---------|---------|---------|
|有/沒有 DFSR 的檔案伺服器環境|   [使用 Azure Site Recovery 進行複寫](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery 不支援共用的磁碟叢集 NAS。 如果您的環境使用任何一種設定，請在適當時使用任何其他方法。 <br> Azure Site Recovery 不支援 SMB 3.0，這表示只有當對檔案所做的變更已在檔案的原始位置中更新時，複寫的 VM 才會納入變更。
|有 DFSR 的檔案伺服器環境     |  [將 DFSR 延伸到 Azure IaaS 虛擬機器：](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR 在頻寬極端變換的環境中運作得很好，不過，這個方法需要 Azure VM 隨時啟動並執行。 您的計劃必須計算 VM 的成本。         |
|Azure IaaS VM     |     [Azure 檔案同步](#use-azure-file-sync-service-to-replicate-your-files)   |     在使用 Azure 檔案同步的災害復原案例中，在容錯移轉期間，需要採取一些手動動作，以確保用戶端電腦可以透明的方式存取檔案共用。 AFS 需要能夠從用戶端機器開啟通訊埠 445。     |


### <a name="site-recovery-support"></a>Site Recovery 支援
雖然 Site Recovery 複寫無法以應用程式驗證，但是此處提供的建議應該也適用於下列案例：
| 來源    |至次要網站    |至 Azure
|---------|---------|---------|
|Azure| -|是|
|Hyper-V|   是 |是
|VMware |是|   是
|實體伺服器|   是 |是
 

> [!IMPORTANT]
> 在您進行以下三種方法的任何一種之前，請先確定已處理下列相依性：

**站對站連線能力**：必須在內部部署網站與 Azure 網路之間建立直接連線，以允許伺服器之間的通訊。  只要採用安全的站對站 VPN 連線，連線到將用為災害復原網站的 Microsoft Azure 虛擬網路，就可以確保能夠達成。  
請參閱：[在內部部署網站與 Azure 網路之間建立站對站 VPN 連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory**：DFSR 取決於 Active Directory。  這表示，將具本機網域控制站的 Active Directory 樹系延伸到 Azure 中的災害復原網站。 即使您未使用 DFSR，如果需要授與/驗證預定使用者的存取權 (就像大部分的組織一樣)，就必須執行這些步驟。
請參閱：[將內部部署 Active Directory 延伸至 Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory)。

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 虛擬機器的災害復原建議

如果您要設定及管理在 Azure IaaS VM 上裝載之檔案伺服器的災害復原，則可以根據是否要移至 [Azure 檔案](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)，在兩個選項當中作選擇。

1. [使用 Azure 檔案同步](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [使用 Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>使用 Azure 檔案同步服務來複寫裝載在 IaaS 虛擬機器上的檔案

**Azure 檔案**服務可用來完全取代或補充傳統內部部署檔案伺服器或 NAS 裝置。 透過 Azure 檔案同步也可以將 Azure 檔案共用複寫到 Windows Server (在內部部署環境或雲端)，以便在資料的使用位置進行高效能和分散式快取。 下列步驟詳細說明與傳統檔案伺服器執行相同功能的 Azure VM 的災害復原建議：
1.  透過[這裡](azure-to-azure-quickstart.md)所說明的步驟使用 Azure Site Recovery 保護機器。
2.  使用 Azure 檔案同步將作為檔案伺服器的 VM 的檔案複寫到雲端。
3.  使用 Azure Site Recovery 的[復原方案](site-recovery-create-recovery-plans.md)功能新增指令碼，以[掛接 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)並存取虛擬機器中的共用。

下列步驟簡單說明如何使用 Azure 檔案同步服務：

1. [在 Azure 中建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 如果您為儲存體帳戶選擇了「讀取權限異地備援儲存體 (RA-GRS)」，則會獲得在發生災害時從次要地區讀取資料的權限。 請參閱 [Azure 檔案共用災害復原策略](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)以了解進一步資訊。
2. [建立檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)。
3. 在 Azure 檔案伺服器上[部署 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide)。
4. 建立同步群組：同步群組內的端點會彼此保持同步。 同步群組必須包含至少一個雲端端點 (代表 Azure 檔案共用) 和一個伺服器端點 (代表 Windows Server 上的路徑)。
5.  您的檔案將會在 Azure 檔案共用和內部部署伺服器之間保持同步。
6.  您的內部部署環境中萬一發生災害，請使用[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉並新增指令碼，以[掛接 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)並存取虛擬機器中的共用。

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>使用 Azure Site Recovery 複寫 IaaS 檔案伺服器虛擬機器

如果您有內部部署用戶端存取 IaaS 檔案伺服器虛擬機器，請一併執行前 2 個步驟，否則繼續進行步驟 3。

1. 在內部部署網站與 Azure 網路之間建立站對站 VPN 連線。
1. 延伸內部部署 Active Directory。
1. 為 IaaS 檔案伺服器機器[設定災害復原](azure-to-azure-tutorial-enable-replication.md)到次要地區。


如需有關復原到次要地區的災害復原詳細資訊，請參閱[這裡](concepts-azure-to-azure-architecture.md)。


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>使用 Azure Site Recovery 複寫內部部署檔案伺服器

下列步驟詳細說明 VMware VM 的複寫，如需複寫 Hyper-V VM 的步驟，請參閱[這裡](tutorial-hyper-v-to-azure.md)。

1.  [準備 Azure 資源](tutorial-prepare-azure.md)以進行內部部署機器的複寫。
2.  在內部部署網站與 Azure 網路之間建立站對站 VPN 連線。  
3. 延伸內部部署 Active Directory。
4.  [準備內部部署 VMware 伺服器](tutorial-prepare-on-premises-vmware.md)。
5.  為內部部署 VM [設定災害復原](tutorial-vmware-to-azure.md)到 Azure。

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>將 DFSR 延伸到 Azure IaaS 虛擬機器：

1.  在內部部署網站與 Azure 網路之間建立站對站 VPN 連線。 
2.  延伸內部部署 Active Directory。
3.  在 Microsoft Azure 虛擬網路上[建立及佈建檔案伺服器 VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)。
確認虛擬機器已新增到相同的 Microsoft Azure 虛擬網路，其具備與內部部署環境交叉連線的能力。 
4.  在 Windows Server 上安裝和[設定 DFS 複寫](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) \(英文\)。
5.  [實作 DFS 命名空間](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces)。
6.  若實作 DFS 命名空間，只要更新 DFS 命名空間資料夾目標，就可以將生產網站的共用資料夾容錯移轉至災害復原網站。  一旦透過 Active Directory 複寫這些 DFS 命名空間的變更，使用者就會以透明的方式連線到適當的資料夾目標。

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>使用 Azure 檔案同步服務複寫內部部署檔案：
使用 Azure 檔案同步服務，您可以將所需的檔案複寫到雲端，當發生災害且無法使用您的內部部署檔案伺服器時，您就可以掛接雲端上想要的檔案位置，然後繼續服務用戶端機器的要求。
整合 Azure 檔案同步與 Azure Site Recovery 的建議方式是
1.  使用[這裡](tutorial-vmware-to-azure.md)所說明的步驟使用 Azure Site Recovery 保護檔案伺服器機器。
2.  使用 Azure 檔案同步將作為檔案伺服器的機器檔案複寫到雲端。
3.  使用 Azure Site Recovery 的復原方案功能新增指令碼，以在 Azure 中已容錯移轉的檔案伺服器 VM 上掛接 Azure 檔案共用。

下列步驟詳細說明如何使用 Azure 檔案同步服務：

1. [在 Azure 中建立儲存體帳戶](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 如果您為儲存體帳戶選擇了「讀取權限異地備援儲存體 (RA-GRS)」(建議)，則會有在發生災害時從次要地區讀取資料的權限。 請參閱 [Azure 檔案共用災害復原策略](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)以了解進一步資訊。
2. [建立檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)。
3. 在您在內部部署檔案伺服器中[部署 Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide)。
4. 建立同步群組：同步群組內的端點會彼此保持同步。 同步群組必須至少包含一個雲端端點 (代表 Azure 檔案共用) 和一個伺服器端點 (代表內部部署 Windows Server 上的路徑)。
1. 您的檔案將會在 Azure 檔案共用和內部部署伺服器之間保持同步。
6.  您的內部部署環境中萬一發生災害，請使用[復原方案](site-recovery-create-recovery-plans.md)執行容錯移轉並新增指令碼，以掛接 Azure 檔案共用並存取虛擬機器中的共用。

> [!NOTE]
> 確定已開啟連接埠 445：Azure 檔案服務使用 SMB 通訊協定。 SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。


## <a name="doing-a-test-failover"></a>執行測試容錯移轉

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
2.  按一下為檔案伺服器環境建立的復原方案。
3.  按一下 [測試容錯移轉]。
4.  選取復原點和 Azure 虛擬網路來開始測試容錯移轉程序。
5.  次要環境啟動後，您就可以執行您的驗證。
6.  完成驗證後，您可以按一下復原方案上的 [清除測試容錯移轉]，而測試容錯移轉環境會清除。

如需有關執行測試容錯移轉的詳細資訊，請參閱[這裡](site-recovery-test-failover-to-azure.md)。

如需有關為 AD 和 DNS 進行測試容錯移轉的指引，請參閱 [AD 和 DNS 的測試容錯移轉考量](site-recovery-active-directory.md)。

## <a name="doing-a-failover"></a>執行容錯移轉

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
2.  按一下為檔案伺服器環境建立的復原方案。
3.  按一下 [容錯移轉]。
4.  選取復原點以啟動容錯移轉程序。

如需有關執行容錯移轉的詳細資訊，請參閱[這裡](site-recovery-failover.md)。
