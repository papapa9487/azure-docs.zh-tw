---
title: "Linux Azure 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs"
description: "本文章提供在 Linux Azure VM 上執行 SQL Server 的常見問題解答。"
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Linux Azure 虛擬機器上 SQL Server 的常見問題集

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

本主題提供關於 [Linux Azure 虛擬機器上的 SQL Server](sql-server-linux-virtual-machines-overview.md) 一些最常見執行問題解答。

> [!NOTE]
> 本主題將重點放在 Linux VM 上 SQL Server 的特定問題。 如果您在 Windows VM 上執行 SQL Server，請參閱 [Windows 常見問題集](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>常見問題集

1. **如何建立具有 SQL Server 的 Linux Azure 虛擬機器？**

   最簡單的解決方法是建立包含 SQL Server 的 Linux 虛擬機器。 如需註冊 Azure 並從入口網站建立 Linux SQL VM 的教學課程，請參閱[在 Azure 入口網站中佈建 Linux SQL Server 虛擬機器](provision-sql-server-linux-virtual-machine.md)。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility)。

1. **如何將 Azure VM 中的 SQL Server 升級至新版本？**

   目前，在 Azure VM 中執行的 SQL Server 不提供任何就地升級。 因此，請建立具有所需 SQL Server 版本的 Azure 虛擬機器，然後使用[標準資料移轉技術](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)，將資料庫移轉到新的伺服器。

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   首先，建立只有 Linux OS 的虛擬機器。 然後針對您的 Linux 發行版本執行 [SQL Server 安裝步驟](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)。 除非您安裝其中一個免費授權的 SQL Server 版本，否則您也必須要有 SQL Server 授權與 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。

1. **是否有適用於 SQL Server 的自備授權 (BYOL) Linux 虛擬機器映像？**

   目前，沒有適用於 SQL Server 的 BYOL Linux 虛擬機器映像。 不過，您可以在僅有 Linux 的 VM 上手動安裝 SQL Server，如先前的問題中所述。

1. **如果是從其中一個隨收隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   否。 您不能從以分鐘計費授權切換為使用您自己的授權。 您必須建立新的 Linux VM，安裝 SQL Server，然後移轉資料。 如需自備授權的詳細資訊，請參閱上一個問題。

1. **還安裝了哪些相關的 SQL Server 套件？**

   若要查看預設安裝在 SQL Server Linux VM 上的 SQL Server 套件，請參閱[已安裝的套件](sql-server-linux-virtual-machines-overview.md#packages)。

1. **Azure VM 上是否支援 SQL Server 高可用性解決方案？**

   目前沒有。 在 Linux 中，Always On 可用性群組和容錯移轉叢集都需要叢集解決方案，例如 Pacemaker。 SQL Server 支援的 Linux 發行版本不支援其在雲端的高可用性附加元件。

1. **我為什麼不能使用有消費限制的 Azure 訂用帳戶來佈建 RHEL 或 SLES SQL Server VM？**

   RHEL 與 SLES 虛擬機器需要沒有消費限制的訂用帳戶，以及與該訂用帳戶相關聯的已驗證付款方式 (通常為信用卡)。 如果您佈建 RHEL 或 SLES VM，但未移除消費限制，您的訂用帳戶將會被停用，而且所有 VM/服務都會停止。 如果您進入此狀態，若要重新啟用該訂用帳戶，請[移除消費限制](https://account.windowsazure.com/subscriptions)。 將會還原您目前計費週期剩餘的信用額度，但如果您選擇重新開始並繼續執行 RHEL 或 SLES VM 映像，則會向您的信用卡收取其額外費用。

## <a name="resources"></a>資源

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux VM](provision-sql-server-linux-virtual-machine.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [佈建 SQL Server Windows VM](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [常見問題集 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)