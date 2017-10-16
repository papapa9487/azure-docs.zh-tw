---
title: "建立 OMS Log Analytics 中的警示 | Microsoft Docs"
description: "Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文描述如何建立警示規則及詳細說明可以採取的各種動作。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-alert-rules-in-log-analytics"></a>使用 Log Analytics 中的警示規則
警示會由自動定期執行記錄搜尋的警示規則所建立。  如果結果符合特定準則，則會建立警示記錄。  此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。   

本文說明使用 OMS 入口網站建立和編輯警示規則的程序。  如需不同設定以及如何實作必要邏輯的詳細資訊，請參閱[了解 Log Analytics 中的警示](log-analytics-alerts.md)。

>[!NOTE]
> 您目前無法使用 Azure 入口網站建立或修改警示規則。 

## <a name="create-an-alert-rule"></a>建立警示規則

若要使用 OMS 入口網站建立警示規則，首先針對應叫用警示的記錄，建立記錄搜尋。  [警示]  按鈕將可供您使用，以便建立和設定警示規則。

>[!NOTE]
> 目前最多可以在 OMS 工作區中建立 250 個警示規則。 

1. 從 [OMS 概觀] 頁面，按一下 [記錄檔搜尋] 。
2. 建立新的記錄檔搜尋查詢，或選取已儲存的記錄檔搜尋。 
3. 按一下頁面頂端的 [警示] 以開啟 [新增警示規則] 畫面。
4. 使用底下[警示規則詳細資訊](#details-of-alert-rules)中的資訊，設定警示規則。
6. 按一下 [儲存]  完成警示規則。  該警示規則會立即開始執行。


## <a name="edit-an-alert-rule"></a>編輯警示規則
您可以在 Log Analytics [設定] 的 [警示] 功能表中，取得所有警示規則的清單。  

![管理警示](./media/log-analytics-alerts/configure.png)

1. 在 OMS 主控台中選取 [設定]  圖格。
2. 選取 [警示] 。

您可以在此檢視中執行多個動作。

* 選取旁邊的 [關閉]  來停用規則。
* 按一下旁邊的鉛筆圖示以編輯警示規則。
* 按一下旁邊的 **X** 圖示以移除警示規則。 

## <a name="details-of-alert-rules"></a>警示規則詳細資訊
當您在 OMS 入口網站中建立或編輯警示規則時，您可使用 [新增警示規則] 或 [編輯警示規則] 頁面。  下表說明此畫面中的欄位。

![警示規則](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>警示資訊
這些是警示規則的基本設定及其建立的警示。

| 屬性 | 說明 |
|:--- |:---|
| 名稱 | 用以識別警示規則的唯一名稱。 此名稱包含在規則所建立的任何警示中。  |
| 說明 | 警示規則的選擇性描述。 |
| 嚴重性 |此規則所建立之任何警示的嚴重性。 |

### <a name="search-query-and-time-window"></a>搜尋查詢和時間範圍
系統會評估可傳回記錄的搜尋查詢和時間範圍，以判斷是否應建立任何警示。

| 屬性 | 說明 |
|:--- |:---|
| 搜尋查詢 | 這是將要執行的查詢。  此查詢所傳回的記錄將會用來判斷是否要建立警示。<br><br>選取 [使用目前搜尋查詢]  以使用目前的查詢，或從清單中選取現有的已儲存搜尋。  查詢語法會提供於文字方塊中，您可以視需要加以修改。 |
| 時間範圍 |指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  可以是介於 5 分鐘與 24 小時之間的任何值。  應大於或等於警示頻率。  <br><br> 例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。 |

當您提供警示規則的時間範圍時，將會顯示該時間範圍內符合搜尋準則的現有記錄數目。  這可以協助您判斷可為您提供預期結果數目的頻率。

### <a name="schedule"></a>排程
定義搜尋查詢的執行頻率。

| 屬性 | 說明 |
|:--- |:---|
| 警示頻率 | 指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間範圍。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br><br>例如，請考慮 30 分鐘的時間範圍，以及 60 分鐘的頻率。  如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。  下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。  1:00 和 1:30 之間建立的任何記錄一律不會評估。 |


### <a name="generate-alert-based-on"></a>產生警示的依據
定義將根據搜尋查詢的結果進行評估以判斷是否應該建立警示的準則。  這些詳細資料將根據您選取的警示規則類型而不同。  您可以從[了解 Log Analytics 中的警示](log-analytics-alerts.md)中取得不同警示規則類型的詳細資料。

| 屬性 | 說明 |
|:--- |:---|
| 隱藏警示 | 當您開啟警示規則的隱藏功能時，此規則的動作會在建立新警示後停用並持續一段您所定義的時間。 採規則仍在執行中，並且會在符合準則時建立警示記錄。 這可讓您有時間更正問題，而不需執行重複的動作。 |

#### <a name="number-of-results-alert-rules"></a>結果數目的警示規則

| 屬性 | 說明 |
|:--- |:---|
| 結果數目 |如果查詢所傳回的記錄數目**大於**或**小於**您所提供的值，則會建立警示。  |

#### <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

| 屬性 | 說明 |
|:--- |:---|
| 彙總值 | 結果中的每個彙總值必須超過才會被視為違規的臨界值。 |
| 觸發警示的依據 | 要針對警示建立的違規數。  您可以跨結果集針對任何違規組合指定 [違規數總計]，或指定 [連續違規] 以要求違規必須發生於連續取樣中。 |

### <a name="actions"></a>動作
警示規則一律會在達到閾值時建立[警示記錄](#alert-records)。  您也可以定義一或多個要執行的回應，例如傳送電子郵件或啟動 Runbook。



#### <a name="email-actions"></a>電子郵件動作
電子郵件動作會傳送內含警示詳細資料的電子郵件給一或多位收件者。

| 屬性 | 說明 |
|:--- |:---|
| 電子郵件通知 |如果您要在警示觸發時傳送電子郵件，請指定 [是]  。 |
| 主旨 |電子郵件的主旨。  您無法修改郵件的內文。 |
| 收件者 |所有電子郵件收件者的地址。  如果您指定多個地址，則使用分號 (;) 分隔這些地址。 |

#### <a name="webhook-actions"></a>Webhook 動作
Webhook 動作可讓您透過單一 HTTP POST 要求叫用外部處理序。

| 屬性 | 說明 |
|:--- |:---|
| Webhook |如果您要在警示觸發時呼叫 Webhook，請指定 [是]  。 |
| Webhook URL |Webhook 的 URL。 |
| 包含自訂 JSON 承載 |如果您要使用自訂承載取代預設承載，請選取此選項。 |
| 輸入您的自訂 JSON 承載 |Webhook 的自訂承載。  如需詳細資料，請參閱上一節。 |

#### <a name="runbook-actions"></a>Runbook 動作
Runbook 動作可在 Azure 自動化中啟動 Runbook。 

>[!NOTE]
> 您必須在工作區中安裝自動化解決方案，才能啟用這個動作。 


| 屬性 | 說明 |
|:--- |:---|
| Runbook | 如果您要在警示觸發時啟動 Azure 自動化，請指定 [是]  。  |
| 自動化帳戶 | 指定從中選取 Runbook 的自動化帳戶。  這是連結至工作區的動作帳戶。 |
| 啟動 Runbook | 建立警示後，選取您想要啟動的 Runbook。 |
| 執行位置 | 選取 [Azure] 以在雲端執行 Runbook。  選取 [Hybrid Worker]，以在已安裝 [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) 的代理程式上執行 Runbook。  |




## <a name="next-steps"></a>後續步驟
* 安裝 [警示管理解決方案](log-analytics-solution-alert-management.md) ，以分析在 Log Analytics 中建立的警示以及從 System Center Operations Manager (SCOM) 收集的警示。
* 深入了解可產生警示的 [記錄檔搜尋](log-analytics-log-searches.md) 。
* 完成 [設定 Webook](log-analytics-alerts-webhooks.md) 和警示規則的逐步解說。  
* 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。

