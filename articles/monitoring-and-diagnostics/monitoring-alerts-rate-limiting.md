---
title: "SMS、電子郵件和 Webhook 的速率限制 | Microsoft Docs"
description: "了解 Azure 如何限制可能來自動作群組的 SMS、電子郵件或 Webhook 通知數目。"
author: anirudhcavale
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
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>SMS 訊息、電子郵件和 Webhook 文章的速率限制
速率限制是當傳送了太多通知給特定電話號碼或電子郵件地址時所發生的通知暫停。 速率限制可確保警示為可管理且可採取動作。

SMS 和電子郵件的規則相同。 以下項目的速率限制閾值：

 - **SMS**：1 小時 10 個訊息。
 - **電子郵件**：1 小時 100 個訊息。

## <a name="rate-limit-rules"></a>速率限制規則
- 當特定電話號碼或電子郵件收到的訊息超過閾值允許數量時，會受到速率限制。
- 電話號碼或電子郵件可以是跨許多訂用帳戶動作群組的一部分。 速率限制適用於所有訂用帳戶。 它適用於觸達閾值時，即使是從多個訂用帳戶傳送訊息。  
- 當電話號碼或電子郵件受到速率限制時，將會傳送另一個通知來溝通速率限制。 通知會說明速率限制到期的時間。

## <a name="rate-limit-of-webhooks"></a>Webhook 的速率限制 ##
沒有對 Webhook 的既有速率限制。

## <a name="next-steps"></a>後續步驟 ##
* 進一步了解 [SMS 警示行為](monitoring-sms-alert-behavior.md)。
* 取得[活動記錄警示的概觀](monitoring-overview-alerts.md)，並了解如何收到警示。  
* 了解如何[設定每當服務健康狀態通知公佈時的警示](monitoring-activity-log-alerts-on-service-notifications.md)。
