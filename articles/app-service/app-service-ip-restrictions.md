---
title: "Azure App Service IP 限制 | Microsoft Docs"
description: "如何搭配使用 IP 限制和 Azure App Service"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/12/2017
ms.author: byvinyal
ms.openlocfilehash: 54c0c5050c812c2a59631541d94c553974acd2f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 靜態 IP 限制 #

IP 限制可讓您定義允許存取應用程式的 IP 位址清單。 允許清單可以包含個別 IP 位址，或以子網路遮罩所定義的 IP 位址範圍。

當用戶端對應用程式產生要求時，就會根據允許清單來評估 IP 位址。 如果此 IP 位址不在清單中，應用程式會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

IP 限制會在指派給應用程式的同一個 App Service 方案執行個體上進行評估。

若要將 IP 限制規則新增至應用程式，請使用功能表來開啟 [網路] > [IP 限制]，然後按一下 [設定 IP 限制]

![IP 限制] (media/app-service-ip-restrictions/ip-restrictions.png)

從這裡，您可以檢閱針對應用程式定義的 IP 限制規則清單。

![IP 限制清單](media/app-service-ip-restrictions/browse-ip-restrictions.png)

您可以按一下 [[+] 新增] 新增 IP 限制規則。

![新增 IP 限制](media/app-service-ip-restrictions/add-ip-restrictions.png)
