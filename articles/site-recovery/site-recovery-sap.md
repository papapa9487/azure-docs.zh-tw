---
title: "使用 Azure Site Recovery 保護多層式 SAP NetWeaver 應用程式部署 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 保護 SAP NetWeaver 應用程式部署"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>使用 Azure Site Recovery 保護多層式 SAP NetWeaver 應用程式部署

大部分的大型與中型 SAP 部署都有某種形式的災害復原方案。  隨著移至應用程式 (如 SAP) 的核心商務程序愈來愈多，強固且可測試的災害復原解決方案愈形重要。  Azure Site Recovery 已經過測試並與 SAP 應用程式整合，以比競爭解決方案低的總體擁有成本 (TCO)，超越大部分的內部部署災害復原解決方案。
使用 Azure Site Recovery，您可以：
* 藉由將元件複寫到 Azure，啟用在內部部署環境執行之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 藉由將元件複寫到另一個 Azure 資料中心，啟用執行 Azure 之 SAP NetWeaver 和非 NetWeaver 生產應用程式的保護功能。
* 使用 Site Recovery 將您的 SAP 部署移轉至 Azure，來簡化雲端移轉。
* 藉由建立隨選生產複本來測試 SAP 應用程式，簡化 SAP 專案升級、測試和原型設計。

本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 保護 SAP NetWeaver 應用程式部署。 本文涵蓋下列最佳做法︰使用 Azure Site Recovery 複寫到另一個 Azure 資料中心，以保護 Azure 上的三層式 SAP NetWeaver 部署，支援的案例和組態，以及如何執行容錯移轉 (包括測試容錯移轉 (災害復原演練) 和實際容錯移轉)。


## <a name="prerequisites"></a>必要條件
開始之前，請確定您瞭解下列項目︰

