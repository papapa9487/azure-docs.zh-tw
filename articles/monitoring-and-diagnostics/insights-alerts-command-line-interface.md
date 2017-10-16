---
title: "建立 Azure 服務的警示 - 跨平台 CLI | Microsoft Docs"
description: "當符合您指定的條件時，觸發電子郵件、通知、呼叫網站 URL (Webhook) 或自動化。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 92246a8da73a244a1c9a924bed55711d71a20fd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>在 Azure 監視器中為 Azure 服務建立計量警示 - 跨平台 CLI
> [!div class="op_single_selector"]
> * [入口網站](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>概觀
此文章將說明如何使用跨平台命令列介面 (CLI) 設定 Azure 計量警示。

> [!NOTE]
> 自 2016 年 9 月 25 日起，「Azure 監視器」是以前所謂「Azure Insights」的新名稱。 不過，命名空間和下列命令中仍有 "insights"。
>
>

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* **計量值** - 當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。    
* **活動記錄檔事件** - 警示可在*每一個*事件上觸發，或是僅在發生特定事件時才觸發。 若要深入了解活動記錄檔警示，請[按一下這裡](monitoring-activity-log-alerts.md)

您可以設定當計量警示觸發時執行下列動作︰

* 傳送電子郵件給服務管理員和共同管理員
* 傳送電子郵件至您指定的其他電子郵件
* 呼叫 webhook
* 啟動執行 Azure Runbook (現階段只能從 Azure 入口網站啟動)

您可以透過下列方式設定和取得有關計量警示規則的資訊

* [Azure 入口網站](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

輸入命令並在結尾加上 -help，可以收到命令的說明。 例如：

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>使用 CLI 建立警示規則
1. 執行必要條件，然後登入 Azure。 請參閱 [Azure 監視器 CLI 範例](insights-cli-samples.md)。 簡單地說，安裝 CLI，執行命令。 這些命令可讓您登入、顯示您正在使用的訂用帳戶、準備執行 Azure 監視器命令。

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. 若要列出資源群組中的現有規則，使用 **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;* 格式

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. 若要建立規則，您需要先取得幾項重要資訊：
  * 您想要為其設定警示的 **資源識別碼**
  * 該資源可使用的 **計量定義**

     取得資源識別碼的方法之一，是使用 Azure 入口網站。 假設已建立資源，在入口網站中選取它。 然後在下一個刀鋒視窗中，選取 [設定] 區段下的 [屬性]。 [資源識別碼]  是下一個刀鋒視窗中的欄位。 另一種方法是使用 [Azure 資源總管](https://resources.azure.com/)。

     以下是 Web 應用程式的範例資源識別碼：

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     若要取得先前資源範例中那些計量的可用計量和單位的清單，使用下列的 CLI 命令︰  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* 是可用度量單位的資料粒度 (間隔 1 分鐘)。 使用不同的資料粒度可提供您不同的計量選項。
4. 若要建立以計量為基礎的警示規則，使用下列格式︰

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    下列範例會設定網站資源的警示。 只要持續 5 分鐘有收到任何流量，就會觸發警示，在這之後，5 分鐘沒收到任何流量也會觸發警示。

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. 若要在計量警示引發時建立 Webhook 或傳送電子郵件，請先建立電子郵件和/或 Webhook。 然後立即建立規則。 您無法使用 CLI 將 webhook 或電子郵件與已建立的規則建立關聯。

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. 您可以藉由查看個別規則來確認您的警示已正確建立。

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. 若要刪除規則，使用此格式︰

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    這些命令會刪除本文中先前建立的規則。

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 深入了解 [在警示中設定 webhook](insights-webhooks-alerts.md)。
* 深入了解[在活動記錄檔事件上設定警示](monitoring-activity-log-alerts.md)。
* 深入了解 [Azure 自動化 Runbook](../automation/automation-starting-a-runbook.md)。
* 依照 [收集診斷記錄檔概觀](monitoring-overview-of-diagnostic-logs.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照 [計量集合概觀](insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
