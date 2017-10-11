---
title: "OMS Log Analytics 中的 Webhook 警示範例 | Microsoft Docs"
description: "您可以執行以回應 Log Analytics 警示的動作之一是 *webhook*，它可讓您透過單一 HTTP 要求來叫用外部處理序。 本文透過一個範例練習在 Log Analytics 中利用 Slack 建立 webhook 動作。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>在 OMS Log Analytics 中建立警示 webhook 動作以傳送訊息給 Slack
您可以執行以回應 [Log Analytics 警示](log-analytics-alerts.md) 的動作之一是 *webhook*，它可讓您透過單一 HTTP 要求來叫用外部處理序。  您可以在 [Log Analytics 中的警示](log-analytics-alerts.md)

在本文中，我們將透過一個範例，練習在 Log Analytics 中利用 Slack (訊息服務) 建立 webhook 動作。

> [!NOTE]
> 您必須有 Slack 帳戶才能完成此範例。  您可以在 [slack.com](http://slack.com)註冊免費帳戶。
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>步驟 1 - 在 Slack 中啟用 webhook
1. 在 [slack.com](http://slack.com)上登入 Slack。
2. 在左窗格的 [通道] 區段中選取通道。  這是訊息將傳往的通道。  您可以選取其中一個預設通道，例如 **general** 或 **random**。  在實際執行案例中，您很有可能會建立特殊通道，例如 **criticalservicealerts**。 <br>
   
   ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. 按一下 [加入應用程式或自訂整合]  開啟 [應用程式目錄]。
4. 在搜尋方塊中輸入 *webhook*，然後選取 [傳入 WebHook]。 <br>
   
   ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. 按一下小組名稱旁邊的 [安裝]  。
6. 按一下 [加入組態] 。
7. 選取您要用於此範例的通道，然後按一下 [加入連入 Webhook 整合] 。  
8. 複製 [Webhook URL] 。  您將會此資訊貼入 [警示組態] 中。 <br>
   
    ![Slack channels](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>步驟 2 - 在 Log Analytics 中建立警示規則
1. [建立警示規則](log-analytics-alerts.md) 。
   * 查詢： ```    Type=Event EventLevelName=error ```
   * 檢查此警示的間隔︰5 分鐘
   * 結果數目：大於 10
   * 在此時間範圍內︰60 分鐘
   * 對 [Webhook] 選取 [是]，對其他動作選取 [否]。
2. 將 Slack URL 貼入 [Webhook URL]  欄位中。
3. 選取選項來 [加入自訂 JSON 承載] 。
4. Slack 需要有 JSON 格式化並加上參數 *text* 的承載。  這是在它建立的訊息中顯示的文字。  您可以透過 *#* 符號使用一或多個警示參數，如下列範例所示。
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![example JSON payload](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. 按一下 [儲存]  以儲存警示規則。
6. 等待足夠的時間來建立警示，然後檢查 Slack 是否出現類似如下的訊息。
   
   ![example webhook in Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Slack 的進階 webhook 承載
您可以使用 Slack 來廣泛自訂傳入訊息。 如需詳細資訊，請參閱 Slack 網站上的 [Incoming Webhook](https://api.slack.com/incoming-webhooks) 。 以下是更複雜的承載，可建立加上格式的豐富訊息︰

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


這會在 Slack 中產生類似如下的訊息。

![example message in Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>摘要
有了此警示規則後，每次符合準則時，我們就將訊息傳送至 Slack。  

這只是舉例說明您可以建立以回應警示的動作。  您可以建立 webhook 動作以呼叫另一個外部服務、建立 Runbook 動作以啟動 Azure 自動化中的 Runbook，或建立電子郵件動作將郵件傳送給自己或其他收件者。   

## <a name="next-steps"></a>後續步驟
* 深入了解 [Log Analytics 中的其他警示動作](log-analytics-alerts-actions.md)，包括其他動作。


