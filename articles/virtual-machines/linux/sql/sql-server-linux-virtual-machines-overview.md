---
title: "Azure Linux 虛擬機器上的 SQL Server 概觀 | Microsoft Docs"
description: "深入了解如何在 Azure Linux 虛擬機器上執行完整的 SQL Server 版本。 取得所有 Linux SQL Server VM 映像和相關內容的直接連結。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: 787e696edd2a446bec280b763fe5ac591782c8ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 虛擬機器上的 SQL Server 概觀 (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

本主題說明在 Azure Linux 擬機器 (VM) 上執行 SQL Server 的選項，以及提供[入口網站映像的連結](#option-1-create-a-sql-vm-with-per-minute-licensing)。

> [!NOTE]
> 如果您已熟悉 SQL Server，並只需要了解如何部署 SQL Server Linux VM，請參閱[在 Azure 中佈建 Linux SQL Server VM](provision-sql-server-linux-virtual-machine.md)。 或者如果您需要使用 SQL Server 建立 Windows VM，請參閱[在 Azure 中佈建 Windows SQL Server VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)。

如果您是資料庫管理員或開發人員，Azure VM 提供您將內部部署 SQL Server 工作負載和應用程式移動到雲端的方式。

## <a name="scenarios"></a>案例

可能會有許多原因讓您選擇主控 Azure 中的資料。 如果您正在開發或要將應用程式移轉至 Azure，它可改善效能，也能在 Azure 中找出後端資料。 您會自動取得多個資料中心的存取權，以獲得全域支援和災害復原。 資料也是高度安全且具有持久性。

在 Azure VM 上執行的 SQL Server，是用來將關聯式資料儲存到 Azure 的選項之一。 您也可以選擇使用 Azure SQL Database 服務。 如需在虛擬機器上的 SQL Server 與 Azure SQL Database 之間選擇的詳細資訊，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

## <a name="create-a-new-sql-vm"></a>建立新的 SQL VM

在[在 Azure 中佈建 Linux SQL Server VM](provision-sql-server-linux-virtual-machine.md) 教學課程中尋找適用於建立新 SQL VM 的逐步指引。

下表提供虛擬機器資源庫中最新的 SQL Server 映像對照表。 按一下任何連結，即可開始建立採用您指定的版本和作業系統的新 SQL VM。

> [!TIP]
> 若要了解這些映像的 VM 和 SQL 定價，請參閱 [Linux SQL Server VM 的定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 若要查看可用的 Windows SQL Server 虛擬機器映像，請參閱 [Azure 虛擬機器 (Windows) 上的 SQL Server 概觀](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)。

## <a id="packages"></a>已安裝的套件

當您設定 Linux 上的 SQL Server 時，可以根據您的需求安裝資料庫引擎套件，以及數個選用的套件。 SQL Server 的 Linux 虛擬機器映像會自動為您安裝大多數的套件。 下表顯示每個發佈會安裝哪些套件。

| 配送映像 | [資料庫引擎](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server 代理程式](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [全文檢索搜尋](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 附加元件](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![yes](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>後續步驟

若要深入了解如何在 Linux 上設定及使用 SQL Server，請參閱 [Linux 上的 SQL Server 概觀](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)。
