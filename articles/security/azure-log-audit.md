---
title: "Azure 記錄與稽核 | Microsoft Docs"
description: "了解如何使用記錄資料來取得應用程式的深入解析。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 035f51d9f12e887d2017b058f0b0471870f411f7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="azure-logging-and-auditing"></a>Azure 記錄與稽核
## <a name="introduction"></a>簡介
### <a name="overview"></a>概觀
為了協助目前和潛在的 Azure 客戶了解和使用可在 Azure 平台上和周圍取得的各種安全性相關功能，Microsoft 開發了一系列的技術白皮書、安全性概觀、最佳作法及檢查清單。 主題範圍兼具廣度與深度，並會定期更新。 本文件是該系列的一部分，以下的＜摘要＞一節將會摘要說明。
### <a name="azure-platform"></a>Azure 平台
Azure 是一個公開且彈性的雲端服務平台，支援最廣泛的作業系統、程式設計語言、架構、工具、資料庫及裝置等選擇。

例如，您可以：
-   透過 Docker 整合執行 Linux 容器。

-   使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 建置應用程式。

-   建置 iOS、Android 和 Windows 裝置的後端。

Azure 公用雲端服務支援數百萬名開發人員和 IT 專家早已仰賴和信任的相同技術。

當您建置 IT 資產或將其移轉至雲端提供者時，您可以依賴該組織的能力來保護您的應用程式和資料，並利用他們所提供的安全性控制來管理您雲端架構資產的安全性。

Azure 的基礎結構設計涵蓋設備與應用程式，可同時裝載數以百萬計的客戶，並提供可靠的基礎供企業符合其安全性需求。 此外，Azure 也為您提供各式各樣可設定的安全性選項及加以控制的功能，讓您可以自訂安全性以符合部署的特殊需求。 本文件將協助您符合這些需求。

### <a name="abstract"></a>摘要
安全性相關事件的稽核和記錄及相關警示是有效資料保護策略中的重要元件。 安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用稽核來監視使用者活動、文件合規性、執行鑑識分析等等。 警示會在安全性事件發生時提供即時通知。

Microsoft Azure 服務和產品提供可設定的安全性稽核和記錄選項，以協助您識別安全性原則和機制間的差距，並解決這些差距以協助防止破壞。 Microsoft 服務提供下列一些 (在某些情況下，是全部) 選項︰集中監視、記錄及分析系統，以提供持續可見性；即時警示；以及報告來協助您管理裝置和服務所產生的大量資訊。

Microsoft Azure 記錄資料可以匯出到安全性事件和事件管理 (SIEM) 系統來進行分析，並與協力廠商稽核解決方案整合。

本技術白皮書介紹如何從裝載於 Azure 上的服務產生、收集及分析安全性記錄，並可協助您深入了解 Azure 部署的安全性。 本技術白皮書的範圍僅限於建置及部署於 Azure 中的應用程式和服務。

> [!Note]
> 此處包含的特定建議可能導致資料、網路或計算資源使用量增加，因而提高您的授權或訂用帳戶成本。

## <a name="types-of-logs-in-azure"></a>Azure 中的記錄類型
雲端應用程式相當複雜，且具有許多移動組件。 記錄會提供資料，以確保應用程式持續運作並以健全狀態執行。 它也可協助您預防潛在問題，或是針對過去所發生的問題進行疑難排解。 除此之外，您還可以使用記錄資料來深入解析應用程式。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。

Azure 會為每項 Azure 服務產生大量記錄。 這些記錄檔會分類為這幾種主要類型︰
-   **控制/管理記錄**可讓您看到 Azure Resource Manager 的 CREATE、UPDATE 和 DELETE 作業。 [Azure 活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)是這個記錄類型的一個範例。

