---
title: "Azure Operations Management Suite (OMS) 文件 - 教學課程 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。 本文會說明 OMS 中包含的各種服務，並提供其詳細內容的連結。"
services: operations-management-suite
author: czeumault
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.openlocfilehash: 12f959376d4923e4e2481e37108ade632ac14902
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Operations Management Suite (OMS) 是什麼？
Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。  因為 OMS 實作為雲端型服務，所以您對基礎結構服務進行最小的投資就可以快速啟動並執行它。  會自動提供新功能，以節省持續性維護和升級成本。

除了自行提供重要服務之外，OMS 還可以整合 System Center 元件 (例如 System Center Operations Manager)，以將現有管理投資擴充到雲端。  System Center 和 OMS 可以搭配運作，來提供完整的混合式管理經驗。

下列各節所提供的高階描述是有關 OMS 的不同價值區域以及可實作它們的服務。  您可以先參考 OMS 架構來了解不同 OMS 元件的概觀，再檢閱各元件的詳細文件。

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![深入解析與分析](media/operations-management-suite-overview/icon-insight-analytics.png) 深入解析與分析
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 可協助您收集、相互關聯、搜尋和處理作業系統和應用程式所產生的記錄檔和效能資料。 它可讓您在所有工作負載和伺服器之間，無論其實體位置為何，使用整合式搜尋和自訂儀表板輕易地分析數百萬筆記錄，提供您即時的深入操作資訊。

您可以輕鬆地新增 Log Analytics 的解決方案，而解決方案定義要收集的資料和其分析的邏輯。  解決方案可能包括自動提供給代理程式的額外功能，而且幾乎不需要進行設定或進行很少的設定。  除了使用個別解決方案所提供的分析工具之外，您可以跨整個資料集執行自訂搜尋，以關聯系統與應用程式之間的資料。  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![自動化與控制](media/operations-management-suite-overview/icon-automation-control.png) 自動化與控制
Azure 自動化會使用根據 PowerShell 並在 Azure 雲端中執行的 [Runbook](../automation/automation-runbook-types.md)，來自動化管理程序。  Runbook 可以存取可使用 PowerShell 管理的任何產品或服務，包括其他雲端 (例如 Amazon Web Services (AWS)) 中的資源。  Runbook 也可以在本機資料中心的伺服器上執行，以管理本機資源。

Azure 自動化使用 [PowerShell DSC](../automation/automation-dsc-overview.md) 來提供組態管理。  您可以建立和管理裝載於 Azure 的 DSC 資源，以及將它們套用到雲端和內部部署系統，來定義和自動強制執行其組態。

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![保護和復原](media/operations-management-suite-overview/icon-protection-recovery.png) 保護和災害復原
[Azure 備份](http://azure.microsoft.com/documentation/services/backup) 會保護您的應用程式資料並保留數年，而您完全不必投入資本投資，並且只需要最少的營運成本。  它可以備份實體和虛擬 Windows 伺服器中的資料，以及應用程式工作負載 (例如 SQL Server 和 SharePoint)。  System Center Data Protection Manager (DPM) 也可以使用它，將受保護資料複寫到 Azure 以進行備援與長期儲存。

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 可協調內部部署 Hyper-V 虛擬機器、VMware 虛擬機器和實體 Windows/Linux 伺服器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 策略做出貢獻。 您可以將電腦複寫到次要資料中心，或將電腦複寫到 Azure 來擴充資料中心。 Site Recovery 也提供工作負載的簡單容錯移轉和復原。 它會與災害復原機制 (例如 SQL Server AlwaysOn) 整合，並提供復原計劃輕鬆容錯移轉跨多部電腦分層的工作負載。

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS 安全性與法規遵循](media/operations-management-suite-overview/icon-security-compliance.png) 安全性與法規遵循
安全性與法規遵循可協助您識別、評定並降低基礎結構的安全性風險。  OMS 的這些功能是透過 Log Analytics 中的多個解決方案所實作，而這些解決方案會分析代理程式系統中的記錄資料和組態，協助您確保環境的未來安全性。

* [安全性和稽核解決方案](oms-security-getting-started.md)會收集並分析受管理系統上的安全性事件，以找出可疑的活動。
* [反惡意程式碼解決方案](../log-analytics/log-analytics-malware.md)會報告受管理系統上的反惡意程式碼保護狀態。  
* 系統更新解決方案會執行受管理系統上安全性更新和其他更新的分析，讓您輕鬆地識別需要修補的系統。

## <a name="next-steps"></a>後續步驟
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)。
* 了解 [Azure 自動化](../automation/automation-intro.md)。
* 了解 [Azure 備份](http://azure.microsoft.com/documentation/services/backup)。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)。

