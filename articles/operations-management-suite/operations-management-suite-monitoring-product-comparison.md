---
title: "Microsoft 監視產品比較 | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。  本文會說明 OMS 中包含的各種服務，並提供其詳細內容的連結。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>Microsoft 監視產品比較
本文根據產品架構、監視資源的邏輯，以及執行所收集資料分析的方式，提供 System Center Operations Manager (SCOM) 與 Operations Management Suite (OMS) 中 Log Analytics 之間的比較。  這是為了讓您對其差異和相對優點有基本的了解。  

## <a name="basic-architecture"></a>基本架構
### <a name="system-center-operations-manager"></a>System Center Operations Manager
所有 SCOM 元件都已安裝在您的資料中心。  [代理程式已安裝](http://technet.microsoft.com/library/hh551142.aspx) 於 SCOM 所管理的 Windows 和 Linux 機器上。  代理程式會連接到與 SCOM 資料庫和資料倉儲通訊的 [管理伺服器](https://technet.microsoft.com/library/hh301922.aspx) 。  代理程式依賴網域驗證來連接到管理伺服器。  受信任網域以外的代理程式可以執行憑證驗證或連接到 [閘道伺服器](https://technet.microsoft.com/library/hh212823.aspx)。

SCOM 需要兩個 SQL Database，一個用於作業資料，另一個做為資料倉儲，以支援報告和資料分析。  [報告伺服器](https://technet.microsoft.com/library/hh298611.aspx) 會執行 SQL Reporting 服務以報告資料倉儲中的資料。 

SCOM 可以使用 [Azure](https://www.microsoft.com/download/details.aspx?id=38414)、[Office 365](https://www.microsoft.com/download/details.aspx?id=43708) 和 [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html)等產品的管理組件來監視雲端資源。  這些管理組件會使用一或多個本機代理程式做為 Proxy，以便探索雲端資源及執行工作流程來測量其效能和可用性。  Proxy 代理程式也用來 [監視網路裝置](https://technet.microsoft.com/library/hh212935.aspx) 和其他外部資源。

Operations 主控台是連接到其中一部管理伺服器的 Windows 應用程式，可讓系統管理員檢視及分析所收集的資料並設定 SCOM 環境。  Web 式主控台可以裝載於任何 IIS 伺服器上並透過瀏覽器提供資料分析。

![SCOM 架構](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
大部分的 OMS 元件位於 Azure 雲端，因此您可以用最低的成本與系統管理投入來進行部署和管理。  Log Analytics 收集的所有資料都會儲存在 OMS 儲存機制中。

Log Analytics 也可以從下列三個來源收集資料：

* 執行 Windows 和 [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) 或 Linux 和[適用於 Linux 的 Operations Management Suite 代理程式](https://technet.microsoft.com/library/mt622052.aspx)的實體和虛擬機器。  這些電腦可以是 Azure 或其他雲端中的內部部署或虛擬機器。
* Azure 儲存體帳戶，其具有 Azure 背景工作角色、Web 角色或虛擬機器所收集的 [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md) 資料。
* [SCOM 管理群組的連線](https://technet.microsoft.com/library/mt484104.aspx)。  在此組態中，代理程式會與 SCOM 管理伺服器通訊，而這些伺服器會將資料傳遞至 SCOM 資料庫，而後資料會傳遞至 OMS 資料存放區。
  系統管理員會分析所收集的資料，並透過裝載於 Azure 並可從任何瀏覽器存取的 OMS 入口網站設定 Log Analytics。  用來存取此資料的行動應用程式適用於標準平台。

![Log Analytics 架構](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>整合 SCOM 與 Log Analytics
SCOM 使用做為 Log Analytics 的資料來源時，您可以在混合式監視環境中利用這兩種產品的功能。  除了繼續從 SCOM 執行管理組件以外，您可以透過 OMS 即將管理的 Operations 主控台來設定現有的 SCOM 代理程式，  
已連線 SCOM 管理群組的資料會利用下列四個方法之一傳遞至 Log Analytics︰

* 代理程式會收集事件和效能資料並傳遞至 SCOM。  而後 SCOM 中的管理伺服器會將資料傳遞至 Log Analytics。
* 某些事件 (例如 IIS 記錄檔和安全性事件) 會繼續從代理程式直接傳遞至 Log Analytics。
* 有些解決方案會將其他軟體傳遞至代理程式，或要求即將安裝的軟體收集其他資料。  此資料通常會直接傳送到 Log Analytics。
* 有些解決方案會直接從 SCOM 管理伺服器收集不是源自代理程式的資料。  例如， [警示管理解決方案](https://technet.microsoft.com/library/mt484092.aspx) 會在警示建立後，從 SCOM 收集警示。

## <a name="monitoring-logic"></a>監視邏輯
SCOM 和 Log Analytics 使用從代理程式收集的類似資料，但它們定義和實作其資料收集邏輯的方式，以及它們分析所收集資料的方式基本上有所差異。

### <a name="operations-manager"></a>Operations Manager
SCOM 的監視邏輯是在 [管理組件](https://technet.microsoft.com/library/hh457558.aspx) 中實作，而這些組件包含可供探索要監視之元件、測量這些元件之健康狀態，以及收集要分析之資料的邏輯。  監視資料可與收集事件或效能計數器一樣簡單，或可以使用指令碼中實作的複雜邏輯。  除了硬體和網路裝置以外，包含完整監視的管理組件可用於各種 [Microsoft 和協力廠商應用程式](http://go.microsoft.com/fwlink/?LinkId=82105) 。  您可以針對自訂應用程式 [撰寫您自己的管理組件](http://aka.ms/mpauthor) 。

管理組件包含多個工作流程，而每個工作流程會執行不同的監視功能，例如效能計數器取樣、檢查服務的狀態，或執行指令碼。  每個工作流程會獨立執行並定義自己的結果，例如將寫入至哪個資料庫，以及是否會產生警示。 

您可以覆寫工作流程詳細資料，例如其執行的頻率、視為錯誤的臨界值，以及其產生之警示的嚴重性。  您也可以加入您自己的工作流程，以提供其他功能。

![覆寫](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

管理伺服器會將管理組件安裝於 Operations Manager 資料庫並自動發佈至代理程式。  每個代理程式會自動下載管理組件，並載入其所安裝的應用程式的相關工作流程。  代理程式收集的資料會傳遞回管理伺服器，以便插入 SCOM 資料庫和資料倉儲。  Operations 主控台可讓您透過自訂檢視、儀表板及管理組件中包含的報告，檢視和分析此資料。

下圖說明管理組件的散發。

![管理組件流程](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>事件和效能集合
Log Analytics 會從使用 Windows 事件記錄檔、IIS 記錄檔和 Syslog 等來源的代理程式系統收集事件和效能計數器。  您可以透過 Log Analytics 入口網站定義收集資料的準則，然後建立記錄檔查詢以分析所收集的資料。  當您建立 OMS 工作區時，會定義一組標準準則，而您可以針對特定應用程式定義其他資料。 

![在 Log Analytics 中定義事件記錄檔](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

雖然 SCOM 有許多詳細的工作流程，通常會為資料定義特定準則以及應在回應中執行的動作，但 Log Analytics 有更多有關資料收集的一般準則。  記錄檔查詢和解決方案會提供更多目標式準則，可供分析雲端中收集的特定資料並採取相關動作。

#### <a name="solutions"></a>解決方案
解決方案會提供資料收集和分析的額外邏輯。  您可以選取要從「方案庫」新增至 OMS 訂用帳戶的解決方案。

![方案庫](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

解決方案主要在雲端執行，並提供 OMS 儲存機制中所收集事件和效能計數器的分析。  它們也可以定義要收集的其他資料，而此資料可以使用記錄檔搜尋進行分析，或是藉由解決方案在 OMS 儀表板中提供的其他使用者介面進行分析。 

例如， [變更追蹤解決方案](https://technet.microsoft.com/library/mt484099.aspx) 會偵測代理程式系統的組態變更，並將事件寫入至 OMS 儲存機制，以便利用數個可彙總所偵測變更的圖形化檢視進行分析。  您可以從已彙總的檢視向下鑽研至可顯示方案所收集之詳細資料的記錄查詢。

雖然您可以選取將哪些解決方案加入至您的訂用帳戶，但您目前無法建立自己的解決方案。  您可以選取事件和效能計數器，根據您自己的記錄查詢收集並建立自訂檢視。

下圖摘要顯示 Log Analytics 的監視邏輯。

![Log Analytics 方案流程](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>健康狀態監視
### <a name="operations-manager"></a>Operations Manager
SCOM 可以為應用程式的不同元件建立模型，並提供每個元件的即時健康狀態。  這不僅可讓您檢視在一段時間內偵測到的錯誤和效能，也可以在任何指定的時間驗證應用程式或系統及其每個元件的實際健康狀態。  因為它了解應用程式可用的時間週期，所以 SCOM 中的健康狀態引擎也支援服務等級協定 (SLA)，以分析和報告一段時間的應用程式可用性。

例如，下列檢視會顯示 SCOM 所監視之 SQL Database 引擎的即時健康狀態。  其中一個資料庫引擎的每個資料庫的健康狀態會顯示在檢視的下半部。

![狀態檢視](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

以下顯示其中一個資料庫引擎的健康狀態總管，包含用來判斷其整體健康狀態的監視器。  這些監視器已定義於 SQL 管理組件中，會對 SCOM 探索到的所有 SQL Database 引擎執行。

![健康狀態總管](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

可以結合多個系統上的元件來測量分散式應用程式的健康狀態。  這特別適合用於包含多個分散式元件的企業營運應用程式。  您可以建立一個模型，以測量每個元件的健康狀態，進而積存至應用程式的可用性。

Active Directory 是其中一個可提供模型來分析其分散式元件的管理組件範例。  下面的範例圖表顯示整體環境的健康狀態以及樹系、網域和網域控制站之間的關聯性。  每個元件都包含子元件和多個類似於上述 SQL 範例的監視器。

![SCOM 圖表檢視](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS 不包含可製作應用程式模型或測量其即時健康狀態的通用引擎。  個別的解決方案可會根據所收集的資料來評估特定服務的整體健康狀態，並在代理程式上安裝自訂邏輯以執行即時分析。  因為在雲端執行的解決方案可以存取 OMS 儲存機制，所以常可提供比管理組件通常執行的更深入分析。 

例如， [AD 評估和 SQL 評估解決方案](https://technet.microsoft.com/library/mt484102.aspx) 可分析所收集的資料，並提供不同環境層面的評比。  它包含改善建議，用以改善環境的可用性和效能。

![AD 評估方案](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>資料分析
SCOM 和 Log Analytics 各自提供不同的功能來分析所收集的資料。  SCOM 在 Operations 主控台中有檢視和儀表板可供分析各種格式和報告中的近期資料，進而以表格式形式呈現資料倉儲中的資料。  Log Analytics 提供完整的記錄檔查詢語言和介面，以便分析 OMS 儲存機制中的資料。  SCOM 做為 Log Analytics 的資料來源時，儲存機制包含 SCOM 所收集的資料，因此 Log Analytics 工具可用來分析來自這兩個系統的資料。

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Views
Operations 主控台中的檢視可讓您以不同的格式檢視 SCOM 所收集的各種資料類型，通常表格式適用於事件、警示和狀態資料，而折線圖適用於效能資料。  檢視會執行最少的資料分析或彙總，但確實可讓您根據特定準則進行篩選。 

![Views](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

管理組件通常會提供多個檢視，以支援其所監視的應用程式或系統。  這可能包括管理組件探索到的不同物件的狀態檢視、偵測到之問題的警示檢視，以及計數器的效能檢視。

這些檢視特別適合用來分析環境的目前狀態，包括未決警示和受監視系統和物件的健康狀態。  您可以向下鑽研到支援特定警示的詳細事件或效能資料，以便診斷其根本原因。 同樣地，您可以檢視不同應用程式元件的效能和健康狀態，以評估其目前的健康狀態。

#### <a name="dashboards"></a>儀表板
Operations 主控台中的儀表板主要處理與檢視相同的資料，但自訂性較佳，而且可以包含更豐富的視覺效果。  有一組標準儀表板可供您使用，以便針對您的用途輕鬆地自訂。  您也可以使用 PowerShell Widget，其可顯示 PowerShell 查詢所傳回的資料。

![儀表板](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

開發人員能將自訂元件加入至他們納入其管理組件中的儀表板。  這些儀表板可針對特定應用程式高度特製化，例如 SQL 管理組件中的儀表板，如下所示。  此儀表板也可做為自訂版本的範本。

![SQL 儀表板](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>報告
SCOM 中的報告會以表格式形式分析資料倉儲中的資料。  您可加以列印和排程，以便以不同的檔案格式 (包括 PDF、CSV 及 Word) 自動交付。  報告會使用資料倉儲中的資料，所以特別適合用於長期趨勢分析。

管理組件通常會提供特定應用程式的自訂報告。  您也可以從可針對自己的應用程式自訂或可供執行臨機操作分析的一般報告庫選取。

以下是範例效能報告，其中顯示 Active Directory 管理組件所收集的資料。

![報告](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics 具有 [查詢語言](https://technet.microsoft.com/library/mt484120.aspx) ，您可以用來執行多個應用程式的資料分析，而不需建立自訂檢視或報告。  因為 OMS 是在雲端實作，所以查詢和資料分析的效能不受任何硬體限制約束，而且可以快速地分析查詢 (包括數百萬筆記錄)。 

Log Analytics 中的查詢也是其他功能的基礎。  您可以儲存查詢、將其結果匯出到 Excel，或讓它自動定期執行並產生警示 (如果結果符合特定準則)。  

![記錄檔查詢流程](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

以下是 Log Analytics 查詢的範例。  在此範例中，會傳回名稱包含 “started” 的所有事件並依事件識別碼分組。  使用者只需提供查詢，而 Log Analytics 會動態產生使用者介面以執行分析。  選取清單中的任何項目，便會傳回詳細的事件資料。

![記錄檔查詢](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

除了提供臨機操作分析以外，Log Analytics 中的查詢可儲存起來以供日後使用，也可以加入至您的 [OMS 儀表板](http://technet.microsoft.com/library/mt484090.aspx) (如下列範例所示)。

![OMS 儀表板](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>後續步驟
* 部署 [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx)。
* 註冊 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics)。  

