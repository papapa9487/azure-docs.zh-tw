---
title: "使用 Azure 報告工具記載個人資料的保護 | Microsoft Docs"
description: "如何使用 Azure 報告服務和技術來協助保護個人資料的隱私權。"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>使用 Azure 報告工具記載個人資料的保護

本文探討如何使用 Azure 報告服務和技術來協助保護個人資料的隱私權。

## <a name="scenario"></a>案例

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、亞得里亞海、波羅的海和不列顛群島的行程。 為了協助這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。

此公司使用 Microsoft Azure 來處理和儲存公司資料。 其中包含其全球客戶群的名稱、地址、電話號碼和信用卡資訊等個人識別資訊。 它也會包含傳統人力資源資訊，例如地址、電話號碼、稅務識別碼，以及有關各地公司員工的其他資訊。 此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫，其中包含的個人資訊可追蹤與目前和過去客戶的關係。

公司員工會從公司的遠端辦公室存取網路，而位於全球的旅行社會存取某些公司資源。

## <a name="problem-statement"></a>問題陳述

公司必須透過多層式安全性策略來保護客戶和員工的個人資料隱私權 (該策略使用 Azure 管理和安全性功能，對個人資的存取和處理施行嚴格控制)，而且必須能夠對內部和外部稽核者示範其防護措施。

## <a name="company-goal"></a>公司目標

在其深度防禦的安全性策略中，公司目標在於追蹤個人資料的所有存取和處理，並確保個人資料的適當隱私權保護已到位並有作用。

## <a name="solutions"></a>解決方案

Microsoft Azure 提供全方位的監視、記錄和診斷工具，協助追蹤及記錄與個人資料存取和處理、資料的地理流程，以及個人資料之第三方存取相關聯的活動和事件。 因為雲端中個人資料的安全性是共同的責任，所以 Microsoft 也會提供給客戶：

- 其自己處理客戶資料的詳細資訊

- 由 Microsoft 管理的安全性措施

- 其傳送客戶資料的位置和方式

- Microsoft 自有隱私權檢閱程序的詳細資料

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 的雲端式、多租用戶目錄和身分識別管理服務。 服務的登入和稽核報告功能提供您詳細的登入和應用程式使用活動資訊，以協助您監視及確保客戶和員工個人資料的適當存取。

活動報告類型有兩種︰

- [稽核活動報告/記錄](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)提供系統活動/工作的詳細記錄

- [登入活動報告/記錄](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)顯示執行稽核報告中所列之每個活動的人員

兩者併用，可以追蹤每項已執行工作及其執行人員的歷程記錄。 這兩種報告均可自訂和篩選。

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>如何存取稽核和安全性記錄？

您可以用三種不同的方式從 Active Directory 入口網站存取稽核和安全性記錄：透過 [活動] 區段 (選取 [稽核記錄] 或 [登入])，或在 Active Directory 中從 [管理] 之下的 [使用者和群組] 或 [企業應用程式]。 透過 Azure Active Directory 報告 API 也可以存取報告。 

1. 在 Azure 入口網站中，選取 [Azure Active Directory]。

2. 在 [活動] 區段中，選取 [稽核警示]。

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. 按一下工具列中的 [資料行] 來自訂清單檢視。

4.  選取清單檢視中的項目，即可查看所有可用的詳細資料。

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Azure Active Directory 報告還包含兩種類型的安全性報告：[標幟為有風險的使用者]和 [有風險的登入]，這可協助您監視 Azure 環境中的潛在風險。

