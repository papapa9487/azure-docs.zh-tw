---
title: "什麼是 Operations Management Suite (OMS) 中的 Log Analytics？ | Microsoft Docs"
description: "Log Analytics 是 Operations Management Suite (OMS) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的操作資料。  本文提供 Log Analytics 之不同元件的簡短概觀，以及詳細內容的連結。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2017
ms.author: bwren
ms.openlocfilehash: 9fcf23f5ff47bd7457e5afa69eb2b9b33e0bf0fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-log-analytics"></a>什麼是 Log Analytics？
Log Analytics 是 [Operations Management Suite \(OMS\)](../operations-management-suite/operations-management-suite-overview.md) 中的一項服務，可監視您的雲端和內部部署環境，以維護其可用性和效能。  它會收集您的雲端和內部部署環境中的資源所產生的資料，以及從其他監視工具提供橫跨多個來源的分析。  本文提供 Log Analytics 所提供值的簡短討論、其運作方式的概觀，並連結至更詳細的內容，以便您進一步探究。

## <a name="is-log-analytics-for-you"></a>您適合使用 Log Analytics 嗎？
如果您的 Azure 環境目前並未採用任何監視機制，您應該開始使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)，其可收集並分析 Azure 資源的監視資料。  Log Analytics 可以[從 Azure 監視器收集資料](log-analytics-azure-storage.md)，使它與其他資料相互關聯並提供其他分析。

如果您想要監視內部部署環境，或具有使用 Azure 監視器或 System Center Operations Manager 等服務的現有監視機制，則 Log Analytics 可帶來重要價值。  它可以直接從您的代理程式，也可以從其他工具將資料收集到單一存放庫中。  Log Analytics 中的分析工具 (例如記錄搜尋、檢視和解決方案) 可處理所有收集的資料，進而為您提供整個環境的集中式分析。


## <a name="using-log-analytics"></a>使用 Log Analytics
您可以透過 OMS 入口網站或 Azure 入口網站存取 Log Analytics，它可在任何瀏覽器中執行並且供您存取組態設定和多項工具來分析及處理收集到的資料。  從入口網站，您可以利用[記錄搜尋](log-analytics-log-searches.md)建構查詢以分析收集的資料、您可以使用由最有價值之搜尋的圖形檢視所自訂的[儀表板](log-analytics-dashboards.md)，和提供額外功能和分析工具的[方案](log-analytics-add-solutions.md)。

