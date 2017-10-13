---
title: "有效地管理 Azure 虛擬機器上 SQL Server 的成本 | Microsoft Docs"
description: "提供選擇正確 SQL Server 虛擬機器定價模型的最佳做法。"
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>SQL Server Azure VM 的定價指導方針

本主題提供 Azure 中 SQL Server 虛擬機器的定價指導方針。 影響成本的選項有數個，而挑選平衡成本和業務需求的正確映像相當重要。

## <a name="free-licensed-sql-server-editions"></a>免費授權的 SQL Server 版本

如果您想要開發、測試或建置某項概念證明，則請使用免費授權的 **SQL Server Developer 版**。 此版本包含 SQL Server Enterprise 版的所有功能，因此，您可以使用它來建置您想要的任何應用程式。 唯一不允許的是以生產環境執行。 SQL Server Developer VM 只會收取 VM 費用，不會收取 SQL Server 授權費用。

如果您想要以生產環境執行輕量型工作負載 (< 4 個核心、< 1 GB 記憶體、< 10 GB/資料庫)，則請使用免費授權的 **SQL Server Express 版**。 SQL Express VM 只會收取 VM 費用，不會收取 SQL 授權費用。

針對這些開發/測試或輕量型生產環境工作負載，您也可以選擇符合這些工作負載的較小 VM 大小來節省成本。 DS1v2 可能是這些工作負載的理想選擇。

若要使用上述其中一個映像來建立 SQL Server 2016 Azure VM，請參閱下列連結：

- [SQL Server 2016 Developer Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [SQL Server 2016 Express Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>付費的 SQL Server 版本

如果您的生產環境工作負載並非輕量型，請使用下列其中一個 SQL Server 版本：

| SQL Server 版本 | 工作負載 |
|-----|-----|
| Web | 小型網站 |
| 標準 | 小型到中型工作負載 |
| Enterprise | 大型或任務關鍵性工作負載|

您有兩個選項來支付這些版本的 SQL Server 授權：「依使用量付費」或「自備授權」。

### <a name="pay-per-usage"></a>依使用量付費

**依使用量支付 SQL Server 授權費用**意謂著執行 Azure VM 的每分鐘費用都包含 SQL Server 授權的費用。 您可以在 [Azure VM 定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard)查看不同 SQL Server 版本 (Web、Standard、Enterprise) 的定價。 所有 SQL Server 版本 (2008 R2 到 2016) 的費用都相同。 一般就 SQL Server 授權而言，每分鐘的授權費用會取決於 VM 核心數量。

針對下列情況，建議採用依使用量支付 SQL Server 授權費用：

- 暫時或定期的工作負載。 例如，每年有幾個月需要支援某個事件或每週一需要支援業務分析的應用程式。
- 存留期或規模不明的工作負載。 例如，有幾個月不需用到或依需求可能需要較多或較少運算能力的應用程式。

若要使用上述其中一個依使用量付費的映像來建立 SQL Server 2016 Azure VM，請參閱下列連結：

- [SQL Server 2016 Web Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [SQL Server 2016 Standard Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> 在 Azure 入口網站中建立 SQL Server 虛擬機器時，[選擇大小] 刀鋒視窗上顯示的估計每月費用並不包含 SQL Server 授權費用。 這是 VM 單獨的成本。
>
> ![選擇 VM 大小刀鋒視窗](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>就免費授權的 SQL Server Express 和 Developer 版本而言，這是預估的總費用。 但針對 Web、Standard 及 Enterprise，則請在 [Windows 虛擬機器定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)查看其他 SQL 授權費用。 在定價頁面上，選取您的目標 SQL Server 版本。

### <a name="bring-your-own-license-byol"></a>自備授權 (BYOL)

**透過「授權行動性」自備 SQL Server 授權** (也稱為 **BYOL**) 意謂著在 Azure VM 中使用現有的「SQL Server 大量授權」搭配「軟體保證」。 使用 BYOL 的 SQL Server VM 只會收取執行 VM 的費用，不會收取 SQL Server 授權費用，但前提是您已經透過「大量授權」方案取得授權和「軟體保證」。

針對下列情況，建議採用透過「授權行動性」自備 SQL 授權：

- 持續性工作負載。 例如，需要全年無休支援業務營運的應用程式。
- 存留期和規模已知的工作負載。 例如，一整年都需要使用且已預測好需求的應用程式。

若要搭配 SQL Server VM 使用 BYOL，您必須具備 SQL Server Standard 或 Enterprise 的授權及[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1)，這是透過某些[大量授權](https://www.microsoft.com/en-us/download/details.aspx?id=10585)方案時的必要選項，也是搭配其他方案時的選選購項目。  根據協議的類型及數量和 (或) 對 SQL Server 之承諾的不同，透過「大量授權」方案提供的定價層級也會不同。 但根據經驗法則，自備授權可為持續性生產環境工作負載提供下列優點：

| BYOL 優點 | 說明 |
|-----|-----|
| **節省成本** | 如果工作負載將持續執行 SQL Server Standard 或 Enterprise 長達「10 個月以上」的時間，則與依使用量付費相比，自備 SQL Server 授權較符合經濟效益。 |
| **長期節省** | 平均而言，前 3 年購買或更新 SQL Server 授權可「每年節省 30% 的費用」。 此外，3 年之後，您就不再需要更新授權，只要支付「軟體保證」費用即可。 屆時，將可「節省 200% 的費用」。 |
| **免費的被動次要複本** | 自備授權的另一個優點是每一 SQL Server 可享有[一個免費的被動次要複本授權](https://azure.microsoft.com/pricing/licensing-faq/)，以用於提供高可用性。 這讓高可用性 SQL Server 部署 (例如使用「Always On 可用性群組」) 的授權費用得以砍半。 執行被動次要複本的權限是透過「容錯移轉伺服器軟體保證」權益來提供。 |

若要使用上述其中一個自備授權映像來建立 SQL Server 2016 Azure VM，請查看前面帶有 "{BYOL}" 的 VM：

- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 Standard Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> 請在 10 天內告訴我們您將在 Azure 中使用多少個 SQL Server 授權。 上述映像的連結包含如何這麼做的相關指示。

## <a name="avoid-unecessary-costs"></a>避免不必要的費用

如果您要使用任何不會持續執行的工作負載，請考慮在非使用中的期間關閉虛擬機器。 您只需依據使用量付費。

例如，如果您只是要在 Azure VM 上試用 SQL Server，您就不會希望不小心讓它持續執行數週而產生費用。 其中一個解決方案就是使用[自動關閉功能](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)。

![SQL VM 自動關閉](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

自動關閉是 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) 所提供的較大一組相似功能的一部分。

針對其他工作負載，請考慮使用指令碼解決方案 (例如 [Azure 自動化](https://azure.microsoft.com/services/automation/)) 來自動關閉並重新啟動 Azure VM。

> [!IMPORTANT]
> 將 VM 關閉並解除配置是唯一可以避免產生費用的方式。 只是停止或使用電源選項來關閉 VM 仍然會產生使用費。

## <a name="next-steps"></a>後續步驟

如需一般的 Azure 定價指導方針，請參閱[使用 Azure 計費與成本管理避免非預期的成本](../../../billing/billing-getting-started.md)。

如需最新的「虛擬機器」定價 (包括 SQL Server)，請參閱 [Azure VM 定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard)。

請檢閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)中的其他「SQL Server 虛擬機器」主題。