如需報告服務的詳細資訊，請參閱 [Azure Active Directory 報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

如需 Azure Active Directory 中可用報告的特定資訊，請瀏覽 [Azure Active Directory 活動報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports)。 這個網站包含如何在入口網站中存取及使用[稽核記錄活動報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)和[登入活動報告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)的詳細資訊。 它也包含[標幟為有風險的使用者](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk)和[有風險的登入](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins)安全性報告的相關資訊。

有關如何以程式設計方式連線到 Azure Active Directory 報告的詳細資訊，請瀏覽 [Azure Active Directory 稽核 API 參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)。

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 可以[從 Azure 監視器收集資料](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage)，使它與其他資料相互關聯並提供其他分析。 Azure 監視器可收集和分析 Azure 環境的監視資料。 

Log Analytics 中的分析工具 (例如記錄搜尋、檢視和解決方案) 可處理所有收集的資料，進而為您提供整個環境的集中式分析。 Log Analytics 可彙總及分析 Windows 事件記錄、IIS 記錄和 Syslog，其有助於偵測可能會對未經授權的使用者公開個人資料的潛在個人資料缺口。

#### <a name="how-do-i-use-log-analytics"></a>如何使用 Log Analytics？

您可以透過 OMS 入口網站或 Azure 入口網站，從任何網頁瀏覽器存取 Log Analytics。 Log Analytics 包含可快速擷取及彙總存放庫中資料的查詢語言。 您可以建立及儲存記錄搜尋，直接在入口網站中分析資料。

若要在 Azure 入口網站中建立 Log Analytics 工作區，請執行下列作業：

1. 從 Marketplace 中的服務清單選取 **Log Analytics**。

2. 選取 [建立]，然後指定 OMS 工作區的名稱、選取您的訂用帳戶、資源群組、位置和定價層。

3. 按一下 [確定] 以顯示工作區清單。

4. 選取工作區以查看其詳細資料。

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

若要深入了解這項服務，請瀏覽 [Log Analytics 文件](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)。

瀏覽[開始使用 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)教學課程，以建立評估工作區並了解如何使用此服務的基本概念。

有關如何連線以使用 Log Analytics 搭配上述記錄的特定資訊，請瀏覽下列網頁：

[Log Analytics 中的 Windows 事件記錄資料來源](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Log Analytics 中的 IIS 記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Log Analytics 中的 Syslog 資料來源](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure 監視器/Azure 活動記錄 

[Azure 監視器](https://azure.microsoft.com/services/monitor/)可針對 Microsoft Azure 中的大多數服務提供基本等級的基礎結構計量與記錄。
監視功能可協助您取得 Azure 應用程式的深入解析。 Azure 監視器依賴 Azure 診斷擴充功能 (Windows 或 Linux) 來收集大多數應用程式等級的計量和記錄。 [Azure 活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)是您可以使用 Azure 監視器檢視的其中一項資源。 它會追蹤每個 API 呼叫，並提供有關在 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 中發生之活動的豐富資訊。
您可以搜尋活動記錄檔 (之前稱為「作業記錄」或「稽核記錄」)，以取得 Azure 基礎結構所見之資源的相關資訊。 

雖然活動記錄中所記錄的大部分資訊與效能和服務健康情況有關，但也有與資料保護相關的資訊。 您可以使用活動記錄來判斷 Azure 訂用帳戶中的資源上任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。

例如，當系統管理員刪除網路安全性群組 (這可能會影響個人資料的保護) 時，它會提供一筆記錄。 活動記錄項目會儲存在 Azure 監視器中長達 90 天。

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>如何使用 Azure 監視器所收集的資料？

有數種方式可使用活動記錄和其他 Azure 監視器資源中的資料。

- 您可以將資料即時串流到其他位置。

- 使用 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-introduction)及設定保留原則，即可讓資料的儲存期間超過預設值。

- 您可以使用 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)、[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)、[Microsoft PowerBI](https://powerbi.microsoft.com/) 或第三方視覺效果工具，將圖形和圖表中的資料視覺化。

- 您可以使用 Azure 監視器 REST API、CLI 命令、[PowerShell](https://docs.microsoft.com/powershell/) cmdlet 或 .NET SDK 來查詢資料。

若要開始使用 Azure 監視器，請在 Azure 入口網站中選取 [更多服務]。

1. 向下捲動至 [監視與管理] 區段中的 [監視器]。

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  監視器會在 [活動記錄] 檢視中開啟。

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

您可以建立並儲存一般篩選的查詢，然後將最重要的查詢釘選至入口網站儀表板，這樣只要發生符合您準則的事件時就會通知您。

1. 您可以依照資源群組、時間範圍和事件類別來篩選檢視。

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. 然後按一下 [釘選] 按鈕，即可將查詢釘選到入口網站儀表板。 這可協助您在服務上建立作業資料的單一資訊來源。 儀表板上會顯示查詢名稱和結果筆數。

您也可以使用監視器來檢視所有 Azure 資源的計量、設定診斷設定和警示，以及搜尋記錄。 有關如何使用 Azure 監視器和活動記錄的詳細資訊，請參閱[開始使用 Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)。

### <a name="azure-diagnostics"></a>Azure 診斷 

Azure 中的診斷功能可以從數個來源收集資料。 Windows 事件記錄 (其中包括安全性記錄) 在追蹤和記載個人資料的保護時特別有用。 安全性記錄可追蹤登入成功和失敗事件，以及權限變更、指出特定攻擊類型的偵測模式、安全性相關原則變更、安全性群組成員資格變更等等。

例如，事件識別碼 4695 會警示您，有人嘗試解除保護可稽核的受保護資料。 這屬於資料保護 API (DPAPI)，其可協助您保護資料，例如私密金鑰、預存認證和其他機密資訊。

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>如何啟用 Windows VM 的診斷擴充功能？

您可以使用 PowerShell 來啟用 Windows VM 的診斷擴充功能，以便收集記錄資料。 這麼做的步驟取決於您使用的部署模型 (Resource Manager 或傳統)。 若要在透過 Resource Manager 部署模型所建立的現有 VM 上啟用診斷擴充功能，您可以使用 [Set-AzureRMVMDiagnosticsExtension PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1)。

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* 是包含診斷組態之 XML 檔案的路徑。 如需在 VM 上啟用 Azure 診斷的詳細指示，請參閱[使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)。

Azure 診斷擴充功能可以將所收集的資料傳輸到 Azure 儲存體帳戶，或傳送到 Application Insights 之類的服務。 您可以接著使用資料進行稽核。

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>如何儲存和檢視診斷資料？

請務必記住，除非您將診斷資料傳輸至 Microsoft Azure 儲存體模擬器或 Azure 儲存體，否則不會永久儲存診斷資料。 若要在 Azure 儲存體中儲存和檢視診斷資料，請遵循下列步驟：

1. 在 ServiceConfiguration.cscfg 檔案中指定儲存體帳戶。 視資料類型而定，Azure 診斷可以使用 Blob 服務或資料表服務。 Windows 事件記錄會以資料表格式儲存。

2. 傳輸資料。 您可以透過組態檔要求傳輸診斷資料。 若為 SDK 2.4 和較舊版本，您也可以程式設計方式提出此要求。

3. 在 Visual Studio 中使用 [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)、[伺服器總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)，或在 Azure Management Studio 中使用 [Azure 診斷管理員](https://www.cerebrata.com/products/azure-diagnostics-manager)來檢視資料。

有關如何執行上述每個步驟的詳細資訊，請參閱[在 Azure 儲存體中儲存和檢視診斷資料](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)。

### <a name="azure-storage-analytics"></a>Azure 儲存體分析 

儲存體分析會記錄對儲存體服務之成功和失敗要求的詳細資訊。 此資訊可用來監視個別要求，這有助於記載儲存在服務中的個人資料存取權。 不過，根據預設，儲存體帳戶不會啟用儲存體分析記錄功能。 您可以在 Azure 入口網站中將它啟用。

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>如何設定儲存體帳戶的監視？

若要設定儲存體帳戶的監視，請執行下列作業：

1. 在 Azure 入口網站中選取 [儲存體帳戶]，然後選取您要監視的帳戶名稱。

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. 在 [監視] 區段中，選取 [診斷]。

3.  選取您想為每項服務監視的計量資料**類型** (Blob、資料表、檔案)。 若要指示 Azure 儲存體，將 Blob、資料表和佇列服務之讀取、寫入及刪除要求的診斷記錄儲存起來，請選取 [Blob 記錄、資料表記錄] 和 [佇列記錄]。

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. 使用底部的滑桿來設定 [保留] 原則 (1 – 365 的天數值)。 預設值為 7 天。

5. 選取 [儲存] 以套用組態設定。

儲存體記錄的記錄項目包含個別要求的下列資訊：

- 時間資訊，例如開始時間、端對端延遲和伺服器延遲。

- 儲存體作業的詳細資料，例如作業類型、用戶端存取之儲存體物件的索引鍵、成功或失敗，以及傳回至用戶端的 HTTP 狀態碼。

- 驗證詳細資料，例如用戶端所使用的驗證類型。

- 並行資訊，例如 ETag 值和上次修改的時間戳記。

- 要求和回應訊息的大小。

有關如何啟用儲存體分析記錄的詳細指示，請參閱[在 Azure 入口網站中監視儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)。

### <a name="azure-security-center"></a>Azure 資訊安全中心 

[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)會監視 Azure 資源的安全性狀態，以便防止和偵測威脅，並提供回應的建議。 它會提供數種方式，協助記載保護個人資料隱私權的安全性措施。

安全性健康情況監視可協助您確保符合安全性原則的規範。 安全性監視是一個主動式策略，可稽核您的資源，以識別不符合組織標準或最佳做法的系統。 您可以監視下列資源的安全性狀態：

- 計算 (虛擬機器和雲端服務)

- 網路 (虛擬網路)

- 儲存體和資料 (伺服器和資料庫稽核和威脅偵測、TDE、儲存體加密)

- 應用程式 (潛在安全性問題)

任何兩個類別中的安全性問題可能會對個人資料的隱私權造成威脅。

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>如何檢視 Azure 資源的安全性狀態？

資訊安全中心會定期分析 Azure 資源的安全性狀態。 您可以在儀表板的 [防護] 區段中，檢視它所識別的任何潛在安全性弱點。

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. 在 [防護] 區段中，選取 [計算] 圖格。 您會在這裡看到 [概觀]，以以及所有 VM 的 [虛擬機器] 狀態與其安全性狀態，還有由資訊安全中心監視之 Web 和背景工作角色的 [雲端服務] 清單。

2. 在 [概觀] 索引標籤中，第二個建議可檢視更多資訊。

3. 在 [虛擬機器] 索引標籤中選取 VM，以檢視其他詳細資料。

在 Azure 資訊安全中心啟用資料收集後，Microsoft Monitoring Agent 會自動佈建於已部署的所有現有和新的虛擬機器上。 從這個代理程式收集的資料會儲存在與您的訂用帳戶相關聯的現有 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 工作區或新的工作區中。

資訊安全中心所提供的[威脅情報報告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。 這些報告會提供實用資訊給您，協助分辨攻擊者的身分識別、目標、目前和過去攻擊活動，以及所使用的策略、工具和程序。 還包含緩和與修復資訊。

這些威脅報告的主要目的在於協助您有效地回應立即的威脅，並協助您採取後續措施以緩和問題。 當您為了報告和稽核目的而記載事件回應時，報告中的資訊也很實用。

威脅情報報告會以 .PDF 格式呈現，對於 Azure 資訊安全中心的每個安全性警示而言，其可透過 [已執行的可疑處理序] 刀鋒視窗的 [報告] 欄位中的連結存取。

如需如何檢視和使用威脅情報報告的詳細資訊，請參閱 [Azure 資訊安全中心威脅情報報告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。

## <a name="next-steps"></a>後續步驟：

[開始使用 Azure Active Directory 報告 API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[什麼是 Log Analytics？](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Microsoft Azure 中的監視概觀](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Azure 活動記錄簡介 (影片)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
