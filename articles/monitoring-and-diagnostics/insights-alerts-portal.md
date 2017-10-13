---
title: "建立 Azure 服務的警示 - Azure 入口網站 | Microsoft Docs"
description: "當符合您指定的條件時，觸發電子郵件、通知、呼叫網站 URL (Webhook) 或自動化。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb
ms.openlocfilehash: 3e09c145d35665ec1c2467b60f06191ac51a5c16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>在 Azure 監視器中為 Azure 服務建立計量警示 - Azure 入口網站
> [!div class="op_single_selector"]
> * [入口網站](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概觀
此文章說明如何使用 Azure 入口網站設定 Azure 計量警示。 

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* **計量值** - 當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。    
* **活動記錄事件** - 警示可在*每一個*事件上觸發，或是僅在發生特定事件時才觸發。 深入了解[活動記錄警示](monitoring-activity-log-alerts.md)。

您可以設定當計量警示觸發時執行下列動作︰

* 傳送電子郵件給服務管理員和共同管理員
* 傳送電子郵件至您指定的其他電子郵件
* 呼叫 webhook
* 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)

> [!NOTE]
> Azure 監視器現已支援公開預覽版的近乎即時計量警示。 這些警示會使用動作群組。 深入了解[近乎即時計量警示](monitoring-near-real-time-metric-alerts.md)。
>
>

您可以透過下列方式設定和取得有關計量警示規則的資訊

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則
1. 在 [入口網站](https://portal.azure.com/)中，找到您要監視的資源並選取。

2. 選取 [監視] 區段底下的 [警示] 或 [警示規則]。 不同資源的文字和圖示會有些許不同。  

    ![監視](./media/insights-alerts-portal/AlertRulesButton.png)

3. 選取 [新增警示]  命令，並填寫各欄位。

    ![新增警示](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. 為您的警示規則命名 ([名稱])，選擇將會顯示在電子郵件通知中的 [描述]。

5. 選取您要監視的 [計量]，然後為計量選擇 [條件] 和 [臨界值]。 同時選擇警示觸發程序之前，計量規則必須滿足的 [期間]。 例如，如果您使用「超過最後 5 分鐘」期間，且您的警示會尋找高於 80% 的 CPU，當 CPU 已持續 5 分鐘高於 80%，警示就會觸發。 一旦發生第一次觸發，它會在 CPU 持續 5 分鐘低於 80 % 時再次觸發。 系統每隔一分鐘就會測量一次 CPU 計量。

6. 如果您想要在警示引發時傳送電子郵件給系統管理員和共同管理員，請勾選 [電子郵件的擁有者...]  。

7. 如果您想要讓其他電子郵件信箱在警示引發時收到通知，在 [其他系統管理員電子郵件]  欄位新增它們。 以分號分隔多個電子郵件 - *email@contoso.com;email2@contoso.com*

8. 如果您想在警示引發時呼叫webhook，在[webhook]  欄位中放入有效的 URI。

9. 如果您使用 Azure 自動化，可以選取警示引發時執行的 Runbook。

10. 完成後選取 [確定]  建立警示。   

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="managing-your-alerts"></a>管理警示
一旦建立警示，您可以選取警示，並且︰

* 檢視圖表，其中顯示計量臨界值與前一天的實際值。
* 編輯或刪除警示。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。

## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 深入了解新的[近乎即時計量警示 (預覽)](monitoring-near-real-time-metric-alerts.md)
* 深入了解 [在警示中設定 webhook](insights-webhooks-alerts.md)。
* 深入了解[在活動記錄檔事件上設定警示](monitoring-activity-log-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](../automation/automation-starting-a-runbook.md)。
* 依照 [診斷記錄檔概觀](monitoring-overview-of-diagnostic-logs.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
