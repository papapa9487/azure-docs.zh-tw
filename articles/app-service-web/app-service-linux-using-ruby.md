---
title: "在 Linux 上的 Azure App Service Web 應用程式中使用 Ruby | Microsoft Docs"
description: "在 Linux 上的 Azure App Service Web 應用程式中使用 Ruby。"
keywords: "azure app service, web 應用程式, 常見問題集, linux, oss, ruby"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 56105d1bc153e552e12c0c408c8f6075e4eff9d0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="using-ruby-in-web-app-on-linux"></a>在 Linux 上的 Web 應用程式中使用 Ruby #

在最後更新我們的後端時，我們開始支援 Ruby 2.3 版。 您可以設定 Linux Web 應用程式的組態，以變更應用程式堆疊。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站 ##

從 [Azure 入口網站](https://portal.azure.com)的 [新增] 功能表中，您可以從 [Web + 行動] 選項，選擇在 Linux 上建立 Web 應用程式，如下圖所示︰

![在 Azure 入口網站上開始建立 Web 應用程式][1]

接著會開啟 [建立] 刀鋒視窗，如下圖所示︰

![[建立] 刀鋒視窗][2]

1. 命名您的 Web 應用程式。
2. 選擇現有的資源群組或建立新群組。 (請參閱[限制](app-service-linux-intro.md)一節中可用的區域。)
3. 選擇現有的 Azure App Service 方案或建立新方案。 (請參閱[限制](app-service-linux-intro.md)一節中的 App Service 方案附註。)
4. 從內建的執行階段堆疊中選擇 Ruby。

在建立您的 Ruby Web 應用程式之後，您可以使用 Git 或 FTP 來部署。

若要深入了解建立 Ruby 應用程式，請參閱[使用者入門指南](app-service-linux-ruby-get-started.md)

## <a name="next-steps"></a>後續步驟
* [什麼是 Linux 上的 Web 應用程式？](app-service-linux-intro.md)
* [本機 Git 部署至 Azure App Service](app-service-deploy-local-git.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](app-service-linux-faq.md)
* [使用 Linux 上的 Azure Web 應用程式建立 Ruby 應用程式](app-service-linux-ruby-get-started.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
