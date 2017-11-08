---
title: "將 Azure 診斷記錄檔串流至 Log Analytics | Microsoft Docs"
description: "了解如何將 Azure 診斷記錄串流至 Log Analytics 命名空間。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 9440bd7f872914887c1f6e50f08a3c273536fcf8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>將 Azure 診斷記錄檔串流至 Log Analytics
您可以使用入口網站、PowerShell Cmdlet 或 Azure CLI，以近乎即時的方式將 **[Azure 診斷記錄](monitoring-overview-of-diagnostic-logs.md)**串流至 Azure Log Analytics。

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>您可以在 Log Analytics 中使用診斷記錄來做什麼

Azure Log Analytics 是一個彈性的記錄搜尋和分析工具，可讓您深入了解從 Azure 資源產生的原始記錄資料。 一些功能包括：

* **記錄搜尋** - 撰寫您記錄資料的進階查詢、將來自各種來源的記錄相互關聯，甚至是產生可釘選至 Azure 儀表板的圖表。
* **警示** - 偵測一或多個事件符合特定查詢的情況，然後以電子郵件或 Webhook 呼叫進行通知。
* **解決方案** - 使用可讓您立即深入了解記錄資料的預先建立檢視和儀表板。
* **進階分析** - 套用機器學習和模式比對演算法，以識別出記錄所揭露的可能原因。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>啟用將診斷記錄串流至 Log Analytics 的功能
您可以透過入口網站或使用 [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings)，啟用以程式控制方式對診斷記錄進行串流的功能。 無論使用哪一種方式，您都需建立一個診斷設定，在其中指定 Log Analytics 工作區，以及要傳送至該工作區的記錄類別和計量。 診斷**記錄類別**是一種資源可以提供的記錄類型。

Log Analytics 工作區並不一定要與發出記錄檔的資源位於相同的訂用帳戶中，只要設定該設定的使用者對兩個訂用帳戶都具備適當的 RBAC 存取權即可。

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用入口網站串流診斷記錄
1. 在入口網站中，瀏覽至 Azure 監視器，然後按一下 [診斷設定]

    ![Azure 監視器的監視區段](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. 選擇性地依資源群組或資源類型篩選清單，然後按一下您要設定診斷設定的資源。

3. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「開啟診斷」。

   ![新增診斷設定 - 無現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   如果資源上已有設定，您將會看此資源上已設定的設定清單。 按一下「新增診斷設定」。

   ![新增診斷設定 - 現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. 為您的設定提供名稱，並選取 [傳送至 Log Analytics] 核取方塊，然後選取 Log Analytics 工作區。
   
   ![新增診斷設定 - 現有的設定](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

4. 按一下 [儲存] 。

過了幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且只要一產生新的事件資料，就會立即將診斷記錄串流至該工作區。 請注意，從發出事件到事件出現在 Log Analytics 中，之間最多會有 15 分鐘的間隔。

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
若要透過 [Azure PowerShell Cmdlet](insights-powershell-samples.md) 啟用串流功能，您可以使用 `Set-AzureRmDiagnosticSetting` Cmdlet 搭配下列參數︰

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

請注意，workspaceID 屬性會採用工作區的完整 Azure 資源識別碼，而不是採用 Log Analytics 入口網站中顯示的工作區識別碼/金鑰。

### <a name="via-azure-cli"></a>透過 Azure CLI
若要透過 [Azure CLI](insights-cli-samples.md) 啟用串流功能，您可以使用如下的 `insights diagnostic set` 命令︰

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

請注意，workspaceId 屬性會採用工作區的完整 Azure 資源識別碼，而不是採用 Log Analytics 入口網站中顯示的工作區識別碼/金鑰。

## <a name="how-do-i-query-the-data-in-log-analytics"></a>如何查詢 Log Analytics 中的資料？

在入口網站的 [記錄搜尋] 刀鋒視窗或 Log Analytics 隨附的 [進階分析] 體驗中，您可以在 [AzureDiagnostics] 資料表底下查詢「記錄管理」解決方案隨附的診斷記錄。 此外，也有[數個適用於 Azure 資源的解決方案](../log-analytics/log-analytics-add-solutions.md)，您可以安裝這些解決方案來立即深入了解要傳送到 Log Analytics 的記錄資料。


## <a name="next-steps"></a>後續步驟
* [深入了解 Azure 診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)
