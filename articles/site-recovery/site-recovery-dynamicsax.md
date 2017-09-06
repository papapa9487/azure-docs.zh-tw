---
title: "使用 Azure Site Recovery 複寫多層式 Dynamics AX 部署 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 複寫和保護 Dynamics AX"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 03127c8f4841b67436c4819628319705af0b2cd5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>使用 Azure Site Recovery 複寫多層式 Dynamics AX 應用程式

## <a name="overview"></a>概觀


Microsoft Dynamics AX 是企業間受歡迎的 ERP 解決方案之一，可橫跨位置將程序標準化、管理資源及簡化合規性。 考量應用程式攸關組織的業務運作，請務必確定在發生任何災害時，應用程式都能在最短的時間內啟動並執行。

現今，Microsoft Dynamics AX 並未提供任何現成的災害復原功能。 Microsoft Dynamics AX 包含許多伺服器元件，例如 Application Object Server、Active Directory (AD)、SQL Database Server、SharePoint Server、Reporting Server 等。手動管理上述每個元件的災害復原，不僅代價昂貴，而且也容易發生錯誤。

本文詳細說明有關如何使用 [Azure Site Recovery](site-recovery-overview.md) 為 Dynamics AX 應用程式建立災害復原解決方案。 也會探討使用單鍵復原方案、支援的組態和必要條件的計劃性/非計劃性/測試容錯移轉。
以 Azure Site Recovery 為基礎的災害復原解決方案已經過完整測試、認證並由 Microsoft Dynamics AX 建議。



## <a name="prerequisites"></a>必要條件

您需要完成下列必要條件，才能使用 Azure Site Recovery 實作 Dynamics AX 應用程式的災害復原。

•   已設定內部部署 Dynamics AX 部署

•   已在 Microsoft Azure 訂用帳戶中建立 Azure Site Recovery 服務保存庫

•   如果 Azure 是您的復原網站，請在 VM 上執行 Azure 虛擬機器整備評估工具，以確保相容於 Azure VM 與 Azure Site Recovery 服務


## <a name="site-recovery-support"></a>Site Recovery 支援

為建立這篇文章，會使用 Windows Server 2012 R2 Enterprise 上的 VMware 虛擬機器與 Dynamics AX 2012R3。 因為站台復原複寫應用程式無從驗證，此處提供的建議也都必須保留下列案例。

### <a name="source-and-target"></a>來源與目標

**案例** | **至次要網站** | **至 Azure**
--- | --- | ---
**Hyper-V** | 是 | 是
**VMware** | 是 | 是
**實體伺服器** | 是 | 是

## <a name="enable-dr-of-dynamics-ax-application-using-azure-site-recovery"></a>使用 Azure Site Recovery 啟用 Dynamics AX 應用程式的 DR
### <a name="protect-your-dynamics-ax-application"></a>保護 Dynamics AX 應用程式
Dynamics AX 的每個元件都需要受到保護，才能做到完整的應用程式複寫與復原。 本節涵蓋︰

**1.Active Directory 的保護**

**2.SQL 層的保護**

**3.應用程式和 Web 層的保護**

**4.網路設定**

**5.復原方案**

### <a name="1-setup-ad-and-dns-replication"></a>1.安裝 AD 和 DNS 複寫

DR 網站上需要有 Active Directory，Dynamics AX 應用程式才能運作。 根據客戶內部部署環境的複雜度而定，有兩個建議的選擇。

**選項 1**

如果客戶的整個內部部署網站有少數的應用程式和單一網域控制站，而且將會一起容錯移轉整個網站，則我們建議使用 ASR 複寫將 DC 電腦複寫至次要網站 (適用於網站對網站和網站對 Azure)。

**選項 2**

如果客戶有大量應用程式、正在執行 Active Directory 樹系，並將一次容錯移轉少數應用程式，則我們建議在 DR 網站 (次要網站或 Azure 中) 另外設定一個網域控制站。

請參閱[在 DR 網站上提供網域控制站的附屬指南](site-recovery-active-directory.md)。 對於本文件的其餘部分，我們會假設 DR 網站上有 DC 可用。

### <a name="2-setup-sql-server-replication"></a>2.設定 SQL Server 複寫
請參閱附屬指南，以取得建議用於保護 [SQL 層](site-recovery-sql.md)之選項的詳細技術指引。

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3.啟用 Dynamics AX 用戶端和 AOS VM 的保護
根據 VM 是部署於 [Hyper-V](site-recovery-hyper-v-site-to-azure.md) 還是 [VMware](site-recovery-vmware-to-azure.md)，執行相關的 Azure Site Recovery 設定。

> [!TIP]
> 要設定為 15 分鐘的建議損毀一致頻率。
>

