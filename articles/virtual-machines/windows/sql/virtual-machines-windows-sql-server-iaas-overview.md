---
title: "Azure Windows 虛擬機器上的 SQL Server 概觀 | Microsoft Docs"
description: "了解如何在 Azure 虛擬機器上執行完整的 SQL Server 版本。"
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
ms.date: 12/02/2017
ms.author: jroth
ms.openlocfilehash: 36fa884b451d41ed7443312cc21b49b0b720a954
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure 虛擬機器上的 SQL Server 是什麼？ (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 可讓您在雲端使用完整的 SQL Server 版本，而不需要管理任何內部部署硬體。 當您使用預付型方案時，SQL Server VM 也會簡化授權成本。

Azure 虛擬機器會在全球許多不同的[地理區域](https://azure.microsoft.com/regions/)中執行。 此外，也提供各種[機器大小](../sizes.md)。 虛擬機器映像庫可讓您使用正確的版本、版次及作業系統建立 SQL Server VM。 這可讓虛擬機器成為許多不同 SQL Server 工作負載的適合選項。

## <a name="automated-updates"></a>自動化更新

SQL Server Azure VM 可以使用 [自動修補](virtual-machines-windows-sql-automated-patching.md) 來排程維護期間，以便自動安裝重要的 Windows 和 SQL Server 更新。

## <a name="automated-backups"></a>自動備份

SQL Server Azure VM 可以利用 [自動備份](virtual-machines-windows-sql-automated-backup-v2.md)，以定期建立資料庫的備份至 Blob 儲存體。 您也可以手動使用此技術。 如需詳細資訊，請參閱 [使用 Azure 儲存體進行 SQL Server 備份與還原](virtual-machines-windows-use-storage-sql-server-backup-restore.md)。

## <a name="high-availability"></a>高可用性

如果您需要高可用性，請考慮設定 SQL Server 可用性群組。 這牽涉到虛擬網路中多個 SQL Server Azure VM。 您可以手動設定高可用性解決方案，也可以在 Azure 入口網站使用範本進行自動設定。 如需所有高可用性選項的概觀，請參閱 [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="get-started-with-sql-vms"></a>開始使用 SQL VM

若要開始使用，請選擇具有您所需版本、版次及作業系統的 SQL Server 虛擬機器映像。 下列各節提供 Azure 入口網站中 SQL Server 虛擬機器資源庫映像的直接連結。

> [!TIP]
> 若要了解這些映像的 VM 和 SQL 定價，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

### <a id="payasyougo"></a> 預付型方案
下表提供預付型 SQL Server 映像的對照表。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

若要查看可用的 Linux SQL Server 虛擬機器映像，請參閱 [Azure 虛擬機器 (Linux) 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)。

### <a id="BYOL"></a> 自備授權
您也可以自備授權 (BYOL)。 在此案例中，您只需支付 VM 費用，而 SQL Server 授權不需任何額外的費用。  長期下來，自備授權可以讓您節省連續生產工作負載的成本。 如需使用此選項的相關需求，請參閱 [SQL Server Azure VM 的價格指引](virtual-machines-windows-sql-server-pricing-guidance.md)。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

在入口網站中，這些映像名稱前面會加上 **{BYOL}**。

### <a name="connect-to-the-vm"></a>連接至 VM
建立 SQL Server VM 之後，從 SQL Server Management Studio (SSMS) 等應用程式或工具來連線該 VM。 如需相關只是，請參閱[連線到 Azure 上的 SQL Server 虛擬機器](virtual-machines-windows-sql-connect.md)。

### <a name="migrate-your-data"></a>遷移資料
如果您有現有的資料庫，您會想要將該資料庫移到新佈建的 SQL VM。 如需移轉選項的清單和指引，請參閱 [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="customer-experience-improvement-program-ceip"></a>客戶經驗改進計畫 (CEIP)
預設會啟用「客戶經驗改進計畫」(CEIP)。 這會定期將報表傳送至 Microsoft 以協助改進 SQL Server。 CEIP 不需要管理工作，除非您要在佈建之後將它停用。 您可以透過遠端桌面連接到 VM，以自訂或停用 CEIP。 然後執行 [SQL Server 錯誤和使用情況報告]  公用程式。 請遵循指示來停用報告功能。 如需有關資料收集的詳細資訊，請參閱 [SQL Server 隱私權聲明](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx)。

## <a name="related-products-and-services"></a>相關產品與服務
### <a name="windows-virtual-machines"></a>Windows 虛擬機器
* [虛擬機器概觀](../overview.md)

### <a name="storage"></a>儲存體
* [Microsoft Azure 儲存體簡介](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>網路
* [虛擬網路概觀](../../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 位址](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [在 Azure 入口網站中建立完整格式的網域名稱](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server 文件](https://docs.microsoft.com/sql/index)
* [Azure SQL Database 比較](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>後續步驟

在 Azure 虛擬機器上開始使用 SQL Server：

* [在 Azure 入口網站中建立 SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md)

獲得有關 SQL VM 常見問題的答案：

* [Azure 虛擬機器上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
