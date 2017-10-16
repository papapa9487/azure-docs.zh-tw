---
title: "Operations Management Suite (OMS) 概觀 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。  本文說明 OMS 的價值、識別 OMS 中包含的各種服務和供應項目，以及提供其詳細內容的連結。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Operations Management Suite (OMS) 是什麼？
本文提供 Operations Management Suite (OMS) 的簡介，包括簡短概述它所提供的商業價值、它所包含的服務和管理解決方案，以及將不同服務和解決方案封裝在一起的供應項目。  文中包含有關部署和使用每個服務和解決方案的詳細文件連結。

## <a name="from-on-premises-to-the-cloud"></a>從內部部署到雲端
Microsoft 一直以來都提供用於管理企業環境的產品。  在 2007 年，有多項產品合併至管理產品的 System Center 套件中。  其中包括可提供軟體發佈和清查等功能的 Configuration Manager、可供主動監視系統和應用程式的 Operations Manager、內含 Runbook 來將手動程序自動化的 Orchestrator，以及用於備份和復原重要資料的 Data Protection Manager。

隨著愈來愈多的計算資源移至雲端，System Center 產品獲得了更多雲端功能(例如 Operations Manager 和 Orchestrator) 以便管理 Azure 中的資源。  其基本上仍設計成內部部署解決方案，而且需要大量投資於內部部署管理環境的部署和維護。  若要完全利用雲端並支援未來的應用程式，則需要有新的管理方式。

## <a name="introducing-operations-management-suite"></a>Microsoft Operations Management Suite 簡介
Operations Management Suite (也稱為 OMS) 是在雲端中從頭設計的管理服務集合。  OMS 元件完全裝載於 Azure 中，而不會部署和管理內部部署資源。  需要進行的設定很少，您可以在幾分鐘內完成啟動並執行。  

- **部署的成本和複雜度最低。**  由於 OMS 的所有元件和資料都儲存在 Azure 中，您不需要考量內部部署元件的複雜度和投資，即可在短時間內啟動並執行 OMS。
- **調整為雲端層級。**  您不必擔心支付不需要的計算資源，或擔心儲存體空間不足，因為雲端讓您僅支付實際使用的資源，並針對您所需的任何負載進行立即調整。  您可以從管理少數資源著手，然後針對整個環境相應增加。
- **利用最新的功能。**  OMS 服務中的功能會持續進行新增和更新。  您可以持續取得最新功能，完全不需要部署更新。
- **整合式服務。**  雖然每項 OMS 服務各自提供顯著的價值，但可一起運作來解決複雜的管理案例。  例如，Azure 自動化中的 Runbook 可透過 Azure Site Recovery 推動容錯移轉程序，然後將資訊記錄至 Log Analytics 以產生警示。
- **總體知識。**  OMS 中的管理解決方案可持續取得最新的資訊。  例如，「安全性和稽核」解決方案可利用在世界各地偵測到的最新威脅來執行威脅分析。
- **隨處存取。**  透過瀏覽器隨處存取管理環境。  在智慧型手機上安裝 OMS 應用程式，以便存取您的監視資料。

### <a name="is-it-just-for-the-cloud"></a>這只適用於雲端嗎？
因為 OMS 服務在雲端執行，並不表示其無法有效地管理您的內部部署環境。  將代理程式置於資料中心的任何 Windows 或 Linux 電腦上，它會將資料傳送到 Log Analytics，以便與從雲端或內部部署服務收集而來的其他所有資料一起進行分析。  使用 Azure 備份和 Azure Site Recovery，運用雲端進行備份及取得內部部署資源的高可用性。  
雲端中的 Runbook 通常無法存取內部部署資源，但您可以在一或多部電腦上安裝代理程式，以在您的資料中心裝載 Runbook。  當您啟動 Runbook 時，只要指定您希望它在雲端或在本機背景工作上執行。

