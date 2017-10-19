---
title: "Azure Windows 虛擬機器上的 SQL Server 概觀 | Microsoft Docs"
description: "深入了解如何在 Azure Windows 虛擬機器上執行完整的 SQL Server 版本。 取得所有 SQL Server VM 映像和相關內容的直接連結。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: b10c995fdd8e241d354c62537a0600b393795c1b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-windows"></a>Azure 虛擬機器上的 SQL Server 概觀 (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

本主題說明在 Azure Windows 虛擬機器 (VM) 上執行 SQL Server 的選項，以及提供[入口網站映像的連結](#option-1-create-a-sql-vm-with-per-minute-licensing)和[常見工作](#manage-your-sql-vm)的概觀。

> [!NOTE]
> 如果您已熟悉 SQL Server，並只需要了解如何部署 SQL Server Windows VM，請參閱[在 Azure 中佈建 Windows SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)。 或者如果您需要使用 SQL Server 建立 Linux VM，請參閱[在 Azure 中佈建 Linux SQL Server VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

如果您是資料庫管理員或開發人員，Azure VM 提供您將內部部署 SQL Server 工作負載和應用程式移動到雲端的方式。

## <a name="scenarios"></a>案例
可能會有許多原因讓您選擇主控 Azure 中的資料。 如果您的應用程式移至 Azure，則也會改善移動資料的效能。 但是還有其他好處。 您會自動取得多個資料中心的存取權，以獲得全域支援和災害復原。 資料也是高度安全且具有持久性。

在 Azure VM 上執行的 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。 它在數個案例中是很好的選擇。 例如，您可能要盡可能以類似方法將 Azure VM 設定為內部部署 SQL Server 的電腦。 或者，您可能要在相同的資料庫伺服器上執行其他應用程式和服務。 有兩個主要資源可協助您思考更多的案例和考量︰

* [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 提供在 Azure VM 上使用 SQL Server 的最佳案例概觀。 
* [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) 可提供 SQL Database 和在 VM 上執行之 SQL Server 的完整比較。

## <a name="create-a-new-sql-vm"></a>建立新的 SQL VM
下列各節提供 Azure 入口網站中 SQL Server 虛擬機器資源庫映像的直接連結。 視您選取的映像而定，您可以按分鐘支付 SQL Server 授權成本，或者可以自備授權 (BYOL)。

在[在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)教學課程中尋找適用於建立新 SQL VM 的逐步指引。 此外，檢閱 [SQL Server VM 的效能最佳作法](virtual-machines-windows-sql-performance.md)，該文說明如何選取適當的機器大小和佈建期間其他可用的功能。

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>選項 1︰利用每分鐘授權建立 SQL VM
下表提供虛擬機器資源庫中最新的 SQL Server 映像對照表。 按一下任何連結，即可開始建立採用您指定的版本和作業系統的新 SQL VM。 

> [!TIP]
> 若要了解這些映像的 VM 和 SQL 定價，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

> [!NOTE]
> 若要查看可用的 Linux SQL Server 虛擬機器映像，請參閱 [Azure 虛擬機器 (Linux) 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)。

## <a id="BYOL"></a> 選項 2︰利用現有授權建立 SQL VM
您也可以自備授權 (BYOL)。 在此案例中，您只需支付 VM 費用，而 SQL Server 授權不需任何額外的費用。 若要使用自己的授權，請使用 SQL Server 版本及下列作業系統的矩陣。 在入口網站中，這些映像名稱前面會加上 **{BYOL}**。

> [!TIP]
> 長期下來，自備授權可以讓您節省連續生產工作負載的成本。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

除了這份清單，還有其他的 SQL Server 版本和作業系統組合可用。 在 Azure 入口網站中透過市集搜尋來尋找其他映像 (搜尋 "{BYOL} SQL Server")。

> [!IMPORTANT]
> 若要使用 BYOL VM 映像，您必須具備 Enterprise 合約和 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 您也需要您要使用的 SQL Server 版本的有效授權。 您必須在佈建您的 VM 的 [10](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) 天內 **提供必要的 BYOL 資訊給 Microsoft** 。 

> [!NOTE]
> 不可能將支付 SQL Server VM 的每分鐘付費授權模式變更成使用自己的授權。 在此情況下，您必須建立新的 BYOL VM，並將資料庫移轉到新的 VM。 

## <a name="manage-your-sql-vm"></a>管理您的 SQL VM
佈建之後您的 SQL Server VM 之後，有幾個選用的管理工作。 在許多方面，您設定和管理 SQL Server 的方式，完全如同您管理內部部署 SQL Server 執行個體。 不過，有些是 Azure 特有的工作。 下列各節強調上述某些領域並提供詳細資訊的連結。

### <a name="connect-to-the-vm"></a>連接至 VM
最基本的管理步驟之一，是透過工具連接到您的 SQL Server VM，例如 SQL Server Management Studio (SSMS)。 如需如何連接至新 SQL Server VM 的指示，請參閱[連接 Azure 上的 SQL Server 虛擬機器](virtual-machines-windows-sql-connect.md)。

### <a name="migrate-your-data"></a>遷移資料
如果您有現有的資料庫，您會想要將該資料庫移到新佈建的 SQL VM。 如需移轉選項的清單和指引，請參閱 [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

### <a name="configure-high-availability"></a>設定高可用性
如果您需要高可用性，請考慮設定 SQL Server 可用性群組。 這牽涉到虛擬網路中多個 Azure VM。 Azure 入口網站有一個範本，己經有您需要的設定。 如需詳細資訊，請參閱 [在 Azure Resource Manager 虛擬機器中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果您想要手動設定「可用性群組」及關聯的接聽程式，請參閱 [在 Azure VM 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

如需其他高可用性注意事項，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

### <a name="back-up-your-data"></a>備份您的資料
Azure VM 可以利用 [自動備份](virtual-machines-windows-sql-automated-backup.md)，以定期建立資料庫的備份至 Blob 儲存體。 您也可以手動使用此技術。 如需詳細資訊，請參閱 [使用 Azure 儲存體進行 SQL Server 備份與還原](virtual-machines-windows-use-storage-sql-server-backup-restore.md)。 如需所有備份與還原選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-windows-sql-backup-recovery.md)。

### <a name="automate-updates"></a>自動更新
Azure VM 可以使用 [自動修補](virtual-machines-windows-sql-automated-patching.md) 來排程維護期間，以便自動安裝重要的 Windows 和 SQL Server 更新。

### <a name="customer-experience-improvement-program-ceip"></a>客戶經驗改進計畫 (CEIP)
預設會啟用「客戶經驗改進計畫」(CEIP)。 這會定期將報表傳送至 Microsoft 以協助改進 SQL Server。 CEIP 不需要管理工作，除非您要在佈建之後將它停用。 您可以透過遠端桌面連接到 VM，以自訂或停用 CEIP。 然後執行 [SQL Server 錯誤和使用情況報告]  公用程式。 請遵循指示來停用報告功能。 

如需有關資料收集的詳細資訊，請參閱 [SQL Server 隱私權聲明](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx)。 

## <a name="next-steps"></a>後續步驟

若有定價方面的問題，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)和 [Azure 定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。 在 **OS/軟體**清單中選取 SQL Server 的目標版本。 然後檢視不同大小的虛擬機器價格。

其他問題？ 請先參閱 [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。 也請將您的問題或意見加入任何 SQL VM 主題的底部來與 Microsoft 和社群互動。