1. [將虛擬機器複寫至 Azure](azure-to-azure-walkthrough-enable-replication.md)
2. 如何[設計修復網路](site-recovery-azure-to-azure-networking-guidance.md)
3. [執行測試容錯移轉至 Azure](azure-to-azure-walkthrough-test-failover.md)
4. [執行容錯移轉至 Azure](site-recovery-failover.md)
5. 如何[複寫網域控制站](site-recovery-active-directory.md)
6. 如何[複寫 SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支援的案例
使用 Azure Site Recovery，您可以針對下列案例實作災害復原解決方案：
* 在一個 Azure 資料中心執行並複寫至另一個 Azure 資料中心 (Azure 對 Azure DR) 的 SAP 系統，其架構[在此](https://aka.ms/asr-a2a-architecture)。
* 在內部部署 VMWare (或實體) 伺服器上執行並複寫至 Azure 資料中心 DR 網站 (VMware-to-Azure DR) 的 SAP 系統，這需要一些額外的元件，其架構[在此](https://aka.ms/asr-v2a-architecture)。
* 在內部部署 Hyper-V 上執行並複寫至 Azure 資料中心 DR 網站 (Hyper-V-to-Azure DR) 的 SAP 系統，這需要一些額外的元件，其架構[在此](https://aka.ms/asr-h2a-architecture)。

本文件使用第一個案例 (Azure 對 Azure DR) 來示範 Azure Site Recovery 的 SAP 災害復原功能。 因為 Azure Site Recovery 複寫無從驗證應用程式，所以此處描述的程序應針對其他案例保留。

### <a name="required-foundation-services"></a>必要的基礎服務
此文件案例已部署下列基礎服務：
* ExpressRoute 或站對站虛擬私人網路 (VPN)
* 至少有一個 Active Directory 網域控制站和 DNS 伺服器在 Azure 中執行

建議在部署 Azure Site Recovery 之前建立上述基礎結構。


## <a name="typical-sap-application-deployment"></a>典型 SAP 應用程式部署
大型 SAP 客戶通常部署 6 到 20 個個別的 SAP 應用程式。  這些應用程式大部分是以 SAP NetWeaver ABAP 或 Java 引擎為基礎。  這些核心 NetWeaver 應用程式 (有些通常是非 SAP 應用程式) 是由許多小型特定非 NetWeaver SAP 獨立引擎支援。  

務必清查在環境中執行的所有 SAP 應用程式，並判斷部署模式 (2 層或 3 層)、版本、修補程式、大小、變換率，以及磁碟持續性需求。

![部署模式](./media/site-recovery-sap/sap-typical-deployment.png)

SAP 資料庫持續層應透過原生 DBMS 工具保護，例如 SQL Server AlwaysOn、Oracle DataGuard 或 HANA System Replication。 用戶端層也不受 Azure Site Recovery 保護，但務必考量會影響此層級的主題，例如 DNS 傳播延遲、安全性和 DR 資料中心的遠端存取。

Azure Site Recovery 是應用程式層的建議解決方案 (包括 (A)SCS)。 其他應用程式 (例如非 NetWeaver SAP 應用程式和非 SAP 應用程式) 形成整體 SAP 部署環境的一部分，也應該使用 Azure Site Recovery 保護。

## <a name="replicate-virtual-machines"></a>複寫虛擬機器
請依照[本指引](azure-to-azure-walkthrough-enable-replication.md)，開始將所有 SAP 應用程式虛擬機器複寫至 Azure DR 資料中心。

如果您使用靜態 IP，您可以在 [計算和網路] 設定的 [網路介面卡] 區段中指定您希望虛擬機器採用的 IP。

![目標 IP](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>建立復原計劃
復原計劃可讓您在多層式應用程式中排序各層的容錯移轉，因此可維護應用程式一致性。 建立多層式 web 應用程式的復原計畫時，請遵循[此處](site-recovery-create-recovery-plans.md)描述的步驟。

### <a name="adding-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
您可能需要在容錯移轉/測試容錯移轉後，於 Azure 虛擬機器上執行某些作業，讓您的應用程式正常運作。 您可以自動執行容錯移轉後置作業，例如更新 DNS 項目，以及變更網站繫結和連線 (如[本文](site-recovery-create-recovery-plans.md#add-scripts)所述在復原方案中新增對應的指令碼)。

### <a name="dns-update"></a>DNS 更新
如果已設定動態 DNS 更新的 DNS，則虛擬機器通常會在啟動之後使用新的 IP 更新 DNS。 如果您想要使用虛擬機器的新 IP 新增更新 DNS 的明確步驟，則請新增此[指令碼來更新 DNS 中的 IP](https://aka.ms/asr-dns-update) 做為復原方案群組上的後置動作。  

## <a name="example-azure-to-azure-deployment"></a>Azure 對 Azure 部署範例
下圖說明 Azure Site Recovery Azure 對 Azure DR 案例：
* 主要資料中心位於新加坡 (Azure 南東亞洲)，而在 DR 資料中心位於香港特別行政區 (Azure 東亞)。  在此案例中，擁有兩部在新加坡以同步模式執行 SQL Server AlwaysOn 的 VM，即可提供本機高可用性。
* 檔案共用 ASCS 可用來為 SAP 單一失敗點提供高可用性。 檔案共用 ASCS 不需要叢集共用磁碟，而且不需要 SIOS 等應用程式。
* DBMS 層的 DR 保護是利用非同步複寫達成。
* 此案例顯示「對稱式 DR」，該詞彙用來描述屬於確切生產複本的 DR 解決方案，因此 DR SQL Server 解決方案可擁有本機高可用性。 資料庫層不強制使用對稱式 DR，而且許多客戶會利用雲端部署的彈性，在 DR 事件之後快速建置本機高可用性節點。
* 本圖說明 Azure Site Recovery 所複寫的 SAP NetWeaver ASCS 和應用程式伺服器層。

![複寫案例](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>執行測試容錯移轉
請依照[本指引](azure-to-azure-walkthrough-test-failover.md)來執行測試容錯移轉。

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
2.  按一下為 SAP 應用程式建立的復原方案。
3.  按一下 [測試容錯移轉]。
4.  選取復原點和 Azure 虛擬網路來開始測試容錯移轉程序。
5.  次要環境啟動後，您就可以執行您的驗證。
6.  完成驗證後，按一下 [清除測試容錯移轉] 以清除容錯移轉環境。

## <a name="doing-a-failover"></a>執行容錯移轉
當您在進行容錯移轉時，請依照[本指引](site-recovery-failover.md)。

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
2.  按一下為 SAP 應用程式建立的復原方案。
3.  按一下 [容錯移轉]。
4.  選取復原點以啟動容錯移轉程序。

## <a name="next-steps"></a>後續步驟
請參閱[本白皮書](http://aka.ms/asr-sap)，了解如何使用 Azure Site Recovery 為 SAP NetWeaver 部署建置災害復原解決方案。 本白皮書也會討論不同 SAP 架構的建議，列出支援 Azure 上的 SAP 的應用程式和 VM 類型，並說明災害復原解決方案可行的測試方案。

深入了解如何使用 Site Recovery [複寫其他工作負載](site-recovery-workload.md)。