## <a name="hybrid-management-with-system-center"></a>使用 System Center 的混合式管理
如果您有現有的 System Center 安裝，可以整合這些元件與 OMS 服務，以提供內部部署和雲端環境都適用的混合式解決方案，以便充分利用每項產品的相關特性。  將現有的 Operations Manager 管理群組連接到 Log Analytics，以分析雲端的受控代理程式。  使用現有的備份程序搭配 Data Protection Manager，將您的資料備份到雲端。  


## <a name="oms-services"></a>OMS 服務
OMS 的核心功能是由在 Azure 中執行的一組服務所提供。  每個服務會提供特定的管理功能，您可以合併服務以達成不同的管理案例。

|| 服務 | 說明 |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | 監視及分析不同資源 (包括實體和虛擬機器) 的可用性和效能。 |
| ![Azure 自動化](media/operations-management-suite-overview/icon-automation.png) | 自動化 | 讓手動程序自動化，並強制設定實體和虛擬機器。 |
| ![Azure 備份](media/operations-management-suite-overview/icon-backup.png) | 備份 | 備份及還原重要資料。 |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | 站台復原 | 為重要應用程式提供高可用性。 |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 藉由將受控資源中的資料收集到中央儲存機制，以提供 OMS 監視服務。  此資料可能包含事件、效能資料或透過 API 提供的自訂資料。 所收集的資料即可用於警示、分析和匯出。  這個方法可讓您合併各種來源的資料，以便結合來自 Azure 服務與現有內部部署環境的資料。  它也可清楚劃分資料的收集與對該資料採取的動作，而讓所有類型的資料可以使用所有的動作。  

