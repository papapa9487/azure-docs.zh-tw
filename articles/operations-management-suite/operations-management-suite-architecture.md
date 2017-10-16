---
title: "Operations Management Suite (OMS) 架構 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。  本文會說明 OMS 中包含的各種服務，並提供其詳細內容的連結。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 76f69946724b5297b1f9a1f715819c69c4a4a51d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="oms-architecture"></a>OMS 架構
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) 是管理內部部署和雲端環境的雲端型服務集合。  本文描述 OMS 的不同內部部署和雲端元件以及其高階雲端運算架構。  您可以參考每個服務的文件，以取得進一步詳細資料。

## <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 收集的所有資料都會儲存到裝載於 Azure 的 OMS 儲存機制中。  已連接來源會產生收集到 OMS 儲存機制的資料。  目前支援三種類型的已連接來源。

* [Windows](../log-analytics/log-analytics-windows-agents.md) 或 [Linux](../log-analytics/log-analytics-linux-agents.md) 電腦上所安裝的代理程式會直接連接到 OMS。
* System Center Operations Manager (SCOM) 管理群組 [連接到 Log Analytics](../log-analytics/log-analytics-om-agents.md) 。  SCOM 代理程式會繼續與管理伺服器通訊，而管理伺服器會將資料和效能資料轉送至 Log Analytics。
* [Azure 儲存體帳戶](../log-analytics/log-analytics-azure-storage.md)，收集 Azure 背景工作角色、Web 角色或虛擬機器中的 [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) 資料。

資料來源可定義 Log Analytics 從已連接來源 (包括事件記錄檔和效能計數器) 收集的資料。  解決方案可新增 OMS 的功能，而且可以輕鬆地從 [OMS 解決方案庫](../log-analytics/log-analytics-add-solutions.md)新增到您的工作區。  有些解決方案可能需要從 SCOM 代理程式直接連接到 Log Analytics，有些則可能需要安裝其他代理程式。

Log Analytics 的 Web 型入口網站可用來管理 OMS 資源、新增和設定 OMS 解決方案，以及檢視和分析 OMS 儲存機制中的資料。

![Log Analytics 高階架構](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure 自動化
[Azure 自動化 Runbook](http://azure.microsoft.com/documentation/services/automation) 是在 Azure 雲端中執行，而且可以存取 Azure 或其他雲端服務中的資源，或可從公用網際網路存取的資源。  您也可以使用[Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) 指定本機資料中心的內部部署電腦，讓 Runbook 可以存取本機資源。

[DSC 組態](../automation/automation-dsc-overview.md) 可以直接套用到 Azure 虛擬機器。  其他實體和虛擬機器可以從 Azure 自動化 DSC 提取伺服器中要求組態。

Azure 自動化的 OMS 解決方案會顯示統計資料，以及啟動 Azure 入口網站進行任何作業的連結。

![Azure 自動化高階架構](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure 備份
[Azure 備份](http://azure.microsoft.com/documentation/services/backup) 中的受保護資料儲存在位於特定地理區域的備份保存庫中。  資料是在相同的區域內進行複寫，而且根據保存庫類型，也可能會複寫到另一個區域，以進行進一步備援。

Azure 備份有三種基本案例。

* 具有 Azure 備份代理程式的 Windows 電腦。  這可讓您將任何 Windows 伺服器或用戶端中的檔案和資料夾直接備份到 Azure 備份保存庫。  
* System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器。 這可讓您運用 DPM 或 Microsoft Azure 備份伺服器，將檔案和資料夾以及 SQL 和 SharePoint 這類應用程式工作負載備份到本機儲存體，然後再複寫到 Azure 備份保存庫。
* Azure 虛擬機器擴充功能。  這可讓您將 Azure 虛擬機器備份到 Azure 備份保存庫。

Azure 備份的 OMS 解決方案會顯示統計資料，以及啟動 Azure 入口網站進行任何作業的連結。

![Azure 備份高階架構](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 可協調虛擬機器和實體伺服器的複寫、容錯移轉和容錯回復。 複寫資料會在 Hyper-V 主機、VMware Hypervisor 與主要和次要資料中心內的實體伺服器之間進行交換，或是在資料中心與 Azure 儲存體之間進行交換。  Site Recovery 會將元資料儲存到位於特定地理 Azure 區域的保存庫中。 Site Recovery 服務不會儲存任何複寫的資料。

Azure Site Recovery 有三種基本複寫案例。

**Hyper-V 虛擬機器的複寫**

* 如果 Hyper-V 虛擬機器是在 VMM 雲端中進行管理，則您可以複寫到次要資料來源或 Azure 儲存體。  複寫到 Azure 是透過安全的網際網路連線進行。  複寫到次要資料中心是透過 LAN 來進行。
* 如果 VMM 未管理 Hyper-V 虛擬機器，則您只能複寫到 Azure 儲存體。  複寫到 Azure 是透過安全的網際網路連線進行。

**VMWare 虛擬機器的複寫**

* 您可以將 VMware 虛擬機器複寫到執行 VMware 的次要資料中心或是 Azure 儲存體。  複寫到 Azure 可以透過站對站 VPN、Azure ExpressRoute 或安全的網際網路連線進行。 複寫到次要資料中心是透過 InMage Scout 資料通道進行。

**實體 Windows 和 Linux 伺服器的複寫** 

* 您可以將實體伺服器複寫到次要資料中心或 Azure 儲存體。 複寫到 Azure 可以透過站對站 VPN、Azure ExpressRoute 或安全的網際網路連線進行。 複寫到次要資料中心是透過 InMage Scout 資料通道進行。  Azure Site Recovery 的 OMS 解決方案會顯示一些統計資料，但必須使用 Azure 入口網站進行所有作業。

![Azure Site Recovery 高階架構](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>後續步驟
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)。
* 了解 [Azure 自動化](https://azure.microsoft.com/documentation/services/automation)。
* 了解 [Azure 備份](http://azure.microsoft.com/documentation/services/backup)。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)。