下圖取自 OMS 入口網站，其顯示的儀表板會顯示工作區中已安裝[解決方案](#add-functionality-with-management-solutions)的摘要資訊。  您可以按一下任何圖格，進一步切入至該解決方案的資料。

![OMS 入口網站](media/log-analytics-overview/portal.png)

Log Analytics 包含可快速擷取及彙總存放庫中資料的查詢語言。  您可以建立並儲存[記錄搜尋](log-analytics-log-searches.md)，直接在入口網站中分析資料，或讓記錄搜尋自動執行，以在查詢的結果表示重要狀況時建立警示。

![記錄搜尋](media/log-analytics-overview/log-search.png)

為了快速取得整體環境健全狀況的圖形化檢視，您可以將儲存記錄搜尋的視覺效果新增至您的 [儀表板](log-analytics-dashboards.md)。   

![儀表板](media/log-analytics-overview/dashboard.png)

為了在 Log Analytics 外分析資料，您可以將資料從 OMS 存放庫工具匯出到例如 [Power BI](log-analytics-powerbi.md) 或 Excel 的工具。  您也可以利用 [記錄搜尋 API](log-analytics-log-search-api.md) 建置運用 Log Analytics 資料的自訂方案，或與其他系統整合。

## <a name="add-functionality-with-management-solutions"></a>透過管理解決方案新增功能
[管理解決方案](log-analytics-add-solutions.md)可將功能新增至 OMS，以提供其他資料和分析工具給 Log Analytics。  它們也可以定義要收集的新記錄類型，可以使用記錄搜尋進行分析，或是藉由儀表板方案所提供的其他使用者介面進行分析。  以下的範例圖顯示[變更追蹤解決方案](log-analytics-change-tracking.md)

![資料追蹤方案](media/log-analytics-overview/change-tracking.png)

有些解決方案適用於各種功能，而其他解決方案則會以一致的方式新增。  您可以輕鬆地瀏覽可用的解決方案，並從方案庫或 Azure Marketplace [將它們新增至 OMS 工作區](log-analytics-add-solutions.md)。  許多將會自動部署並立即開始運作，而有些則可能需要適度設定。

![方案庫](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Log Analytics 元件
Log Analytics 的中心是裝載在 Azure 雲端的 OMS 存放庫。  資料會藉由設定資料來源以及將方案新增至訂用帳戶中，而從連接的來源收集到存放庫。  資料來源和方案都會建立不同的記錄類型，它們具有自己的屬性集，但仍可以一起分析，以便查詢存放庫。  這可讓您使用相同的工具和方法，來處理由不同來源收集的不同類型的資料。

![OMS 存放庫](media/log-analytics-overview/overview.png)

連接的來源是指產生 Log Analytics 所收集資料的電腦和其他資源。  這可以包括安裝在直接連接的 [Windows](log-analytics-windows-agents.md) 和 [Linux](log-analytics-linux-agents.md) 電腦的代理程式，或[已連線的 System Center Operations Manager 管理群組](log-analytics-om-agents.md)中的代理程式。  對於 Azure 資源，Log Analytics 會從 [Azure 監視器和 Azure 診斷](log-analytics-azure-storage.md)收集資料。

[資料來源](log-analytics-data-sources.md) 是從每一個已連接來源收集的不同種類的資料。  這包括來自 [Windows](log-analytics-data-sources-windows-events.md) 和 Linux 代理程式的[事件](log-analytics-data-sources-windows-events.md)和[效能資料](log-analytics-data-sources-performance-counters.md)，以及例如 [IIS 記錄](log-analytics-data-sources-iis-logs.md)和[自訂文字記錄](log-analytics-data-sources-custom-logs.md)等來源。  您設定想要收集的每個資料來源，組態會自動傳遞到每一個已連接的來源。

如果您有自訂需求，則可以使用 [HTTP 資料收集器 API](log-analytics-data-collector-api.md)，將資料從 REST API 用戶端寫入至存放庫。

## <a name="log-analytics-architecture"></a>Log Analytics 架構
Log Analytics 的部署需求非常少，因為中心元件託管於 Azure 雲端。  這包括存放庫，以及可讓您關聯與分析所收集資料的服務。  可以從任何瀏覽器存取入口網站，因此不需要用戶端軟體。

您必須在 [Windows](log-analytics-windows-agents.md) 和 [Linux](log-analytics-linux-agents.md) 電腦上安裝代理程式，但已經是[連線的 SCOM 管理群組](log-analytics-om-agents.md)成員的電腦則不需要其他代理程式。  SCOM 代理程式將會繼續與管理伺服器通訊，管理伺服器會將其資料轉送至 Log Analytics。  不過，某些方案需要代理程式以便直接與 Log Analytics 通訊。  每個方案的文件將詳述其通訊需求。

當您 [註冊 Log Analytics](log-analytics-get-started.md)時，會建立一個 OMS 工作區。  您可以將工作區視為唯一的 Log Analytics 環境，有其自己的資料存放庫、資料來源和方案。 您可以在訂用帳戶中建立多個工作區以支援多個環境，例如生產環境和測試環境。

![Log Analytics 架構](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>後續步驟
* [申請免費的 Log Analytics 帳戶](log-analytics-get-started.md) ，在自己的環境中測試。
* 檢視可用來將資料收集到 OMS 存放庫的不同 [資料來源](log-analytics-data-sources.md) 。
* [瀏覽方案庫中可用的方案](log-analytics-add-solutions.md)，將功能新增至 Log Analytics。

