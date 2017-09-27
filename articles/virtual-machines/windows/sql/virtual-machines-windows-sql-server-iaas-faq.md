---
title: "Azure 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs"
description: "本文章提供在 Azure VM 上執行 SQL Server 的常見問題解答。"
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: v-shysun
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 447ece9653a4cf69153f9c85e222e3ea4e8bae16
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---
# <a name="frequently-asked-questions-for-sql-server-on-azure-virtual-machines"></a>Azure 虛擬機器上 SQL Server 的常見問題集
本主題提供關於 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)一些最常見的執行問題解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>常見問題集

1. **如何使用 SQL Server 建立 Azure 虛擬機器？**

    最簡單的解決方法是建立包含 SQL Server 的虛擬機器。 如需註冊 Azure 並從入口網站建立 SQL VM 的教學課程，請參閱 [在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 您可以選取使用以分鐘計費 SQL Server 授權的虛擬機器映像，或者您可以使用映像，讓您自備自己的 SQL Server 授權。 您也可以選擇手動在 VM 上安裝 SQL Server，並重複使用內部部署授權。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **SQL VM 和 SQL Database 服務之間的差異為何？**

    從概念上來說，在 Azure 虛擬機器上執行 SQL Server 與在遠端資料中心中執行 SQL Server 並沒什麼不同。 對比之下， [SQL Database](../../../sql-database/sql-database-technical-overview.md) 可提供資料庫即服務的功能。 使用 SQL Database 時，您無法存取主控資料庫的機器。 如需完整的比較，請參閱 [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何將內部部署 SQL Server 資料庫移轉至雲端？**

    首先，請使用 SQL Server 執行個體來建立 Azure 虛擬機器。 然後將您的內部部署資料庫移轉至該執行個體。 如需資料移轉策略，請參閱 [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

1. **是否可以在相同的 VM 上安裝第二個 SQL Server 執行個體？是否可以變更預設執行個體的已安裝功能？**

    是。 SQL Server 安裝媒體位於 **C** 磁碟機的資料夾中。 您可從該位置執行 **Setup.exe** 來新增新的 SQL Server 執行個體，或變更機器上 SQL Server 的其他已安裝功能。 請注意，某些功能 (例如自動備份、自動修補和 Azure Key Vault 整合) 只能在預設執行個體上運作。

1. **是否可以將 SQL Server 的預設執行個體解除安裝**

    是。 但有一些考量。 如前一個回應所述，依賴 [SQL Server IaaS 代理程式擴充功能](virtual-machines-windows-sql-server-agent-extension.md)的功能只能在預設執行個體上運作。 如果您將預設執行個體解除安裝，擴充功能會繼續尋找它，而且可能會產生事件記錄錯誤。 這些錯誤來自下列兩個來源：**Microsoft SQL Server 認證管理**和 **Microsoft SQL Server IaaS 代理程式**。 其中一個錯誤應如下所示：
    
        A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. 
        
    如果您決定要將預設執行個體解除安裝，則也會將 [SQL Server IaaS 代理程式擴充功能](virtual-machines-windows-sql-server-agent-extension.md)解除安裝。

1. **如何將 Azure VM 中的 SQL Server 升級至新版本？**

    目前，在 Azure VM 中執行的 SQL Server 不提供任何就地升級。 因此，請使用所需的 SQL Server 版本來建立新的 Azure 虛擬機器，然後使用標準 [資料移轉技術](virtual-machines-windows-migrate-sql.md)，將資料庫移轉到新的伺服器。

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

    做法有二種。 您可以佈建其中一個[支援授權的虛擬機器映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)。 另一個選項是將 SQL Server 安裝媒體複製到 Windows Server VM 上，然後在 VM 上安裝 SQL Server。 基於授權原因，您必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如果是從其中一個隨收隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

    不用。 您不能從以分鐘計費授權切換為使用您自己的授權。 因此，請使用其中一個 [BYOL 映像](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)來建立新的 Azure 虛擬機器，然後使用標準 [資料移轉技術](virtual-machines-windows-migrate-sql.md)，將資料庫移轉到新的伺服器。

1. **Azure VM 上是否支援 SQL Server 容錯移轉叢集執行個體 (FCI)？**

   是。 您可以[在 Windows Server 2016 上建立 Windows 容錯移轉叢集](virtual-machines-windows-portal-sql-create-failover-cluster.md)，並使用儲存空間直接存取 (S2D) 來連結叢集儲存體。 或者，您可以使用 [Azure 虛擬機器中的 SQL Server 的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述的協力廠商叢集或儲存體解決方案。

1. **如果只是用於待命/容錯移轉，需要對 Azure VM 上的 SQL Server 授權付費嗎？**

    如果您有軟體保證並且使用[虛擬機器授權常見問題集](http://azure.microsoft.com/pricing/licensing-faq/)中所述的授權機動性，則您不需要對參加為 HA 部署中被動次要複本的一個 SQL Server 授權付費。

1. **如何將更新和 Service Pack 套用到 SQL Server VM 上？**

    虛擬機器可讓您控制主機電腦，包括套用更新的時間與方法。 針對作業系統，您可以手動套用 Windows 更新，或是啟用名為 [自動修補](virtual-machines-windows-sql-automated-patching.md)的排程服務。 自動修補會安裝任何標示為重要的更新，包括該類別目錄中的 SQL Server 更新。 其他選擇性的 SQL Server 更新，則必須手動安裝。

1. **是否可以設定虛擬機器資源庫中未顯示的組態 (例如 Windows 2008 R2 + SQL Server 2012)？**

    不用。 針對包含 SQL Server 的虛擬機器資源庫映像，您必須選取其中一個提供的映像。

1. **如何在 Azure VM 上安裝 SQL 資料工具？**

     請從 [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313) 下載並安裝 SQL 資料工具。

## <a name="resources"></a>資源

如需 Azure 虛擬機器上的 SQL Server 概觀，請觀看影片 [Azure VM is the best platform for SQL Server 2016 (Azure VM 是 SQL Server 2016 的最佳平台)](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)。 您也可以在 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)主題中，取得詳盡的簡介。

其他資源包括：

* [在 Azure 入口網站中佈建 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md) 