-   **資料層面的記錄**可讓您看到使用 Azure 資源時所引發的事件。 這個記錄類型的範例是虛擬機器中的 Windows 事件系統、安全性和應用程式記錄，以及透過 Azure 監視器設定的[診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。


-   **已處理的事件**提供分析已代替您處理之事件/警示的相關資訊。 這個類型的範例是 [Azure 資訊安全中心警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)，[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)已在其中處理和分析您的訂用帳戶，並提供簡要的安全性警示。

下表列出 Azure 中提供的最重要記錄類型。

| 記錄分類 | 記錄類型 | 使用方式 | 整合 |
| ------------ | -------- | ------ | ----------- |
|[活動記錄](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Azure Resource Manager 資源上控制層面的事件| 讓您了解訂用帳戶中的資源所執行之作業。| REST API 與 [Azure 監視器](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure 診斷記錄](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|關於訂用帳戶中 Azure Resource Manager 作業的經常性資料|   讓您了解資源自行執行的作業| Azure 監視器、[資料流](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[AAD 報告](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-azure-portal)|記錄和報告|使用者登入活動，以及使用者和群組管理相關的系統活動資訊|[Graph API](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[雲端服務與虛擬機器](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Windows 事件記錄與 Linux Syslog|  在虛擬機器上擷取系統資料和記錄資料，並將該資料傳送到您所選擇的儲存體帳戶。|   使用 [WAD](https://docs.microsoft.com/en-us/azure/azure-diagnostics) (Windows Azure 診斷儲存體) 的 Windows 和 Azure 監視器上的 Linux|
|[Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/storage-analytics)|儲存體記錄，並提供儲存體帳戶的計量資料|讓您了解追蹤要求、分析使用趨勢，以及診斷儲存體帳戶的問題。|  REST API 或[用戶端程式庫](https://msdn.microsoft.com/en-us/library/azure/mt347887.aspx)|
|[NSG (網路安全性群組) 流程記錄](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON 格式，並顯示每個規則的輸出和輸入流程|檢視透過網路安全性群組輸入和輸出 IP 流量的相關資訊|[網路監看員](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insight](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)|記錄、例外狀況及自訂診斷|  多個平台上的 Web 開發人員所適用的應用程式效能管理 (APM) 服務。| REST API、[Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)|
|處理資料/安全性警示| Azure 資訊安全中心警示，OMS 警示| 資訊安全資訊與警示。|   REST API、JSON|

### <a name="activity-log"></a>活動記錄檔
[Azure 活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您了解訂用帳戶中的資源所執行之作業。 活動記錄之前稱做「稽核記錄」或「作業記錄」，因為它會報告訂用帳戶中[控制層面的事件 (英文)](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)。 您可以使用活動記錄，來判斷訂用帳戶中的資源上所採用之任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。 您也可以了解作業的狀態和其他相關屬性。 活動記錄檔不包含讀取作業 (GET)。

這裡的 PUT、POST、DELETE 是指資源上活動記錄包含的所有寫入作業。 例如，您可以在進行疑難排解時使用活動記錄來尋找錯誤，或是監視組織中使用者修改資源的方式。

![活動記錄檔](./media/azure-log-audit/azure-log-audit-fig1.png)


您可以使用 Azure 入口網站、[CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli)、PowerShell Cmdlet、[Azure 監視器 REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough)，從活動記錄擷取事件。 活動記錄的資料保留期限為 19 天。

整合案例
-   [建立電子郵件或可觸發關閉活動記錄檔事件的 webhook 警示。](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [將活動記錄串流至事件中樞](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)，以供第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。

-   使用 [PowerBI 內容套件](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)，在 PowerBI 中分析活動記錄。

-   [將活動記錄儲存到儲存體帳戶，以供封存或手動檢查](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log)。 您可以使用記錄設定檔來指定保留時間 (以天為單位)。

-   在 Azure 入口網站中查詢和檢視活動記錄。

-   透過 PowerShell Cmdlet、CLI 或 REST API 查詢活動記錄。

-   將活動記錄與記錄設定檔匯出至 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)。

您可以使用並非發出記錄的同一個訂用帳戶中的儲存體帳戶或[事件中樞命名空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive)。 進行此設定的使用者必須具有這兩個訂用帳戶的適當 [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) 存取權。
### <a name="azure-diagnostic-logs"></a>Azure 診斷記錄
Azure 診斷記錄是由資源發出的，提供有關該資源之作業的豐富、經常性資料。 這些記錄的內容會因資源類型而不同 (例如，[Windows 事件系統記錄檔](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)是 VM 之診斷記錄的一個分類，而 [Blob、表格及佇列記錄](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)是儲存體帳戶之診斷記錄檔的分類)，而且與活動記錄不同，後者可讓您深入了解在訂用帳戶中的資源上執行之作業。

![Azure 診斷記錄](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure 診斷記錄提供多個組態選項，亦即 Azure 入口網站、使用 PowerShell、命令列介面 (CLI) 和 REST API。

**整合案例**
-   將診斷記錄檔儲存到 [儲存體帳戶](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) 以利稽核或手動檢查。 您可以使用診斷設定來指定保留時間 (以天為單位)。

-   [將診斷記錄串流至事件中樞](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs)，以供第三方服務或自訂的分析解決方案 (如 [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)) 擷取。

-   以 [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 分析記錄。

**支援的服務、診斷記錄的結構描述，以及每個資源類型支援的記錄分類**


| 服務 | 結構描述與文件 | 資源類型 | 類別 |
| ------- | ------------- | ------------- | -------- |
|負載平衡器| [Azure 負載平衡器的記錄檔分析 (預覽)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|  LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|網路安全性群組|[網路安全性群組 (NSG) 的記錄檔分析](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|應用程式閘道|[應用程式閘道的診斷記錄功能](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|金鑰保存庫|[Azure 金鑰保存庫記錄](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure 搜尋服務|[啟用和使用搜尋流量分析](https://docs.microsoft.com/en-us/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[存取 Azure Data Lake Store 的診斷記錄](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|稽核|
|Data Lake Analytics|[存取 Azure Data Lake Analytics 的診斷記錄](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|稽核|
|||Microsoft.DataLakeAnalytics/accounts|要求|
|||Microsoft.DataLakeStore/accounts|要求|
|Logic Apps|[Logic Apps B2B 自訂追蹤結構描述](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch 診斷記錄](https://docs.microsoft.com/en-us/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure 自動化|[Azure 自動化的記錄檔分析](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|事件中樞|[Azure 事件中樞診斷記錄](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|串流分析|[作業診斷記錄](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|執行|
|||Microsoft.StreamAnalytics/streamingjobs|編寫|
|服務匯流排|[Azure 服務匯流排診斷記錄](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory 報告
Azure Active Directory (Azure AD) 包括您的目錄的安全性、活動和稽核報告。 [Azure Active Directory 稽核報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)可協助客戶識別其 Azure Active Directory 中發生的特殊權限動作。 特殊權限動作包括提高權限變更 (例如，角色建立或密碼重設)、原則設定變更 (例如密碼原則) 或目錄設定變更 (例如，網域同盟設定變更)。

報告會提供的稽核記錄包括事件名稱、執行動作的執行者、受變更影響的目標資源，以及日期和時間 (UTC)。 客戶能透過 [Azure 入口網站](https://portal.azure.com/)擷取其 Azure Active Directory 的稽核事件清單，如[檢視您的稽核記錄](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)中所述。 以下是包含的報告清單：

| 安全性報告 | 活動報告 | 稽核報告 |
| :--------------- | :--------------- | :------------ |
|從不明來源登入| 應用程式使用情況：摘要| 目錄稽核報告|
|在多次失敗後登入|  應用程式使用情況：詳細||
|從多個地理區域登入|    應用程式儀表板||
|從具有可疑活動的 IP 位址登入|   帳戶佈建錯誤||
|異常的登入活動|    個別使用者裝置||
|從可能受感染的裝置登入|   個別使用者活動||
|具有異常登入活動的使用者| 群組活動報告||
||密碼重設登錄活動報告||
||密碼重設活動|||

這些報告的資料對您的應用程式 (例如 SIEM 系統、稽核和商業智慧工具) 非常有用。 Azure AD 報告 API 透過一組以 REST 為基礎的 API 提供資料的程式設計方式存取。 您可以從各種程式設計語言和工具呼叫這些 [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started)。

Azure AD 稽核報告中的事件會保留 180 天。

> [!Note]
> 如需保留報告的詳細資訊，請參閱 [Azure Active Directory 報告保留原則](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)。

若是對於儲存其稽核事件較長的保留時間感興趣的客戶，報告 API 可用來定期將[稽核事件](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)提取至不同的資料存放區。

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>使用 Azure 診斷的虛擬機器記錄
[Azure 診斷](https://docs.microsoft.com/azure/azure-diagnostics)是 Azure 中可對部署的應用程式啟用診斷資料收集的功能。 您可以使用來自數個不同來源的診斷延伸模組。 目前支援 [Azure 雲端服務的 Web 和背景工作角色](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)。

![使用 Azure 診斷的虛擬機器記錄](./media/azure-log-audit/azure-log-audit-fig3.png)

執行 Microsoft Windows 和[Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) 的 [Azure 虛擬機器](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)。

您可以使用下列方式，在虛擬機器上啟用 Azure 診斷：

-   使用 Visual Studio，請參閱[使用 Visual Studio 來追蹤 Microsoft Azure 虛擬機器](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [從遠端在 Azure 虛擬機器上設定 Azure 診斷](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [使用 PowerShell 在 Azure 虛擬機器上設定診斷](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [使用 Azure Resource Manager 範本建立具有監視和診斷的 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>儲存體分析
[Azure 儲存體分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)會執行記錄，並提供儲存體帳戶的計量資料。 您可以使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。 儲存體分析記錄適用於 [Blob、佇列及表格服務](https://docs.microsoft.com/azure/storage/storage-introduction)。 儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。

這項資訊可用來監視個別要求，並診斷儲存體服務的問題。 系統會以最佳方式來記錄要求。 只有在對服務端點提出要求時，才會建立記錄項目。 例如，如果儲存體帳戶在其 Blob 端點中有活動，而不是在其表格或佇列端點中，則只會建立關於 Blob 服務的記錄。

若要使用儲存體分析，您必須針對想要監視的每個服務個別啟用它。 您可以在 [Azure 入口網站](https://portal.azure.com/)中啟用它；如需詳細資訊，請參閱[在 Azure 入口網站中監視儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)。 您也可以利用程式設計方式，透過 REST API 或用戶端程式庫來啟用儲存體分析。 使用 [設定服務屬性] 作業，分別為各個服務啟用儲存體分析。

彙總的資料會儲存於已知的 Blob (用於記錄) 和已知的資料表 (用於度量) 中，您可以使用 Blob 服務和資料表服務 API 來存取。

儲存體分析在儲存的資料量上有 20 TB 的限制，但此限制與儲存體帳戶的總限制無關。 所有記錄都會儲存在名為 $logs 之容器內的[區塊 Blob](https://docs.microsoft.com/azure/storage/storage-analytics) 中，該容器是在針對儲存體帳戶啟用儲存體分析時自動建立的。

> [!Note]
> 如需計費和資料保留原則的詳細資訊，請參閱[儲存體分析和計費](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)。
>
> [!Note]
> 如需儲存體帳戶限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](https://docs.microsoft.com/azure/storage/storage-scalability-targets)。

系統會記錄下列類型的已驗證與匿名要求。



| 已驗證  | 匿名|
| :------------- | :-------------|
| 成功的要求 | 成功的要求 |
|失敗的要求，包括逾時、節流、網路、授權和其他錯誤 | 使用共用存取簽章 (SAS) 的要求，包括失敗和成功的要求 |
| 使用共用存取簽章 (SAS) 的要求，包括失敗和成功的要求 |用戶端與伺服器的逾時錯誤 |
|   分析資料的要求 |    失敗的 GET 要求，錯誤碼為 304 (未修改) |
| 系統不會記錄儲存體分析本身所提出的要求 (例如，記錄檔的建立或刪除)。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄檔格式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)主題中。 | 系統不會記錄所有其他失敗的匿名要求。 記錄資料的完整清單記錄於[儲存體分析記錄作業和狀態訊息](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)及[儲存體分析記錄格式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)中。 |

### <a name="azure-networking-logs"></a>Azure 網路記錄
Azure 中的網路記錄和監視功能相當完善，主要涵蓋分類有二種：

-   [網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher)：網路監看員的功能隨附了案例式網路監視。 這項服務包括封包擷取、下一個躍點、IP 流量驗證、安全性群組檢視、NSG 流量記錄。 案例層級監視可提供端對端的網路資源檢視，而非個別的網路資源監視。

-   [資源監視](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring)：資源層級監視由診斷記錄、計量、疑難排解和資源健康狀態這四個功能所組成。 這些功能全是建置在網路資源層級。

![Azure 網路記錄](./media/azure-log-audit/azure-log-audit-fig4.png)

網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。

**NSG 流程記錄**：網路安全性群組的流程記錄可讓您擷取群組中安全性規則所允許或拒絕之流量的相關記錄。 這些流程記錄是以 JSON 格式撰寫，會顯示每個規則的輸出和輸入流程、套用流程的目標 NIC、有關流程的 5 個 Tuple 資訊 (來源/目的地 IP、來源/目的地連接埠、通訊協定)，以及流量是被允許或拒絕的。

### <a name="network-security-group-flow-logging"></a>網路安全性群組流程記錄

[網路安全性群組流程記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)是網路監看員的一項功能，可讓您檢視透過網路安全性群組輸入和輸出 IP 流量的相關資訊。 這些流程記錄是以 JSON 格式撰寫，會顯示每個規則的輸出和輸入流程、套用流程的目標 NIC、有關流程的 5 個 Tuple 資訊 (來源/目的地 IP、來源/目的地連接埠、通訊協定)，以及流量是被允許或拒絕的。

雖然流程記錄檔是以「網路安全性群組」為目標，但其顯示方式與其他記錄檔不同。 流程記錄只會儲存於儲存體帳戶內。

在其他記錄上看到的保留原則也同樣適用於流量記錄。 記錄的保留原則可設定為 1 天到 365 天。 如果未設定保留原則，則會永遠保留記錄檔。

**診斷記錄**

網路資源會定期和自發地建立事件，並記錄到儲存體帳戶、傳送到事件中樞或 Log Analytics。 這些記錄可讓您深入了解資源的健全狀況。 您可以在 Power BI 和 Log Analytics 等工具中檢視這些記錄。 若要了解如何檢視診斷記錄，請瀏覽 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)。

![診斷記錄檔](./media/azure-log-audit/azure-log-audit-fig5.png)

診斷記錄適用於[負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)、[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)、路由和[應用程式閘道](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。

網路監看員提供診斷記錄檢視。 此檢視包含所有支援診斷記錄的網路資源。 從這個檢視中，您可以方便且快速地啟用和停用網路資源。


除了先前的記錄功能，網路監看員目前具有下列功能：
- [拓撲](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview)：提供網路層級檢視，顯示資源群組中網路資源之間的各種相互連線和關聯。

- [變數封包擷取](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)：擷取進出虛擬機器的封包資料。 進階篩選選項和微調控制項 (例如，能夠設定時間和大小限制) 可讓您靈活擷取資料。封包資料可以 .cap 格式儲存在 Blob 存放區或本機磁碟上。

-   [IP 流程驗證](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)：根據流程資訊 5 個 Tuple 封包參數 (目的地 IP、來源 IP、目的地連接埠、來源連接埠和通訊協定) 檢查是否允許或拒絕封包。 如果封包遭到安全性群組拒絕，則會傳回拒絕封包的規則和群組。

-   [下一個躍點](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)：決定在 Azure 網路網狀架構中路由傳送之封包的下一個躍點，讓您得以診斷任何設定錯誤的使用者定義路由。

-   [安全性群組檢視](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview)：取得要在 VM 上套用的有效和已套用安全性規則。

-   [虛擬網路閘道和連線疑難排解](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)：能夠針對虛擬網路閘道和連線進行疑難排解。

-   [網路訂用帳戶限制](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits)：可讓您根據限制檢視網路資源使用量。

### <a name="application-insight"></a>Application Insight

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多個平台上的 Web 開發人員所適用的可延伸應用程式效能管理 (APM) 服務。 您可以使用它來監視即時 Web 應用程式。 它會自動偵測效能異常。 其中包括強大的分析工具可協助您診斷問題，並了解使用者實際如何運用您的應用程式。

 它是設計來協助您持續改善效能和可用性。

 它適用於各種不同平台上的應用程式，包括裝載在內部部署或雲端的 .NET、Node.js 和 J2EE。 它可與您的 devOps 程序整合，並有與各種開發工具的連接點。

![Application Insight](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights 是以開發小組為目標，以協助您了解您的應用程式的執行和使用情況。 它可監視︰

-   **要求率、回應時間和失敗率** - 找出哪些頁面在每天哪些時段最受歡迎，以及使用者位於何處。 查看哪些頁面的表現最好。 如果您的回應時間和失敗率隨著要求增加而提高，您或許有資源配置問題。

-   **相依比率、回應時間和失敗率** - 找出外部服務是否會使您降低效能。

-   **例外狀況**：分析彙總的統計資料，或挑選特定執行個體並深入了解堆疊追蹤和相關要求。 伺服器和瀏覽器例外狀況都會報告。

-   **頁面檢視和載入效能** - 由使用者的瀏覽器報告。

-   來自網頁的 **AJAX 呼叫** - 比率、回應時間和失敗率。

-   **使用者和工作階段計數**。

-   Windows 或 Linux 伺服器電腦中的**效能計數器**，例如 CPU、記憶體和網路使用量。

-   來自 Docker 或 Azure 的**主機診斷**。

-   來自您應用程式的**診斷追蹤記錄檔** - 讓您使追蹤事件與要求相互關聯。

-   您在用戶端或伺服器程式碼中自行撰寫的**自訂事件和計量**，可追蹤商業事件，例如售出的項目或獲勝的遊戲。

**整合案例及描述清單：**

| 整合案例 | 說明 |
| --------------------- | :---------- |
|[應用程式對應](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-app-map)|應用程式的元件，包含重要計量和警示。||
|[執行個體資料的診斷搜尋](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-diagnostic-search)| 搜尋和篩選事件，例如要求、例外狀況、相依性呼叫、記錄追蹤，以及頁面檢視。||
|[彙總資料的計量瀏覽器](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-metrics-explorer)|瀏覽、篩選和分割彙總的資料，例如，要求、錯誤和例外狀況的比率；回應時間、頁面載入時間。||
|[儀表板](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-dashboards#dashboards)|來自多個資源的交互式資料並與其他人員共用。 非常適用於多元件的應用程式，以及小組聊天室中的連續顯示。||
|[即時計量串流](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-live-stream)|當您部署新的組建時，請觀看這些近乎即時的效能指標，以確定一切如預期運作。||
|[分析](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics)|使用這個功能強大的查詢語言，回答有關您應用程式效能和使用方式的艱難問題。||
|[自動和手動警示](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-alerts)|如果在常見模式之外發生一些狀況，則自動警示會適應您應用程式的一般遙測和觸發程式模式。 您也可以在自訂或標準計量的特定層級上設定警示。||
|[Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)|查看程式碼中的效能資料。 從堆疊追蹤移至程式碼。||
|[Power BI](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-power-bi)|整合使用量計量和其他商業智慧。||
|[REST API](https://dev.applicationinsights.io/)|撰寫程式碼，對您的計量和未經處理資料執行查詢。||
|[連續匯出](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)|將送達的未經處理資料大量匯出至儲存體。||

### <a name="azure-security-center-alerts"></a>Azure 資訊安全中心警示
[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)會自動收集、分析及整合您 Azure 資源、網路和已連接的合作夥伴解決方案 (例如，防火牆和端點保護解決方案) 的記錄資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。

資訊安全中心威脅偵測的運作方式如下：從您的 Azure 資源、網路及已連線的協力廠商解決方案自動收集安全性資訊。 它會分析這項資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。 資訊安全中心的安全性警示會排定優先順序，並提供如何補救威脅的建議。

![Azure 資訊安全中心](./media/azure-log-audit/azure-log-audit-fig7.png)

資訊安全中心會運用進階安全性分析，其遠勝於以簽章為基礎的方法。 對於大量資料和[機器學習服務 (英文)](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 技術的突破適用於評估整個雲端網狀架構的事件：使用手動方式來偵測無法識別的威脅，以及預測攻擊的演化。 這些安全性分析包括︰

-   **整合性威脅情報**：運用 Microsoft 產品和服務、Microsoft 數位犯罪防治中心 (DCU)、Microsoft Security Response Center (MSRC) 以及外部摘要的全域威脅情報，尋找已知的不良執行者。

-   **行為分析**：套用已知模式來探索惡意行為。

-   **異常偵測**︰使用統計剖析來建置歷程基準。 它會對偏離已確立基準 (符合潛在攻擊向量) 的情況提出警示。


許多安全性作業和事件回應小組依賴安全性資訊及事件管理 (SIEM) 方案對安全性警示進行分級和調查做為起點。 利用 Azure 記錄整合，客戶可以將資訊安全中心警示，以及 Azure 診斷和 Azure 稽核記錄檔所收集的虛擬機器安全性事件，與其 Log Analytics 或 SIEM 方案以接近即時的方式進行同步處理。


## <a name="log-analytics"></a>Log Analytics

Log Analytics 是 [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 中的一項服務，可協助您收集和分析雲端和內部部署環境中的資源所產生的資料。 它可讓您在所有工作負載和伺服器之間 (無論其實體位置為何)，使用整合式搜尋和自訂儀表板輕易地分析數百萬筆記錄，提供您即時的深入資訊。

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Log Analytics 的中心是裝載在 Azure 雲端的 OMS 存放庫。 資料會藉由設定資料來源以及將方案新增至訂用帳戶中，而從連接的來源收集到存放庫。 資料來源和方案都會建立不同的記錄類型，它們具有自己的屬性集，但仍可以一起分析，以便查詢存放庫。 這可讓您使用相同的工具和方法，來處理由不同來源收集的不同類型的資料。

連接的來源是指產生 Log Analytics 所收集資料的電腦和其他資源。 這可包括安裝在直接連接之 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) 和 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) 電腦的代理程式，或[已連接的 System Center Operations Manager 管理群組](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)中的代理程式。 Log Analytics 也可以從 [Azure 儲存體](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)收集資料。

[資料來源](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) 是從每一個已連接來源收集的不同種類的資料。 這包括來自 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) 和 Linux 代理程式的事件和[效能資料](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)，以及像是 [IIS 記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)和[自訂文字記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)等來源。 您設定想要收集的每個資料來源，組態會自動傳遞到每一個已連接的來源。

有四種不同的方式可[收集 Azure 服務的記錄和計量](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)：
1.  Azure 診斷直達 Log Analytics (下表中的診斷)

2.  Azure 診斷至 Azure 儲存體至 Log Analytics (下表中的儲存體)

3.  Azure 服務的連接器 (下表中的連接器)

4.  使用指令碼來收集，再將資料公佈至 Log Analytics (下表中的空格，適用於未列出的服務)

| 服務 | 資源類型 | 記錄檔 | 度量 | 方案 |
| :------ | :------------ | :--- | :------ | :------- |
|應用程式閘道|  Microsoft.Network/<br>applicationGateways|  診斷|診斷|    [Azure 應用程式](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)[閘道分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application insights||     連接器|  連接器|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [ 連接器 (預覽) (英文)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|自動化帳戶|   Microsoft.Automation/<br>AutomationAccounts|    診斷||       [詳細資訊](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Batch 帳戶|    Microsoft.Batch/<br>batchAccounts|  診斷|    診斷||
|傳統雲端服務||       儲存體||       [詳細資訊](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|辨識服務|    Microsoft.CognitiveServices/<br>accounts|       診斷|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   診斷|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>accounts|   診斷|||
|事件中樞命名空間|   Microsoft.EventHub/<br>namespaces|  診斷|    診斷||
|IoT 中樞|  Microsoft.Devices/<br>IotHubs||     診斷||
|金鑰保存庫| Microsoft.KeyVault/<br>vaults|  診斷  || [金鑰保存庫分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-key-vault)|
|負載平衡器|    Microsoft.Network/<br>loadBalancers|    診斷|||
|Logic Apps|    Microsoft.Logic/<br>workflows|  診斷|    診斷||
||Microsoft.Logic/<br>integrationAccounts||||
|網路安全性群組|   Microsoft.Network/<br>networksecuritygroups|診斷||   [Azure 網路安全性群組分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|復原保存庫|   Microsoft.RecoveryServices/<br>vaults|||[Azure 復原服務分析 (預覽)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|搜尋服務|   Microsoft.Search/<br>searchServices|    診斷|    診斷||
|服務匯流排命名空間| Microsoft.ServiceBus/<br>namespaces|    診斷|診斷|    [服務匯流排分析 (預覽)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       儲存體||    [Service Fabric Analytics (Service Fabric 分析) (預覽)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>資料庫||       診斷||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|儲存體|||         指令碼| [Azure 儲存體分析 (預覽)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|虛擬機器|  Microsoft.Compute/<br>virtualMachines|  擴充功能|  分機||
||||診斷||
|虛擬機器擴展集|   Microsoft.Compute/<br>virtualMachines    ||診斷||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Web 伺服器陣列|Microsoft.Web/<br>serverfarms||   診斷
|網站| Microsoft.Web/<br>sites ||      診斷|    [詳細資訊](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>與內部部署之 SIEM 系統整合的記錄
[Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324)可讓您將來自 Azure 資源的未經處理記錄，整合到內部部署**安全性資訊及事件管理 (SIEM) 系統**內。

![記錄整合](./media/azure-log-audit/azure-log-audit-fig9.png)

Azure 記錄整合會從您的 Windows (WAD) 虛擬機器、Azure 活動記錄，Azure 資訊安全中心警示和 Azure 資源提供者記錄收集 Azure 診斷。 這項整合提供您內部部署或在雲端中所有資產統一的儀表板，以便您彙總、相互關聯、分析和警示安全性事件。



Azure 記錄整合目前支援整合 Azure 活動記錄、您 Azure 訂用帳戶中 Windows 虛擬機器的 Windows 事件記錄檔、Azure 資訊安全中心警示、Azure 診斷記錄及 Azure Active Directory 稽核記錄。

| 記錄類型 | 支援 JSON 的記錄分析 (Splunk、ArcSight、Qradar) |
| :------- | :-------------------------------------------------------- |
|AAD 稽核記錄|    yes|
|活動記錄檔| 是|
|ASC 警示 |是|
|診斷記錄 (資源記錄)|  是|
|VM 記錄|   是，透過轉送的事件，而非透過 JSON|


下表說明記錄分類和 SIEM 整合詳細資訊。

[開始使用 Azure 記錄整合](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started)：這個教學課程會逐步引導您安裝 Azure 記錄整合，以及整合來自 Azure WAD 儲存體的記錄、Azure 活動記錄、Azure 資訊安全中心警示以及 Azure Active Directory 稽核記錄。

整合案例

-   [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 此部落格文章說明如何設定 Azure 記錄整合，以搭配使用合作夥伴解決方案 Splunk、HP ArcSight 和 IBM QRadar。

-   [Azure 記錄整合常見問題集 (FAQ)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) - 此常見問題集會回答有關 Azure 記錄整合的問題。

-   [以 Azure 記錄整合來整合資訊安全中心警示](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) - 這份文件說明如何將資訊安全中心警示以及 Azure 診斷和 Azure 稽核記錄檔所收集的虛擬機器安全性事件，與您的 Log Analytics 或 SIEM 方案進行同步處理。

## <a name="next-steps"></a>後續步驟

- [稽核和記錄](https://www.microsoft.com/trustcenter/security/auditingandlogging)

藉由保持可見度並快速回應即時安全性警示來保護資料。

- [Azure 中的安全性記錄和稽核記錄收集 (英文)](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

您需要強制執行哪些設定，以確定您的 Azure 執行個體正在收集正確的安全性和稽核記錄。

- [設定網站集合的稽核設定](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

身為網站集合管理員，可擷取特定使用者所採取的動作歷程記錄，也可以擷取特定日期範圍內所採取的動作歷程記錄。 

- [搜尋 Office 365 安全與規範中心的稽核記錄](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

您可以使用 Office 365 安全與規範中心來搜尋整合的稽核記錄，以檢視您 Office 365 組織中的使用者和系統管理員活動。


