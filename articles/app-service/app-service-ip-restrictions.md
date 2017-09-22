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
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 83cdc42d412f646ddf1ecd1b65bf9aa46983b26b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 靜態 IP 限制 #

IP 限制可讓您定義要加以封鎖，不得存取應用程式的 IP 位址清單。 封鎖清單可以包含個別的 IP 位址，或以子網路遮罩所定義的 IP 位址範圍。

當用戶端產生應用程式要求時，系統會根據封鎖清單評估 IP 位址。 如果發現相符項目，應用程式會回覆 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 狀態碼。

IP 限制會在指派給應用程式的同一個 App Service 方案執行個體上進行評估。

若要將 IP 限制規則新增至應用程式，請使用功能表來開啟 [網路] > [IP 限制]，然後按一下 [設定 IP 限制]

![IP 限制] (media/app-service-ip-restrictions/ip-restrictions.png)

從這裡，您可以檢閱針對應用程式定義的 IP 限制規則清單。

![IP 限制清單](media/app-service-ip-restrictions/browse-ip-restrictions.png)

您可以按一下 [[+] 新增] 新增 IP 限制規則。

![新增 IP 限制](media/app-service-ip-restrictions/add-ip-restrictions.png)

