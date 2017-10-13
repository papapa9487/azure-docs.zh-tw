---
title: "與 Operations Management Suite (OMS) 進行整合 | Microsoft Docs"
description: "除了使用 OMS 的標準功能，您還可以將它與其他管理應用程式和服務進行整合，以提供混合式管理環境、提供您環境的唯一自訂管理案例，或為您的客戶提供自訂管理體驗。  本文提供與 OMS 進行整合的不同選項及提供詳細技術資訊文章連結的概觀。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>與 Operations Management Suite (OMS) 進行整合
Operations Management Suite 是 Microsoft 的雲端型 IT 管理解決方案，可協助您管理並保護內部部署和雲端基礎結構。  除了使用 OMS 的標準功能，您還可以將它與其他管理應用程式和服務進行整合，以提供混合式管理環境、提供您環境的唯一自訂管理案例，或為您的客戶提供自訂管理體驗。  本文提供與 OMS 服務進行整合的不同選項及提供詳細技術資訊文章連結的概觀。 

## <a name="log-analytics"></a>Log Analytics
Log Analytics 收集的管理資料都會儲存到裝載於 Azure 的存放庫中。  記錄檔搜尋中可使用儲存在存放庫中的所有資料，這些記錄檔搜尋會跨極大量的資料提供快速分析。  整合需求可能會以新的資料填入存放庫，讓它可用於分析，或者是在存放庫中擷取資料，以提供新的視覺效果或與其他管理工具整合。

存放庫中的每一筆資料會以記錄的形式儲存。  當您填入存放庫時，應該將您的解決方案使用的記錄類型和其屬性的描述提供給使用者。  當您擷取資料時，需要您正在使用之資料的相關資訊。

![填入 OMS 存放庫](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>填入 Log Analytics 存放庫
有多種方式可填入 OMS 存放庫。  您所使用的方法將取決於各種因素，例如來源資料的所在位置、資料的格式，以及您需要支援的用戶端。  一旦資料儲存在存放庫後，則其收集方式將沒有差異。

下列章節說明用來填入 OMS 存放庫的不同選項。

#### <a name="connected-sources-and-data-sources"></a>連接的來源和資料來源
連接的來源是可以擷取 OMS 存放庫資料的位置。  資料來源和解決方案會在連接來源上執行，並定義所收集的特定資料。  如果您的應用程式將資料寫入這些資料來源其中之一，則您可以藉由設定資料來源收集它。  例如，如果您的應用程式建立 Syslog 事件，則可以藉由 Linux 代理程式上的 Syslog 資料來源收集它們。

* [Log Analytics 中的資料來源](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>解決方案
解決方案會擴充 OMS 的功能。  解決方案會從連線來源收集資料，或是它可能會執行存放庫中已收集記錄的分析。  Microsoft 所提供的每個解決方案都有個別文章，提供它所收集資料的相關詳細資料。

* [Log Analytics 中的解決方案](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP 資料收集器 API
Log Analytics HTTP 資料收集器 API 是 REST API，可讓您將 JSON 資料新增至 Log Analytics 存放庫。  當您的應用程式未透過其他資料來源或解決方案其中之一提供資料時，您可以利用這個 API。  它可以用來從任何用戶端填入存放庫，這些用戶端可呼叫 API 且不會依賴任何資料來源或方案的集合排程。

* [Log Analytics HTTP 資料收集器 API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>從 Log Analytics 存放庫擷取資料
有多種方式可從 OMS 存放庫擷取資料。  您可能想要讓使用者使用 OMS 主控台擷取資料，並為他們提供各種不同的視覺效果和分析。  您也可以從其他管理解決方案等外部處理序中擷取資料。

#### <a name="log-searches"></a>記錄檔搜尋
儲存在 OMS 存放庫中的所有資料都可透過記錄搜尋提供使用。  使用者可在 OMS 主控台執行他們自己的臨機操作分析，或針對特定記錄檔搜尋建立具有視覺效果的儀表板。  解決方案可以根據預先定義的搜尋包含具有視覺效果的自訂檢視。  您可以使用 Log Search API，從外部應用程式或管理工具存取 OMS 存放庫中的資料。  

* [Log Analytics 中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)
* [Log Analytics 記錄檔搜尋 REST API](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics Cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>自訂檢視
檢視設計工具可讓您在 OMS 主控台建立自訂檢視，為使用者提供解決方案中的資料視覺效果與分析。  每個檢視都包含主控台主頁面上所顯示的圖格，和根據您所定義之記錄搜尋的任意數目視覺效果組件。

* [Log Analytics 檢視設計工具](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics 可以自動將資料從 OMS 儲存機制匯出到 Power BI，讓您可以利用其視覺效果和分析工具。  它會在排程上執行這個匯出，讓資料處於最新狀態。 

* [將 Log Analytics 資料匯出至 Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>自動化
OMS 可以自動化程序來回應收集到的資料，或執行其他管理功能。  它可從您的應用程式收集資料，並將它插入 OMS 存放庫，或者您可自動化校正存放庫中已知的問題，以回應存放庫中找到的資料。 

![自動化](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook
Azure 自動化中的 Runbook 會在 Azure 雲端中執行 PowerShell 指令碼和工作流程。  您可以使用它們來管理 Azure 中的資源，或可從雲端存取的任何其他資源。  也可以使用混合式 Runbook 背景工作角色在本機資料中心執行 Runbook。  您可以使用多種方法，例如 PowerShell 或自動化 API，從 Azure 入口網站或從外部處理程序啟動 Runbook。

* [在 Azure 自動化中啟動 Runbook](../automation/automation-starting-a-runbook.md)
* [Azure 自動化 Cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)
* [自動化 REST API](https://msdn.microsoft.com/library/mt662285.aspx)
* [自動化 .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alerts
警示規則會根據排程自動執行記錄搜尋。  如果結果符合特定準則，產生的警示可以在 Azure 自動化中啟動 Runbook，或呼叫可以啟動外部處理序的 webhook。  這兩個回應都可包括警示的詳細資料，包含記錄搜尋中傳回的資料。

* [Log Analytics 中的警示](../log-analytics/log-analytics-alerts.md)
* [Log Analytics 警示 API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>備份與站台復原
Azure 備份與 Site Recovery 提供保護您企業資料，並確認伺服器和應用程式可用性的服務。  您可以利用這些服務來執行諸如為應用程式提供備份服務，或啟動虛擬機器的容錯移轉等案例。

* [Azure 備份 Cmdlet](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Azure Site Recovery Cmdlet](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>自訂解決方案
您可以將整合邏輯封裝至在您的工作區或客戶的工作區中執行的自訂解決方案。  除了其他可提供完整管理案例的資源之外，您的方案可以包含本文中任何的整合方法。  當移除解決方案時解決方案中的資源會予以封裝，它所建立的所有資源會從 OMS 工作區和 Azure 訂用帳戶中移除。

例如，您的解決方案可包含要收集並處理資料的自動化 Runbook，然後使用 HTTP 資料收集器 API 填入 Log Analytics 存放庫。  您也可以包含會顯示及分析所收集資料的自訂檢視。  

* 建立自訂解決方案 (即將推出)    

## <a name="next-steps"></a>後續步驟
* 參考 [OMS SDK](operations-management-suite-sdk.md) 以取得關於自動化 OMS 服務的技術資訊。  

