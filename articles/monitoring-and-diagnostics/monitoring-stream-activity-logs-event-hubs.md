---
title: "將 Azure 活動記錄檔串流至事件中樞 | Microsoft Docs"
description: "了解如何將 Azure 活動記錄檔串流至事件中樞。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/06/2017
ms.author: johnkem
ms.openlocfilehash: 88c5701279f370914fac68872d67b02a7571748a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>將 Azure 活動記錄檔串流至事件中樞
您可以使用入口網站中內建的「匯出」選項，或透過 Azure PowerShell Cmdlet 或 Azure CLI 來啟用記錄設定檔中服務匯流排規則識別碼的方式，迅速將 [**Azure 活動記錄檔**](monitoring-overview-activity-logs.md)串流至任何應用程式。

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>您可以使用活動記錄檔與事件中樞執行的項目
此處提供一些您可以使用活動記錄檔串流功能的方法：

* **串流至協力廠商記錄與遙測系統** – 經過一段時間，事件中樞串流會成為將活動記錄檔輸送到協力廠商的 SIEM 與記錄分析解決方案的機制。
* **建置自訂遙測及記錄平台** – 如果您已有自建遙測平台或正在考慮建置一個，事件中樞所具備的高度可調整發佈訂閱特質可讓您靈活擷取活動記錄檔。 [請參閱此處的 Dan Rosanova 指南，以在全球級別的遙測平台中使用事件中樞。](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>啟用活動記錄檔的串流功能
您可以以程式控制的方式，或透過入口網站來啟用活動記錄檔的串流功能。 無論您使用何種方式，您都會選擇服務匯流排命名空間及該命名空間的共用存取原則，而且在第一個新的活動記錄檔事件發生時，該命名空間中將會建立事件中樞。 如果您沒有服務匯流排命名空間，您必須先建立一個。 如果您先前已將活動記錄檔事件串流處理到此服務匯流排命名空間，則會重複使用先前建立的事件中樞。 共用存取原則會定義串流機制具有的權限。 目前，串流到事件中樞需要**管理**、**傳送**和**接聽**權限。 您可以在傳統入口網站 [設定] 索引標籤下，為您的服務匯流排命名空間建立或修改服務匯流排命名空間共用存取原則。 若要更新活動記錄檔設定檔以加入串流，進行變更的使用者必須擁有該服務匯流排授權規則的 ListKey 權限。

服務匯流排或事件中樞命名空間不一定要和訂用帳戶發出記錄檔屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。

### <a name="via-azure-portal"></a>透過 Azure 入口網站
1. 使用入口網站左側的功能表，瀏覽至 [活動記錄檔]  刀鋒視窗。
   
    ![在入口網站中瀏覽至活動記錄檔](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 按一下刀鋒視窗頂端的 [匯出]  按鈕。
   
    ![入口網站中的匯出按鈕](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在出現的刀鋒視窗中，您可以選取想要串流事件的區域，以及服務匯流排命名空間，以在其中建立事件中樞建立以對這些事件進行串流處理。
   
    ![匯出活動記錄檔刀鋒視窗](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 按一下 [儲存]  來儲存這些設定。 您的訂用帳戶時會立即套用設定。

### <a name="via-powershell-cmdlets"></a>透過 PowerShell Cmdlet
如果記錄檔設定檔已存在，您必須先移除該設定檔。

1. 使用 `Get-AzureRmLogProfile` 來識別記錄檔設定檔是否存在
2. 如果有，使用 `Remove-AzureRmLogProfile` 來進行移除。
3. 使用 `Set-AzureRmLogProfile` 來建立設定檔：

```
Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

舉例來說，服務匯流排規則識別碼一組字串，格式如下：{service bus resource ID}/authorizationrules/{key name} 

### <a name="via-azure-cli"></a>透過 Azure CLI
如果記錄檔設定檔已存在，您必須先移除該設定檔。

1. 使用 `azure insights logprofile list` 來識別記錄檔設定檔是否存在
2. 如果有，使用 `azure insights logprofile delete` 來進行移除。
3. 使用 `azure insights logprofile add` 來建立設定檔：

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

服務匯流排規則識別碼是此格式的字串︰ `{service bus resource ID}/authorizationrules/{key name}`。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>我要如何透過事件中樞取用記錄檔資料？
[記錄檔的結構描述可在此取得](monitoring-overview-activity-logs.md)。 每個事件位於名為「記錄」的 JSON blob 陣列中。

## <a name="next-steps"></a>後續步驟
* [將活動記錄檔封存至儲存體帳戶](monitoring-archive-activity-log.md)
* [閱讀 Azure 活動記錄檔的概觀](monitoring-overview-activity-logs.md)
* [根據活動記錄檔事件設定警示](insights-auditlog-to-webhook-email.md)

