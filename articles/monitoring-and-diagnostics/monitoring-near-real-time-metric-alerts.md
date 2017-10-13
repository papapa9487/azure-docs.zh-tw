---
title: "Azure 監視器中近乎即時計量警示 | Microsoft Docs"
description: "近乎即時計量警示可讓您以極高的頻率 (小至 1 分鐘) 監視 Azure 資源計量。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>近乎即時計量警示 (預覽)
Azure 監視器現在支援一種新的計量警示，稱為近乎即時計量警示 (預覽)。 此功能目前為公開預覽狀態。
這些警示在數方面都與一般計量警示不同

- **改善延遲** - 近乎即時計量警示能以每 1 分鐘一次這樣快的頻率監視計量值中的變更。
- **對計量條件有更好的控制** - 近乎即時計量警示可讓使用者定義更豐富的警示規則。 警示現在支援監視計量的最大值、最小值、平均與總計值。
- **可監視多個計量的整合監視功能** - 近乎即時計量警示能以單一值監視多個計量 (目前支援兩個)。 若兩個計量在指定的時間內都達到其個別閾值，則會觸發警示。
- **模組化通知系統** - 近乎即時計量警示使用[動作群組](monitoring-action-groups.md)。 此功能可讓使用者以模組化方式建立動作，並針對多個警示規則重複使用它們。

> [!NOTE]
> 近乎即時計量警示功能目前處於公開預覽狀態。 功能與使用者體驗可能會變更。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>我可以為哪種資源建立近乎即時計量警示？
近乎即時計量警示支援的完整資源類型清單：

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>建立近乎即時計量警示
目前，近乎即時計量警示只能透過 Azure 入口網站來建立。 即將支援透過 PowerShell、命令列介面 (CLI) 與 Azure 監視器 REST API 來設定近乎即時計量警示。

1. 在 [入口網站](https://portal.azure.com/)中，找到您要監視的資源並選取。 此資源應該是[上一節](#what-resources-can-i-create-near-real-time-metric-alerts-for)所列的其中一種資源類型。 您也可以從 [監視器] > [警示] 針對目前支援的所有資源類型執行相同的動作。

2. 選取 [監視] 區段底下的 [警示] 或 [警示規則]。 不同資源的文字和圖示會有些許不同。
   ![監視](./media/insights-alerts-portal/AlertRulesButton.png)

3. 按一下 [加入近乎即時計量警示 (預覽)] 命令。 若該命令呈現灰色，請確定您已在篩選器中選取該資源。

    ![[加入近乎即時計量警示] 按鈕](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. 為您的警示規則命名 ([名稱])，選擇將會顯示在電子郵件通知中的 [描述]。
5. 選取您要監視的 [計量]，然後為該計量選擇 [條件]、[時間彙總], 與 [閾值] 值。 (選擇性) 選取您要監視的另一個 [計量]，然後為第二個計量選擇 [條件]、[時間彙總], 與 [閾值] 值。 

    ![加入近乎即時計量警示1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![加入近乎即時計量警示2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. 選擇警示觸發之前，計量規則必須滿足的 [期間]。 例如，如果您使用「超過最後 5 分鐘」期間，且您的警示會尋找高於 80% 的 CPU (與超過 500 MB 的 NetworkIn)，當 CPU 已持續 5 分鐘高於 80%，警示就會觸發。 一旦發生第一次觸發，它會在 CPU 持續 5 分鐘低於 80 % 時再次觸發。 系統會根據 [評估頻率] 來評估警示


6. 從下拉式清單挑選適當的 [嚴重性]。

7. 指定您要使用新的或現有的 [動作群組]。

8. 若選擇建立**新的** 動作群組，請為該動作群組指定一個簡短名稱，指定動作 (簡訊、電子郵件、Webhook) 並填寫個別詳細資料。


8. 完成後選取 [確定]  建立警示。   

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="managing-near-real-time-metric-alerts"></a>管理近乎即時計量警示
一旦建立警示，您可以選取警示，並且︰

* 檢視圖表，其中顯示計量臨界值與前一天的實際值。
* 編輯或刪除警示。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。