下列快照集顯示 Dynamics 元件 VM 在「從 VMware 站台到 Azure」保護案例中的保護狀態。
![受保護的項目](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4.設定網路功能
設定 VM 計算和網路設定

對於 AX 用戶端和 AOS VM，設定 Azure Site Recovery 中的網路設定，讓 VM 網路能在容錯移轉之後連結到正確的 DR 網路。 確保這些層的 DR 網路可路由傳送到 SQL 層。

您可以在複寫的項目中選取 VM 來進行網路設定，如以下快照集所示。

* 對於 AOS 伺服器，選取正確的可用性設定組。

* 如果您是使用靜態 IP 位址，請在![網路設定](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)的 [目標 IP] 欄位中指定您想要虛擬機器採用的 IP。



### <a name="5-creating-a-recovery-plan"></a>5.建立復原計劃

您可以在 Azure Site Recovery 中建立復原方案，將容錯移轉程序自動化。 在復原方案中新增應用程式層和 Web 層。 在不同群組中將它們排序，讓前端關機出現在應用程式層之前。

1)  在您的訂用帳戶中選取 Azure Site Recovery 保存庫，然後按一下 [復原方案] 圖格。

2)  按一下 [+ 復原方案] 並指定名稱。

3)  選取 [來源] 和 [目標]。 目標可以是 Azure 或次要網站。 如果您選擇 Azure，則必須指定部署模型

![建立復原方案](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  選取復原方案的 AOS 和用戶端 VM，然後按一下 ✓。
![建立復原方案](./media/site-recovery-dynamics-ax/selectvms.png)


![復原方案](./media/site-recovery-dynamics-ax/recoveryplan.png)

新增如下所述的各種步驟，即可自訂 Dynamics AX 應用程式的復原方案。 以上的快照集顯示新增所有步驟之後的完整復原方案。

*步驟：*

*1.SQL Server 容錯移轉步驟*

請參閱 [SQL Server DR 解決方案](site-recovery-sql.md)附屬指南，以了解 SQL Server 在容錯回復期間的特定考量。

*2.容錯移轉群組 1︰容錯移轉 AOS VM*

確定所選取的復原點儘可能接近資料庫 PIT，但不為繼續進行。

*3.指令碼︰新增負載平衡器 (僅限E-A)* 在 AOS VM 群組開始新增負載平衡器之後，新增指令碼 (透過 Azure 自動化)。 您可以使用指令碼來執行此工作。 請參閱[如何為多層式應用程式 DR 新增負載平衡器](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

*4.容錯移轉群組 2︰容錯移轉 AX 用戶端 VM。*
容錯移轉 Web 層 VM 作為復原方案的一部份。


### <a name="doing-a-test-failover"></a>執行測試容錯移轉

請參閱「AD DR 解決方案」和「SQL Server DR 解決方案」附屬指南，以了解 AD 和 SQL Server 在容錯回復期間各自的特定考量。

1.  移至 Azure 入口網站並選取您的 Site Recovery 保存庫。
2.  按一下為 Dynamics AX 建立的復原方案。
3.  按一下 [測試容錯移轉]。
4.  選取虛擬網路來開始測試容錯移轉程序。
5.  次要環境啟動後，您就可以執行您的驗證。
6.  驗證完成後，您可以選取 [完成驗證]，並會清除測試容錯移轉環境。

請依照[本指引](site-recovery-test-failover-to-azure.md)來執行測試容錯移轉。

### <a name="doing-a-failover"></a>執行容錯移轉

1.  移至 Azure 入口網站並選取您的 Site Recovery 保存庫。
2.  按一下為 Dynamics AX 建立的復原方案。
3.  按一下 [容錯移轉]，然後選取 [容錯移轉]。
4.  選取目標網路，然後按一下 ✓ 啟動容錯移轉程序。

當您在進行容錯移轉時，請依照[本指引](site-recovery-failover.md)。

### <a name="perform-a-failback"></a>執行容錯回復

請參閱「SQL Server DR 解決方案」附屬指南，以了解 SQL Server 在容錯回復期間的特定考量。

1.  移至 Azure 入口網站並選取您的 Site Recovery 保存庫。
2.  按一下為 Dynamics AX 建立的復原方案。
3.  按一下 [容錯移轉]，然後選取 [容錯移轉]。
4.  按一下 [變更方向]。
5.  選取適當的選項 - 資料同步處理和 VM 建立選項
6.  按一下 ✓ 啟動 [容錯回復] 程序。


當您在進行容錯回復時，請依照[本指引](site-recovery-failback-azure-to-vmware.md)。

##<a name="summary"></a>摘要
使用 Azure Site Recovery，您可以為 Dynamics AX 應用程式建立一個完整的自動化災害復原方案。 當發生中斷時，您可以在幾秒鐘內從任何地方起始容錯移轉，並且在數分鐘內啟動並執行應用程式。

## <a name="next-steps"></a>後續步驟
閱讀[我可以保護哪些工作負載?](site-recovery-workload.md)，深入了解如何以 Azure Site Recovery 保護企業工作負載。

