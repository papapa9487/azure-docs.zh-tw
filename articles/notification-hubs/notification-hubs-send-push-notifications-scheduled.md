---
title: "如何傳送排定通知 | Microsoft Docs"
description: "本主題說明如何透過 Azure 通知中樞使用排定通知。"
services: notification-hubs
documentationcenter: .net
keywords: "推播通知,推播通知,排程推播通知"
author: ysxu
manager: erikre
editor: 
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: efac6e1ecc00359f1622d380333140bc055c83e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-scheduled-notifications"></a>作法：傳送排定通知
## <a name="overview"></a>概觀
如果您想要在未來某個時間點傳送通知，但卻沒有簡單的方法可喚醒您的後端程式碼來傳送通知。 標準層通知中樞支援的功能可讓您排程未來 7 天的通知。

傳送通知時，只要使用通知中樞 SDK 中的 [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) 類別，如下列範例所示︰

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

此外，您也可以使用其 notificationId 取消先前已排程的通知︰

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

您可以傳送的排定通知數目沒有限制。

