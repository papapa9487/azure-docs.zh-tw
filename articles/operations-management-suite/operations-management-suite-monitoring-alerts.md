---
title: "Microsoft 監視產品中的警示管理 | Microsoft Docs"
description: "警示表示有一些需要系統管理員注意的問題。  本文說明如何在 System Center Operations Manager (SCOM) 和 Log Analytics 中建立和管理警示的差異，並提供運用這兩項產品做為混合式警示管理策略的最佳作法。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>使用 Microsoft 監視管理警示
警示表示有一些需要系統管理員注意的問題。  在如何建立警示、如何管理和分析警示以及在偵測到嚴重問題時如何通知您的方面，System Center Operations Manager (SCOM) 與 Operations Management Suite (OMS) 中的 Log Analytics 之間有一些顯著的差異。

## <a name="alerts-in-operations-manager"></a>Operations Manager 中的警示
SCOM 中的警示是由個別規則或監視器所產生，表示發生特定問題。  規則產生警示以表示發生一些與受管理物件健康狀態無直接相關的重要問題時，如果監視器進入錯誤狀態，則監視器可能會產生警示。  管理組件包含各種工作流程，可建立其所管理的應用程式或服務的警示。  在設定新管理組件的程序期間會進行調整，確保您不會收到您不認為重要之問題的過多警示。

![SCOM 警示檢視](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM 提供系統管理員可以變更警示狀態的完整警示管理，因為他們會一起合作來解決問題。  解決問題之後，系統管理員會設定在顯示作用中警示的檢視中不再顯示警示時關閉警示。  監視器恢復狀況良好狀態時，就可以自動解決監視器所產生的警示。

![警示狀態](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Log Analytics 中的警示
定期自動執行的記錄檔查詢會建立 Log Analytics 中的警示。  您可以從任何記錄檔查詢來建立警示規則。  如果查詢傳回符合所指定準則的結果，則會建立警示。  這可能是建立警示的特定查詢，如果偵測到特定事件，或您可以使用一般查詢，來尋找與特定應用程式相關的任何錯誤事件。

Log Analytics 警示會以事件形式寫入 OMS 儲存機制中，而且可以使用記錄檔查詢進行擷取。  它們的狀態與 SCOM 事件的狀態不同，因此，您可以表示問題的解決時間。

![OMS 警示](media/operations-management-suite-monitoring-alerts/oms-alert.png)

使用 SCOM 做為 Log Analytics 的資料來源時，會將建立和修改的 SCOM 警示寫入 OMS 儲存機制中。  

![SCOM 警示](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[警示管理解決方案](http://technet.microsoft.com/library/mt484092.aspx) 提供作用中警示和數個常見查詢來擷取多組不同警示的摘要。  這可讓您更有效地分析的警示數目多於 SCOM 中報告的警示數目。  您可以從摘要向下切入詳細資料，並建立臨機操作查詢來擷取多組不同的警示。

![警示管理解決方案](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>通知
SCOM 中的通知會將郵件或文字傳送給您，以回應符合特定準則的警示。  您可以建立不同的通知訂用帳戶，這些通知訂用帳戶會通知不同的人 (視所監視物件、警示嚴重性、偵測到問題的種類或時間的這類準則而定)。

少量訂用帳戶可以用來實作大量管理組件的完整通知策略。

![SCOM 警示](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

如果已透過在每個 [警示規則](http://technet.microsoft.com/library/mt614775.aspx)上設定電子郵件通知動作來建立警示，則 Log Analytics 可以透過郵件通知您。  SCOM 無法使用單一規則來訂閱多個警示。  因為 OMS 未提供任何預先設定的警示規則，所以您也需要建立自己的警示規則。

![Log Analytics 警示](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

您無法完全管理 Log Analytics 中的 SCOM 警示，因為您只能在 Operations 主控台中進行修改。  Log Analytics 可做為警示管理程序的一部分，以提供 SCOM 單獨沒有的分析工具。

## <a name="alert-remediation"></a>警示補救
[補救](http://technet.microsoft.com/library/mt614775.aspx) 指的是嘗試自動更正警示所識別的問題。

SCOM 可讓您執行診斷和復原，以回應監視器進入狀況不良狀態。  這與建立警示的監視器同時發生。  診斷和復原通常實作為代理程式上所執行的指令碼。  診斷會嘗試收集有關偵測到問題的詳細資訊，復原則會嘗試更正問題。

Log Analytics 可讓您啟動 [Azure 自動化 Runbook](https://azure.microsoft.com/documentation/services/automation/) 或呼叫 webhook，以回應 Log Analytics 警示。  Runbook 可以包含 PowerShell 中所實作的複雜邏輯。  指令碼是在 Azure 中執行，而且可以從雲端存取任何 Azure 資源或外部資源。  Azure 自動化確實可以在本機資料中心的伺服器上執行 Runbook，但是啟動 Runbook 以回應 Log Analytics 警示時，目前無法使用這項功能。

SCOM 中的復原以及 OMS 中的 Runbook 都可以包含 PowerShell 指令碼，但復原較難建立和管理，因為它們必須包含在管理組件內。  Runbook 會儲存在 Azure 自動化中，而 Azure 自動化提供功能來撰寫、測試和管理 Runbook。

如果您使用 SCOM 做為 Log Analytics 的資料來源，則可以使用記錄檔查詢來建立 Log Analytics 警示，以擷取 OMS 儲存機制中所儲存的 SCOM 警示。  這可讓您執行 Azure 自動化 Runbook，以回應 SCOM 警示。  當然，因為 Runbook 是在 Azure 中執行，所以這不是復原內部部署問題的可行策略。

## <a name="next-steps"></a>後續步驟
* 了解 [System Center Operations Manager (SCOM) 中警示](https://technet.microsoft.com/library/hh212913.aspx)的詳細資料。