![Log Analytics 概觀](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>收集資料
有各種方法可讓您將資料放入儲存機制，以便 Log Analytics 進行分析。

- **Windows 或 Linux 電腦和虛擬機器。**  在您想從中收集資料的 [Windows](../log-analytics/log-analytics-windows-agents.md) 和 [Linux](../log-analytics/log-analytics-linux-agents.md) 電腦或虛擬機器上安裝 Microsoft Monitoring Agent。  此代理程式將會從定義欲收集之事件和效能資料的 Log Analytics 組態自動下載。  您可以使用 Azure 入口網站，在 Azure 中執行的虛擬機器上輕鬆安裝此代理程式。  如果有現有的 Operations Manager 環境，您可以將管理群組連接至 Log Analytics，並從所有現有的代理程式自動開始收集資料。
- **Azure 服務。**  Log Analytics 會將 [Azure 診斷和 Azure 監視](../log-analytics/log-analytics-azure-storage.md)中的遙測資料收集至儲存機制，以便監視 Azure 資源。
- **資料收集器 API。**  Log Analytics 具有[可供從任何用戶端填入資料的 REST API](../log-analytics/log-analytics-data-collector-api.md)。  這可讓您從協力廠商應用程式收集資料或實作自訂管理案例。  常見的方法是在 Azure 自動化中使用 Runbook 來收集資料，然後使用資料收集器 API 將它寫入儲存機制。

#### <a name="reporting-and-analyzing-data"></a>報告及分析資料
Log Analytics 包含強大的查詢語言，可擷取儲存機制中儲存的資料。  因為所有來源的資料都會儲存為記錄，您可以分析單一查詢中多個來源的資料。
  
除了隨意式分析，Log Analytics 還提供多種分法來報告及分析查詢資料。

- **檢視和儀表板。**  [檢視](../log-analytics/log-analytics-view-designer.md)和[儀表板](../log-analytics/log-analytics-dashboards.md)可在入口網站中以視覺化方式呈現查詢的結果。  管理解決方案通常包含各種檢視，可用於分析解決方案中的資料。  您也可以建立自己的自訂檢視來分析資料，並使其立即可用於您的自訂入口網站中。
- **匯出。**  您可以選擇匯出任何查詢的結果，以便在 Log Analytics 之外進行分析。  您甚至可以安排定期匯出至 [Power BI](../log-analytics/log-analytics-powerbi.md)，其可提供強大的視覺效果與分析功能。
- **記錄搜尋 API。**  Log Analytics 具有[可供從任何用戶端收集資料的 REST API](../log-analytics/log-analytics-log-search-api.md)。  這可讓您以程式設計方式使用在儲存機制中收集的資料，或從其他監視工具存取資料。

#### <a name="alerting"></a>警示
Log Analytics 可以在偵測到問題時[主動警示](../log-analytics/log-analytics-alerts.md)您，或採取矯正動作。  如同 Log Analytics 中的所有其他分析，這是透過記錄搜尋完成。  此搜尋會定期執行，如果結果符合特定準則，就會建立警示。

![Log Analytics 警示](media/operations-management-suite-overview/overview-alerts.png)

除了在 Log Analytics 儲存機制中建立警示記錄以外，警示可以採取下列動作。

- **電子郵件。**  傳送電子郵件，主動通知您所偵測到的問題。
- **Runbook。**  Log Analytics 中的警示可以在 Azure 自動化中啟動 Runbook。  這麼做通常是為了嘗試更正所偵測到的問題。  如果 Azure 或其他雲端有問題，則可在雲端啟動 Runbook，而如果實體或虛擬機器有問題，則可在本機代理程式上啟動。
- **Webhook。**  警示可以啟動 Webhook，並將記錄搜尋結果中的資料傳遞給它。  這可讓您與外部服務 (例如替代警示系統) 整合，或可嘗試對外部網站採取矯正動作。

### <a name="azure-automation"></a>Azure 自動化
[Azure 自動化](http://azure.microsoft.com/documentation/services/automation)提供 OMS 的程序自動化和組態管理。  它可讓手動程序自動化，並有助於強制設定實體和虛擬機器。  

#### <a name="process-automation"></a>程序自動化
Azure 自動化可使用以 PowerShell 指令碼或 PowerShell 工作流程為基礎的 [Runbook](../automation/automation-runbook-types.md)，讓手動程序自動化。  它也包含支援 Runbook 的資產，例如可在多個 Runbook 與認證之間共用的變數，以及可讓您儲存 Runbook 驗證所需加密資訊的連線。
Runbook 提供套件中其他服務的程序自動化。  由於其他各項服務可利用 PowerShell 或透過 REST API 進行存取，所以您可建立 Runbook 來執行一些功能，例如：在 Log Analytics 中收集管理資料，或使用 Azure 備份起始備份。

##### <a name="accessing-resources"></a>存取資源
由於 Runbook 是以 PowerShell 為基礎，所以可管理任何可利用 PowerShell Cmdlet 存取的資源。  當您將[模組載入](../automation/automation-integration-modules.md)到您的自動化帳戶時，該帳戶中的所有 Runbook 均可使用該模組。 
 
當 Runbook 在雲端執行時，其可存取任何可從雲端存取的資源。  這可能是您的 Azure 訂用帳戶中的資源、其他雲端 (例如 Amazon Web Services (AWS)) 中的資源，或可透過 REST API 存取的服務。  雲端中的 Runbook 不會在任何認證下執行，但它們可以利用認證、連線和憑證等資產來向他們存取的資源進行驗證。

資料中心內的資源最可能無法從在雲端執行的 Runbook 存取。  您可以在資料中心安裝一或多個[混合式 Runbook 背景工作](../automation/automation-hybrid-runbook-worker.md)，以執行需要存取本機資源的 Runbook。  當您啟動 Runbook 時，請指定它應在雲端或在特定背景工作上執行。

![Azure 自動化 Runbook](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>啟動 Runbook
Runbook 可以[透過多種方法啟動](../automation/automation-starting-a-runbook.md)，以便包含在各種管理案例中。  

- **Azure 入口網站。**  如同其他 Azure 服務，您可以從 Azure 入口網站管理 Azure 自動化。  除了啟動 Runbook 以外，您可以匯入 Runbook 或撰寫自己的 Runbook。
- **已排程。**  您可以安排 Runbook 定期啟動。  這可讓您自動重複一般管理程序，或將資料收集至 Log Analytics 。
- **PowerShell 和 API。**  您可以啟動 Runbook，並從 PowerShell Cmdlet 或 Azure 自動化 REST API 將必要的參數資訊傳遞給它們。  
- **Webhook。**  您可針對任何 Runbook 建立 Webhook，好讓它能夠從外部應用程式或網站啟動。
- **Log Analytics 警示。**  Log Analytics 中的警示可以自動啟動 Runbook，以嘗試矯正警示所找到的問題。

#### <a name="configuration-management"></a>組態管理
[PowerShell Desired State Configuration (DSC)](../automation/automation-dsc-overview.md) 是 Windows PowerShell 中的管理平台，可讓您部署及強制設定實體和虛擬機器。  Azure 自動化可管理 DSC 組態，並且在雲端中提供代理程式可以存取的提取伺服器，以擷取所需的組態。

![Azure 自動化 DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure 備份和 Azure Site Recovery
Azure 備份和 Azure Site Recovery 對於商務持續性和災害復原很有幫助。  它們各自具有一些功能，可協助您確保應用程式在發生中斷時保持可用狀態，並且在系統恢復連線時回到正常作業。  這兩種服務對於為貴組織定義的復原點目標 (RPO) 和復原時間目標 (RTO) 很有幫助。 RPO 定義資料在中斷期間無法使用的可接受限制，而 RTO 則限制服務或應用程式在中斷期間無法使用的可接受時間量。

#### <a name="azure-backup"></a>Azure 備份
[Azure 備份](http://azure.microsoft.com/documentation/services/backup)可提供 OMS 的資料備份和還原服務。  它可保護您的應用程式資料並保留數年，而您完全不必投入資本投資，並且只需要最少的營運成本。  它可以備份實體和虛擬 Windows 伺服器中的資料，以及應用程式工作負載 (例如 SQL Server 和 SharePoint)。  System Center Data Protection Manager (DPM) 也可以使用它，將受保護資料複寫到 Azure 以進行備援與長期儲存。

Azure 備份中受保護的資料會儲存在位於特定地理區域的備份保存庫中。 資料是在相同的區域內進行複寫，而且根據保存庫類型，也可能會複寫到另一個區域，以供進一步復原。

Azure 備份有三種基本案例。

- **具有 Azure 備份代理程式的 Windows 電腦。** 將任何 Windows 伺服器或用戶端中的檔案和資料夾直接備份到 Azure 備份保存庫。<br><br>![具有 Azure 備份代理程式的 Windows 電腦](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器。** 運用 DPM 或 Microsoft Azure 備份伺服器，將檔案和資料夾以及 SQL 和 SharePoint 這類應用程式工作負載備份到本機儲存體，然後再複寫到 Azure 備份保存庫。 在 Hyper-V 或 VMware 上支援 Windows 和 Linux 虛擬機器。<br><br>![System Center Data Protection Manager (DPM) 或 Microsoft Azure 備份伺服器](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure 虛擬機器擴充功能。** 將 Azure 中的 Windows 或 Linux 虛擬機器備份到 Azure 備份保存庫。<br><br>![Azure 虛擬機器擴充功能。](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 藉由協調內部部署虛擬和實體機器至 Azure 或次要網站的複寫，來提供商務持續性。 如果主要網站無法使用，您會容錯移轉到次要位置，讓使用者可以繼續工作，並且在系統回到正常運作時容錯回復。 

Azure Site Recovery 可為伺服器和應用程式提供高可用性。  它可協調內部部署 Hyper-V 虛擬機器、VMware 虛擬機器和實體 Windows/Linux 伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 策略做出貢獻。 您可以將電腦複寫到次要資料中心，或將電腦複寫到 Azure 來擴充資料中心。 Site Recovery 也提供工作負載的簡單容錯移轉和復原。 它會與災害復原機制 (例如 SQL Server AlwaysOn) 整合，並提供復原計劃輕鬆容錯移轉跨多部電腦分層的工作負載。

Azure Site Recovery 有三種基本複寫案例。

- **Hyper-V 虛擬機器的複寫。**  如果 Hyper-V 虛擬機器是在 VMM 雲端中進行管理，則您可以複寫到次要資料來源或 Azure 儲存體。 複寫到 Azure 是透過安全的網際網路連線進行。 複寫到次要資料中心是透過 LAN 來進行。  如果 VMM 未管理 Hyper-V 虛擬機器，則您只能複寫到 Azure 儲存體。 複寫到 Azure 是透過安全的網際網路連線進行。<br><br>![Hyper-V 虛擬機器的複寫](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **VMware 虛擬機器的複寫。**  您可以將 VMware 虛擬機器複寫到執行 VMware 的次要資料中心或是 Azure 儲存體。 複寫到 Azure 可以透過站對站 VPN、Azure ExpressRoute 或安全的網際網路連線進行。 複寫到次要資料中心是透過 InMage Scout 資料通道進行。<br><br>![VMware 虛擬機器的複寫](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **實體 Windows 和 Linux 伺服器的複寫。**  您可以將實體伺服器複寫到次要資料中心或 Azure 儲存體。 複寫到 Azure 可以透過站對站 VPN、Azure ExpressRoute 或安全的網際網路連線進行。 複寫到次要資料中心是透過 InMage Scout 資料通道進行。 Azure Site Recovery 的 OMS 解決方案會顯示一些統計資料，但必須使用 Azure 入口網站進行所有作業。<br><br>![實體 Windows 和 Linux 伺服器的複寫](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery 會將元資料儲存到位於特定地理 Azure 區域的保存庫中。 Site Recovery 服務不會儲存任何複寫的資料。

## <a name="management-solutions"></a>管理解決方案
[管理解決方案](operations-management-suite-solutions.md)是預先封裝的邏輯集合，可利用一或多項 OMS 服務實作特定管理案例。  Microsoft 和合作夥伴會提供不同的解決方案，您可以輕鬆地將其新增至您的 Azure 訂用帳戶，以提升您的 OMS 投資價值。  身為合作夥伴的您可以建立自己的解決方案，來支援您的應用程式和服務，並透過 Azure Marketplace 或快速入門範本將其提供給使用者。

[更新管理解決方案](oms-solution-update-management.md)就是利用多項服務來提供其他功能的良好解決方案範例。  這個解決方案使用 Windows 和 Linux 適用的 Log Analytics 代理程式，來收集每個代理程式上必要更新的相關資訊。  它會將此資料寫入至 Log Analytics 儲存機制，以便透過內含的儀表板分析資料。  當您建立部署時，Azure 自動化中的 Runbook 用來安裝必要的更新。  您可在入口網站中管理這整個程序，而不需擔心基礎詳細資料。

![方案](media/operations-management-suite-overview/overview-solution.png)

大多數解決方案都可以執行下列一或多項功能。

- 收集其他資訊。  Log Analytics 會從用戶端和服務收集各種資料，包括事件和效能資料。  管理解決方案通常使用 Azure 自動化 runbook，收集無法從其他資料來源取得的額外資訊。
- 提供所收集資訊的其他分析。  管理解決方案包含儀表板和檢視，可提供資料的分析和視覺效果。  這些項目可連回預先定義的記錄搜尋，讓您深入探詢詳細資料。  管理解決方案也會對已經收集到存放庫中的資料執行分析，例如搜尋安全性事件中表示威脅的模式。
- 新增功能。  Microsoft 所提供的一些解決方案可以核心服務的功能為建置基礎，以提供額外的功能。  例如，「服務對應」會提供自己的主控台進行探索，並即時對應伺服器和程序相依性。
Microsoft 和合作夥伴會定期將解決方案新增至 OMS，讓您持續提升您的投資價值。  您可以透過 OMS 入口網站中的 [解決方案目錄] 瀏覽和安裝 Microsoft 解決方案，或透過 Azure 入口網站中的 Azure Marketplace 瀏覽並安裝 Microsoft 和合作夥伴解決方案。  

![方案庫](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>後續步驟
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)。
* 了解 [Azure 自動化](../automation/automation-intro.md)。
* 了解 [Azure 備份](http://azure.microsoft.com/documentation/services/backup)。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)。
* 探索不同 OMS 供應項目中[可用的解決方案](../log-analytics/log-analytics-add-solutions.md)。 

